JavaScript Engine Questions
===========================

Node started on the V8 JavaScript engine and has been tightly coupled with V8.
The specific requirements and dependencies on various V8 behavior are not well
documented. One of the biggest difficulties in making Node run with ChakraCore
was to find out the various dependencies and implement accordingly.

V8 has a huge API surface and it is impractical for ChakraShim to match every
V8 API exactly. ChakraShim tried to implement those needed by Node. This
document lists some JS engine aspects or cases that we are uncertain what Node
requires.


### Sandbox requirement

Node [`vm` module] (https://nodejs.org/api/vm.html) provides a powerful sandbox
mechanism, meanwhile places corresponding cross-context support requirements on
JS engine. ChakraShim used proxy-based and later changed to native JSRT
implementation, but still doesn't match official node-v8 behavior exactly. The
cross-context data flow cases seem quite difficult to implement correctly
without a concrete spec. For example Node still has following patch code:

[node_contextify.cc](https://github.com/nodejs/node/blob/master/src/node_contextify.cc#L118)

```c++
// XXX(isaacs): This function only exists because of a shortcoming of
// the V8 SetNamedPropertyHandler function.
//
// It does not provide a way to intercept Object.defineProperty(..)
// calls.  As a result, these properties are not copied onto the
// contextified sandbox when a new global property is added via either
// a function declaration or a Object.defineProperty(global, ...) call.
//
// Note that any function declarations or Object.defineProperty()
// globals that are created asynchronously (in a setTimeout, callback,
// etc.) will happen AFTER the call to copy properties, and thus not be
// caught.
//
// The way to properly fix this is to add some sort of a
// Object::SetNamedDefinePropertyHandler() function that takes a callback,
// which receives the property name and property descriptor as arguments.
//
// Luckily, such situations are rare, and asynchronously-added globals
// weren't supported by Node's VM module until 0.12 anyway.  But, this
// should be fixed properly in V8, and this copy function should be
// removed once there is a better way.
void CopyProperties() {
  HandleScope scope(env()->isolate());

  Local<Context> context = PersistentToLocal(env()->isolate(), context_);
  Local<Object> global =
      context->Global()->GetPrototype()->ToObject(env()->isolate());
  Local<Object> sandbox_obj = sandbox();

  Local<Function> clone_property_method;

  Local<Array> names = global->GetOwnPropertyNames();
  int length = names->Length();
  for (int i = 0; i < length; i++) {
    Local<String> key = names->Get(i)->ToString(env()->isolate());
    bool has = sandbox_obj->HasOwnProperty(context, key).FromJust();
    if (!has) {
      // Could also do this like so:
      //
      // PropertyAttribute att = global->GetPropertyAttributes(key_v);
      // Local<Value> val = global->Get(key_v);
      // sandbox->ForceSet(key_v, val, att);
      //
      // However, this doesn't handle ES6-style properties configured with
      // Object.defineProperty, and that's exactly what we're up against at
      // this point.  ForceSet(key,val,att) only supports value properties
      // with the ES3-style attribute flags (DontDelete/DontEnum/ReadOnly),
      // which doesn't faithfully capture the full range of configurations
      // that can be done using Object.defineProperty.
      if (clone_property_method.IsEmpty()) {
        Local<String> code = FIXED_ONE_BYTE_STRING(env()->isolate(),
            "(function cloneProperty(source, key, target) {\n"
            "  if (key === 'Proxy') return;\n"
            "  try {\n"
            "    var desc = Object.getOwnPropertyDescriptor(source, key);\n"
            "    if (desc.value === source) desc.value = target;\n"
            "    Object.defineProperty(target, key, desc);\n"
            "  } catch (e) {\n"
            "   // Catch sealed properties errors\n"
            "  }\n"
            "})");

        Local<Script> script =
            Script::Compile(context, code).ToLocalChecked();
        clone_property_method = Local<Function>::Cast(script->Run());
        CHECK(clone_property_method->IsFunction());
      }
      Local<Value> args[] = { global, key, sandbox_obj };
      clone_property_method->Call(global, ARRAY_SIZE(args), args);
    }
  }
}
```


### HandleScope

ChakraCore manages object references differently than V8. V8 requires `Local`
handles (handles on stack) be registered and managed by a `HandleScope`
manually, so that V8 can scan those `HandleScope`s and see the registered
handles. ChakraCore GC scans the native stack directly and doesn't need
manual registration. Registering references on stack is a waste with ChakraCore.
However ChakraShim still has to simulate `HandleScope` mechanism because of
concern that usage pattern like following might exist:

```c++
  Local<Value>* args = new Local<Value>[N];
  ...
  someFunction->Call(..., args);
```

In this case the `args` handles are on the native heap which is not scanned by
ChakraCore GC. ChakraShim has to put them in a `HandleScope` to make them
visible to ChakraCore GC.

Does this pattern exist? For all typical usage as seen in Node and native
modules, the handle registering/escaping and HandleScope maintenance are not
needed with ChakraCore. Simulating a `HandleScope` just adds extra work and
gives ChakraCore/ChakraShim a non-trivial performance penalty.


### Function `Signature`

V8 [`Signature` and `AccessorSignature`](https://github.com/nodejs/node/blob/ad0ce574320966ab2c7410a2c2ec8530e2bae500/deps/v8/include/v8.h#L4786)
can validate native callbacks and ensure they are called only on specified
`receiver` type. NODE_SET_PROTOTYPE_METHOD uses this feature to ensure methods
be called on the right type of receiver objects.

[NODE_SET_PROTOTYPE_METHOD](https://github.com/nodejs/node/blob/ad0ce574320966ab2c7410a2c2ec8530e2bae500/src/node.h#L260)

```c++
inline void NODE_SET_PROTOTYPE_METHOD(v8::Local<v8::FunctionTemplate> recv,
                                      const char* name,
                                      v8::FunctionCallback callback) {
  ...
  v8::Local<v8::Signature> s = v8::Signature::New(isolate, recv);
```

ChakraShim has a simple implementation but does not support all V8 features (in
addition to code comment below, V8 also supports parameters validation which
seems not used by Node and not implemented by Chakrashim):

[Utils::CheckSignature] (https://github.com/nodejs/node-chakracore/blob/cdd92ee1ef39363c0f7aad3bfd2c0f93f8ad1413/deps/chakrashim/src/v8signature.cc#L42)

```c++
  // v8 signature check walks hidden prototype chain to find holder. Chakra
  // doesn't support hidden prototypes. Just check the receiver itself.
  bool matched = Utils::IsInstanceOf(*thisPointer, *receiverInstanceTemplate);
```

We are uncertain how crucial this signature checking is to Node and if
ChakraShim's implementation suffices.


### Second-pass weak callback

Latest v8 enhanced weak callback to 2 passes (* exposed more internals). It
appears not used by Node yet, but might in future.

```c++
  // When first called, the embedder MUST Reset() the Global which triggered the
  // callback. The Global itself is unusable for anything else. No v8 other api
  // calls may be called in the first callback. Should additional work be
  // required, the embedder must set a second pass callback, which will be
  // called after all the initial callbacks are processed.
  // Calling SetSecondPassCallback on the second pass will immediately crash.
  void SetSecondPassCallback(Callback callback) const { *callback_ = callback; }
```

Without sufficient documentation, it would be hard for ChakraCore/ChakraShim to
figure out what happens in each pass and support it.

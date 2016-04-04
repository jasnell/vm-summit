JSRT APIs added for Node-ChakraCore
===================================

This is a list of
[JSRT](https://msdn.microsoft.com/en-us/library/dn249673(v=vs.94).aspx)
APIs we added or changed specifically to support running Node.js with ChakraCore
(in reverse chronological order).

- [JsInstanceOf] (#jsinstanceof)
- [JsGetTypedArrayInfo] (#jsgettypedarrayinfo)
- [JsGetContextOfObject] (#jsgetcontextofobject)
- [JsSetContextData] (#jssetcontextdata)
- [JsCreateExternalArrayBuffer] (#jscreateexternalarraybuffer)
- [EnableExperimentalFeatures] (#enableexperimentalfeatures)
- [JsSetIndexedPropertiesToExternalData] (#jssetindexedpropertiestoexternaldata)
- [JsNumberToInt] (#jsnumbertoint)
- [JsCreateNamedFunction] (#jscreatenamedfunction)
- [JsSetObjectBeforeCollectCallback] (#jssetobjectbeforecollectcallback)


### JsInstanceOf

```c++
STDAPI_(JsErrorCode)
    JsInstanceOf(
        _In_ JsValueRef object,
        _In_ JsValueRef constructor,
        _Out_ bool *result);
```

Added for performance. Node native code has some type checks in hot call paths,
e.g. calling [FunctionTemplate::HasInstance](https://github.com/nodejs/node/blob/9148114c93861359a502801499d4c26d0b761174/deps/v8/include/v8.h#L4426).
ChakraShim implementation depends on JS `instanceof` operator. Previously we
injected and called a trivial custom JS function which delegates to
`instanceof`. This JSRT API enables us to call it directly and makes it much
more efficient.


### JsGetTypedArrayInfo

```c++
STDAPI_(JsErrorCode)
    JsGetTypedArrayInfo(
        _In_ JsValueRef typedArray,
        _Out_opt_ JsTypedArrayType *arrayType,
        _Out_opt_ JsValueRef *arrayBuffer,
        _Out_opt_ unsigned int *byteOffset,
        _Out_opt_ unsigned int *byteLength);
```

Added for performance. These are used frequently in latest
[`Buffer`](https://github.com/nodejs/node/blob/master/src/node_buffer.cc)
implementation. Node calls native V8 APIs to check TypedArray type ([Value::IsUint8Array](https://github.com/nodejs/node/blob/9148114c93861359a502801499d4c26d0b761174/deps/v8/include/v8.h#L1879)), accesses TypedArray's
[`arrayBuffer`](https://github.com/nodejs/node/blob/9148114c93861359a502801499d4c26d0b761174/deps/v8/include/v8.h#L3505),
[`byteOffset`](https://github.com/nodejs/node/blob/9148114c93861359a502801499d4c26d0b761174/deps/v8/include/v8.h#L3509),
[`byteLength`](https://github.com/nodejs/node/blob/9148114c93861359a502801499d4c26d0b761174/deps/v8/include/v8.h#L3513)
properties in various `Buffer` APIs. Previous ChakraShim implementation was to
call `JsGetProperty(ref, propertyId)` with each propertyId respectively. This
new API allows JSRT to directly retrieve those info from internal TypedArray
data structure.


### JsGetContextOfObject

```c++
STDAPI_(JsErrorCode)
    JsGetContextOfObject(
        _In_ JsValueRef object,
        _Out_ JsContextRef *context);
```

Added to better support cross-context (vm module). Node accesses object's
[CreationContext](https://github.com/nodejs/node/blob/21d66d621c5b1ce27498fcb1cb846fd34fce4234/src/node.cc#L1304),
bind unbound script with
[UnboundScript::BindToCurrentContext](https://github.com/nodejs/node/blob/a53b2ac4b1dcde5579d9cba814ca0c4b78e59a9f/src/node_contextify.cc#L808),
both of which needs to access the object's creation context.


### JsSetContextData

```c++
STDAPI_(JsErrorCode)
    JsGetContextData(
        _In_ JsContextRef context,
        _Out_ void **data);

STDAPI_(JsErrorCode)
    JsSetContextData(
        _In_ JsContextRef context,
        _In_ void *data);
```

Added for performance. Node stores
[`EmbedderData`](https://github.com/nodejs/node/blob/fab240a886b69ef9fa78573fc210c15cfe0018f0/src/env-inl.h#L196)
in v8::Context. ChakraShim implements `EmbedderData` (and other feature support)
with ContextShim. This API allows ChakraShim to attach ContextShim data to a
[ScriptContext](https://msdn.microsoft.com/en-us/library/dn249655(v=vs.94).aspx)
and accesses it efficiently.


### JsCreateExternalArrayBuffer

```c++
STDAPI_(JsErrorCode)
    JsCreateExternalArrayBuffer(
        _Pre_maybenull_ _Pre_writable_byte_size_(byteLength) void *data,
        _In_ unsigned int byteLength,
        _In_opt_ JsFinalizeCallback finalizeCallback,
        _In_opt_ void *callbackState,
        _Out_ JsValueRef *result);
```

Added to support latest
[`Buffer` implementation](https://github.com/nodejs/node/blob/7d73e60f60e2161457ca4c923008c9064a36bced/src/node_buffer.cc#L281)
and shared access to native memory from script, e.g.
[domain_flag->fields](https://github.com/nodejs/node/blob/21d66d621c5b1ce27498fcb1cb846fd34fce4234/src/node.cc#L1070).
Most often an ArrayBuffer is created and managed by the script engine. This new
API allows ArrayBuffer to use/interop with external memory buffer.


### EnableExperimentalFeatures

```c++
JsRuntimeAttributeEnableExperimentalFeatures = 0x00000020,
...

STDAPI_(JsErrorCode)
    JsCreateRuntime(
        _In_ JsRuntimeAttributes attributes,
        _In_opt_ JsThreadServiceCallback threadService,
        _Out_ JsRuntimeHandle *runtime);
```

During ChakraShim development, we noticed that new stable language features
were released at different time for V8 and Chakra. In one case Node started to
use ES6 function generator (e.g.
[readline.js](https://github.com/nodejs/node/blob/21d66d621c5b1ce27498fcb1cb846fd34fce4234/lib/internal/readline.js#L130))
when the feature is official with V8 but still experimental with Chakra. How to
resolve this timing differences completely remains an issue. Adding this new
flag to
[JsCreateRuntime](https://msdn.microsoft.com/en-us/library/dn249646(v=vs.94).aspx)
allows ChakraShim to turn on Chakra experimental
features and unblock such scenarios for the timebeing.


### JsSetIndexedPropertiesToExternalData

```c++
STDAPI_(JsErrorCode)
    JsHasIndexedPropertiesExternalData(
        _In_ JsValueRef object,
        _Out_ bool* value);

STDAPI_(JsErrorCode)
    JsGetIndexedPropertiesExternalData(
        _In_ JsValueRef object,
        _Out_ void** data,
        _Out_ JsTypedArrayType* arrayType,
        _Out_ unsigned int* elementLength);

STDAPI_(JsErrorCode)
    JsSetIndexedPropertiesToExternalData(
        _In_ JsValueRef object,
        _In_ void* data,
        _In_ JsTypedArrayType arrayType,
        _In_ unsigned int elementLength);
```

Added to better support Node's legacy v0.12 `Buffer` implementation. At the
time `Buffer` depends on
[Object::SetIndexedPropertiesToExternalArrayData](https://github.com/nodejs/node-v0.x-archive/blob/master/deps/v8/include/v8.h#L2358) to access external native memory buffer from JS. Without JSRT support
ChakraShim worked around with interceptors which was very slow. These APIs
were added but without JIT support initially. Improved `Buffer` performance by
10+ times. Now that Node switched to TypedArray these APIs are also deprecated.


### JsNumberToInt

```c++
STDAPI_(JsErrorCode)
    JsNumberToInt(
        _In_ JsValueRef value,
        _Out_ int *intValue);
```

Added for compliance and performance. Without this API the host had to get
double value and then convert to int manually, which was both inefficient and error prone (most host would just cast to int, not following the ECMA toInt32
spec).


### JsCreateNamedFunction

```c++
STDAPI_(JsErrorCode)
    JsCreateNamedFunction(
        _In_ JsValueRef name,
        _In_ JsNativeFunction nativeFunction,
        _In_opt_ void *callbackState,
        _Out_ JsValueRef *function);
```

Added for compat and convenience. Node native code creates functions and uses
`FunctionTemplate::SetClassName` to set its name property (e.g.,
[Timer](https://github.com/nodejs/node/blob/6fff47ffacfe663efeb0d31ebd700a65bf5521ba/src/timer_wrap.cc#L33),
[Process](https://github.com/nodejs/node/blob/6fff47ffacfe663efeb0d31ebd700a65bf5521ba/src/process_wrap.cc#L34)
), which is visible by
`toString()`. JSRT used to have only `JsCreateFunction` (without name parameter)
. With later ES spec, `name` property is non-writable by default. A host who
wishes to customize function name would need to configure `name` first then set
its value. The new API simplifies it.


### JsSetObjectBeforeCollectCallback

```c++
STDAPI_(JsErrorCode)
    JsSetObjectBeforeCollectCallback(
        _In_ JsRef ref,
        _In_opt_ void *callbackState,
        _In_ JsObjectBeforeCollectCallback objectBeforeCollectCallback);
```

Node implements a lot of functionalities in native C++ code and exposes them
as JS objects. To connect a native C++ object with a wrapper JS object, Node
stores the C++ object pointer in one internal field of the wrapper JS object,
and also stores a "weak" handle of the wrapper JS object in the C++ object.
The C++ object's lifetime is attached to that of the wrapper JS object. The
supporting V8 API is [Persistent::SetWeak](https://github.com/nodejs/node/blob/9148114c93861359a502801499d4c26d0b761174/deps/v8/include/v8.h#L574).
V8 invokes registered weak callback before collecting the wrapper JS object,
and the callback can then cleanup and delete the C++ object (e.g.,
[BaseObject::WeakCallback]
(https://github.com/nodejs/node/blob/master/src/base-object-inl.h#L41),
[ObjectWrap::WeakCallback](https://github.com/nodejs/node/blob/ad0ce574320966ab2c7410a2c2ec8530e2bae500/src/node_object_wrap.h#L98)).

ChakraShim originally simulated weak callback with [ExternalObject finalizer]
(https://msdn.microsoft.com/en-us/library/dn249638(v=vs.94).aspx). However we
ran into several issues with that approach:

- V8 SetWeak/ClearWeak works on any JS objects. The finalizer only works with
ExternalObjects (e.g. created by the shim itself). Some node code creates
wrapper JS objects from script and passes to native code, which broken the
finalizer scheme.
  * We worked around it by attaching an ExternalObject with finalizer to the
  given JS object as a property. However that poluted the JS object, and in
  addition there were subtle lifetime differences.

- In V8 weak callback one can resurrect the object. The finalizer can not.

Since this API is central to Node object model, we added
[`JsSetObjectBeforeCollectCallback`]
(https://msdn.microsoft.com/en-us/library/mt125488(v=vs.94).aspx).
Calling it inside a `JsObjectBeforeCollectCallback` ("weak callback") extends the object's lifetime similarly as V8 resurrect.

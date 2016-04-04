JSRT APIs used by Node-ChakraCore
=================================

This is a list of all the [JSRT](https://msdn.microsoft.com/en-us/library/dn249673.aspx)
that are imported into node.exe from Node.js with ChakraCore.

## JSRT APIs Used

_Italicized APIs were added [specifically for Node.js](NewJsrtApis.md)_

 * [JSRT Runtime Management](#jsrt-runtime-management)

   - `JsCreateRuntime`
   - `JsDisposeRuntime`
   - `JsDisableRuntimeExecution`
   - `JsEnableRuntimeExecution`
   - `JsIsRuntimeExecutionDisabled`
   - `JsCollectGarbage`
   - `JsGetAndClearException`
   - `JsHasException`
   - `JsSetException`
   - `JsGetRuntimeMemoryUsage`
   - `JsSetPromiseContinuationCallback`

 * [JSRT Script Context Management](#jsrt-script-context-management)

   - `JsCreateContext`
   - `JsSetCurrentContext`
   - [_`JsGetContextData`_](NewJsrtApis.md#jssetcontextdata)
   - [_`JsSetContextData`_](NewJsrtApis.md#jssetcontextdata)
   - [_`JsGetContextOfObject`_](NewJsrtApis.md#jsgetcontextofobject)
   - `JsGetGlobalObject`

 * [GC Object Reference Tracking](#gc-object-reference-tracking)

   - `JsAddRef`
   - `JsRelease`
   - [_`JsSetObjectBeforeCollectCallback`_](NewJsrtApis.md#jssetobjectbeforecollectcallback)

 * [JS Equality Comparison](#js-equality-comparison)

   - `JsEquals`
   - `JsStrictEquals`

 * [JS Singleton Values](#js-singleton-values)

   - `JsGetUndefinedValue`
   - `JsGetNullValue`
   - `JsGetTrueValue`
   - `JsGetFalseValue`

 * [JS Object Creation](#js-object-creation)

   - `JsCreateArray`
   - `JsCreateArrayBuffer`
   - `JsCreateError`
   - [_`JsCreateExternalArrayBuffer`_](NewJsrtApis.md#jscreateexternalarraybuffer)
   - `JsCreateExternalObject`
   - `JsCreateFunction`
   - [_`JsCreateNamedFunction`_](NewJsrtApis.md#jscreatenamedfunction)
   - `JsCreateObject`
   - `JsCreateRangeError`
   - `JsCreateReferenceError`
   - `JsCreateSymbol`
   - `JsCreateSyntaxError`
   - `JsCreateTypeError`

 * [JS Value Coercion](#js-value-coercion)

   - `JsConvertValueToBoolean`
   - `JsConvertValueToNumber`
   - `JsConvertValueToObject`
   - `JsConvertValueToString`

 * [JS / Native Value Conversions](#js--native-value-conversions)

   - `JsBooleanToBool`
   - `JsDoubleToNumber`
   - `JsIntToNumber`
   - `JsNumberToDouble`
   - [_`JsNumberToInt`_](NewJsrtApis.md#jsnumbertoint)
   - `JsPointerToString`
   - `JsStringToPointer`

 * [JS Value Manipulation](#js-value-manipulation)

   - `JsGetValueType`
   - [_`JsInstanceOf`_](NewJsrtApis.md#jsinstanceof)
   - `JsGetPrototype`
   - `JsSetPrototype`
   - `JsGetStringLength`
   - [_`JsGetTypedArrayInfo`_](NewJsrtApis.md#jsgettypedarrayinfo)
   - `JsGetArrayBufferStorage`

 * [JSRT PropertyId Retrieval](#jsrt-propertyid-retrieval)

   - `JsGetPropertyIdFromName`
   - `JsGetPropertyIdFromSymbol`

 * [JS Object Property Manipulation](#js-object-property-manipulation)

   - `JsDefineProperty`
   - `JsDeleteIndexedProperty`
   - `JsDeleteProperty`
   - `JsGetIndexedProperty`
   - `JsGetOwnPropertyDescriptor`
   - `JsGetOwnPropertyNames`
   - `JsGetProperty`
   - `JsHasIndexedProperty`
   - `JsHasProperty`
   - `JsSetIndexedProperty`
   - `JsSetProperty`

 * [JS Script Parse and Function Invocation](#js-script-parse-and-function-invocation)

   - `JsParseScript`
   - `JsCallFunction`
   - `JsConstructObject`

 * [External Object Data](#external-object-data)

   - `JsGetExternalData`

### JSRT Runtime Management

```C
    STDAPI_(JsErrorCode)
        JsCreateRuntime(
            _In_ JsRuntimeAttributes attributes,
            _In_opt_ JsThreadServiceCallback threadService,
            _Out_ JsRuntimeHandle *runtime);

    STDAPI_(JsErrorCode)
        JsDisposeRuntime(
            _In_ JsRuntimeHandle runtime);

    STDAPI_(JsErrorCode)
        JsDisableRuntimeExecution(
            _In_ JsRuntimeHandle runtime);

    STDAPI_(JsErrorCode)
        JsEnableRuntimeExecution(
            _In_ JsRuntimeHandle runtime);

    STDAPI_(JsErrorCode)
        JsIsRuntimeExecutionDisabled(
            _In_ JsRuntimeHandle runtime,
            _Out_ bool *isDisabled);

    STDAPI_(JsErrorCode)
        JsCollectGarbage(
            _In_ JsRuntimeHandle runtime);

    STDAPI_(JsErrorCode)
        JsGetAndClearException(
            _Out_ JsValueRef *exception);

    STDAPI_(JsErrorCode)
        JsHasException(
            _Out_ bool *hasException);

    STDAPI_(JsErrorCode)
        JsSetException(
            _In_ JsValueRef exception);

    STDAPI_(JsErrorCode)
        JsGetRuntimeMemoryUsage(
            _In_ JsRuntimeHandle runtime,
            _Out_ size_t *memoryUsage);

    STDAPI_(JsErrorCode)
        JsSetPromiseContinuationCallback(
            _In_ JsPromiseContinuationCallback promiseContinuationCallback,
            _In_opt_ void *callbackState);
```

Create and control a JSRT runtime.

Disable/enable runtime execution.

Force garbage collection.

Manage exception state.

Query runtime memory usage.

Set promise continuation callback handler for scheduling promise callbacks
in the host event loop.

### JSRT Script Context Management

```C
    STDAPI_(JsErrorCode)
        JsCreateContext(
            _In_ JsRuntimeHandle runtime,
            _Out_ JsContextRef *newContext);

    STDAPI_(JsErrorCode)
        JsSetCurrentContext(
            _In_ JsContextRef context);

    STDAPI_(JsErrorCode)
        JsGetContextData(
            _In_ JsContextRef context,
            _Out_ void **data);

    STDAPI_(JsErrorCode)
        JsSetContextData(
            _In_ JsContextRef context,
            _In_ void *data);

    STDAPI_(JsErrorCode)
        JsGetContextOfObject(
            _In_ JsValueRef object,
            _Out_ JsContextRef *context);

    STDAPI_(JsErrorCode)
        JsGetGlobalObject(
            _Out_ JsValueRef *globalObject);

```

Create and active/de-activate current script context on a JSRT runtime.

Set and retrieve associated data with a given context.
[`JsGetContextData` and `JsSetContextData`](NewJsrtApis.md#jssetcontextdata)
were added for Node.js support.

Get the context a given object was created on.
[`JsGetContextOfObject`](NewJsrtApis.md#jsgetcontextofobject) was added for Node.js support.

Get the global object from a given script context.

### GC Object Reference Tracking

```C
    STDAPI_(JsErrorCode)
        JsAddRef(
            _In_ JsRef ref,
            _Out_opt_ unsigned int *count);

    STDAPI_(JsErrorCode)
        JsRelease(
            _In_ JsRef ref,
            _Out_opt_ unsigned int *count);

    STDAPI_(JsErrorCode)
        JsSetObjectBeforeCollectCallback(
            _In_ JsRef ref,
            _In_opt_ void *callbackState,
            _In_ JsObjectBeforeCollectCallback objectBeforeCollectCallback);

```

Add and release refences to JSRT garbage collected objects.

Set callback to hook a given object's eventual collection.
[`JsSetObjectBeforeCollectCallback`](NewJsrtApis.md#jssetobjectbeforecollectcallback)
was added for Node.js.

### JS Equality Comparison

```C
    STDAPI_(JsErrorCode)
        JsEquals(
            _In_ JsValueRef object1,
            _In_ JsValueRef object2,
            _Out_ bool *result);

    STDAPI_(JsErrorCode)
        JsStrictEquals(
            _In_ JsValueRef object1,
            _In_ JsValueRef object2,
            _Out_ bool *result);

```

JS semantics equality and strict equality.

### JS Singleton Values

```C
    STDAPI_(JsErrorCode)
        JsGetUndefinedValue(
            _Out_ JsValueRef *undefinedValue);

    STDAPI_(JsErrorCode)
        JsGetNullValue(
            _Out_ JsValueRef *nullValue);

    STDAPI_(JsErrorCode)
        JsGetTrueValue(
            _Out_ JsValueRef *trueValue);

    STDAPI_(JsErrorCode)
        JsGetFalseValue(
            _Out_ JsValueRef *falseValue);
```

Retrieve the runtime's singleton values for each of the JS `undefined`,
`null`, `true`, and `false` values respectively.

### JS Object Creation

```C
    STDAPI_(JsErrorCode)
        JsCreateArray(
            _In_ unsigned int length,
            _Out_ JsValueRef *result);

    STDAPI_(JsErrorCode)
        JsCreateArrayBuffer(
            _In_ unsigned int byteLength,
            _Out_ JsValueRef *result);

    STDAPI_(JsErrorCode)
        JsCreateError(
            _In_ JsValueRef message,
            _Out_ JsValueRef *error);

    STDAPI_(JsErrorCode)
        JsCreateExternalArrayBuffer(
            _Pre_maybenull_ _Pre_writable_byte_size_(byteLength) void *data,
            _In_ unsigned int byteLength,
            _In_opt_ JsFinalizeCallback finalizeCallback,
            _In_opt_ void *callbackState,
            _Out_ JsValueRef *result);

    STDAPI_(JsErrorCode)
        JsCreateExternalObject(
            _In_opt_ void *data,
            _In_opt_ JsFinalizeCallback finalizeCallback,
            _Out_ JsValueRef *object);

    STDAPI_(JsErrorCode)
        JsCreateFunction(
            _In_ JsNativeFunction nativeFunction,
            _In_opt_ void *callbackState,
            _Out_ JsValueRef *function);

    STDAPI_(JsErrorCode)
        JsCreateNamedFunction(
            _In_ JsValueRef name,
            _In_ JsNativeFunction nativeFunction,
            _In_opt_ void *callbackState,
            _Out_ JsValueRef *function);

    STDAPI_(JsErrorCode)
        JsCreateObject(
            _Out_ JsValueRef *object);

    STDAPI_(JsErrorCode)
        JsCreateRangeError(
            _In_ JsValueRef message,
            _Out_ JsValueRef *error);

    STDAPI_(JsErrorCode)
        JsCreateReferenceError(
            _In_ JsValueRef message,
            _Out_ JsValueRef *error);

    STDAPI_(JsErrorCode)
        JsCreateSymbol(
            _In_ JsValueRef description,
            _Out_ JsValueRef *result);

    STDAPI_(JsErrorCode)
        JsCreateSyntaxError(
            _In_ JsValueRef message,
            _Out_ JsValueRef *error);

    STDAPI_(JsErrorCode)
        JsCreateTypeError(
            _In_ JsValueRef message,
            _Out_ JsValueRef *error);
```

Create the various types of JS objects.

Two special types are external objects and externally backed `ArrayBuffer`
objects.  External objects allow association with host data via a `void*`
pointer.  External `ArrayBuffer` objects appear as such to JS code but use a
host specified memory buffer as the storage.  External `ArrayBuffer` objects
allow for an optional host specified finalizer callback.

[`JsCreateExternalArrayBuffer`](NewJsrtApis.md#jscreateexternalarraybuffer)
and [`JsCreateNamedFunction`](NewJsrtApis.md#jscreatenamedfunction)
were added for Node.js.

### JS Script Parse and Function Invocation

```C
    STDAPI_(JsErrorCode)
        JsParseScript(
            _In_z_ const wchar_t *script,
            _In_ JsSourceContext sourceContext,
            _In_z_ const wchar_t *sourceUrl,
            _Out_ JsValueRef *result);

    STDAPI_(JsErrorCode)
        JsCallFunction(
            _In_ JsValueRef function,
            _In_reads_(argumentCount) JsValueRef *arguments,
            _In_ unsigned short argumentCount,
            _Out_opt_ JsValueRef *result);

    STDAPI_(JsErrorCode)
        JsConstructObject(
            _In_ JsValueRef function,
            _In_reads_(argumentCount) JsValueRef *arguments,
            _In_ unsigned short argumentCount,
            _Out_ JsValueRef *result);
```

Parse script text into a function object.

Call a function object normally or as a constructor call.

### JS Value Coercion

```C
    STDAPI_(JsErrorCode)
        JsConvertValueToBoolean(
            _In_ JsValueRef value,
            _Out_ JsValueRef *booleanValue);

    STDAPI_(JsErrorCode)
        JsConvertValueToNumber(
            _In_ JsValueRef value,
            _Out_ JsValueRef *numberValue);

    STDAPI_(JsErrorCode)
        JsConvertValueToObject(
            _In_ JsValueRef value,
            _Out_ JsValueRef *object);

    STDAPI_(JsErrorCode)
        JsConvertValueToString(
            _In_ JsValueRef value,
            _Out_ JsValueRef *stringValue);
```

Converts a value to a boolean, number, object, or string respectively,
according to ES semantics defined in e.g. ES 2015
[7.1.2](http://www.ecma-international.org/ecma-262/6.0/index.html#sec-toboolean),
[7.1.3](http://www.ecma-international.org/ecma-262/6.0/index.html#sec-tonumber),
[7.1.13](http://www.ecma-international.org/ecma-262/6.0/index.html#sec-toobject),
[7.1.12](http://www.ecma-international.org/ecma-262/6.0/index.html#sec-tostring)
respectively.

### JS / Native Value Conversions

```C
    STDAPI_(JsErrorCode)
        JsBooleanToBool(
            _In_ JsValueRef value,
            _Out_ bool *boolValue);

    STDAPI_(JsErrorCode)
        JsDoubleToNumber(
            _In_ double doubleValue,
            _Out_ JsValueRef *value);

    STDAPI_(JsErrorCode)
        JsIntToNumber(
            _In_ int intValue,
            _Out_ JsValueRef *value);

    STDAPI_(JsErrorCode)
        JsNumberToDouble(
            _In_ JsValueRef value,
            _Out_ double *doubleValue);

    STDAPI_(JsErrorCode)
        JsNumberToInt(
            _In_ JsValueRef value,
            _Out_ int *intValue);

    STDAPI_(JsErrorCode)
        JsPointerToString(
            _In_reads_(stringLength) const wchar_t *stringValue,
            _In_ size_t stringLength,
            _Out_ JsValueRef *value);

    STDAPI_(JsErrorCode)
        JsStringToPointer(
            _In_ JsValueRef value,
            _Outptr_result_buffer_(*stringLength) const wchar_t **stringValue,
            _Out_ size_t *stringLength);

```

Convert between JS values and C `bool`, `int`, `double`, and `wchar_t*` strings.

[`JsNumberToInt`](NewJsrtApis.md#jsnumbertoint)
was added for Node.js

### JS Value Manipulation

```C
    STDAPI_(JsErrorCode)
        JsGetValueType(
            _In_ JsValueRef value,
            _Out_ JsValueType *type);

    STDAPI_(JsErrorCode)
        JsInstanceOf(
            _In_ JsValueRef object,
            _In_ JsValueRef constructor,
            _Out_ bool *result);

    STDAPI_(JsErrorCode)
        JsGetPrototype(
            _In_ JsValueRef object,
            _Out_ JsValueRef *prototypeObject);

    STDAPI_(JsErrorCode)
        JsSetPrototype(
            _In_ JsValueRef object,
            _In_ JsValueRef prototypeObject);

    STDAPI_(JsErrorCode)
        JsGetStringLength(
            _In_ JsValueRef stringValue,
            _Out_ int *length);

    STDAPI_(JsErrorCode)
        JsGetTypedArrayInfo(
            _In_ JsValueRef typedArray,
            _Out_opt_ JsTypedArrayType *arrayType,
            _Out_opt_ JsValueRef *arrayBuffer,
            _Out_opt_ unsigned int *byteOffset,
            _Out_opt_ unsigned int *byteLength);

    STDAPI_(JsErrorCode)
        JsGetArrayBufferStorage(
            _In_ JsValueRef arrayBuffer,
            _Outptr_result_bytebuffer_(*bufferLength) BYTE **buffer,
            _Out_ unsigned int *bufferLength);

```

Get the JS type of a value.

Test JS semantics instance-of with a given value and constructor function.
[`JsInstanceOf`](NewJsrtApis.md#jsinstanceof) was added for Node.js

Get and set an object's prototype.

Get a string's length.

Get a `TypedArray`'s type, buffer, offset, and length internal properties.
[`JsGetTypedArrayInfo`](NewJsrtApis.md#jsgettypedarrayinfo) was added for
Node.js.

Get an `ArrayBuffer`'s buffer and length properties.

### JSRT PropertyId Retrieval

```C
    STDAPI_(JsErrorCode)
        JsGetPropertyIdFromName(
            _In_z_ const wchar_t *name,
            _Out_ JsPropertyIdRef *propertyId);

    STDAPI_(JsErrorCode)
        JsGetPropertyIdFromSymbol(
            _In_ JsValueRef symbol,
            _Out_ JsPropertyIdRef *propertyId);
```

Get the `PropertyId` value for a given property name string or symbol value.
For use with the property manipulation APIs.

### JS Object Property Manipulation

```C
    STDAPI_(JsErrorCode)
        JsDefineProperty(
            _In_ JsValueRef object,
            _In_ JsPropertyIdRef propertyId,
            _In_ JsValueRef propertyDescriptor,
            _Out_ bool *result);

    STDAPI_(JsErrorCode)
        JsDeleteIndexedProperty(
            _In_ JsValueRef object,
            _In_ JsValueRef index);

    STDAPI_(JsErrorCode)
        JsDeleteProperty(
            _In_ JsValueRef object,
            _In_ JsPropertyIdRef propertyId,
            _In_ bool useStrictRules,
            _Out_ JsValueRef *result);

    STDAPI_(JsErrorCode)
        JsGetIndexedProperty(
            _In_ JsValueRef object,
            _In_ JsValueRef index,
            _Out_ JsValueRef *result);

    STDAPI_(JsErrorCode)
        JsGetOwnPropertyDescriptor(
            _In_ JsValueRef object,
            _In_ JsPropertyIdRef propertyId,
            _Out_ JsValueRef *propertyDescriptor);

    STDAPI_(JsErrorCode)
        JsGetOwnPropertyNames(
            _In_ JsValueRef object,
            _Out_ JsValueRef *propertyNames);

    STDAPI_(JsErrorCode)
        JsGetProperty(
            _In_ JsValueRef object,
            _In_ JsPropertyIdRef propertyId,
            _Out_ JsValueRef *value);

    STDAPI_(JsErrorCode)
        JsHasIndexedProperty(
            _In_ JsValueRef object,
            _In_ JsValueRef index,
            _Out_ bool *result);

    STDAPI_(JsErrorCode)
        JsHasProperty(
            _In_ JsValueRef object,
            _In_ JsPropertyIdRef propertyId,
            _Out_ bool *hasProperty);

    STDAPI_(JsErrorCode)
        JsSetIndexedProperty(
            _In_ JsValueRef object,
            _In_ JsValueRef index,
            _In_ JsValueRef value);

    STDAPI_(JsErrorCode)
        JsSetProperty(
            _In_ JsValueRef object,
            _In_ JsPropertyIdRef propertyId,
            _In_ JsValueRef value,
            _In_ bool useStrictRules);
```

JS operations to define, get, set, delete, and test for presence of properties
of a given object.  Indexed property access refers to array like integer
property 

### External Object Data

```C
    STDAPI_(JsErrorCode)
        JsGetExternalData(
            _In_ JsValueRef object,
            _Out_ void **externalData);
```

Get the `void*` data pointer associated with an external object.

## JSRT APIs Not Currently Used by Node-ChakraCore

Included for reference.

   - `JsBoolToBoolean`
   - `JsCreateDataView`
   - `JsCreateTypedArray`
   - `JsCreateURIError`
   - `JsGetCurrentContext`
   - `JsGetDataViewStorage`
   - `JsGetExtensionAllowed`
   - `JsGetIndexedPropertiesExternalData`
   - `JsGetOwnPropertySymbols`
   - `JsGetPropertyIdType`
   - `JsGetPropertyNameFromId`
   - `JsGetRuntime`
   - `JsGetRuntimeMemoryLimit`
   - `JsGetSymbolFromPropertyId`
   - `JsGetTypedArrayStorage`
   - `JsHasExternalData`
   - `JsHasIndexedPropertiesExternalData`
   - `JsIdle`
   - `JsParseSerializedScript`
   - `JsParseSerializedScriptWithCallback`
   - `JsPreventExtension`
   - `JsRunScript`
   - `JsRunSerializedScript`
   - `JsRunSerializedScriptWithCallback`
   - `JsSerializeScript`
   - `JsSetExternalData`
   - `JsSetIndexedPropertiesToExternalData`
   - `JsSetRuntimeBeforeCollectCallback`
   - `JsSetRuntimeMemoryAllocationCallback`
   - `JsSetRuntimeMemoryLimit`


Fetch API integrated with Streams
===

This document is about the integration of [the Streams API](https://streams.spec.whatwg.org/) with [the Fetch API](https://fetch.spec.whatwg.org/#fetch-api).
Basically, the integration only adds a way to represent the body data and does not affect the fetch algorithm.

## [Bodies] (https://fetch.spec.whatwg.org/#bodies)
A body is a byte stream, which means it is a piped pair of readable and writable byte streams. It has an associated ...(omit)

__The read end__ of a body is the readable stream of the body. It is of type ReadableByteStream.

__The write end__ of a body is the writable stream of the body. It is of type WritableByteStream.

## [Body mixin](https://fetch.spec.whatwg.org/#body-mixin)

```
dictionary BodyStreamInit {
    DOMString contentType;
    ReadableStream stream;
};
typedef (Blob or BufferSource or FormData or URLSearchParams or USVString or BodyStreamInit) BodyInit;
```

To extract a byte stream and a 'Content-Type' value from a BodyStreamInit object, run these steps:
1. Call [[pipeTo]] of object's _stream_ attribute with the write end of the body. Rethrow any exception.
2. Set *Content-Type* to object's _contentType_ attribute.

```
[NoInterfaceObject] interface Body : ReadableByteStream {
    ...
};
```
Objects implementing the Body mixin gain an associated __body__ (a pair of readable and writable byte streams) and a __MIME type__ (initially the empty byte sequence). Objects implments the Body mixin expose methods and attributes of __the read end__ of the body.
The __bodyUsed__ attribute's getter must return if __the read end__ of the body is locked.

Objects implementing the __Body__ mixin also have an associated consume body algorithm, which given a type, runs these steps:
1. Let _p_ be a new promise.
2. Let stream be _the read end_ of the body.
3. If used flag is set, reject _p_ with a TypeError.
4. Otherwise, acquire an exclusive lock of _stream_ and run these substeps in parallel: (subsequent algorithm is as same as the original one).


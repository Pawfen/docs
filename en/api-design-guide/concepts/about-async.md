---

__system: {"dislikeVariants":["No answer to my question","Recomendations didn't help","The content doesn't match title","Other"]}
---
# Working with operationsAny operations that change the state of a resource are [asynchronous](async.md) operations. When they are called, the server returns the `Operation` object. Use this object for [operation status monitoring](operation.md#monitoring).All asynchronous operations support _idempotence_. This means that if the same operation is called multiple times, the server performs this operation only once. For information about how idempotence works and how it can be used, see the section [Idempotency](idempotency.md).
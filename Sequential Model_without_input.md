# Memory usage with and without input specified to Sequential Model

When you make a Sequential model without input shape specified there isn't actually enough information at compile time to create the weights. (For instance in the dense layer above, there's no way to know that the kernel needs to be shape [10, 120] and it can't allocate a [???, 120] Variable.) So Sequential defers weight creation until it sees the first batch, at which point it can create variables. So the lack of memory growth is simply reflecting the fact that the example doesn't reach the point to which allocation is deferred.

On the other hand, when input shape is provided then Sequential actually can allocate weights before seeing the first batch. The reason that those weights aren't freed when the model goes out of scope is that unless otherwise specified, all tensors used by keras share the same global graph. It has the nice property of allowing mixing and matching of models (this is why you can compose models from other models, for instance), but it also means that this graph holds references to weights and therefore blocks their garbage collection even beyond the life of the python Model object.

tf.keras.backend.clear_session destroys the underlying global graph, and therefore allows variables to be freed. 

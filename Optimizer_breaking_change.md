# Type of breakage**: Breakage with changing code.

APIs that are affected: 

1. [`tf.train.GradientDescentOptimizer`](https://www.tensorflow.org/api_docs/python/tf/train/GradientDescentOptimizer)
2. [`tf.train.MomentumOptimizer`](https://www.tensorflow.org/api_docs/python/tf/train/MomentumOptimizer)
3. [`tf.train.RMSPropOptimizer`](https://www.tensorflow.org/api_docs/python/tf/train/RMSPropOptimizer)
4. [`tf.train.AdamOptimizer`](https://www.tensorflow.org/api_docs/python/tf/train/AdamOptimizer)
5. [`tf.train.AdadeltaOptimizer`](https://www.tensorflow.org/api_docs/python/tf/train/AdadeltaOptimizer)
6. [`tf.train.AdagradOptimizer`](https://www.tensorflow.org/api_docs/python/tf/train/AdagradOptimizer)
7. [`tf.train.FtrlOptimizer`](https://www.tensorflow.org/api_docs/python/tf/train/FtrlOptimizer)

**Side Note**: there are two extra `tf.contrib`-owned optimizers that will also have breaking changes: 

1. [`tf.contrib.opt.NadamOptimizer`](https://www.tensorflow.org/api_docs/python/tf/contrib/opt/NadamOptimizer)
2. [`tf.contrib.opt.AdaMaxOptimizer`](https://www.tensorflow.org/api_docs/python/tf/contrib/opt/AdaMaxOptimizer)

**Description of change**: The current endpoint `tf.train.xxxOptimizer()` is being deprecated in favor of `tf.keras.optimizers.xxx()`. Specifically:

1. `tf.train.GradientDescentOptimizer(lr=???)` -> `tf.keras.optimizers.SGD(learning_rate=???)`
2. `tf.train.MomentumOptimizer(lr=???, momentum=???)` -> `tf.keras.optimizers.SGD(learning_rate=???, momentum=???)`
3. `tf.train.RMSPropOptimizer(lr=???)` -> `tf.keras.optimizers.RMSprop(learning_rate=???)`
4. `tf.train.AdamOptimizer(lr=???, beta1=???, beta2=???)` -> `tf.keras.optimizers.Adam(learning_rate=???, beta_1=???, beta_2=???)`
5. `tf.train.AdadeltaOptimizer(lr=???)` -> `tf.keras.optimizers.Adadelta(learning_rate=???)`
6. `tf.train.AdagradOptimizer(lr=???)` -> `tf.keras.optimizers.Adagrad(learning_rate=???)`
7. `tf.train.FtrlOptimizer(lr=???)` -> `tf.keras.Ftrl(learning_rate=???)`

TensorFlow users of `tf.train.xxxOptimizer()` will be updated to `tf.keras.optimizers.xxx()`, and checkpoints from the old `tf.train.xxxOptimizer()` calls will no longer work.

**Variable name change map**:  The `tf.keras.optimizers.xxx` weights are in different format than existing `tf.train.xxxOptimizer`. There isn't direct mapping for that.

**Target time window**: Undecided since the update requires non-trivial user side change.

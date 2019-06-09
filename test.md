# 1.Can I run TF1.x code, unmodified in TF2.0?
Ans: No. But, with simple addition of two lines of code, you could run TF1.x code in TF2.0 (except for contrib)

```python
import tensorflow.compat.v1 as tf
tf.disable_v2_behavior()
```
However, this does not let you take advantage of many of the improvements made in TensorFlow 2.0. 

# 2.Steps to follow for converting TF1.x to TF2.0?
Ans:
  * install `tf-nightly-2.0-preview` or `tf-nightly-gpu-2.0-preview`
  * The upgrade script can be run on a single Python file
```python
tf_upgrade_v2 — infile foo.py — outfile foo-upgraded.py
```
  * You can also run it on a directory tree
```python
# upgrade the .py files and copy all the other files to the outtree
tf_upgrade_v2 — intree foo/ — outtree foo-upgraded/
# just upgrade the .py files
tf_upgrade_v2 — intree foo/ — outtree foo-upgraded/ — copyotherfiles False
```
   * Make sure to read the detailed log file `report.txt`
   * add compat.v1 where ever necessary (to change tf.* to tf.compat.v1.*)
   * check add keywords arguments and reorder them accotrdingly
   * Based on `report.txt`, do manual check of some lines of code
   
[Here](https://medium.com/tensorflow/upgrading-your-code-to-tensorflow-2-0-f72c3a4d83b5) is the source for more detailed instructions.

# 3. How to make TF2.0 Code native after conversion from TF1.x?

Ans:
## i. Replace tf.Session.run calls with a python function
For Example
```python
# TensorFlow 1.X
outputs = session.run(f(placeholder), feed_dict={placeholder: input})
# TensorFlow 2.0
outputs = f(input)
```
when you are satisfied with the results, add a tf.function() decorator to make it run efficiently in graph mode.

## ii. Use python objects to track variables and losses
  * Use tf.Variable instead of tf.get_variable.

[Here](https://github.com/tensorflow/docs/blob/master/site/en/r2/guide/migration_guide.ipynb) is the source for more detailed instructions.

## iii. Upgrade your training loops
Use the highest level API that works for your use case. Prefer tf.keras.Model.fit over building your own training loops.

These high level functions manage a lot of the low-level details that might be easy to miss if you write your own training loop. For example, they automatically collect the regularization losses, and set the training=True argument when calling the model.

## iv. Upgrade your data input pipelines

Use tf.data datasets for data input. Thse objects are efficient, expressive, and integrate well with tensorflow.

They can be passed directly to the tf.keras.Model.fit method.

model.fit(dataset, epochs=5)
They can be iterated over directly standard Python:

for example_batch, label_batch in dataset:
    break

# 4. Convert a code from TF1.x to TF2.0
Ans:
TF.1.x code
```python
in_a = tf.placeholder(dtype=tf.float32, shape=(2))
in_b = tf.placeholder(dtype=tf.float32, shape=(2))

def forward(x):
  with tf.variable_scope("matmul", reuse=tf.AUTO_REUSE):
    W = tf.get_variable("W", initializer=tf.ones(shape=(2,2)),
                        regularizer=tf.contrib.layers.l2_regularizer(0.04))
    b = tf.get_variable("b", initializer=tf.zeros(shape=(2)))
    return W * x + b

out_a = forward(in_a)
out_b = forward(in_b)

reg_loss = tf.losses.get_regularization_loss(scope="matmul")

with tf.Session() as sess:
  sess.run(tf.global_variables_initializer())
  outs = sess.run([out_a, out_b, reg_loss],
                  feed_dict={in_a: [1, 0], in_b: [0, 1]})
```

After converting to TF2.0
In the converted code:

The variables are local Python objects.
The forward function still defines the calculation.
The sess.run call is replaced with a call to forward
The optional tf.function decorator can be added for performance.
The regularizations are calculated manually, without referring to any global collection.
No sessions or placeholders.

```python
W = tf.Variable(tf.ones(shape=(2,2)), name="W")
b = tf.Variable(tf.zeros(shape=(2)), name="b")

@tf.function
def forward(x):
  return W * x + b

out_a = forward([1,0])
print(out_a)


out_b = forward([0,1])

regularizer = tf.keras.regularizers.l2(0.04)
reg_loss = regularizer(W)
```


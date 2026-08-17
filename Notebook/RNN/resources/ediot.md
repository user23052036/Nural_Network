### My Doubt About RNN Input Shape

In an RNN/LSTM, the input shape is generally:

$$
(batch_size,\ timesteps,\ features)
$$

For the IMDB dataset, after padding every review to 50 words:

```python
X_train.shape = (25000, 50)
```

Here, each review has **50 timesteps**, where each timestep represents one word ID.

If I want to use an RNN directly without an `Embedding` layer, can I treat each word ID as **one feature** per timestep?

For example:

```python
Input(shape=(50, 1))
```

Since my data is currently `(25000, 50)`, I can use:

```python
X_train = X_train[..., np.newaxis]
```

which changes the shape to:

```python
(25000, 50, 1)
```

Here, `np.newaxis` does not create new information. It simply changes:

```text
[2071, 56, 26]
```

into:

```text
[[2071],
 [  56],
 [  26]]
```

So each timestep now has **1 feature**, where that single feature is the original word ID.

My main question was:

> **When we write `Input(shape=(50, 2))`, where do those 2 features come from?**

The answer is: **the two features must come from the way we represent/construct the input data. Keras does not automatically decide what those two features are.**

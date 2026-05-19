
You are mixing up three different responsibilities:

1. `input_length`
2. `input_shape`
3. `Input(shape=...)`

Let's separate them properly.

---

# 1. `input_length`

```python
Embedding(input_dim=10000, output_dim=5, input_length=50)
```

`input_length=50` means:

> "Each sequence contains 50 tokens."

So your input looks like:

```python
[12, 45, 67, ..., 90]   # length = 50
```

This is only describing:

```text
sequence length = 50
```

Historically, Keras used this for shape inference.

But modern Keras often ignores/deprecates it for building models.

So:

* it is metadata-like
* not the preferred way now
* does NOT reliably build the model

---

# 2. `input_shape=(50,)`

```python
Embedding(..., input_shape=(50,))
```

This means:

> "Each sample has shape (50,)"

This is much more explicit.

The comma matters.

```python
(50,)
```

means:

```text
1D tensor of length 50
```

Without comma:

```python
(50)
```

becomes integer `50`, not tuple.

---

Your actual batch input becomes:

```text
(batch_size, 50)
```

Example:

```text
(32, 50)
```

meaning:

* 32 samples in batch
* each sample has 50 tokens

---

# 3. `Input(shape=(50,))`

```python
model = Sequential([
    Input(shape=(50,)),
    Embedding(input_dim=10000, output_dim=5),
    ...
])
```

This is the cleanest and most modern approach.

Here:

```python
Input(shape=(50,))
```

creates an explicit input tensor.

Then the Embedding layer receives already-defined input.

---

# Are `Input(shape=(50,))` and `input_shape=(50,)` same?

Practically:

> YES, almost same.

Both tell Keras:

```text
Input tensor shape = (50,)
```

Difference is architectural cleanliness.

---

# Internal difference

## Option A

```python
Embedding(..., input_shape=(50,))
```

The layer itself defines input shape.

---

## Option B

```python
Input(shape=(50,))
Embedding(...)
```

A separate Input layer defines the shape.

This is cleaner because:

* separation of concerns
* easier for Functional API
* clearer model graphs
* preferred in modern Keras

---

# Important conceptual point

Your Embedding layer input is NOT words.

It is integer indices.

Before embedding:

```text
(batch_size, sequence_length)
```

Example:

```text
(32, 50)
```

After embedding:

Each integer becomes a vector of size 5.

So output becomes:

```text
(batch_size, 50, 5)
```

because:

* 50 words/tokens
* each mapped to 5-dimensional embedding

---

# Flow in your model

Input:

(32,50)

After Embedding:

(32,50,5)

After Bidirectional RNN(32):

(32,64)

because:

```text
32 forward + 32 backward = 64
```

After Dense(1):

(32,1)

---

# What you should use going forward

Use this:

```python
model = Sequential([
    Input(shape=(50,)),
    Embedding(input_dim=10000, output_dim=5),
    Bidirectional(SimpleRNN(32)),
    Dense(1, activation='sigmoid')
])
```

Avoid relying on:

```python
input_length=
```

It exists mostly for backward compatibility now.

# Keras Embedding Input: `input_length` vs `input_shape` vs `Input()`

---

## The Core Problem

When you use an `Embedding` layer, Keras needs to know the shape of your input **before** it can build the rest of the model. There are three ways to tell it. They differ in *where* and *how reliably* they communicate this information.

---

## Option A — `input_length=50` (Legacy, avoid)

```python
Embedding(input_dim=10000, output_dim=5, input_length=50)
```

### What it actually is

`input_length` is **not** a shape argument. It was originally added to help Keras calculate the output size of the Embedding layer for downstream layers. It's metadata — a hint, not a contract.

### Why it's unreliable now

Modern Keras (2.x and 3.x) has partially deprecated it. It:
- May or may not trigger proper model building depending on your Keras version
- Does **not** reliably allow you to call `model.summary()` before fitting
- Cannot be used in the Functional API at all

### When you'll still see it

Old tutorials, Stack Overflow answers from 2018–2021, and code written for Keras 1.x. It works in many simple cases, but you're relying on legacy behavior.

---

## Option B — `input_shape=(50,)` (Works, but not preferred)

```python
Embedding(input_dim=10000, output_dim=5, input_shape=(50,))
```

### What it actually is

`input_shape` is a **standard Keras layer argument** supported by every layer, not just Embedding. It tells Keras: *"the tensor entering this layer has this shape."*

The tuple `(50,)` means a **1D tensor of length 50** — one sequence of 50 token indices.

> The comma is mandatory. `(50,)` is a tuple. `(50)` is just the integer `50`.

### What Keras sees

```
Input tensor shape:  (batch_size, 50)
                      ↑             ↑
                      implicit      your 50
```

Keras automatically prepends `batch_size`. You never specify it.

### Why it works reliably

Using `input_shape` causes Keras to internally create a placeholder input tensor. The model is properly built, `model.summary()` works, and shape inference propagates to all downstream layers.

### Limitation

The shape is embedded inside the Embedding layer's own config. For complex models with shared inputs or multiple input branches, this gets messy.

---

## Option C — `Input(shape=(50,))` (Modern, preferred)

```python
from tensorflow.keras.layers import Input

model = Sequential([
    Input(shape=(50,)),
    Embedding(input_dim=10000, output_dim=5),
    Bidirectional(SimpleRNN(32)),
    Dense(1, activation='sigmoid')
])
```

### What it actually is

`Input()` creates an **explicit input tensor** as a separate, standalone layer. The Embedding layer then receives an already-defined, fully-typed tensor — it doesn't need to define input shape itself at all.

### Why it's better

| Concern | `input_shape=` | `Input()` |
|---|---|---|
| Model builds correctly | Yes | Yes |
| Works in Functional API | Awkward | Yes, designed for it |
| Shape is clearly separated from layer logic | No | Yes |
| `model.summary()` shows input layer explicitly | No | Yes |
| Easier to debug shape errors | Harder | Easier |

### Are Option B and Option C equivalent?

**In a Sequential model, yes — the result is the same.** Both produce a built model with the same architecture. The difference is **architectural clarity**, not output.

Use `Input()` because when you move to the Functional API (which you will for anything non-trivial), it's the only valid approach anyway.

---

## Shape Flow Through the Model

Using the example: `Input(shape=(50,))`, `Embedding(output_dim=5)`, `Bidirectional(SimpleRNN(32))`, `Dense(1)`

```
Input:               (batch, 50)
                      ↓
Embedding:           (batch, 50, 5)
                       ↑      ↑
                       50 tokens, each → 5-dim vector

Bidirectional(RNN(32)):  (batch, 64)
                           ↑
                           32 forward + 32 backward

Dense(1):            (batch, 1)
```

The Embedding layer maps each integer index to a learnable vector. So a sequence of 50 integers becomes a matrix of shape `(50, 5)`.

---

## Quick Reference

```python
# ❌ Avoid — legacy, unreliable
Embedding(input_dim=10000, output_dim=5, input_length=50)

# ✅ Works — acceptable in simple Sequential models
Embedding(input_dim=10000, output_dim=5, input_shape=(50,))

# ✅ Preferred — explicit, clean, Functional API compatible
model = Sequential([
    Input(shape=(50,)),
    Embedding(input_dim=10000, output_dim=5),
    Bidirectional(SimpleRNN(32)),
    Dense(1, activation='sigmoid')
])
```

---

## One-Line Summary

- `input_length` → tells Keras sequence length as a legacy hint. Don't rely on it.
- `input_shape=(50,)` → tells the Embedding layer what shape to expect. Works.
- `Input(shape=(50,))` → creates an explicit input tensor before any layer sees it. Best.
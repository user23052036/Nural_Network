You're right that **axis=0 operates down the rows**, but the resulting shape is **not `(3,1)`** here because of `keepdims=True`.

Given:

```python
x = np.random.rand(3, 2)
```

The shape is:

$$
x.shape = (3,2)
$$

Think of it as:

```text
[a  b]   ← row 1
[c  d]   ← row 2
[e  f]   ← row 3
```

### `axis=0`

`axis=0` means **sum vertically/down each column**:

```text
[a  b]
[c  d]  → [a+c+e, b+d+f]
[e  f]
```

Without `keepdims`:

```python
np.sum(x, axis=0).shape
```

would be:

```text
(2,)
```

### But `keepdims=True`

This tells NumPy:

> "Keep the dimension that I summed over."

So instead of `(2,)`, it becomes:

```text
[[a+c+e, b+d+f]]
```

Therefore:

$$
y.shape = (1,2)
$$

### So the correct answer is **(1, 2)**.

The key distinction:

* `axis=0` → sum **down the rows** → one value per **column**
* `keepdims=True` → preserve the summed dimension
* Therefore `(3,2) → (1,2)`

Your `(3,1)` would result from:

```python
np.sum(x, axis=1, keepdims=True)
```

because `axis=1` sums across each row, leaving **3 row results**:

$$
(3,2) \rightarrow (3,1)
$$

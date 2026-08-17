Good. This is one of those pandas details that beginners skip and later get confused by.

You need to understand:

```text
DataFrame rows have TWO things:
1. actual data
2. index labels
```

Example:

```python
df
```

might look like:

```text
   age gender
0   21   1
1   30   0
2   25   1
```

Here:

```text
0 1 2
```

is the INDEX.

Not actual data.

---

# What happens during filtering

Suppose:

```python
class0 = df[df['gender'] == 0]
```

Now maybe only row 1 matched.

So:

```python
class0
```

becomes:

```text
   age gender
1   30   0
```

Notice:

```text
index = 1
```

NOT 0.

Because pandas preserves original row labels.

---

# Then when you concat

Suppose:

```python
train0
```

has:

```text
index:
1
5
9
```

and

```python
train1
```

has:

```text
index:
2
8
11
```

After:

```python
pd.concat([train0, train1])
```

you get:

```text
index:
1
5
9
2
8
11
```

Indices become messy.

---

# Why is that a problem?

Sometimes:

* duplicate indices happen
* indexing bugs happen
* exporting/loading becomes confusing
* debugging becomes painful

Some ML pipelines assume clean sequential indices.

---

# `ignore_index=True`

This tells pandas:

> "Forget old indices. Create fresh numbering."

So:

```python
pd.concat([train0, train1], ignore_index=True)
```

becomes:

```text
index:
0
1
2
3
4
5
```

Clean.

---

# What does `reset_index()` do?

It does similar work AFTER dataframe creation.

Suppose:

```python
df
```

looks like:

```text
    age gender
5    21   1
9    30   0
15   25   1
```

Now:

```python
df.reset_index()
```

gives:

```text
   index age gender
0   5     21   1
1   9     30   0
2   15    25   1
```

Old indices moved into a new column called `"index"`.

---

# Usually we use:

```python
df.reset_index(drop=True)
```

This means:

```text
drop old index completely
```

Result:

```text
   age gender
0   21   1
1   30   0
2   25   1
```

---

# Difference between them

## `ignore_index=True`

Used DURING operations like:

```python
concat
```

or:

```python
append
```

---

## `reset_index()`

Used AFTER dataframe already exists.

---

# Important subtle thing

This:

```python
.sample(frac=1)
```

shuffles rows BUT keeps original indices.

Example:

Before:

```text
0
1
2
3
```

After shuffle:

```text
2
0
3
1
```

Rows shuffled.

Indices unchanged.

That surprises many beginners.

---

# Best practice after shuffle

Usually:

```python
df = df.sample(frac=1).reset_index(drop=True)
```

This gives:

* shuffled rows
* fresh clean indices

That is the standard clean pattern.

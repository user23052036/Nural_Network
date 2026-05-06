Good. This is one of those pandas details that beginners skip and later get confused by.

You need to understand:

```text
DataFrame rows have TWO things:
1. actual data
2. index labels
```

Example:

```python id="5vgk4y"
df
```

might look like:

```text id="mofowr"
   age gender
0   21   1
1   30   0
2   25   1
```

Here:

```text id="1i3m7r"
0 1 2
```

is the INDEX.

Not actual data.

---

# What happens during filtering

Suppose:

```python id="a5d9ng"
class0 = df[df['gender'] == 0]
```

Now maybe only row 1 matched.

So:

```python id="0a4mh5"
class0
```

becomes:

```text id="8slyhv"
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

```python id="0wg7ma"
train0
```

has:

```text id="btf9iz"
index:
1
5
9
```

and

```python id="4q3g6r"
train1
```

has:

```text id="n6f02c"
index:
2
8
11
```

After:

```python id="zzmd5d"
pd.concat([train0, train1])
```

you get:

```text id="5j45cf"
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

```python id="cbdy1y"
pd.concat([train0, train1], ignore_index=True)
```

becomes:

```text id="d8lc73"
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

```python id="wj01y2"
df
```

looks like:

```text id="1v3v2x"
    age gender
5    21   1
9    30   0
15   25   1
```

Now:

```python id="q1y9ly"
df.reset_index()
```

gives:

```text id="t7m50r"
   index age gender
0   5     21   1
1   9     30   0
2   15    25   1
```

Old indices moved into a new column called `"index"`.

---

# Usually we use:

```python id="4n8tca"
df.reset_index(drop=True)
```

This means:

```text
drop old index completely
```

Result:

```text id="7o4u08"
   age gender
0   21   1
1   30   0
2   25   1
```

---

# Difference between them

## `ignore_index=True`

Used DURING operations like:

```python id="cx3u2h"
concat
```

or:

```python id="y0qkv8"
append
```

---

## `reset_index()`

Used AFTER dataframe already exists.

---

# Important subtle thing

This:

```python id="q3wpmb"
.sample(frac=1)
```

shuffles rows BUT keeps original indices.

Example:

Before:

```text id="8br12n"
0
1
2
3
```

After shuffle:

```text id="skg1mn"
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

```python id="y4nx5j"
df = df.sample(frac=1).reset_index(drop=True)
```

This gives:

* shuffled rows
* fresh clean indices

That is the standard clean pattern.

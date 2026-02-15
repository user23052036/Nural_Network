Nothing is “wrong” with Python.

You’re seeing **floating-point representation error**.

You probably did something like:

```python
L1 = 14.0
L2 = 14.001
print(L2 - L1)
```

and got:

```
0.0009999999999994458
```

That’s expected.

---

## Why This Happens

Python uses **IEEE-754 double precision (64-bit floating point)**.

Binary floating point **cannot exactly represent most decimal fractions**.

Just like:

* `1/3` cannot be represented exactly in decimal (0.3333...)
* `0.001` cannot be represented exactly in binary

So internally Python stores something like:

```
0.001 ≈ 0.0009999999999994458
```

When printed with full precision, you see the tiny error.

This is not a Python bug.
It’s a **fundamental limitation of binary floating-point arithmetic**.

---

## How To Fix It (Depending on What You Want)

### 1️⃣ If you only care about display:

```python
print(round(L2 - L1, 3))
```

or

```python
print(f"{L2 - L1:.3f}")
```

Output:

```
0.001
```

---

### 2️⃣ If you need exact decimal arithmetic (finance, precision systems)

Use `decimal`:

```python
from decimal import Decimal

L1 = Decimal("14.0")
L2 = Decimal("14.001")

print(L2 - L1)
```

Output:

```
0.001
```

---

### 3️⃣ If comparing floats

Never do:

```python
if L2 - L1 == 0.001:
```

Instead use:

```python
import math
math.isclose(L2 - L1, 0.001)
```

---

## Key Insight

This is not:

* a Python issue
* a bug
* a rounding problem

It’s **binary floating-point precision limitation**.

Every major language behaves the same:

* C
* C++
* Java
* JavaScript
* Python

---

If you want, I can explain exactly how 0.001 looks in binary and why this happens at the bit level.

Because scaling makes the optimization problem **better conditioned**.

That is the real reason. “Learns faster” is just the symptom.

## 1) Gradient descent is sensitive to feature scale

Suppose one input feature is in the range `0–1` and another is in `0–100000`.

Then in the first layer, the large feature can dominate the pre-activation:

[
z = w_1 x_1 + w_2 x_2 + b
]

If (x_2) is huge, even a small change in (w_2) causes a large change in (z).
So the loss surface becomes **stretched** in some directions and **flat** in others.

That creates a badly shaped valley.

Gradient descent then has a problem:

* along the steep direction, a learning rate that is too large causes oscillation
* along the flat direction, the same learning rate makes progress painfully slow

So you get inefficient zig-zag updates.

Scaling makes the curvature of the loss surface more balanced, so one learning rate works reasonably well in all directions.

That is why training becomes faster and more stable.

---

## 2) Neural nets are not just “learning weights”; they are learning on top of geometry

The first layer weights see your raw input directly.

If features are on wildly different scales, then the network has to waste capacity just to compensate for that mismatch. It has to first learn something like:

* “ignore this huge-valued feature unless its weight is tiny”
* “rescale this feature internally before useful learning begins”

That is unnecessary work.

Scaling gives the model inputs that are already in a similar numeric range, so the early layers can learn meaningful patterns sooner.

---

## 3) Activations can saturate

This matters a lot for sigmoid and tanh, and still affects ReLU-based networks in a different way.

### Sigmoid / tanh

If the input to the neuron is very large positive or negative, the activation saturates:

* sigmoid → close to 0 or 1
* tanh → close to -1 or 1

In saturation, the derivative becomes very small.

Then backprop gives tiny gradients, so learning slows down drastically.

Scaled inputs keep pre-activations closer to the non-saturated region where gradients are useful.

---

## 4) Backprop depends on the forward pass values

You said you understand backprop. Good. Then the key connection is this:

[
\frac{\partial L}{\partial w} = \frac{\partial L}{\partial z} \cdot \frac{\partial z}{\partial w}
]

For a neuron, (\frac{\partial z}{\partial w} = x).

So the gradient for a weight is directly proportional to the input feature value.

That means if one feature is 1000 times larger than another, the corresponding weight updates can also be 1000 times larger.

That is unstable. Scaling reduces this imbalance.

---

## 5) What normalization and standardization really do

They do not magically make the data “better.” They make the optimization problem easier.

### Standardization

[
x' = \frac{x - \mu}{\sigma}
]

This gives roughly zero mean and unit variance.

Effects:

* features are on comparable scales
* optimization behaves more uniformly
* gradients are less skewed

### Normalization

Often maps values to a fixed interval, like `[0, 1]`.

This is especially useful when:

* feature ranges are bounded
* you use sigmoid/tanh
* you want a predictable numeric range

---

## 6) Intuition with a valley

Imagine trying to walk to the bottom of a long narrow valley.

If the valley is very steep in one direction and very flat in the other, you keep bouncing side to side while barely moving forward.

That is what gradient descent looks like on unscaled data.

Scaling makes the valley more circular. Then the path to the minimum is smoother and faster.

---

## 7) Important nuance

Scaling does **not always** matter equally for every model.

For neural networks, it is usually very helpful because:

* optimization is gradient-based
* activations can saturate
* first-layer weights directly multiply raw inputs

For tree-based models, scaling is usually much less important.

---

## Bottom line

Data scaling helps because it:

1. **improves the conditioning** of the optimization problem
2. **prevents one feature from dominating gradients**
3. **keeps activations in useful ranges**
4. **makes gradient descent converge faster and more reliably**

The cleanest mental model is this:

> Scaling does not change the information in the data much, but it changes the shape of the loss landscape so gradient descent can move efficiently.

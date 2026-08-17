No. **It does not pick 10,000 reviews.**

`num_words=10000` means:

* The IMDB dataset still contains the **same reviews**.
* Keras keeps/maps only the **10,000 most frequently occurring words** in the vocabulary.
* Words outside those 10,000 are treated as **unknown/out-of-vocabulary** tokens.

For example, suppose a review is:

> `"This movie was absolutely fantastic"`

If `"fantastic"` is outside the top 10,000 words, it may be represented as an unknown token, while the other words retain their indices.

So:

$$
\text{num_words}=10000
\quad\Rightarrow\quad
\text{maximum vocabulary size} \approx 10000
$$

**It controls the words, not the number of reviews.**

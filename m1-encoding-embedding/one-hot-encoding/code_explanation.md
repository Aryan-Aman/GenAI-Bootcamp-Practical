# Goal

Encode each word in a small corpus as a unique one-hot vector using sklearn.

---

# Input

- Input type: `list[str]`
- Example sentences:
  - `"SDE loves coding"`
  - `"SDE codes AI"`
- Why needed:
  - The corpus provides the token set from which the encoder learns categories.
  - Sentence structure is preserved initially, then flattened for fitting.

---

# Step-by-Step Pipeline

1. Import encoder and helper libraries.
2. Define the sample corpus.
3. Lowercase and tokenize each sentence using regex.
4. Store tokenized sentences in `tokens`.
5. Flatten all tokens into a 2D list `[[word], ...]`.
6. Fit `OneHotEncoder` on the flattened words.
7. Inspect learned vocabulary.
8. Transform words or full sentences into one-hot vectors.

---

# Important Transformations

## Raw sentence -> token list
- Input: `"SDE loves AI"`
- Output: `['sde', 'loves', 'ai']`
- Reason:
  - encoder works on categorical tokens, not full raw sentences

## Token list -> flattened 2D input
- Input: `[['sde', 'loves'], ['sde', 'codes']]`
- Output: `[['sde'], ['loves'], ['sde'], ['codes']]`
- Reason:
  - sklearn expects 2D input even when there is only one feature column

## Word -> one-hot vector
- Input: `['sde']`
- Output style: `[0, 0, 1, 0, ...]`
- Meaning:
  - only the vocabulary index for `sde` is active

---

# Code Walkthrough

## Imports
- `OneHotEncoder`
  - categorical encoder used to map each word to a binary vector.
- `numpy`
  - reshapes vocabulary into encoder-compatible 2D form.
- `re`
  - lightweight tokenizer for extracting words.

## Sample corpus
- `doc = [...]`
- Serves as a controlled input corpus.
- Small enough to manually verify the encoded output.

## Tokenization loop
- `re.findall(r'\b\w+\b', sentence.lower())`
- Why written:
  - lowercasing removes casing duplicates
  - regex extracts clean word tokens without punctuation
- Output:
  - `tokens`, a `list[list[str]]`

## Flatten words
- `all_words = [[word] for sentence in tokens for word in sentence]`
- Why:
  - each row is treated as one categorical sample
  - sklearn encoders do not accept a simple 1D list here
- Output:
  - 2D list of words

## Fit encoder
- `encoder = OneHotEncoder(sparse_output=False)`
- `encoder.fit(all_words)`
- Why:
  - learns all unique categories once
  - `sparse_output=False` makes the result dense and easy to inspect in a notebook

## Vocabulary inspection
- `encoder.categories_[0]`
- Why:
  - shows the learned token ordering
  - needed to understand which position in each vector corresponds to which token

## Transform words/sentences
- `encoder.transform([[word] for word in sentence])`
- Input:
  - one sentence converted to 2D token rows
- Output:
  - matrix where each row is the one-hot encoding of a token in that sentence

---

# Internal Workflow

- Encoder scans the training tokens.
- It stores each distinct token as a category.
- During transform:
  - token category index is located
  - binary vector is created with a single `1`
- No context is learned.
- No word similarity is captured.
- Vocabulary is fixed after `fit()`.

---

# Alternative Approaches

- Manual word-to-index dictionary plus `numpy.eye()`.
- `pandas.get_dummies()` for quick categorical expansion.
- `CountVectorizer(binary=True)` if the goal is binary word presence at document level.

---

# Common Mistakes / Debugging Notes

- Passing 1D input instead of 2D input to sklearn.
- Forgetting lowercase normalization and ending up with duplicate tokens.
- Confusing sentence-level encoding with word-level encoding.
- Expecting semantic similarity from one-hot vectors.
- Forgetting that one-hot vector size grows with vocabulary size.

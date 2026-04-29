# Goal

Convert a small corpus into TF-IDF vectors so that common words are down-weighted and more informative words receive higher importance.

---

# Input

- Input type: `list[str]`
- Example documents:
  - `"SDE loves coding"`
  - `"SDE codes AI"`
- Why needed:
  - TF-IDF compares token usage across multiple documents.
  - IDF cannot be computed meaningfully from a single document.

---

# Step-by-Step Pipeline

1. Import `TfidfVectorizer`.
2. Define the document list.
3. Initialize vectorizer.
4. Fit the vectorizer on the corpus.
5. Compute TF-IDF weights for each token in each document.
6. Inspect sparse output.
7. Convert to dense array for readable debugging.
8. Inspect vocabulary order.

---

# Important Transformations

## Raw text -> token statistics
- `TfidfVectorizer` internally lowercases and tokenizes by default.
- It builds vocabulary and document frequency counts.

## Count statistics -> TF-IDF weights
- Term frequency captures word usage inside a document.
- Inverse document frequency penalizes words that appear across many documents.
- Final output is usually normalized per document.

## Sparse matrix -> dense view
- `.toarray()` is used only to display the vectors.
- Dense conversion is not suitable for large corpora.

---

# Code Walkthrough

## Import
- `TfidfVectorizer`
  - combines tokenization, vocabulary building, TF calculation, IDF calculation, and normalization.
  - cleaner than building `CountVectorizer` + `TfidfTransformer` separately for a small demo.

## Data definition
- `data = [...]`
- Keeps the same small corpus pattern used in other notebooks.
- Makes comparison with BoW straightforward.

## Vectorizer initialization
- `vectorizer = TfidfVectorizer()`
- Why written:
  - simple baseline configuration
  - default tokenization and lowercasing are enough for this demo

## Fit and transform
- `tf_idf_vec = vectorizer.fit_transform(data)`
- `fit` phase:
  - builds vocabulary
  - computes document frequency per token
- `transform` phase:
  - computes TF-IDF score per token per document
- Output:
  - sparse matrix `(num_documents, vocab_size)`

## Sparse inspection
- printing `tf_idf_vec` shows only non-zero entries.
- Useful to understand that the matrix is sparse, not dense.

## Dense inspection
- `tf_idf_vec.toarray()`
- Why:
  - easier to see row-wise numeric vectors during learning

## Vocabulary inspection
- `vectorizer.get_feature_names_out()`
- Why:
  - explains what each matrix column means

---

# Internal Workflow

- Each document is tokenized.
- Vocabulary is created across the full corpus.
- For each token in each document:
  - compute term frequency
  - compute inverse document frequency from global corpus stats
  - multiply them
  - normalize document vector
- Result:
  - words common to all docs get lower weight
  - words specific to some docs stand out more

---

# Alternative Approaches

- `CountVectorizer` + `TfidfTransformer` for a more explicit two-step pipeline.
- `HashingVectorizer` for stateless large-scale pipelines.
- BM25 when ranking/search behavior is the main goal.

---

# Common Mistakes / Debugging Notes

- Assuming TF-IDF captures semantics; it still stays lexical.
- Forgetting that unseen words are ignored after fitting.
- Calling `.toarray()` on very large matrices.
- Expecting word order information.
- Re-fitting on test data and accidentally changing the vocabulary.

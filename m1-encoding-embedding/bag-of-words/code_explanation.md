# Goal

Convert a small text corpus into Bag-of-Words vectors after explicit text preprocessing.

---

# Input

- Input type: `list[str]`
- Example documents:
  - `"SDE loves coding"`
  - `"SDE codes AI"`
- Why needed:
  - A BoW model needs multiple documents to build a shared vocabulary.
  - Small inputs make column mapping and frequency counts easy to inspect.

---

# Step-by-Step Pipeline

1. Import preprocessing and vectorization libraries.
2. Download NLTK resources required for tokenization, stopwords, and lemmatization.
3. Define `clean_text()`.
4. Lowercase each sentence.
5. Remove punctuation and numbers with regex.
6. Tokenize into words.
7. Remove stopwords.
8. Lemmatize tokens.
9. Join tokens back into cleaned strings.
10. Pass cleaned documents to `CountVectorizer`.
11. Build vocabulary and count word frequency per document.
12. Inspect sparse matrix, dense matrix, and vocabulary.

---

# Important Transformations

## Raw text -> cleaned text
- Input: `"SDE codes in Python"`
- Output style: `"sde code python"`
- Why:
  - Reduces noise.
  - Prevents duplicate variants from becoming different columns.

## Cleaned documents -> sparse count matrix
- Input: list of cleaned documents
- Operation: `vectorizer.fit_transform(cleaned_doc)`
- Output: sparse matrix of shape `(num_documents, vocab_size)`

## Sparse matrix -> dense array
- Operation: `.toarray()`
- Why:
  - Only for inspection.
  - Not memory-safe for large corpora.

---

# Code Walkthrough

## Imports
- `CountVectorizer`
  - Builds vocabulary and count matrix.
- `re`
  - Removes non-alphabetic characters.
- `nltk`, `word_tokenize`, `stopwords`, `WordNetLemmatizer`
  - Handle cleanup stages that sklearn does not fully cover by default.
- `pandas`
  - Imported for optional inspection; not central to the current flow.

## NLTK downloads
- `punkt_tab`, `stopwords`, `wordnet`
- Required so tokenization and lemmatization work locally.
- If missing, preprocessing cells fail before vectorization starts.

## `clean_text(text)`
- `text.lower()`
  - Normalizes casing.
- `re.sub(r'[^a-zA-Z\s]', '', text)`
  - Keeps letters and spaces only.
- `word_tokenize(text)`
  - Splits sentence into tokens.
- Stopword filtering
  - Removes high-frequency low-signal terms.
- Lemmatization
  - Reduces inflected forms to normalized base forms.
- `' '.join(tokens)`
  - Converts token list back to string because `CountVectorizer` expects documents as strings.

## Cleaning the corpus
- `cleaned_doc = [clean_text(sentence) for sentence in doc]`
- Applies the same transformation to every document.
- Output: clean document list ready for consistent vectorization.

## Vectorizer setup
- `vectorizer = CountVectorizer()`
- Keeps vocabulary creation and counting inside sklearn.
- Good default baseline for lexical feature extraction.

## Fit + transform
- `bow_matrix = vectorizer.fit_transform(cleaned_doc)`
- `fit` phase:
  - scans all cleaned docs
  - creates vocabulary
  - assigns column index to each token
- `transform` phase:
  - fills counts per document per token
- Output:
  - sparse matrix

## Vocabulary inspection
- `vectorizer.get_feature_names_out()`
- Returns tokens in the same column order used by the matrix.
- Needed for debugging and interpretation.

## Alternative tokenizer block
- A custom tokenizer is passed into `CountVectorizer`.
- Purpose:
  - shows that preprocessing can be embedded inside the vectorizer instead of being run separately.
- Tradeoff:
  - less transparent intermediate outputs.

---

# Internal Workflow

- BoW ignores sequence.
- Only token presence/count matters.
- Two documents with same words in different order will map to the same vector.
- Vocabulary is frozen after `fit()`.
- New unseen words during `transform()` are ignored.

---

# Alternative Approaches

- `TfidfVectorizer` when counts should be reweighted by importance.
- Manual dictionary + counter loops for educational implementation.
- `CountVectorizer(ngram_range=(1,2))` to include phrases.

---

# Common Mistakes / Debugging Notes

- Forgetting NLTK downloads.
- Passing raw noisy text and expecting lemmatized columns automatically.
- Using `.toarray()` on large corpora.
- Assuming BoW preserves word order.
- Forgetting to inspect vocabulary before interpreting matrix columns.
- Removing too much with regex and accidentally deleting useful tokens.

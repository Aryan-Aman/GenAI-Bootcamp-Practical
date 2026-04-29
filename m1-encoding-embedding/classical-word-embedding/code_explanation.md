# Goal

Use a pretrained Word2Vec embedding model to inspect word vectors, semantic similarity, analogies, outlier detection, and a simple sentence embedding built from word averages.

---

# Input

## External model input
- Pretrained model name: `word2vec-google-news-300`
- Loaded through `gensim.downloader`
- Why needed:
  - provides a ready-made embedding space without training locally

## Query input
- Single words such as `"aman"`, `"men"`, `"king"`, `"woman"`
- Word lists such as `["breakfast", "lunch", "dinner", "car"]`
- Sentence string:
  - `"I am aman and i am a gen ai engineer"`
- Why needed:
  - notebook demonstrates several embedding API patterns on different input types

---

# Step-by-Step Pipeline

1. Import gensim and numpy.
2. Download/load pretrained Google News word vectors.
3. Retrieve a word vector and inspect its dimension.
4. Try a phrase lookup to observe word-level vocabulary constraints.
5. Run semantic APIs:
   - `most_similar()`
   - `similarity()`
   - `doesnt_match()`
6. Perform analogy arithmetic with vector operations.
7. Split a sentence into words.
8. Keep only tokens available in the model vocabulary.
9. Collect their vectors.
10. Average vectors with `np.mean(..., axis=0)` to build a simple sentence representation.

---

# Important Transformations

## Word -> dense vector
- Input: `model['aman']`
- Output: 300-dimensional dense vector
- Meaning:
  - semantic position of that word in pretrained vector space

## Multiple words -> semantic score
- Input: `model.similarity('mango', 'fruit')`
- Output: scalar cosine similarity-like score
- Meaning:
  - higher score implies closer vector neighborhood

## Sentence -> average embedding
- Input: tokenized sentence words present in vocabulary
- Operation: average all vectors
- Output: one 300-d vector representing the sentence approximately

---

# Code Walkthrough

## Imports
- `gensim.downloader as api`
  - downloads pretrained embedding models
- `numpy`
  - used for vector averaging
- `Word2Vec`, `KeyedVectors`
  - relevant to the embedding ecosystem; pretrained usage here behaves like keyed vector lookup

## Model loading
- `model = api.load("word2vec-google-news-300")`
- Why written:
  - gets a large pretrained embedding space immediately
- Output:
  - keyed vector model with 300-dimensional word embeddings

## Word vector lookup
- `model["aman"]`
- `len(model["aman"])`
- Why:
  - validates that vector retrieval works
  - confirms embedding dimensionality

## Phrase lookup failure
- `model["i am aman"]`
- Why included:
  - highlights that pretrained Word2Vec is token-level, not sentence-level
- Expected behavior:
  - key lookup failure if full phrase is not in vocabulary

## Similar words
- `model.most_similar("aryan")`
- `model.most_similar("america")`
- Why:
  - shows nearest-neighbor queries in embedding space

## Similarity scoring
- `model.similarity("mango", "fruit")`
- Why:
  - quick semantic closeness check between token pairs

## Odd-one-out
- `model.doesnt_match([...])`
- Why:
  - finds the token least aligned with the semantic cluster

## Analogy arithmetic
- `vec = model['king'] - model['man'] + model['woman']`
- `model.most_similar([vec])`
- Why:
  - demonstrates vector-space relational structure

## Sentence tokenization
- `sentenc.lower().split()`
- Why:
  - simple whitespace split for a quick sentence embedding demo
- Tradeoff:
  - basic split does not handle punctuation robustly

## Vocabulary filtering
- loop checks `if word in model:`
- Why:
  - avoids lookup errors for OOV tokens
- Output:
  - only valid word vectors are kept

## Sentence vector
- `np.mean(word_vectors, axis=0)`
- Why:
  - simplest baseline sentence embedding from word vectors
- Output:
  - one 300-d vector

---

# Internal Workflow

- Pretrained vectors are learned externally on a large corpus.
- Each known token maps to one fixed dense vector.
- Similarity methods compare directions/distances in vector space.
- Analogy arithmetic manipulates these vectors linearly.
- Sentence averaging compresses multiple token vectors into one representation, but removes order and syntax.

---

# Alternative Approaches

- FastText for better handling of rare/subword tokens.
- GloVe vectors for another pretrained static embedding baseline.
- `sentence-transformers` for proper sentence-level embeddings.
- SpaCy vectors for lighter production NLP pipelines.

---

# Common Mistakes / Debugging Notes

- Expecting phrase lookup to work like sentence embedding.
- Not checking vocabulary membership before lookup.
- Averaging an empty `word_vectors` list when all tokens are OOV.
- Assuming static embeddings understand context dynamically.
- Using `.split()` on punctuated text and getting noisy tokens.

# Quick Revision

- Load pretrained model with `api.load()`.
- `model[word]` returns dense vector.
- Google News model uses 300-d embeddings.
- Word2Vec lookup is word-level, not sentence-level.
- `most_similar()` = nearest neighbors.
- `similarity(a, b)` = semantic closeness score.
- `doesnt_match()` = semantic outlier in a list.
- Analogy: `king - man + woman` style vector math.
- Sentence baseline = average valid word vectors.
- Always filter OOV words before lookup.
- Static embeddings do not change by context.

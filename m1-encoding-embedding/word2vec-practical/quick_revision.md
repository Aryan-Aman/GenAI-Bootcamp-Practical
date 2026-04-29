# Quick Revision

- Read all `.txt` files from `./data/`.
- `sent_tokenize()` splits corpus into sentence strings.
- `simple_preprocess()` converts each sentence to clean tokens.
- Final training input = `list[list[str]]`.
- `Word2Vec(..., sg=0)` = CBOW.
- `Word2Vec(..., sg=1)` = Skip-Gram.
- `build_vocab()` creates vocabulary only.
- `train()` learns embeddings.
- `window` = context size.
- `min_count` = rare-word filter.
- `vector_size` = embedding dimension.
- Trained vectors live in `model.wv`.
- Use `most_similar`, `similarity`, `doesnt_match` for inspection.
- Small corpus = weaker semantic quality.

# Quick Revision

- Input: list of documents.
- `TfidfVectorizer()` handles tokenization + weighting.
- `fit_transform()` = vocabulary + IDF + vector generation.
- Output is sparse matrix.
- `.toarray()` only for readable inspection.
- `get_feature_names_out()` gives column-token mapping.
- Common tokens across documents get lower weight.
- Rare but useful tokens get higher weight.
- TF-IDF still ignores word order.
- Good baseline for retrieval/classification features.

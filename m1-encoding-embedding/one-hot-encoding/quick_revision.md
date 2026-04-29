# Quick Revision

- Input: list of short sentences.
- Tokenize with regex and lowercase.
- Store sentence tokens in `tokens`.
- Flatten to `all_words = [[word] ...]` for sklearn.
- `OneHotEncoder.fit()` learns vocabulary.
- `encoder.categories_[0]` gives token order.
- `transform()` returns one binary row per token.
- `sparse_output=False` gives dense readable output.
- One-hot = unique identity only.
- No semantics, no context, no compression.
- Vocabulary size = vector length.

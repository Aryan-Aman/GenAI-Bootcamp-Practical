# Quick Revision

- Input: small list of sentences.
- Preprocess flow:
  - lowercase
  - regex cleanup
  - tokenize
  - remove stopwords
  - lemmatize
- `clean_text()` returns cleaned string, not token list.
- `CountVectorizer()` builds vocabulary automatically.
- `fit_transform()` = learn vocabulary + generate count matrix.
- Output is sparse CSR matrix.
- `.toarray()` only for display.
- `get_feature_names_out()` maps matrix columns to words.
- BoW captures counts, not semantics.
- BoW ignores word order.
- Unseen words at inference are ignored.
- Good baseline for text classification and quick prototypes.

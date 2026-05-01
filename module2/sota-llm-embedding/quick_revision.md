# Quick Revision

- `SentenceTransformer` loads pretrained dense embedding models from Hugging Face.
- `all-MiniLM-L6-v2` is a popular small model (384-dim output).
- Fallback model list prevents crashes when one model fails to download.
- `.encode(text)` returns a fixed-size NumPy array regardless of input length.
- Same model encodes both words and sentences into the same vector space.
- Sentence-level embeddings are more semantically meaningful than single-word ones.
- `embedding.shape` and `len(embedding)` both confirm vector dimension.
- First-time model loading requires internet access for weight download.
- Use a local folder path if Hugging Face download is blocked by SSL/firewall.
- Dense embeddings capture semantic meaning — unlike BoW/TF-IDF which are purely lexical.
- These vectors can be used for similarity search, clustering, retrieval, and classification.

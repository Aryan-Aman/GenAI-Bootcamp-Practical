# Quick Revision

- `SentenceTransformer` loads open-source embedding models locally after first download.
- Fallback model list handles download failures gracefully.
- `.encode(text)` returns a fixed-size NumPy array — same dimension for words, sentences, or paragraphs.
- Gemini embeddings require API keys and return Python lists, not NumPy arrays.
- `embed_query()` embeds one text; `embed_documents()` embeds a list.
- Gemini image embedding: load bytes → `types.Part.from_bytes()` → `embed_content()`.
- CLIP maps images into a 512-dim joint vision-language space.
- CLIP output includes batch dimension: `(1, 512)` for one image.
- `torch.no_grad()` is required during CLIP inference — no gradients needed.
- Cosine similarity: direction-based, range [-1, 1]. Most common for semantic search.
- Dot product: includes magnitude, range (-∞, +∞). Use when vectors are pre-normalized.
- Euclidean distance: absolute distance, range [0, +∞). Lower = more similar.
- Semantic search finds related meaning even with different words.
- Keyword search only finds exact token overlap — misses paraphrases.
- `Counter` provides O(1) word frequency lookups for keyword scoring.
- Never print real API keys — use placeholder display strings.
- Python lists lack `.shape`; wrap with `np.array()` to inspect dimensions.

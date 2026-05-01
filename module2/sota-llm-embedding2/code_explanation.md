# Goal

Explore multiple embedding strategies in one notebook: open-source sentence embeddings, Gemini text embeddings, Gemini image embeddings, CLIP image features, semantic similarity search, and keyword search baseline.

---

# Input

**Model inputs**
- Hugging Face sentence models:
  - `sentence-transformers/all-MiniLM-L6-v2`
  - `sentence-transformers/paraphrase-MiniLM-L6-v2`
  - `BAAI/bge-small-en-v1.5`
- Gemini text model: `models/gemini-embedding-001`
- Gemini image model: `gemini-embedding-2`
- CLIP model: `openai/clip-vit-base-patch32`

**Text inputs**
- Word: `"hello"`
- Sentence: `"Exercise is good for health"`
- Paragraph: data centre description
- Query: `"How water is used in data centres?"`
- Documents: 7 sentences (5 relevant + 2 unrelated)

**Image input**
- `ai.jpg` — local image file used for Gemini and CLIP image embeddings.

---

# Step-by-Step Pipeline

1. Import `SentenceTransformer` and suppress warnings.
2. Map environment variables to standard API key names.
3. Display placeholder-safe key status.
4. Load open-source sentence embedding model (fallback loop).
5. Encode a word, sentence, and paragraph with the open-source model.
6. Inspect shapes and lengths at each level.
7. Initialize Gemini text embedding model via LangChain.
8. Generate Gemini text embedding for a query.
9. Load image bytes and generate Gemini image embedding.
10. Load CLIP processor and model.
11. Convert image to CLIP tensor inputs.
12. Extract CLIP image features.
13. Define similarity functions (cosine, dot product, Euclidean).
14. Embed query and documents with Gemini.
15. Compute semantic similarity scores per document.
16. Build a keyword search baseline using Counter.
17. Compare semantic vs keyword search results.

---

# Important Transformations

## Text -> open-source sentence embedding
- Input: `open_source_embedding_model.encode(text)`
- Output: NumPy array (e.g., 384-dim for MiniLM)
- Meaning: contextual semantic representation

## Text -> Gemini text embedding
- Input: `google_embedding_model.embed_query(text)` or `embed_documents(docs)`
- Output: Python list of floats (convert to NumPy for `.shape`)
- Meaning: API-based semantic vector from Google's model

## Image -> Gemini image embedding
- Input: image bytes via `types.Part.from_bytes(...)`
- Output: list of float values
- Meaning: high-level image representation from Gemini

## Image -> CLIP features
- Input: PIL image → `CLIPProcessor` → `CLIPModel.get_image_features()`
- Output: tensor of shape `(1, 512)` — batch dimension included
- Meaning: image embedding in CLIP's joint vision-language space

## Query + documents -> similarity scores
- Cosine similarity: direction-based, range [-1, 1]
- Dot product: magnitude + direction, range (-∞, +∞)
- Euclidean distance: straight-line distance, range [0, +∞), smaller = more similar

## Query + documents -> keyword scores
- Tokenize both, count exact word overlap using `Counter`
- No semantic understanding — purely lexical matching

---

# Code Walkthrough

## Imports and warnings
- `SentenceTransformer` — main open-source embedding API
- `warnings.filterwarnings` — suppresses noisy progress bar messages

## API key mapping
- `os.environ[...]` maps custom env var names to standard ones
- Why:
  - SDKs like LangChain and google-genai expect specific variable names
  - Avoids hardcoding keys in code

## Key display
- Placeholder strings confirm keys are set without printing real secrets
- Why written this way:
  - Never risk leaking credentials in notebook output

## Open-source model loading
- Fallback loop over 3 candidates
- `load_errors` dict collects failures
- `RuntimeError` raised if all fail
- Why:
  - Robust to network or model availability issues

## Word / sentence / paragraph encoding
- Same `.encode()` call for all — model handles any text length
- `embedding[:10]` for preview, `.shape` and `len()` for dimension
- Paragraph defined but not encoded inline — available for experimentation

## Gemini text embeddings
- `GoogleGenerativeAIEmbeddings` from langchain-google-genai
- `embed_query(text)` returns a single embedding list
- `embed_documents(docs)` returns a list of embedding lists
- `np.array(embedding_gogl).shape` needed because output is a Python list, not NumPy

## Gemini image embedding
- `genai.Client` created with API key
- Image loaded as bytes from local file
- `types.Part.from_bytes(data=..., mime_type="image/jpeg")` wraps the image
- `client.models.embed_content(model="gemini-embedding-2", contents=[...])` — returns embedding object
- `result.embeddings[0].values` extracts the vector

## CLIP image features
- `CLIPProcessor` preprocesses image into tensor inputs
- `CLIPModel.get_image_features(**image_inputs)` extracts features
- `torch.no_grad()` disables gradient computation (inference only)
- Output shape: `(1, 512)` — the 1 is batch dimension
- Why CLIP:
  - Maps images and text into the same embedding space for multimodal tasks

## Similarity functions
- `dot_product(a, b)` — `np.dot(a, b)`
- `cosine_similarity(a, b)` — normalized dot product
- `euclidean_distance(a, b)` — L2 norm of difference
- Why three metrics:
  - Different tasks prefer different metrics
  - Cosine is direction-only, dot includes magnitude, Euclidean measures absolute distance

## Semantic search
- Query and documents embedded with Gemini
- Loop computes all three metrics per document
- Results stored as list of dicts for easy inspection
- Why:
  - Shows how embeddings enable meaning-based retrieval

## Keyword search
- Query and documents tokenized by `.split()`
- `Counter` counts word frequencies in each document
- Score = sum of query word counts found in document
- Wrapped into reusable `keyword_search_all_documents()` function
- Results sorted by score descending
- Why included:
  - Baseline comparison to show semantic search captures meaning that keyword search misses

---

# Internal Workflow

- Open-source models (SentenceTransformer) run locally — no API calls needed after download.
- Gemini embeddings require API key and network access per call.
- CLIP maps images into a 512-dim joint space shared with text.
- Semantic search ranks by vector proximity — captures paraphrases and related concepts.
- Keyword search ranks by exact token overlap — misses synonyms and rephrasings.
- The mango and football documents should rank low in semantic search and near-zero in keyword search.

---

# Alternative Approaches

- BM25 instead of raw keyword counts for stronger lexical retrieval.
- Vector databases (FAISS, Pinecone, Qdrant) for large-scale semantic search.
- Normalize embeddings before dot-product when comparing across different models.
- Use `transformers` directly with manual pooling for custom embedding behavior.
- Use multimodal models (CLIP, SigLIP) for combined text-image retrieval.

---

# Common Mistakes / Debugging Notes

- Printing real API keys in notebook output — always use placeholders.
- Confusing Python lists with NumPy arrays when checking `.shape`.
- Forgetting CLIP output has batch dimension `(1, 512)` not `(512,)`.
- Sorting Euclidean distance ascending but cosine/dot descending.
- Re-embedding query inside the loop instead of once outside.
- Expecting keyword search to find semantically related results.
- Forgetting that first Hugging Face model download requires internet.

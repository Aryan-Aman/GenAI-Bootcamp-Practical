# Goal

Train custom Word2Vec embeddings on local text files and inspect learned vectors using both CBOW and Skip-Gram configurations.

---

# Input

## Corpus files
- `./data/story2_data.txt`
- `./data/wordvec_data.txt`
- Format:
  - plain UTF-8 text files
  - long narrative text
- Why needed:
  - Word2Vec requires many tokenized sentences to learn local context patterns.

## Model input format
- Final training input: `list[list[str]]`
- Example style:
  - `[['the', 'year', 'was'], ['aman', 'had', 'once']]`
- Why needed:
  - gensim Word2Vec expects one tokenized sentence per list.

---

# Step-by-Step Pipeline

1. Import file handling, tokenization, preprocessing, and `Word2Vec`.
2. Download NLTK resources.
3. Iterate over all text files in `./data/`.
4. Read each file into a string.
5. Split each file into sentences with `sent_tokenize()`.
6. Convert each sentence into cleaned tokens with `simple_preprocess()`.
7. Append tokenized sentences to `story_data`.
8. Inspect sample tokenized output and corpus size.
9. Initialize CBOW model hyperparameters.
10. Build vocabulary from `story_data`.
11. Train the CBOW model.
12. Query vectors, similarity, outlier, and nearest neighbors.
13. Repeat training flow for Skip-Gram by changing `sg=1`.

---

# Important Transformations

## File text -> sentence list
- Input: full file string
- Operation: `sent_tokenize(corpus)`
- Output: list of sentence strings
- Why:
  - Word2Vec learns on sentence-local context windows

## Sentence string -> token list
- Input: one raw sentence
- Operation: `simple_preprocess(sentence)`
- Output: normalized token list
- Why:
  - lowercasing and punctuation cleanup produce gensim-ready tokens

## Tokenized corpus -> Word2Vec vocabulary
- Input: `story_data`
- Operation: `build_vocab(story_data)`
- Output:
  - token frequency table
  - internal word index mappings
  - corpus statistics

## Vocabulary -> trained embeddings
- Input: tokenized corpus plus vocabulary
- Operation: `train(...)`
- Output:
  - dense vectors for vocabulary words

---

# Code Walkthrough

## Imports
- `os`
  - iterates through files in `./data/`
- `nltk`
  - supports sentence tokenization resources
- `sent_tokenize`
  - splits large text into sentence units
- `simple_preprocess`
  - gensim utility for lowercase tokenization and cleanup
- `Word2Vec`
  - trains dense word embeddings
- `numpy`, `pandas`, `gensim`, `word_tokenize`, `stopwords`
  - present in notebook, but not all are required in the final training path

## NLTK download cell
- `nltk.download('all', quiet=True)`
- Purpose:
  - avoids missing resource errors during tokenization
- Engineering note:
  - heavy for production; better to download only required resources

## Reading files
- loop over `os.listdir(data_folder)`
- `with open(..., encoding='utf-8') as f:`
- Why:
  - ensures file-safe reading and correct text encoding
- Output:
  - `corpus` string per file

## Sentence tokenization
- `raw_sentences = sent_tokenize(corpus)`
- Why:
  - Word2Vec expects sentence-grouped tokens, not one giant flat token stream

## Token normalization
- `story_data.append(simple_preprocess(sentence))`
- Why:
  - creates clean token lists in one line
- Output:
  - `story_data`, the main training corpus

## Corpus sanity check
- `print(story_data[:1])`
- `len(story_data)`
- sum of token counts
- Why:
  - verify preprocessing before training

## CBOW model initialization
- `Word2Vec(window=6, min_count=3, vector_size=300, epochs=50, sg=0)`
- Why each parameter:
  - `window=6`: context span around target word
  - `min_count=3`: ignore very rare words
  - `vector_size=300`: embedding size
  - `epochs=50`: more passes over a relatively small corpus
  - `sg=0`: CBOW mode

## Vocabulary creation
- `cbow_model.build_vocab(story_data)`
- What it does:
  - counts tokens
  - filters tokens using `min_count`
  - prepares internal lookup tables
- Important:
  - no actual learning yet

## Training
- `cbow_model.train(story_data, total_examples=cbow_model.corpus_count, epochs=cbow_model.epochs)`
- What it does:
  - updates weights using context windows from tokenized sentences
- Output:
  - trained embedding space

## Queries after training
- `wv['aman']`
  - returns learned vector
- `wv.most_similar('aman')`
  - nearest neighbors in learned space
- `wv.doesnt_match([...])`
  - semantic outlier from list
- `wv.similarity('aman', 'engineer')`
  - pairwise semantic closeness

## Skip-Gram section
- same setup, but `sg=1`
- Why:
  - compare the second Word2Vec training strategy on the same corpus

---

# Internal Workflow

- Corpus is converted to tokenized sentence windows.
- Model scans the corpus to build vocabulary and frequencies.
- Training uses local context windows.
- CBOW:
  - predicts center word from surrounding context
- Skip-Gram:
  - predicts surrounding context words from a center word
- Similar contexts push words toward similar vector regions.
- Final vectors are stored under `model.wv`.

---

# Alternative Approaches

- `Word2Vec(sentences=story_data, ...)` to auto-run vocabulary building internally.
- `FastText` for subword-aware training.
- `sentence-transformers` for sentence-level embeddings instead of word embeddings.
- `spaCy` or pretrained transformer encoders for contextual embeddings.

---

# Common Mistakes / Debugging Notes

- Passing list of sentence strings instead of tokenized sentence lists.
- Thinking `build_vocab()` trains the model; it does not.
- Setting `min_count` too high on a small corpus and losing important words.
- Inspecting `raw_sentences` after looping files and assuming it contains all files; it only stores the last file unless accumulated.
- Training before vocabulary is built.
- Expecting good semantic quality from very small corpora without enough repeated context.
- Forgetting that `story_data` is already tokenized, so no extra tokenization is needed before training.

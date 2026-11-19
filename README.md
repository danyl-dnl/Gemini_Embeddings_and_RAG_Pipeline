# Gemini Embeddings and RAG Pipeline

A notebook-based learning project exploring sentence embeddings, cosine similarity, retrieval-augmented generation (RAG), and Gemini-powered multi-query retrieval.

The implementation documented here is in `Gemini_Embeddings_and_RAG_Pipeline_GDG_day2.ipynb`. The notebook was supplied separately for this README and is **not currently committed in this repository**; add it to the repository before expecting a fresh clone to run the examples.

## What the notebook demonstrates

### Sentence embeddings and similarity

The notebook loads `sentence-transformers/all-MiniLM-L6-v2` and creates normalized sentence embeddings. It inspects their dimensions and defines a small manual cosine-similarity function, then compares sample words and sentences such as related device descriptions and animal names.

It also uses the Google Gen AI SDK to request embeddings from Gemini's `gemini-embedding-2` model and compares selected sentence vectors with cosine similarity.

### Basic retrieval-augmented generation

The notebook builds small example corpora from fictional event and expedition briefs. It splits text on whitespace into fixed-size word chunks (the examples use different chunk sizes), embeds each chunk, and keeps each embedding beside its source text in a Python list.

For a question, `find_relevant_context` embeds the query, calculates cosine similarity against every stored chunk, and returns the single highest-scoring chunk. `ask_llm` places that question and retrieved text into a prompt and calls Gemini's `gemini-3.5-flash-lite` model to draft an answer.

### Multi-query retrieval

The final section asks Gemini to rewrite a complex question as several `<query>...</query>` entries. It parses those entries, retrieves the best-matching chunk for each generated query, and sends the collected contexts to Gemini for an answer.

## How to run

The repository currently contains only this README. After adding the notebook, install the Python packages in a notebook cell or your environment:

```bash
python -m pip install sentence-transformers google-genai
```

The first Sentence Transformers use downloads `all-MiniLM-L6-v2`, so allow time and network access for the initial model download. Gemini embedding and generation cells require a Google Gen AI API key.

### Keep the API key out of the notebook

The supplied notebook currently initializes the Gemini client with a key written directly in a code cell. **Treat that key as exposed and rotate/revoke it before using or sharing the notebook.** Do not commit a replacement key. Instead, set `GEMINI_API_KEY` in the runtime's secret/environment settings and initialize the client like this:

```python
import os
from google import genai

client = genai.Client(api_key=os.environ["GEMINI_API_KEY"])
```

For Google Colab, add `GEMINI_API_KEY` through the Secrets panel and read it from the runtime; do not paste the secret into a notebook cell.

## Scope and limitations

- The retrieval store is an in-memory Python list, not a vector database or persistent index.
- Chunking is a simple whitespace split with fixed word counts. It does not preserve paragraph boundaries, use overlap, or account for model token limits.
- Every query scans every chunk and returns only the top chunk per query. There is no approximate-nearest-neighbor index, metadata filtering, reranker, or configurable top-k retrieval.
- Multi-query output is parsed with a regular expression. The notebook does not validate the generated queries, deduplicate retrieved chunks, or handle an empty parsed query list.
- The answer prompt includes retrieved text, but the notebook does not produce citations, validate factual claims, or include an evaluation set for retrieval and answer quality.
- Similarity examples illustrate embedding behavior; they are not a benchmark or a recommendation/search product evaluation.
- The corpora in the notebook are fictional exercise briefs, not verified event or scientific information.

## Files

| File | Description |
| --- | --- |
| `README.md` | Project overview, setup, security note, and implementation limits |
| `Gemini_Embeddings_and_RAG_Pipeline_GDG_day2.ipynb` | Notebook source supplied separately; add it to this repository to make the project runnable from a clone |

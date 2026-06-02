# Colombia FAQ Chatbot — RAG

A multilingual FAQ chatbot about Colombia built with **Retrieval-Augmented Generation (RAG)**.
It retrieves the most relevant passages from a local corpus using **FAISS** and generates
grounded answers with a free **Hugging Face** model. The assistant detects the language of each
question and replies in the same one: **English, Portuguese, or Spanish**.

## Overview

The project follows a classic RAG pipeline:

```
Local .txt corpus  ->  Multilingual embeddings  ->  FAISS index
                                                        |
                          Question  ->  Retrieval  ->  Top-K passages
                                                        |
                              Language detection  ->  Grounded generation  ->  Answer
```

The model answers only from the retrieved context. If the information is not present, it
says so instead of inventing an answer.

## Features

- Local knowledge base about Colombia (geography, history, culture, food, tourism, economy).
- Semantic search over the corpus with FAISS and cosine similarity.
- Multilingual question answering (EN / PT / ES) with automatic language detection.
- Interactive chat interface inside the notebook (ipywidgets).
- Question-and-answer history saved to `history.json`.
- Optional LangChain implementation of the same pipeline.

## Tech stack

| Component        | Choice                                                        |
|------------------|---------------------------------------------------------------|
| Embeddings       | `sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2` |
| Vector index     | FAISS (`IndexFlatIP`, normalized vectors)                     |
| Generator        | `Qwen/Qwen2.5-3B-Instruct` (free, multilingual)               |
| Language ID      | `langdetect`                                                  |
| Interface        | `ipywidgets`                                                  |
| Bonus            | LangChain                                                     |

## How to run (Google Colab, recommended)

1. Open `colombia_rag_chatbot_Juan_Nieto.ipynb` in Google Colab.
2. Go to `Runtime` -> `Change runtime type` and select a **GPU (T4)**.
3. Run `Runtime` -> `Run all`.
4. Use the chat box in the "Interactive chat" section to ask questions in English,
   Portuguese, or Spanish.

The notebook is self-contained: it writes the `.txt` corpus on its own, so no manual
uploads are needed.

## How to run (local, optional)

A GPU is recommended. With Python 3.10+ installed:

```bash
pip install -r requirements.txt
jupyter notebook colombia_rag_chatbot_Juan_Nieto.ipynb
```

## Repository structure

```
.
├── colombia_rag_chatbot_Juan_Nieto.ipynb   # main notebook
├── requirements.txt                        # dependencies
├── .gitignore
└── README.md
```

## How it works

1. **Corpus.** Short `.txt` files, one per topic, are written into `corpus_colombia/`.
2. **Chunking.** Each file is split into overlapping chunks so retrieval returns specific
   passages rather than whole documents.
3. **Indexing.** Every chunk is embedded with a multilingual model and stored in a FAISS index.
   Vectors are normalized so inner product equals cosine similarity.
4. **Retrieval.** The question is embedded and the top-K most similar chunks are returned.
5. **Generation.** The question language is detected and the model is asked to answer, in that
   language, using only the retrieved context.

## Notes

- A multilingual embedding model is used so that questions in Portuguese and Spanish match an
  English corpus. The `all-MiniLM-L6-v2` model can be used instead by changing `EMB_MODEL`,
  at the cost of weaker cross-lingual retrieval.

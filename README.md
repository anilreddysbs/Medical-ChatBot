# Medical PDF Q&A Chatbot

A Flask-based retrieval-augmented generation (RAG) application that answers questions using information retrieved from a Pinecone vector index built from PDF documents. The project uses Hugging Face Sentence Transformers for embeddings and a Groq-hosted chat model to generate responses.

## How it works

1. PDF files are loaded from the `data/` directory.
2. The text is split into overlapping chunks.
3. The `all-MiniLM-L6-v2` embedding model converts each chunk into a 384-dimensional vector.
4. The vectors and document metadata are stored in a Pinecone index named `medical-chatbot`.
5. The Flask app retrieves the most relevant passages and passes them to the chat model with a medical-domain system prompt.
6. The generated answer is returned through the chat interface.

## Technology

- Python and Flask
- LangChain
- Sentence Transformers (`all-MiniLM-L6-v2`)
- Pinecone
- Groq API
- PyPDF

## Requirements

- Python 3.10 recommended
- A Pinecone account and API key
- A Groq API key
- A Pinecone index configured for 384-dimensional vectors and cosine similarity

## Setup

### 1. Clone the repository

```bash
git clone https://github.com/anilreddysbs/Medical-ChatBot.git
cd Medical-ChatBot
```

### 2. Create and activate a virtual environment

```bash
python -m venv .venv
```

```bash
# Windows PowerShell
.venv\Scripts\Activate.ps1

# macOS / Linux
source .venv/bin/activate
```

### 3. Install dependencies

```bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

### 4. Configure credentials

Create a `.env` file in the project root:

```dotenv
PINECONE_API_KEY=your_pinecone_api_key
GROQ_API_KEY=your_groq_api_key
```

Never commit real credentials. Keep the `.env` file private.

### 5. Add PDF documents

Place the PDF documents you are permitted to process in the `data/` directory.

### 6. Build or refresh the Pinecone index

Run:

```bash
python store_index.py
```

The indexing script reads PDFs from `data/`, chunks the extracted text, generates embeddings, and upserts the vectors into the `medical-chatbot` index. The current script creates the index in Pinecone's AWS `us-east-1` region when it does not already exist.

### 7. Start the application

```bash
python app.py
```

Open [http://127.0.0.1:8080](http://127.0.0.1:8080) in your browser.

## Project structure

```text
app.py          Flask chat application and retrieval chain
store_index.py  PDF indexing and Pinecone vector upsert
src/helper.py   PDF loading, text splitting, and embeddings
src/prompt.py   System prompt used for answer generation
data/           Source PDF documents
research/       Development and experiment materials
```

## Configuration details

- Embedding model: `sentence-transformers/all-MiniLM-L6-v2`
- Embedding dimension: `384`
- Chunk size: `500` characters
- Chunk overlap: `20` characters
- Pinecone index: `medical-chatbot`
- Retrieval: similarity search with the top 3 results
- Chat model: `openai/gpt-oss-20b` through Groq

## Important limitations and responsible use

This is an educational RAG project, not a medical device or a substitute for professional medical advice. Generated responses can be incomplete or incorrect; do not use them for diagnosis, treatment, or emergency decisions.

The current Flask entry point runs with debug mode enabled. Keep it restricted to local development and do not expose that development server directly to the public internet. Review authentication, data privacy, logging, and deployment security before any broader use.

Only index documents you are authorized to process. Avoid uploading real patient records or other sensitive personal information to third-party services unless the necessary legal basis, permissions, and safeguards are in place.

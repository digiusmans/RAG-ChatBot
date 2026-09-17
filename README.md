# RAG PDF Chatbot

A Retrieval-Augmented Generation (RAG) PDF chatbot built for the **IBM Generative AI with Python** specialization. This project migrates the lab's IBM watsonx architecture to the free **Hugging Face Inference API**, using `meta-llama/Llama-3.1-8B-Instruct` in **GitHub Codespaces**.

Upload a PDF, then ask natural-language questions about its contents through a lightweight Flask web interface.

## ✨ Features

- PDF ingestion and question answering with retrieval-augmented generation.
- In-memory ChromaDB vector store for simple, session-oriented usage.
- Hugging Face embeddings and hosted Llama 3.1 inference.
- Browser-based HTML/CSS/JavaScript interface.
- CORS support for development and Codespaces workflows.

## 🧰 Tech Stack

- **Python 3.11**
- **Flask** and **Flask-CORS**
- **LangChain 0.3+**
- **ChromaDB**
- **Hugging Face Inference API / Endpoint**
- `sentence-transformers/all-MiniLM-L6-v2` embeddings
- HTML, CSS, and JavaScript

## 🏗️ RAG Workflow

```text
PDF Upload
	↓
PyPDFLoader
	↓
RecursiveCharacterTextSplitter
	↓
Hugging Face Embeddings
	↓
In-Memory ChromaDB
	↓
Llama 3.1 Contextual QA
	↓
Answer in the browser
```

The uploaded document is loaded and split into searchable chunks. Embeddings are stored in an in-memory ChromaDB collection. For each question, relevant chunks are retrieved and supplied as context to `meta-llama/Llama-3.1-8B-Instruct` through Hugging Face.

## 📁 Project Structure

```text
RAG-ChatBot/
├── server.py                 # Flask application and API routes
├── worker.py                 # Document processing and RAG helpers
├── templates/
│   └── index.html             # Chatbot web page
├── static/                    # Front-end assets (CSS, JavaScript, images)
├── .env                      # Local secrets; do not commit
└── README.md
```

## 🚀 Setup and Installation

### 1. Create and activate a Python 3.11 environment

In the repository root:

```bash
python3.11 -m venv .venv
source .venv/bin/activate
```

On Windows PowerShell, use:

```powershell
py -3.11 -m venv .venv
.venv\Scripts\Activate.ps1
```

### 2. Configure Hugging Face authentication

Create `.env` in the project root and add your Hugging Face access token:

```dotenv
API_KEY=hf_your_token_here
```

Create a token with the permissions required by your Hugging Face account and model access. Keep `.env` private and never commit it to source control.

### 3. Install dependencies

Upgrade packaging tools, then install the application dependencies:

```bash
python -m pip install --upgrade pip setuptools wheel
python -m pip install \
  "numpy<2.0.0" \
  "flask" \
  "flask-cors" \
  "python-dotenv" \
  "langchain>=0.3.0" \
  "langchain-community" \
  "langchain-huggingface" \
  "chromadb" \
  "pypdf" \
  "sentence-transformers" \
  "huggingface-hub"
```

> **Important:** `numpy<2.0.0` is intentionally pinned for compatibility with ChromaDB and related dependencies. If this repository includes a dependency lockfile, prefer installing from that file while retaining this NumPy constraint.

## ▶️ Run the Application

Start the Flask server from the repository root:

```bash
source .venv/bin/activate
python server.py
```

The application listens on:

```text
http://localhost:8000
```

The server is configured to use port `8000` and `host='0.0.0.0'`, which makes it reachable through the forwarded-port panel in GitHub Codespaces. Open the forwarded URL, upload a PDF, wait for processing to finish, and submit questions in the chat interface.

## 🔧 Troubleshooting

- **Question submitted before a document is loaded:** Upload a PDF first and wait for the success/status message before asking a question.
- **NumPy or ChromaDB import/version errors:** Remove the environment and recreate it, then reinstall with `numpy<2.0.0`:
  ```bash
  rm -rf .venv
  python3.11 -m venv .venv
  source .venv/bin/activate
  python -m pip install "numpy<2.0.0" chromadb
  ```
- **Missing API key or authentication failures:** Confirm `.env` is in the same directory from which `server.py` is launched, contains `API_KEY=...`, and has no surrounding quotation or spelling errors.
- **Codespaces cannot open the site:** Confirm port `8000` is forwarded and set to the appropriate visibility. Check the terminal for server startup or model-download errors.
- **Slow first request:** Embedding models and hosted inference may need to initialize or download files on their first use.
- **Large PDFs:** Start with a smaller document. In-memory indexing is intended for development and resets when the server restarts.

## 🔐 Security and Production Notes

- Do not commit `.env`, access tokens, uploaded documents, or generated vector data.
- In-memory ChromaDB is not persistent and is not suitable for multi-user production deployments without a durable, isolated vector store.
- For production, add authentication, upload limits, file validation, request timeouts, structured logging, and a production WSGI server.



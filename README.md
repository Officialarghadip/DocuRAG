# DocuRAG — AI-Powered Document Intelligence

**DocuRAG** is a document question-answering application built around **Retrieval-Augmented Generation (RAG)**. It helps users upload documents, retrieve relevant information, and ask questions grounded in the uploaded content. The project includes a web interface, a FastAPI backend, document-processing and search services, and separate user/admin workflows.

> **Project team:** CODENOVA  
> **Context:** Cognizant NPN Hackathon

---

## Contents

- [Highlights](#highlights)
- [How It Works](#how-it-works)
- [Technology Stack](#technology-stack)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Installation and Setup](#installation-and-setup)
- [Run the Application](#run-the-application)
- [Using DocuRAG](#using-docurag)
- [API Documentation](#api-documentation)
- [Configuration](#configuration)
- [Troubleshooting](#troubleshooting)
- [Security Notes](#security-notes)
- [Limitations and Future Improvements](#limitations-and-future-improvements)

## Highlights

### Document Q&A

- Upload PDF documents and supported image files.
- Extract text from documents and process scanned content with OCR where supported.
- Split extracted text into smaller chunks for retrieval.
- Search for relevant content using embeddings and a FAISS vector index, with hybrid-search services available in the backend.
- Generate answers using the Google Gemini API with retrieved document context.
- Return source and page information where available.
- Keep a query history and use caching to reduce repeated work.

### User and administration workflows

- User registration and login.
- JWT-based authentication and role-based access controls.
- Document and query management.
- Admin workflows for managing users and documents, reviewing processing failures, and viewing system information.

## How It Works

DocuRAG follows a Retrieval-Augmented Generation workflow:

1. **Upload** — a user uploads a document or image.
2. **Extract** — the backend extracts text and processes supported scanned/image content.
3. **Chunk** — extracted text is divided into manageable passages with document metadata.
4. **Embed and index** — passages are encoded as vectors and stored in a FAISS index.
5. **Retrieve** — a user submits a question and the search service finds relevant passages.
6. **Generate** — retrieved context is sent to Gemini to generate a context-aware response.
7. **Present sources** — the application displays the answer and available source/page references.

RAG can help make answers more relevant to the supplied documents, but it does not guarantee that every generated answer is correct. Always verify important information against the cited source material.

## Technology Stack

| Layer | Technologies |
|---|---|
| Frontend | Vue 3, Vite, JavaScript, HTML, CSS, Bootstrap |
| Backend API | Python, FastAPI, Uvicorn |
| Database and authentication | SQLite, JWT, bcrypt/secure password hashing, role-based authorization |
| Retrieval and embeddings | Sentence Transformers, FAISS, vector embeddings, hybrid search |
| Generative AI | Google Gemini API via the Google GenAI SDK |
| Document and image processing | PyMuPDF, Pillow, OCR/image-processing utilities |

## Project Structure

```text
DocuRAG-main/
├── backend/
│   ├── api/                 # API routers
│   ├── routes/              # Route modules
│   ├── services/            # Document processing, retrieval, image and cache services
│   ├── app.py
│   └── fastapi_app.py       # FastAPI application entry point
├── database/
│   └── database.py          # Database setup and helpers
├── frontend/
│   ├── src/
│   │   ├── App.vue
│   │   ├── main.js
│   │   └── style.css
│   ├── package.json
│   └── vite.config.js
├── vector_store/            # Included vector index and chunk data
├── .env.example             # Environment-variable template
├── requirements.txt         # Python dependencies
├── start_backend.bat        # Windows backend launcher
└── README.md
```

## Prerequisites

Install the following before starting:

- **Python 3.12** recommended for compatibility with the pinned AI/scientific Python packages. Python 3.14 may cause dependency installation failures for packages such as NumPy or other compiled libraries.
- **Node.js and npm** compatible with the Vite version declared in `frontend/package.json`.
- A working internet connection for installing dependencies and calling the Gemini API.
- A **Google Gemini API key** for AI-generated answers.

Check the installed versions in PowerShell:

```powershell
py -0p
node --version
npm --version
```

## Installation and Setup

### 1. Open the project folder

Extract the project ZIP and open PowerShell or the VS Code terminal in the project root:

```powershell
cd C:\path\to\DocuRAG-main
```

### 2. Create and activate a Python virtual environment

On Windows PowerShell:

```powershell
py -3.12 -m venv venv
.\venv\Scripts\Activate.ps1
python --version
```

Confirm that the displayed version is Python 3.12.x. If PowerShell cannot find `py -3.12`, install Python 3.12 first.

### 3. Install backend dependencies

From the project root, with the virtual environment activated:

```powershell
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

If installation fails while building NumPy or another compiled package, check that the virtual environment is using Python 3.12 rather than Python 3.14.

### 4. Configure environment variables

Copy the example file to `.env` in the project root:

```powershell
Copy-Item .env.example .env
```

Open `.env` and configure the values for your local environment. At minimum, set your Gemini API key and a long, unique JWT secret:

```dotenv
GEMINI_API_KEY=replace-with-your-gemini-api-key
DOCURAG_JWT_SECRET=replace-with-a-long-random-secret
```

The example file also includes admin-account settings. Change any example/default password before using the application. Never commit `.env` or real credentials to source control.

### 5. Install frontend dependencies

Open a **second terminal** and navigate to the frontend folder:

```powershell
cd C:\path\to\DocuRAG-main\frontend
npm install
```

Do not skip `npm install`: it installs the local Vite executable used by `npm run dev`.

## Run the Application

Run the backend and frontend in **two separate terminals**.

### Terminal 1 — Backend

From the project root, activate the virtual environment and run:

```powershell
.\venv\Scripts\Activate.ps1
python -m uvicorn backend.fastapi_app:app --host 127.0.0.1 --port 8000 --reload
```

Using `python -m uvicorn` helps avoid Windows PATH issues where the `uvicorn` command is not recognized.

Backend base URL: <http://127.0.0.1:8000>  
Interactive API docs: <http://127.0.0.1:8000/docs>

### Terminal 2 — Frontend

```powershell
cd C:\path\to\DocuRAG-main\frontend
npm run dev
```

Vite prints a local URL, commonly <http://localhost:5173/>. Open the exact URL shown in your terminal.

To create a production frontend build:

```powershell
npm run build
```

## Using DocuRAG

1. Start the backend and frontend.
2. Open the frontend URL printed by Vite.
3. Register or sign in with an appropriate account.
4. Upload a supported PDF or image and wait for processing to finish.
5. Ask a question about the uploaded content.
6. Review the answer and any source/page references provided.
7. Use the admin area with a configured admin account for available management functions.

Exact supported file types and available actions depend on the current implementation and configuration.

## API Documentation

FastAPI provides interactive API documentation when the backend is running:

- Swagger UI: <http://127.0.0.1:8000/docs>
- ReDoc: <http://127.0.0.1:8000/redoc>

Use these pages to inspect and test the endpoints exposed by the running backend.

## Configuration

| Variable | Purpose |
|---|---|
| `GEMINI_API_KEY` | Credentials used to access the Google Gemini API |
| `DOCURAG_JWT_SECRET` | Secret used to sign/verify authentication tokens; set a strong private value |
| `ADMIN_USERNAME` | Configured admin username, if used by the account setup code |
| `ADMIN_EMAIL` | Configured admin email, if used by the account setup code |
| `ADMIN_PASSWORD` | Initial/configured admin password, if used by the account setup code |

Keep secrets private and make sure `.env` is loaded by the backend configuration used in your environment.

## Troubleshooting

### `uvicorn` is not recognized

Activate the virtual environment and run Uvicorn as a Python module:

```powershell
.\venv\Scripts\Activate.ps1
python -m uvicorn backend.fastapi_app:app --host 127.0.0.1 --port 8000 --reload
```

### `ModuleNotFoundError: No module named 'fastapi'`

Make sure the virtual environment is active and install the requirements from the project root:

```powershell
python -m pip install -r requirements.txt
```

### NumPy installation fails with `metadata-generation-failed`

This commonly happens when a pinned package cannot use a compatible prebuilt wheel for the active Python version. Create a fresh virtual environment with Python 3.12, activate it, and reinstall `requirements.txt`.

### `'vite' is not recognized`

Run the following inside `frontend/`:

```powershell
npm install
npm run dev
```

If installation fails, check your Node.js version and the error printed by npm.

### Gemini error: `[Errno 11001] getaddrinfo failed`

This usually indicates that the hostname could not be resolved. Check that your internet connection and DNS are working, then test from PowerShell:

```powershell
nslookup generativelanguage.googleapis.com
Test-NetConnection generativelanguage.googleapis.com -Port 443
```

If DNS resolution fails, troubleshoot the network, DNS, VPN, proxy, or firewall. If the hostname resolves and port 443 is reachable, verify the Gemini API key and backend configuration, then restart the backend. Do not share your API key in logs or screenshots.

### Frontend opens, but API requests fail

- Confirm the backend is running on port `8000`.
- Check the browser console and backend terminal for the full error.
- Check the CORS and API base-URL settings used by the frontend.
- Confirm the required environment variables are configured and the backend was restarted after changes.

## Security Notes

- Never commit `.env`, API keys, passwords, JWT secrets, or real user data.
- Replace example credentials and secrets before running beyond a local demo.
- Restrict CORS origins and review authentication/authorization settings before deployment.
- Do not expose the development server directly to the public internet.
- Verify generated answers against source documents, especially for sensitive or high-impact decisions.

## Limitations and Future Improvements

Potential areas for further development include:

- More robust document-format validation and processing feedback.
- Improved retrieval evaluation and answer-grounding checks.
- Automated tests for API routes, document processing, and authentication.
- Production deployment configuration, monitoring, and secret management.
- Better handling of API/network failures and clearer user-facing error messages.

## Team

- **Team:** CODENOVA
- **Event:** Cognizant NPN Hackathon
- **Project:** DocuRAG

---

If you build on this project, please document any additional setup steps, supported formats, and environment variables introduced by your changes.

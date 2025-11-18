# RAG - Demonstration Project
A complete **Retrieval-Augmented Generation (RAG)** application that demonstrates modern AI capabilities for answering questions about Ultimate Frisbee rules and strategies. This project showcases how to build a production-ready RAG system using cutting-edge technologies.


## 🎯 **What This Project Demonstrates**

This repository serves as a **comprehensive tutorial project** for learning about **RAG (Retrieval-Augmented Generation) implementation**.

## 🚀 **Key Features**

- **📚 Intelligent Document Processing**: Automatically indexes and processes PDF documents (e.g., WFDF Ultimate Frisbee Rules)
- **🤖 AI-Powered Q&A**: Ask natural language questions and get accurate, context-aware answers
- **📊 Source Attribution**: Every answer includes relevant source documents with similarity scores and page references
- **💬 Query History**: Track all queries and responses with full conversation history
- **🎨 Modern UI**: Beautiful, responsive React interface with real-time updates

## 🛠 **Technology Stack**

### **Backend (Python)**
- **FastAPI**: High-performance API framework with automatic OpenAPI documentation
- **SQLModel**: Modern Python SQL toolkit combining SQLAlchemy + Pydantic
- **LlamaIndex**: RAG framework for document processing and querying
- **PostgreSQL + pgvector**: Vector database for embeddings storage
- **Ollama**: Local LLM serving (Gemma, Llama, Mistral, etc.)

### **Frontend (TypeScript/React)**
- **React 19**: Modern React with latest features
- **Vite**: Lightning-fast build tool
- **TailwindCSS**: Utility-first CSS framework
- **SWR**: Data fetching with caching and revalidation
- **Radix UI**: Accessible, unstyled UI components

### **Infrastructure**
- **pgvector**: PostgreSQL extension for vector operations
- **uv**: Fast Python package management
- **Ollama**: Local LLM inference engine

---

## 🏃‍♂️ **Quick Start - Local Development**

### Prerequisites

- **Python 3.11+** (for backend)
- **Node.js 18+** (for frontend)
- **PostgreSQL 17+** (with pgvector extension)
- **Ollama** (for LLM inference)
- **8GB+ RAM** (for running local LLMs)
- **Git** (for cloning the repository)

---

## 📦 **Installation Steps**

### 1. Clone and Setup

```bash
# Clone the repository
git clone https://github.com/dev-it-with-me/RagUltimateAdvisor.git
# Copy environment template (if it exists)
cp .env.example .env 2>/dev/null || touch .env
```

### 2. Install Prerequisites

#### **Install PostgreSQL**
```bash
# macOS
brew install postgresql@18

# Start PostgreSQL
brew services start postgresql@18
```

#### **Install pgvector Extension**
```bash
# macOS
brew install pgvector

# Or compile from source for PostgreSQL 18
cd /tmp
git clone --branch v0.8.1 https://github.com/pgvector/pgvector.git
cd pgvector
export PATH="/Library/PostgreSQL/18/bin:$PATH"
make
sudo make install
```

#### **Install Ollama**
```bash
# Download from https://ollama.ai
# Or use Homebrew
brew install ollama

# Start Ollama service
ollama serve
```

#### **Install uv (Python Package Manager)**
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
source $HOME/.local/bin/env
```

### 3. Configure Environment Variables

Edit `.env` file with your settings:

```bash
# Database Configuration
APP_PG_HOST=localhost
APP_PG_PORT=5432
APP_PG_USER=ultimateadvisor
APP_PG_PASSWORD=1234
APP_PG_DATABASE=ultimateadvisor_db

# Vector Store Configuration
APP_VECTOR_TABLE_NAME=documents
APP_EMBED_DIM=768

# Ollama Configuration
APP_OLLAMA_BASE_URL=http://localhost:11434
APP_CHAT_MODEL=gemma3:4b
APP_EMBEDDING_MODEL=embeddinggemma

# Application Configuration
APP_DATA_FOLDER=./data

# Frontend Configuration (for standalone development)
VITE_API_BASE_URL=http://localhost:8001
```

### 4. Setup Database

```bash
# Create PostgreSQL user and database
psql -h localhost -U postgres -d postgres << 'EOF'
CREATE USER ultimateadvisor WITH PASSWORD '1234';
CREATE DATABASE ultimateadvisor_db OWNER ultimateadvisor;
GRANT ALL PRIVILEGES ON DATABASE ultimateadvisor_db TO ultimateadvisor;
EOF

# Enable pgvector extension
psql -h localhost -U postgres -d ultimateadvisor_db << 'EOF'
CREATE EXTENSION IF NOT EXISTS vector;
EOF
```

### 5. Install Python Dependencies

```bash
# Install dependencies
uv sync

# Initialize database tables
uv run python src/scripts/run_init_db.py
```

### 6. Download Ollama Models

```bash
# Pull required models (this may take a few minutes)
ollama pull gemma3:4b
ollama pull embeddinggemma
```

### 7. Load Documents

```bash
# Place your PDF documents in the data/ folder
# Then load embeddings into the vector store
uv run python src/scripts/run_load_embeddings.py
```

### 8. Start the Backend

```bash
# Start FastAPI backend server
uv run uvicorn src.main:app --host 0.0.0.0 --port 8001 --reload
```

### 9. Start the Frontend (Optional)

```bash
# In a new terminal
cd frontend

# Install dependencies
npm install

# Start development server
npm run dev
```

---

## 🌐 **Access the Application**

- **Frontend UI**: http://localhost:5173
- **Backend API**: http://localhost:8001
- **API Documentation**: http://localhost:8001/docs
- **API Redoc**: http://localhost:8001/redoc

---

## 📖 **Usage Examples**

Try asking these questions in the chat interface:

- "What happens if the disc goes out of bounds?"
- "How do you score in Ultimate?"
- "What is the spirit of the game?"
- "What are the field dimensions?"
- "What causes a turnover?"

### Data Flow

1. **Document Processing**: PDF documents are chunked and embedded using Ollama
2. **Vector Storage**: Embeddings are stored in PostgreSQL with pgvector extension
3. **Query Processing**: User questions are embedded and matched against stored vectors
4. **Response Generation**: Retrieved context is sent to the chat model for answer generation
5. **History Tracking**: All conversations are persisted for future reference

---

## 🛠️ **Quick Commands Reference**

### Backend Commands
```bash
# Start backend server
uv run uvicorn src.main:app --host 0.0.0.0 --port 8001 --reload

# Initialize database
uv run python src/scripts/run_init_db.py

# Load embeddings
uv run python src/scripts/run_load_embeddings.py
```

### Frontend Commands
```bash
# Install dependencies
npm install

# Start development server
npm run dev

# Build for production
npm run build
```

### Database Commands
```bash
# Connect to database
PGPASSWORD=1234 psql -h localhost -U ultimateadvisor -d ultimateadvisor_db

# Check document count
curl http://localhost:8001/rag/documents/count

# Check health status
curl http://localhost:8001/rag/health
```

### Ollama Commands
```bash
# Check running models
curl http://localhost:11434/api/tags

# List available models
ollama list

# Pull a model
ollama pull <model-name>
```

---

## 📂 **Project Structure**

```
RagUltimateAdvisor/
├── src/                      # Backend source code
│   ├── main.py              # FastAPI application entry point
│   ├── config.py            # Configuration management
│   ├── dependencies.py      # Dependency injection
│   ├── schemas.py           # Pydantic models
│   ├── rag/                 # RAG implementation
│   │   ├── routes.py        # RAG API endpoints
│   │   ├── services.py      # RAG business logic
│   │   └── repositories.py  # Vector store operations
│   ├── history/             # Query history tracking
│   │   ├── routes.py        # History API endpoints
│   │   ├── services.py      # History business logic
│   │   ├── repositories.py  # Database operations
│   │   └── models.py        # SQLModel definitions
│   └── scripts/             # Utility scripts
│       ├── run_init_db.py   # Database initialization
│       └── run_load_embeddings.py  # Document loading
├── frontend/                 # React frontend
│   ├── src/
│   │   ├── App.tsx          # Main application component
│   │   ├── components/      # Reusable components
│   │   ├── hooks/           # Custom React hooks
│   │   ├── lib/             # Utility functions
│   │   └── types/           # TypeScript type definitions
│   └── package.json         # Frontend dependencies
├── data/                     # PDF documents folder
├── tests/                    # Test files
├── .env                      # Environment variables
├── pyproject.toml           # Python dependencies
└── README.md                # This file
```

---

## 🧪 **Testing**

```bash
# Run tests
uv run pytest tests/

# Run specific test
uv run pytest tests/test_query_vector_store.py
```



## 🐛 **Troubleshooting**

### Common Issues

**1. "No documents in vector store" error**
- Make sure you've loaded documents: `uv run python src/scripts/run_load_embeddings.py`
- Check that PDF files exist in the `data/` folder

**2. "Connection refused" error**
- Ensure PostgreSQL is running: `brew services start postgresql@18`
- Verify Ollama is running: `ollama serve`
- Check ports are not in use: `lsof -i :8001` and `lsof -i :5173`

**3. pgvector extension not found**
- Install pgvector: `brew install pgvector` or compile from source
- Enable in database: `CREATE EXTENSION IF NOT EXISTS vector;`

**4. Frontend can't connect to backend**
- Check `frontend/.env` has correct `VITE_API_BASE_URL=http://localhost:8001`
- Verify backend is running on port 8001
- Clear browser cache and restart frontend

**5. Ollama model download issues**
- Check internet connection
- Try pulling models individually: `ollama pull gemma3:4b`
- Ensure enough disk space for models (~4GB per model)

---

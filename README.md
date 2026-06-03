# ResearchOS: Multi-Agent RAG Research Assistant

ResearchOS is an AI-powered research platform that automates web searching, performs retrieval-augmented generation (RAG) using local vector embeddings, and generates source-cited markdown reports that stream in real time. 

Powered by a sequential **LangGraph** multi-agent workflow, **FastAPI** Server-Sent Events (SSE) streaming, and a responsive **React** glassmorphic interface.

---

## 🚀 Key Features

* **Sequential Multi-Agent Architecture**: Uses LangGraph to manage graph state across three specialized nodes (Search, RAG, Writer).
* **Real-time Event Streaming**: Utilizes FastAPI and Server-Sent Events (SSE) to stream token chunks and active agent status updates directly to the client.
* **Cost-Efficient Local Embeddings**: Embeds web contexts completely locally using `sentence-transformers` (`all-MiniLM-L6-v2`) and indexes them into a local `ChromaDB` vector database.
* **Accurate Source Citations**: Grounded synthesis prompts ensure the generated report includes inline markdown links referring back to the crawled web pages.
* **Premium Client Utilities**:
  * **Interactive status telemetry** tracking active agents.
  * **Persistent search history** saved to local storage for instant loading.
  * **Quick-start preset prompts** for testing.
  * **Export to Markdown** capability.

---

## 🛠️ Tech Stack

* **Frontend**: React, TypeScript, Vite, Vanilla CSS (Premium Glassmorphic Design), Lucide icons.
* **Backend**: FastAPI, LangGraph, LangChain, ChromaDB, Sentence-Transformers, Uvicorn, Python Dotenv.
* **APIs**:
  * **Tavily AI**: Optimized web search API for LLMs.
  * **Groq Cloud**: Lightning-fast inference engine running `llama-3.3-70b-versatile`.

---

## 🔄 System Architecture

```mermaid
graph TD
    User([User Query]) --> API[FastAPI /research endpoint]
    
    subgraph LangGraph Workflow
        API --> State[(State: query, search_results, chunks, report)]
        State --> SearchAgent[1. Search Agent: Tavily API]
        SearchAgent -- Web Content --> RAGAgent[2. RAG Agent: local ChromaDB]
        RAGAgent -- Top Chunks --> WriterAgent[3. Writer Agent: Groq LLM]
    end

    subgraph Streaming Telemetry
        SearchAgent -- SSE Status: search --> Frontend[React Client UI]
        RAGAgent -- SSE Status: rag --> Frontend
        WriterAgent -- SSE Status: writer --> Frontend
        WriterAgent -- SSE Chunk: Markdown Tokens --> Frontend
    end
    
    Frontend --> RenderedReport([Rendered Markdown Report])
```

---

## 💻 Local Installation & Setup

### Prerequisites
* Python 3.10+
* Node.js 18+
* Tavily API Key & Groq API Key

### 1. Clone & Set Up Backend
1. Navigate to the `backend` directory:
   ```bash
   cd backend
   ```
2. Create and activate a virtual environment:
   ```bash
   python3 -m venv venv
   source venv/bin/activate
   ```
3. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
4. Create a `.env` file in the `backend/` directory:
   ```ini
   GROQ_API_KEY=gsk_your_groq_api_key_here
   TAVILY_API_KEY=tvly_your_tavily_api_key_here
   ```
5. Start the FastAPI server:
   ```bash
   uvicorn main:app --reload --host 127.0.0.1 --port 8000
   ```

### 2. Set Up Frontend
1. Navigate to the `frontend` directory:
   ```bash
   cd ../frontend
   ```
2. Install Node modules:
   ```bash
   npm install
   ```
3. Start the development server:
   ```bash
   npm run dev
   ```
4. Open your browser to `http://localhost:5173`.

---

## ☁️ Production Deployment

### 1. Backend (Railway)
* Initialize a Git repository and push your project to GitHub.
* Connect your GitHub repo to a new project on **Railway.app**.
* In Settings ➔ General, set the **Root Directory** to `/backend`.
* In Variables, add:
  * `GROQ_API_KEY` = *your_live_key*
  * `TAVILY_API_KEY` = *your_live_key*
  * `PORT` = `8000`
* Generate a public domain under Networking.

### 2. Frontend (Vercel)
* Connect your GitHub repo to a new project on **Vercel.com**.
* Set the **Root Directory** to `frontend`.
* Under Environment Variables, add:
  * `VITE_API_URL` = *Your deployed Railway backend domain URL (no trailing slash)*.
* Click **Deploy**.

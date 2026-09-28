# LangGraph Multi-Utility PDF Chatbot

A Streamlit-based AI chatbot built with **LangGraph** and **LangChain** that combines conversational memory, PDF question answering (RAG), web search, stock-price lookup, and calculator tools in a single application.

## 🚀 Features

- 💬 **Conversational Chat** — Maintains separate conversations using LangGraph thread IDs.
- 📄 **PDF Question Answering** — Upload a PDF and ask questions about its content.
- 🔎 **RAG Pipeline** — Splits PDF text into chunks, creates embeddings, stores them in FAISS, and retrieves relevant context for questions.
- 🌐 **Web Search** — Uses DuckDuckGo search through the `ddgs` package for web-based queries.
- 📈 **Stock Price Tool** — Fetches stock information using Alpha Vantage.
- 🧮 **Calculator Tool** — Supports addition, subtraction, multiplication, and division.
- 🛠️ **Tool Calling** — Uses OpenAI tool calling with LangGraph's `ToolNode` and conditional routing.
- 💾 **Persistent Conversation State** — Uses SQLite checkpointing through `SqliteSaver`.
- 🖥️ **Streamlit UI** — Simple interface for uploading documents, chatting, and switching between previous conversations.

## 🏗️ Architecture

```text
                         ┌──────────────────────┐
                         │      Streamlit       │
                         │     Frontend UI      │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │      LangGraph       │
                         │   Chat Orchestration │
                         └──────────┬───────────┘
                                    │
                         ┌──────────▼───────────┐
                         │      ChatOpenAI      │
                         │    Tool Calling      │
                         └──────────┬───────────┘
                                    │
                     ┌──────────────┼──────────────┐
                     │              │              │
                     ▼              ▼              ▼
               ┌──────────┐   ┌──────────┐   ┌──────────┐
               │ RAG Tool │   │ Web Tool │   │Calculator│
               └────┬─────┘   └──────────┘   └──────────┘
                    │
                    ▼
              ┌──────────────┐
              │     FAISS    │
              │ Vector Store │
              └──────┬───────┘
                     │
                     ▼
                  PDF Data

              ┌────────────────┐
              │ Stock Price    │
              │     Tool       │
              └────────────────┘

              ┌────────────────┐
              │ SQLite         │
              │ Checkpointer   │
              └────────────────┘
```

## 🔄 RAG Workflow

```text
PDF Upload
    │
    ▼
PyPDFLoader
    │
    ▼
Text Extraction
    │
    ▼
RecursiveCharacterTextSplitter
    │
    ▼
Document Chunks
    │
    ▼
OpenAI Embeddings
    │
    ▼
FAISS Vector Store
    │
    ▼
Retriever
    │
    ▼
User Question
    │
    ▼
RAG Tool
    │
    ▼
Relevant PDF Context
    │
    ▼
ChatOpenAI
    │
    ▼
Answer
```

## 🧰 Tech Stack

| Technology | Purpose |
|---|---|
| Python | Application development |
| Streamlit | Frontend/UI |
| LangGraph | Agent workflow and state management |
| LangChain | LLM and tool integration |
| OpenAI GPT-4o-mini | Chat model |
| OpenAI Embeddings | Document embeddings |
| FAISS | Vector database |
| PyPDF | PDF loading |
| DuckDuckGo / `ddgs` | Web search |
| Alpha Vantage | Stock-price API |
| SQLite | Conversation checkpointing |

## 📁 Project Structure

```text
langgraph_chatbot/
│
├── langgraph_rag_backend.py
├── streamlit_rag_frontend.py
├── requirements.txt
├── .env
├── .gitignore
├── chatbot.db
└── README.md
```

> `chatbot.db` and `.env` should not be committed to GitHub if they contain local conversation data or secrets.

## ⚙️ Installation

### 1. Clone the repository

```bash
git clone git@github.com:git-manisha/Chatbot.git
```

### 2. Create a virtual environment

Windows:

```bash
python -m venv myenv
myenv\Scripts\activate
```

macOS/Linux:

```bash
python3 -m venv myenv
source myenv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure environment variables

Create a `.env` file:

```env
OPENAI_API_KEY=your_openai_api_key
ALPHAVANTAGE_API_KEY=your_alpha_vantage_api_key
```

Do not hard-code API keys in Python source code.

For the stock-price tool, use the environment variable instead of putting the Alpha Vantage key directly in the URL.

### 5. Run the application

```bash
streamlit run streamlit_rag_frontend.py
```

The application will open in your browser.

## 💡 Example Use Cases

### Ask questions about a PDF

Upload a document and ask:

```text
What is the main objective of this document?
```

or:

```text
Explain the architecture described in the PDF.
```

### Use the calculator

```text
Calculate 125 * 48
```

### Search the web

```text
What are the latest developments in Apache Spark?
```

### Check a stock

```text
What is the latest price of AAPL?
```

## 🧠 LangGraph Workflow

The chatbot uses a simple LangGraph workflow:

```text
START
  │
  ▼
chat_node
  │
  ├──── Normal response ────► END
  │
  ▼
tools
  │
  ▼
chat_node
```

The `chat_node` sends the conversation to the LLM. If the model requests a tool, `tools_condition` routes execution to the `ToolNode`. After the tool completes, the result is sent back to the LLM for the final response.

## 🔐 Security Notes

- Never commit `.env` files.
- Never expose API keys in source code.
- Add `.env`, `chatbot.db`, and Python cache files to `.gitignore`.
- If an API key has already been committed to GitHub, revoke/rotate it and replace it with a new key.

Example `.gitignore`:

```gitignore
# Environment
.env

# Virtual environment
myenv/
venv/
.venv/

# Python
__pycache__/
*.py[cod]

# Jupyter
.ipynb_checkpoints/

# SQLite database
chatbot.db

# Local FAISS/index files
*.faiss
*.pkl
```

## 📌 Future Improvements

- Generate and persist AI-based conversation titles.
- Add streaming tool results and richer tool-status UI.
- Persist uploaded-document metadata across application restarts.
- Add support for multiple PDFs per conversation.
- Add document citation/page references in RAG responses.
- Add authentication and user-specific conversation storage.
- Deploy the application using Streamlit Community Cloud or another cloud platform.

## 👩‍💻 Author

**Manisha Soni**

Data Engineer | Data Science & Analytics

Interested in **Data Engineering, AI/LLM applications, RAG, LangGraph, Python, SQL, and Azure**.

Financial Stock Analysis using LlamaIndex
A RAG-powered (Retrieval-Augmented Generation) Streamlit application that lets you query a corpus of financial news articles to generate AI-driven stock outlook reports and competitor analyses.
Overview
This project indexes hundreds of financial news articles into a local ChromaDB vector store and exposes a conversational query interface via a Streamlit web app. Users can generate two types of reports:

Single Stock Outlook — a forward-looking report on a given ticker (2023–2027), including risks and headwinds.
Competitor Analysis — a comparative report on two stocks side by side.

Under the hood, the app uses MistralAI as the LLM and a HuggingFace embedding model to retrieve relevant article chunks before generating a response.
Architecture
articles/ (HTML financial news)
     │
     ▼
HuggingFace Embeddings (BAAI/bge-small-en-v1.5)
     │
     ▼
ChromaDB Vector Store (persisted locally at ./chroma_db)
     │
     ▼
LlamaIndex VectorStoreIndex + Query Engine
     │
     ▼
MistralAI LLM (mistral-small-latest)
     │
     ▼
Streamlit UI
Dataset
The articles/ directory contains 570 HTML financial news articles sourced from Benzinga (BZ) and Briefing.com (BRFG/BRFUPDN), covering the following tickers:
TickerCompanyAAPLAppleGOOGAlphabetMETAMeta PlatformsMSFTMicrosoftNVDANVIDIATSMTSMC
Articles span from January 2021 to April 2023. The articles.zip file contains the same dataset in compressed form.
Prerequisites

Python 3.9+
A Mistral AI API key

Installation
bash# Clone the repository
git clone https://github.com/your-username/financial_stock_analysis.git
cd financial_stock_analysis

# Create and activate a virtual environment (recommended)
python -m venv venv
source venv/bin/activate   # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
Configuration
Create a .env file in the project root:
envMISTRAL_API_KEY=your_mistral_api_key_here

The .env file is listed in .gitignore and will not be committed.

Indexing the Articles
Before running the app for the first time, you need to index the articles into ChromaDB. The app loads the index from the existing chroma_db/ directory on startup. If the directory doesn't exist yet, you'll need to run an ingestion step to parse the HTML files and build the vector store.
A typical ingestion script (not included — see LlamaIndex docs) would:

Load the HTML files from articles/
Parse and chunk the text
Embed with BAAI/bge-small-en-v1.5
Persist to ./chroma_db

Once indexed, the chroma_db/ directory can be reused across sessions.
Running the App
bashstreamlit run app.py
The app will be available at http://localhost:8501.
Usage
Single Stock Outlook

Select Single Stock Outlook from the dropdown.
Enter a ticker symbol (e.g. NVDA, AAPL, MSFT).
Click away or press Enter — the app will query the index and generate a 2023–2027 outlook report including risks and headwinds.

Competitor Analysis

Select Competitor Analysis from the dropdown.
Enter two ticker symbols (e.g. GOOG and META).
The app will generate a comparative analysis of the two stocks based on the indexed news.


Only tickers present in the articles/ dataset will yield meaningful results: AAPL, GOOG, META, MSFT, NVDA, TSM.

Project Structure
financial_stock_analysis/
├── app.py               # Main Streamlit application
├── requirements.txt     # Python dependencies
├── .gitignore
├── articles/            # 570 HTML financial news articles
├── articles.zip         # Compressed copy of the articles
├── chroma_db/           # Persisted ChromaDB vector store (generated)
└── test.ipynb           # Scratch notebook
Dependencies
PackagePurposestreamlitWeb UIllama-indexRAG orchestration (indexing, retrieval, query engine)mistralaiLLM provider (mistral-small-latest)llama-index-llms-mistralaiLlamaIndex MistralAI integrationllama-index-embeddings-huggingfaceHuggingFace embedding modelllama-index-vector-stores-chromaChromaDB vector store integrationchromadbLocal persistent vector databasepython-dotenvEnvironment variable managementpandasData utilitiesib_insyncInteractive Brokers API (included, not used in app.py)jinja2Templating (transitive dependency)
Notes

The ChromaDB store persists between runs — re-indexing is only needed if you add new articles.
The ib_insync package in requirements.txt suggests live brokerage data integration may have been planned or is used in the notebook.
Chunk size is set to 512 tokens with a 50-token overlap, which is well-suited for dense financial prose.

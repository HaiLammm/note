# CHATBOT.md

## Development Commands

### Running the Bot
```bash
python bot.py
```

### Installing Dependencies
```bash
pip install -r requirements.txt
```

### Updating Vector Store
```bash
python update_vectorstore.py
```

## Architecture Overview

This is a Discord AI chatbot that integrates Retrieval-Augmented Generation (RAG) for contextual responses. The bot uses Ollama for local LLM inference and ChromaDB for vector storage.

### Structure 

```bash
discord-bot/
├── bot.py                 # entry chính, khởi tạo Bot & load modules (bổ sung load RAG)
├── config.py              # token, prefix, intents (bổ sung config cho Ollama và RAG)
├── commands/              # chứa tất cả các lệnh của bot
│   ├── init.py
│   ├── general.py         # lệnh cơ bản: ping, help, info (bổ sung help cho AI)
│   ├── admin.py           # lệnh quản trị: clear, ban, kick
│   └── ai.py              # Mới: Lệnh chat AI với RAG (ví dụ: !chat "câu hỏi")
├── events/                # chứa các event listener
│   ├── init.py
│   ├── on_ready.py        # Bổ sung init RAG khi bot ready
│   ├── on_message.py      # Bổ sung xử lý tin nhắn với RAG (khi mention bot)
│   └── on_member_join.py
├── utils/                 # tiện ích mở rộng
│   ├── logger.py
│   ├── database.py        # Có thể dùng để lưu dữ liệu RAG nếu cần (ví dụ: SQLite cho metadata)
│   └── api_helper.py      # Bổ sung helper cho Ollama API nếu cần
├── rag/                   # Mới: Chứa logic RAG
│   ├── init.py
│   ├── rag_chain.py       # Xây dựng chain RAG (retriever + generator)
│   ├── embeddings.py      # Setup embedding model (từ Ollama hoặc HuggingFace)
│   ├── vectorstore.py     # Setup và load vector store (Chroma)
│   └── data_loader.py     # Load dữ liệu vào vector store (từ file/text/URL)
├── data/                  # Mới: Thư mục lưu dữ liệu cho RAG (ví dụ: PDFs, TXT cho kiến thức tùy chỉnh)
│   └── example_knowledge.txt  # File mẫu dữ liệu
├── .env                   # Mới: Config nhạy cảm
└── requirements.txt       # Đã cập nhật 
```

### Core Modules

- **`commands/`**: Discord bot commands organized as cogs
  - `general.py`: Basic user commands (help, ping, stats)
  - `admin.py`: Administrative commands (kick, ban, clear messages) for server management
  - `ai.py`: AI-powered commands that leverage the RAG system for contextual responses
- **`events/`**: Asynchronous event handlers for Discord.py library
  - `on_ready.py`: Bot initialization and status logging when connected to Discord
  - `on_message.py`: Main message processing handler that routes queries to the RAG system
  - `on_member_join.py`: Welcome message system using configured channel ID
- **`rag/`**: Complete RAG implementation using LangChain framework
  - `data_loader.py`: Document loading and text chunking utilities with preprocessing
  - `embeddings.py`: Vector embedding generation and management using sentence transformers
  - `rag_chain.py`: LangChain pipeline setup, vectorstore integration, and prompt engineering
  - `vectorstore/`: ChromaDB storage directory with persistence and indexing
- **`utils/`**: Shared utility functions and helpers
  - Logging configuration and formatted output to `logs/bot.log`
  - Database operations using SQLite for user data and conversation history
  - API wrappers for external services and error handling utilities
- **`data/`**: Static knowledge base and configuration files
  - `knowledge.txt`: Primary knowledge base document for RAG system
  - Additional documentation files for domain-specific context

### Configuration

Requires `.env` file with:
- `DISCORD_TOKEN`: Discord bot token
- `OLLAMA_HOST`: Ollama server URL (default: http://localhost:11434)
- `OLLAMA_MODEL`: Ollama model name (default: phi3:latest)
- `WELCOME_CHANNEL_ID`: Discord channel ID for welcome messages

### Data Flow

1. **Knowledge Base Initialization**: Knowledge documents in `data/knowledge.txt` are loaded and processed using the `data_loader.py` module, which handles text chunking with configurable chunk sizes, overlap settings, and preprocessing steps
2. **Embedding Generation**: Text chunks are converted to high-dimensional vector embeddings using sentence transformer models (e.g., all-MiniLM-L6-v2) through the `embeddings.py` module, enabling semantic similarity comparisons
3. **Vector Storage**: Generated embeddings are stored in ChromaDB vectorstore with persistent storage, metadata indexing, and efficient nearest-neighbor search capabilities for scalable retrieval
4. **Query Processing**: When a user sends a message, Discord event handlers in `events/on_message.py` capture the message and determine if it requires AI processing based on command prefixes or keywords
5. **Semantic Search**: The user query is embedded using the same sentence transformer model and used to perform k-nearest neighbor search against the stored vectors using cosine similarity scoring
6. **Context Retrieval**: Top-k most semantically similar document chunks are retrieved from the vectorstore, providing relevant context while filtering out irrelevant content
7. **Response Generation**: Retrieved context chunks are fed into a LangChain RAG chain that combines them with a carefully engineered prompt template and sends them to the Ollama LLM for contextual response generation
8. **Response Delivery**: The generated response is sent back through Discord's API, and conversation context may be stored in the SQLite database for future reference or continuity



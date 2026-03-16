# 🧠 Nexus-AI — Autonomous Multi-Agent Personal Memory Framework

> A Telegram-based AI assistant that remembers everything you tell it — text, photos, documents — and answers questions using semantic vector search.

![n8n](https://img.shields.io/badge/n8n-workflow-orange)
![Groq](https://img.shields.io/badge/Groq-Llama%203.3--70B-blue)
![Cohere](https://img.shields.io/badge/Cohere-Embeddings-green)
![Supabase](https://img.shields.io/badge/Supabase-pgvector-darkgreen)
![Telegram](https://img.shields.io/badge/Telegram-Bot-blue)

---

## 🎯 What It Does

Nexus-AI is a personal memory bot that:
- 💾 **Saves** any text memory via Telegram (`Save: your memory here`)
- 📸 **Scans photos** using Groq Vision AI and saves what it sees
- 🔍 **Retrieves** memories using natural language questions
- 🧠 **Answers** using RAG (Retrieval Augmented Generation) with semantic similarity search

---

## 🏗️ Architecture

```
Telegram Message
      ↓
Loop Prevention Filter
      ↓
Smart Router (Text vs Photo)
      ↓
┌─────────────────────────────────────┐
│  PHOTO PATH         TEXT PATH       │
│  Get Photo File     Text Router     │
│  Groq Vision AI  ┌──Save──Retrieve──┤
│  Cohere Embed    │  Cohere Embed    │
│  Supabase Save   │  HTTP Request    │
│                  │  Vector Search   │
│                  │  Nexus-Brain     │
└──────────────────┴──────────────────┘
      ↓
Telegram Reply
```

---

## 🛠️ Tech Stack

| Component | Technology |
|-----------|------------|
| Workflow Automation | n8n |
| LLM (Text) | Groq Llama 3.3-70B |
| LLM (Vision) | Groq Llama 4 Scout 17B |
| Embeddings | Cohere embed-v3 (1024 dims) |
| Vector Database | Supabase + pgvector |
| Interface | Telegram Bot |
| Deployment | Local / Render.com |

---

## ✨ Features

- 🤖 **Zero UI** — entire interaction through Telegram
- 🧠 **Semantic Search** — finds memories by meaning, not keywords
- 👁️ **Vision Memory** — scan boarding passes, receipts, notes, photos
- 📊 **Confidence Scores** — shows similarity % for retrieved memories
- ⚡ **Fast** — Groq inference in under 1 second
- 🔒 **Private** — your data stored in your own Supabase instance

---

## 🚀 How To Use

### Save a Memory
```
Save: My exam is on 15th March 2026
Save: I love playing cricket and my favourite player is Virat Kohli
Save: My birthday is on 25th December
```

### Retrieve a Memory
```
When is my exam?
Who is my favourite cricketer?
When is my birthday?
```

### Scan a Photo
Just send any photo directly to the bot!
- Receipts
- Boarding passes
- Handwritten notes
- Any document

---

## 📦 Setup

### Prerequisites
- n8n instance (local or cloud)
- Groq API key (free at console.groq.com)
- Cohere API key (free at cohere.com)
- Supabase project (free at supabase.com)
- Telegram Bot Token (from @BotFather)

### Database Setup
Run this SQL in Supabase:
```sql
CREATE EXTENSION IF NOT EXISTS vector;

CREATE TABLE documents (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  content TEXT,
  metadata JSONB,
  embedding vector(1024)
);

CREATE OR REPLACE FUNCTION match_documents(
  query_embedding vector(1024),
  match_threshold float,
  match_count int
)
RETURNS TABLE (
  id UUID,
  content TEXT,
  metadata JSONB,
  similarity float
)
LANGUAGE sql STABLE AS $$
  SELECT id, content, metadata,
    1 - (embedding <=> query_embedding) AS similarity
  FROM documents
  WHERE 1 - (embedding <=> query_embedding) > match_threshold
  ORDER BY similarity DESC
  LIMIT match_count;
$$;
```

### Import Workflow
1. Download `Nexus-AI-workflow.json`
2. Open n8n → Workflows → Import
3. Add your credentials (Groq, Cohere, Supabase, Telegram)
4. Activate workflow
5. Start chatting!

---

## 📸 Screenshots

> Bot saving a memory and retrieving it with 74.5% confidence

---

## 🔮 Future Plans

- [ ] Multi-user support
- [ ] Memory categories/tags
- [ ] Voice message transcription
- [ ] Web dashboard for memory management
- [ ] Memory export/import

---

## 👨‍💻 Author

**Nemish Sapara**
- GitHub: [@nemishsapara69](https://github.com/nemishsapara69)

---

## ⭐ Star this repo if you found it useful!

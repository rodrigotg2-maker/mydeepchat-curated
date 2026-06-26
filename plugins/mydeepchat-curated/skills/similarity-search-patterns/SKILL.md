---
name: similarity-search-patterns
description: "Implement efficient similarity search with vector databases. Use when building semantic search, implementing nearest neighbor queries, or optimizing retrieval performance. Primary target: PostgreSQL + pgvector for PiskeSoul."
risk: safe
source: community
date_added: "2026-06-26"
---
<!-- FORK ADAPTADO: Agregar mención explícita a PostgreSQL pgvector como target principal; nota sobre resources/implementation-playbook.md ausente · origen: sickn33-antigravity-awesome-skills-plugins-antigravity-awesome-skills-claude-skills-similarity-search-patterns-skill-md · 2026-06-26 -->

# Similarity Search Patterns

Patterns for implementing efficient similarity search in production systems.

## Use this skill when

- Building semantic search systems (búsqueda de productos en PiskeSoul)
- Implementing RAG retrieval for knowledge bases
- Creating recommendation engines
- Optimizing search latency with pgvector
- Scaling to millions of vectors
- Combining semantic and keyword search (hybrid search)

## Do not use this skill when

- The task is unrelated to similarity search patterns
- You need a different domain or tool outside this scope

## Instructions

- Clarify goals, constraints, and required inputs.
- Apply relevant best practices and validate outcomes.
- Provide actionable steps and verification.

## Primary Stack: PostgreSQL + pgvector

For PiskeSoul and projects using PostgreSQL, the recommended approach is **pgvector**:

```sql
-- Enable pgvector extension
CREATE EXTENSION IF NOT EXISTS vector;

-- Add vector column to a table
ALTER TABLE products ADD COLUMN embedding vector(1536);

-- Create HNSW index (fast approximate search)
CREATE INDEX ON products USING hnsw (embedding vector_cosine_ops)
WITH (m = 16, ef_construction = 64);

-- Semantic search query
SELECT id, name, 1 - (embedding <=> $1::vector) AS similarity
FROM products
ORDER BY embedding <=> $1::vector
LIMIT 10;
```

```python
# FastAPI endpoint with pgvector (SQLAlchemy)
from pgvector.sqlalchemy import Vector
from sqlalchemy import Column, text

class Product(Base):
    __tablename__ = "products"
    id = Column(Integer, primary_key=True)
    name = Column(String)
    embedding = Column(Vector(1536))

async def semantic_search(query: str, limit: int = 10) -> list[Product]:
    embedding = await get_embedding(query)  # OpenAI or local model
    results = await db.execute(
        select(Product)
        .order_by(Product.embedding.cosine_distance(embedding))
        .limit(limit)
    )
    return results.scalars().all()
```

## Alternative Vector Stores

| Store    | Best for                                | Notes                          |
|----------|-----------------------------------------|--------------------------------|
| pgvector | PostgreSQL projects, production OLTP    | **Recomendado para PiskeSoul** |
| Qdrant   | Pure vector workloads, complex filters  | Docker-friendly                |
| Pinecone | Managed, no infra, serverless           | Requires API key               |
| Chroma   | Local dev, prototyping                  | Python-native                  |

## Hybrid Search (Semantic + Keyword)

```python
async def hybrid_search(query: str, limit: int = 10) -> list[dict]:
    # Vector (semantic) search
    embedding = await get_embedding(query)
    vector_results = await semantic_search(embedding, limit=limit * 2)

    # Keyword (BM25) search
    keyword_results = await db.execute(
        select(Product)
        .where(Product.name.ilike(f"%{query}%"))
        .limit(limit * 2)
    )

    # Reciprocal Rank Fusion (RRF)
    return reciprocal_rank_fusion(
        [r.id for r in vector_results.scalars()],
        [r.id for r in keyword_results.scalars()],
        k=60
    )[:limit]
```

## Performance Optimization

- **Index type**: Use HNSW for low-latency queries; IVFFlat for memory-constrained environments
- **Dimensions**: 1536 (OpenAI text-embedding-3-small), 768 (sentence-transformers), 3072 (text-embedding-3-large)
- **Batch embeddings**: Generate embeddings in batches of 100-1000 to avoid rate limits
- **Caching**: Cache embeddings for common queries (Redis or simple dict)

## Embedding Generation

```python
import openai

async def get_embedding(text: str, model: str = "text-embedding-3-small") -> list[float]:
    response = await openai.AsyncOpenAI().embeddings.create(
        input=text,
        model=model
    )
    return response.data[0].embedding
```

> **Note**: The `resources/implementation-playbook.md` referenced in the original skill is not available. The patterns above cover the core implementation.

## Limitations

- Use this skill only when the task clearly matches the scope described above.
- Do not treat the output as a substitute for environment-specific validation, testing, or expert review.
- Stop and ask for clarification if required inputs, permissions, safety boundaries, or success criteria are missing.

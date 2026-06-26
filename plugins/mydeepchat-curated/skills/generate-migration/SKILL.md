---
name: generate-migration
description: Generate Alembic database migrations for FastAPI/SQLAlchemy projects. Use when creating migrations, adding/removing columns or tables, adding indexes, or resolving migration conflicts. Complements migraciones-seguras-piskesoul.
---
<!-- FORK ADAPTADO: Django/Sentry commands (sentry django makemigrations, bin/update-migration, SafeDeleteModel, DeletionAction) → Alembic/SQLAlchemy equivalents; testing patterns adaptados a pytest + SQLAlchemy · origen: getsentry-sentry-agents-skills-generate-migration-skill-md · 2026-06-26 -->

# Generate Alembic Database Migrations

## Commands

Generate migrations automatically based on model changes:

```powershell
alembic revision --autogenerate -m "describe_the_change"
```

Generate an empty migration (for data migrations or custom work):

```powershell
alembic revision -m "backfill_column_foo"
```

## After Generating

1. **Review the generated migration** in `alembic/versions/` for correctness
2. **Inspect the SQL** before applying:

   ```powershell
   alembic upgrade head --sql
   ```

3. **Apply the migration locally**:

   ```powershell
   alembic upgrade head
   ```

4. **Check current revision**:

   ```powershell
   alembic current
   alembic history --verbose
   ```

> **Safety**: Always review generated SQL before applying. Alembic does NOT automatically detect data-loss operations as unsafe — manual review is required.

### Don't test the ORM

Don't write tests that only exercise SQLAlchemy's ORM. Standard operations — create/update/delete, cascade deletes, unique-constraint enforcement — are provided by SQLAlchemy and Postgres and are assumed to work. Test _your_ logic (business rules, custom validators, computed properties).

### Do test data migrations and backfills

A migration that **backfills or transforms data** is your logic and must have a test.

```python
# tests/migrations/test_backfill_product_slug.py
import pytest
from alembic import command
from alembic.config import Config
from sqlalchemy import create_engine, text

@pytest.fixture
def alembic_runner(tmp_path, db_url):
    """Run migration up/down cycle for testing."""
    cfg = Config("alembic.ini")
    cfg.set_main_option("sqlalchemy.url", db_url)
    return cfg

def test_backfill_product_slug(alembic_runner, db_session):
    # Seed data BEFORE migration
    db_session.execute(text(
        "INSERT INTO products (id, name) VALUES (1, 'Pisco Reservado'), (2, 'Moscatel')"
    ))
    db_session.commit()

    # Apply the specific migration
    command.upgrade(alembic_runner, "abc123_backfill_product_slug")

    # Assert post-migration state
    rows = db_session.execute(text("SELECT id, slug FROM products ORDER BY id")).fetchall()
    assert rows[0].slug == "pisco-reservado"
    assert rows[1].slug == "moscatel"
```

## Guidelines

### Adding Columns

```python
# In your SQLAlchemy model
class Product(Base):
    __tablename__ = "products"
    id = Column(Integer, primary_key=True)
    name = Column(String, nullable=False)
    # New column:
    slug = Column(String, nullable=True)  # Start nullable when backfilling
```

- Use `server_default` for NOT NULL columns with defaults:

  ```python
  is_active = Column(Boolean, nullable=False, server_default=text("true"))
  ```

- Nullable columns: `nullable=True`
- NOT NULL columns: must have `server_default` set

### Adding Indexes

For large tables, add indexes in a separate migration from the column change:

```python
# Generated migration
from alembic import op
import sqlalchemy as sa

def upgrade():
    # Create index concurrently to avoid table lock (PostgreSQL)
    op.create_index(
        "ix_products_slug",
        "products",
        ["slug"],
        postgresql_concurrently=True
    )

def downgrade():
    op.drop_index("ix_products_slug", table_name="products")
```

> For large tables, `CREATE INDEX CONCURRENTLY` avoids locking. Set `postgresql_concurrently=True` in `op.create_index()`. This requires running the migration outside of a transaction — add `alembic.ini` setting or use a separate migration.

### Deleting Columns

Two-phase process to avoid data loss:

**Phase 1 — Make nullable, remove code references:**

```python
def upgrade():
    op.alter_column("products", "legacy_field", nullable=True)
```

Deploy, then:

**Phase 2 — Drop the column:**

```python
def upgrade():
    op.drop_column("products", "legacy_field")
```

### Removing a Table

Two-phase process:

**Phase 1 — Remove all ORM model references**, add `checkfirst=True` to downgrade:

```python
def upgrade():
    pass  # Just remove the model class; table still exists

def downgrade():
    op.create_table("deprecated_table", ...)
```

**Phase 2 — Drop the table:**

```python
def upgrade():
    op.drop_table("deprecated_table")
```

### Renaming Columns/Tables

Prefer using `Column(name="old_name")` to keep the old DB column name instead of renaming:

```python
# Keep old column name in DB, new Python attribute name
new_name = Column("old_db_column_name", String)
```

If you must rename, do it in PostgreSQL without data loss:

```python
def upgrade():
    op.alter_column("products", "old_name", new_column_name="new_name")
```

## Resolving Merge Conflicts

If two branches both create migrations from the same base, Alembic will detect a conflict. Resolve manually:

```powershell
# List current heads (shows multiple if conflict)
alembic heads

# Create a merge migration
alembic merge -m "merge_branch_a_and_branch_b" <revision_a> <revision_b>
```

The merge migration has two `down_revision` values and an empty body — review it and apply normally.

## Running Tests with Migrations

```powershell
# Run migration-specific tests
pytest tests/migrations/ -v

# Run all tests with a fresh DB
pytest --create-db

# Reuse existing test DB
pytest --reuse-db
```

## Quick Reference

| Task | Command |
|------|---------|
| Auto-generate migration | `alembic revision --autogenerate -m "description"` |
| Empty migration | `alembic revision -m "description"` |
| Preview SQL (dry run) | `alembic upgrade head --sql` |
| Apply migrations | `alembic upgrade head` |
| Show current version | `alembic current` |
| Show history | `alembic history --verbose` |
| Downgrade one step | `alembic downgrade -1` |
| Downgrade to base | `alembic downgrade base` |
| Resolve conflict | `alembic merge <rev_a> <rev_b> -m "merge"` |

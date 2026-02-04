---
title: "Usage Example"
---

# Usage Example: Generating C4 Diagrams from notes-api

This guide shows how to use c4-literate-python to generate C4
diagrams from the annotated notes-api codebase.

## Prerequisites

```bash
# Install c4-literate-python
cd c4-literate-python
pip install -e .

# Verify installation
c4-literate --help
```

## Step 1: Run the Tangler

```bash
# Navigate to your notes-api directory
cd /path/to/notes-api

# Generate Structurizr DSL
c4-literate tangle . -o workspace.dsl
```

**Expected Output:**
```
Tangling Python code from: .
Found 15 code elements
Extracted 23 C4 annotations
Built model with 2 containers, 3 components
Generated Structurizr DSL: workspace.dsl
```

## Step 2: View with Structurizr Lite

```bash
# Using Docker
docker pull structurizr/lite
docker run -p 8080:8080 -v $(pwd):/usr/local/structurizr \
    structurizr/lite

# Open browser
open http://localhost:8080
```

## What Gets Generated

The tangler will create `workspace.dsl` containing:

### Containers
- **API Application** (from app/main.py)
- **Database Layer** (from app/database.py)

### Components
- **Data Validation Layer** (from app/models.py)
- **FastAPI Application Instance**
- **Note Data Model**

### Relationships
- API Application → Database Layer (SQLAlchemy)
- API Application → Data Models (Pydantic)
- API Consumer (Person) → API Application (HTTPS)

## Step 3: Validate Annotations

Check for issues:

```bash
c4-literate validate .
```

**Clean output:**
```
Validating C4 annotations in: .

✅ All annotations valid!
   Containers: 2
   Components: 3
   Relationships: 5
```

## Step 4: Debug Extraction

View extracted annotations:

```bash
# Text format
c4-literate extract . | head -20

# JSON format
c4-literate extract . --format json > annotations.json
```

## Generated DSL Example

The `workspace.dsl` file will look like:

```dsl
workspace {

    model {

        apiConsumer = person "API Consumer"

        apiApplication = container "API Application" {
            technology "Python 3.12, FastAPI 0.104"
            description "REST API providing CRUD operations..."
        }

        databaseLayer = container "Database Layer" {
            technology "SQLAlchemy 2.0, SQLite 3"
            description "ORM-based persistence layer..."
        }

        # Relationships
        apiConsumer -> apiApplication "Creates notes" "HTTPS/JSON"
        apiApplication -> databaseLayer "Manages persistence" "SQLAlchemy"

    }

    views {
        container * "ContainerView" {
            include *
            autoLayout
        }
        
        styles {
            element "Person" {
                shape person
            }
            element "Container" {
                background #438dd5
                color #ffffff
            }
        }
    }
}
```

## Viewing Diagrams

In Structurizr Lite (http://localhost:8080), you'll see:

1. **Container Diagram** - Shows API Application, Database Layer,
                          and API Consumer with relationships
2. **Component Diagram** - Shows internal components within
                          containers

## Troubleshooting

### No annotations found

```bash
# Check that files have @c4- annotations
grep -r "@c4-" app/
```

### Validation errors

```bash
c4-literate validate .
```

Common issues:
- Missing `@c4-technology` on containers
- Missing `@c4-description` on containers
- Malformed relationship syntax

### DSL syntax errors

Open workspace.dsl and check for:
- Balanced quotes
- Balanced braces
- Valid identifiers (no spaces in IDs)

## Next Steps

1. **Customize diagrams** - Edit generated DSL
2. **Add deployment views** - When supported
3. **Integrate with CI/CD** - Auto-generate on commit
4. **Combine with MkDocs** - For complete architecture docs

## Tips

- Run `c4-literate validate` before `tangle`
- Use JSON output for programmatic processing
- Keep annotations concise (they become diagram text)
- Test in Structurizr Lite before committing DSL

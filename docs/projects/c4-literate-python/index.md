---
icon: simple/python
title: "C4 Literate Python"
---

# C4 Literate Python

Generate C4 architecture diagrams from literate Python code.

## Overview

C4 Literate Python is a tool that extracts C4 model annotations from
well-documented Python codebases and generates Structurizr DSL for
creating architecture diagrams.

**Key Features:**
- 📝 Embed architecture documentation directly in Python code
- 🔄 Automatic diagram generation from annotations
- ✅ Validation of C4 annotations
- 🎨 Support for Container and Component diagrams
- 🚀 Zero runtime dependencies (stdlib only)

## Installation

```bash
pip install c4-literate-python
```

Or install from source:

```bash
git clone https://github.com/yourusername/c4-literate-python.git
cd c4-literate-python
pip install -e .
```

## Quick Start

### 1. Annotate Your Python Code

Add C4 annotations to your Python module docstrings:

```python
"""
My API Module

C4 MODEL MAPPING
----------------
@c4-container: API Application
@c4-technology: Python 3.12, FastAPI 0.104
@c4-description: REST API providing CRUD operations

@c4-uses: Database Layer - "Persists data" - "SQLAlchemy"
"""

from fastapi import FastAPI

app = FastAPI()
```

### 2. Generate Structurizr DSL

```bash
c4-literate tangle ./my_project -o workspace.dsl
```

### 3. View Diagrams

Use Structurizr Lite to view your generated diagrams:

```bash
docker run -p 8080:8080 -v $(pwd):/usr/local/structurizr \
    structurizr/lite
```

Open http://localhost:8080 to see your architecture diagrams!

## Annotation Schema

### Container Declaration

```python
"""
@c4-container: API Application
@c4-technology: Python 3.12, FastAPI
@c4-description: Main REST API server
@c4-responsibilities:
    - Handle HTTP requests
    - Validate input data
    - Return JSON responses
"""
```

### Component Declaration

```python
"""
@c4-component: Authentication Service
@c4-technology: JWT, bcrypt
@c4-description: Handles user authentication
"""
```

**Important:** Components are automatically assigned to containers
based on filepath. If a component is declared in the same file as a
container, it will be nested inside that container. Otherwise, it
will be assigned to the first available container. See
`docs/ADR-001-component-assignment.md` for details and alternatives.

### Relationships

```python
"""
@c4-uses: Database Layer - "Reads/writes data" - "SQLAlchemy"
@c4-used-by: API Gateway - "Routes requests" - "HTTP"
@c4-used-by-person: End User - "Makes API calls" - "HTTPS"
"""
```

### Operations (Function Level)

```python
def create_user(user_data):
    """
    Create a new user.
    
    @c4-operation: create_user
    @c4-calls: Database Layer - "Persists user record"
    """
    pass
```

## CLI Commands

### Tangle (Generate DSL)

```bash
# From directory
c4-literate tangle ./my_project

# From single file
c4-literate tangle ./my_module.py

# Custom output file
c4-literate tangle ./my_project -o my_workspace.dsl
```

### Validate

Check annotations without generating:

```bash
c4-literate validate ./my_project
```

### Extract (Debug)

View extracted annotations:

```bash
# Text format
c4-literate extract ./my_project

# JSON format
c4-literate extract ./my_project --format json
```

## Complete Example

See the `examples/` directory for a complete annotated codebase.

## Schema Reference

Full annotation schema is documented in:
- **Source of Truth:** `c4-annotations-schema.yaml` (edit this file)
- **User Documentation:** `SCHEMA.md` (generated from YAML)

**Regenerating Documentation:**

After editing the YAML schema, regenerate the Markdown docs:

```bash
make docs
# or
python3 generate_schema_docs.py
```

Key annotations:
- `@c4-container` - Deployable unit (web app, database, etc.)
- `@c4-component` - Logical grouping within container
- `@c4-technology` - Tech stack used
- `@c4-description` - Brief description
- `@c4-uses` - Dependency relationship
- `@c4-calls` - Operation-level call
- `@c4-operation` - Significant function/method

## Design Philosophy

### Literate Programming

C4 Literate Python follows literate programming principles:
- Architecture documentation lives with code
- Annotations are human-readable
- No separate diagram files to maintain
- Documentation evolves with code

### Minimal Dependencies

Core tool uses only Python standard library:
- `ast` for parsing Python
- `re` for annotation extraction
- No external dependencies required

### PEP8 Compliance

Handles multi-line annotations wrapped at 79 characters:

```python
@c4-description: This is a very long description that wraps
                 across multiple lines for PEP8 compliance
```

The tangler automatically rejoins these into single-line
descriptions for clean DSL generation.

## Workflow Integration

### Git Pre-commit Hook

Generate diagrams before committing:

```bash
#!/bin/bash
# .git/hooks/pre-commit
c4-literate tangle ./src -o docs/architecture/workspace.dsl
git add docs/architecture/workspace.dsl
```

### CI/CD Pipeline

```yaml
# .github/workflows/docs.yml
- name: Generate Architecture Diagrams
  run: |
    pip install c4-literate-python
    c4-literate tangle ./src -o workspace.dsl
```

## Future Roadmap

- [ ] System Context diagram support
- [ ] Deployment diagram support
- [ ] Export to other formats (PlantUML, Mermaid)
- [ ] MkDocs integration (weaver)
- [ ] VS Code extension
- [ ] Real-time validation in IDE

## Contributing

Contributions welcome! See CONTRIBUTING.md.

## License

MIT License - see LICENSE file.

## Credits

- **C4 Model** by Simon Brown: https://c4model.com/
- **Structurizr** by Simon Brown: https://structurizr.com/
- Inspired by literate programming principles from Donald Knuth

## Links

- Documentation: https://github.com/yourusername/c4-literate-python
- Issues: https://github.com/yourusername/c4-literate-python/issues
- C4 Model: https://c4model.com/
- Structurizr DSL: https://github.com/structurizr/dsl

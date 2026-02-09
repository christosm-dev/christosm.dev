---
icon: simple/python
title: "C4 Literate Python"
---

# C4 Literate Python

> Generate C4 architecture diagrams from literate Python code. **[View source on GitHub](https://github.com/christosm-dev/c4-literate-python)**

## Project Overview

C4 Literate Python extracts C4 model annotations from well-documented Python codebases and generates Structurizr DSL for creating architecture diagrams. Following literate programming principles, architecture documentation lives alongside the code it describes - no separate diagram files to maintain and no drift between code and documentation.

## Technology Stack

| Technology | Role |
|------------|------|
| Python 3.12 | Core language (stdlib only - zero runtime dependencies) |
| `ast` module | Python source code parsing |
| `re` module | Annotation extraction from docstrings |
| Structurizr DSL | Output format for C4 architecture diagrams |
| YAML / JSON | Annotation schema definition |

## Key Features

- Embed architecture documentation directly in Python docstrings
- Automatic Structurizr DSL generation from annotations
- Validation of C4 annotations before generation
- Support for Container and Component diagrams
- Zero runtime dependencies (Python standard library only)
- PEP8-compliant multi-line annotation handling

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
    @c4-operation: create_user
    @c4-calls: Database Layer - "Persists user record"
    """
    pass
```

## Schema Reference

Full annotation schema is documented in:
- **Source of Truth:** `c4-annotations-schema.yaml` (edit this file)
- **User Documentation:** `SCHEMA.md` (generated from YAML)

Key annotations: `@c4-container`, `@c4-component`, `@c4-technology`, `@c4-description`, `@c4-uses`, `@c4-calls`, `@c4-operation`

## Design Philosophy

### Literate Programming

- Architecture documentation lives with code
- Annotations are human-readable
- No separate diagram files to maintain
- Documentation evolves with code

### Minimal Dependencies

Core tool uses only Python standard library (`ast` for parsing, `re` for extraction). No external dependencies required.

### PEP8 Compliance

Handles multi-line annotations wrapped at 79 characters. The tangler automatically rejoins these into single-line descriptions for clean DSL generation.

## Codebase Overview

```
c4-literate-python/
├── src/
│   └── c4_literate/             # Main package: parser, tangler, validator, CLI
├── tests/                       # Unit tests for annotation extraction and DSL generation
├── docs/                        # ADRs and design documentation
├── c4-annotations-schema.yaml   # Source of truth for annotation schema
├── c4-annotations-schema.json   # JSON version of annotation schema
├── SCHEMA.md                    # Generated schema documentation
├── USAGE_EXAMPLE.md             # Annotated codebase walkthrough
├── generate_schema_docs.py      # Script to regenerate SCHEMA.md from YAML
├── workspace.dsl                # Example Structurizr DSL output
├── Makefile                     # Build targets: test, lint, docs
├── pyproject.toml               # Package configuration and dependencies
└── README.md                    # This file
```

## Future Work

- [ ] System Context diagram support
- [ ] Deployment diagram support
- [ ] Export to other formats (PlantUML, Mermaid)
- [ ] MkDocs integration (weaver)
- [ ] VS Code extension for real-time validation
- [ ] Multi-workspace support for monorepos

## Resources

- [C4 Model](https://c4model.com/) by Simon Brown
- [Structurizr DSL](https://github.com/structurizr/dsl)
- [Contributing Guide](CONTRIBUTING.md)
- [License](LICENSE) (MIT)

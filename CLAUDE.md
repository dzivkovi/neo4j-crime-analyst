# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Neo4j-based surveillance analytics documentation and reference repository for law enforcement investigations. It provides comprehensive documentation, Cypher query examples, and Neo4j MCP (Model Context Protocol) integration for analyzing communication session data using the POLE (Person, Object, Location, Event) schema.

**Note**: This repository contains documentation, query examples, and configuration.

## Design Principles
- Follow **Perfection is achieved, not when there is nothing more to add, but when there is nothing left to take away** advice by Antoine de Saint-Exupéry.
- **Optimize for User Interaction Pattern**: Before structuring anything, ask "How will users interact with this?" Browsing/scanning tasks favor flat layouts that minimize cognitive load; direct navigation tasks can use logical hierarchy.
- **Defensive Programming**: Test everything, validate all assumptions, never rush implementation. Every query must be tested with MCP server before documentation. Expect failures and plan for them.
- **Documentation-First**: Always check latest official docs before implementing. Technology changes faster than training data or existing code.
- **Use `rg` first**: ALWAYS use `rg` (ripgrep) for searching before trying `grep` or `find` combinations. It's faster and better.
- **cypher-shell NEVER supports `-c` flag**: ALWAYS use `echo "QUERY" | docker exec -i $NEO_NAME cypher-shell -u neo4j -p Sup3rSecur3!` pattern.

## Task Planning
- **Complex tasks (3+ steps)**: Use TodoWrite to track progress
- **Simple tasks**: Execute directly without todo overhead

## Essential Commands

### Neo4j Docker Operations (When Using Docker)
```bash
# Set container name
export NEO_NAME="neo4j-default"

# Quick connection test
docker exec -it ${NEO_NAME} cypher-shell -u neo4j -p Sup3rSecur3!

# Run query files from queries/ directory
docker exec -i ${NEO_NAME} cypher-shell -u neo4j -p Sup3rSecur3! < queries/vector-search-verification.cypher
docker exec -i ${NEO_NAME} cypher-shell -u neo4j -p Sup3rSecur3! < queries/eval-suite.cypher
```

### ⚠️ **CRITICAL: cypher-shell Command Patterns**
```bash
# ✅ CORRECT: Single query via echo pipe
echo "MATCH (n) RETURN count(n);" | docker exec -i $NEO_NAME cypher-shell -u neo4j -p Sup3rSecur3!

# ✅ CORRECT: Script file input
docker exec -i $NEO_NAME cypher-shell -u neo4j -p Sup3rSecur3! < script.cypher

# ✅ CORRECT: With parameters
echo "RETURN \$param;" | docker exec -i $NEO_NAME cypher-shell -u neo4j -p Sup3rSecur3! --param "param => 'value'"

# ❌ WRONG: cypher-shell does NOT support -c flag
# docker exec -i $NEO_NAME cypher-shell -u neo4j -p Sup3rSecur3! -c "MATCH (n) RETURN count(n);"
```

### 🚀 **PREFERRED: Neo4j MCP Server (No Docker/Passwords Needed!)**

The MCP Neo4j server supports full parameter passing, including sensitive data like API keys. This eliminates hardcoded passwords and docker commands.

#### MCP Setup
```bash
# Check if MCP Neo4j is configured
claude mcp list | grep neo4j

# If not configured, add to Claude Code settings
```

#### MCP Usage Pattern
```python
# ✅ CORRECT: Pass parameters including API keys
result = mcp__neo4j__read_neo4j_cypher(
    query="""
    WITH genai.vector.encode($searchText, 'OpenAI', {
        token: $apiKey,
        model: 'text-embedding-3-small',
        dimensions: 1536
    }) as embedding
    CALL db.index.vector.queryNodes('ContentVectorIndex', 20, embedding)
    YIELD node, score
    WHERE score > 0.7
    RETURN node, score
    """,
    params={
        "searchText": "vehicle theft",
        "apiKey": os.environ.get("OPENAI_API_KEY")
    }
)
```

#### Key MCP Advantages
1. **No hardcoded passwords** - Credentials in Claude config
2. **Secure parameter passing** - API keys as parameters, not in query strings
3. **No docker exec overhead** - Direct database connection
4. **Automatic sanitization** - Neo4j prevents injection attacks

#### Parameter Rules
- Query uses `$paramName` syntax
- `params` dict keys must match query parameter names exactly
- All Neo4j data types supported (strings, numbers, lists, etc.)

#### Common Patterns
```python
# Simple parameter
params={"userId": 123}

# Multiple parameters
params={"name": "John", "age": 30}

# API key for GenAI
params={"apiKey": os.environ.get("OPENAI_API_KEY")}

# Complex objects
params={"embedding": [0.1, 0.2, 0.3...]}
```

### Container Management (Conceptual Reference)
```bash
# Check container status
docker ps | grep ${NEO_NAME}

# View Neo4j logs for troubleshooting
docker logs ${NEO_NAME}

# Container restart (data loss - use for fresh start only)
docker stop ${NEO_NAME} && docker rm ${NEO_NAME}
```

### Available Query Examples
The `queries/` directory contains ready-to-use Cypher queries:
- `eval-suite.cypher` - Business requirements validation queries
- `investigative.cypher` - Law enforcement use case queries
- `network-visualizations.cypher` - Communication network analysis
- `vector-search-verification.cypher` - Semantic search examples
- `graph-visualization-examples.cypher` - Graph visualization patterns
- `data-exploration.cypher` - Basic data exploration queries
- `practical-investigation-queries.cypher` - Real-world investigation patterns

## Neo4j GenAI Python Project

### Project Context
This project leverages Neo4j v5's latest Generative AI features including vector search, embeddings, and GraphRAG patterns.

### Key Technologies
- Neo4j v5.x with GDS (Graph Data Science) library
- Python async driver for Neo4j
- Vector embeddings and similarity search
- GraphRAG implementation

### Documentation References

#### Project Documentation
- [MCP Integration Guide](docs/mcp.md) - Natural language access to Neo4j
- [Graph Schema](docs/neo4j-graph-schema.mmd) - Visual entity-relationship diagram
- [Backup & Restore](docs/neo4j-backup-and-restore.md) - Database backup procedures

#### Core Libraries
- [Python Driver](https://neo4j.com/docs/python-manual/current/)
- [Python Driver GitHub](https://github.com/neo4j/neo4j-python-driver)
- [GDS Python Client](https://neo4j.com/docs/graph-data-science-client/current/)
- [GDS Client API](https://neo4j.com/docs/graph-data-science-client/current/api/)
- [GDS GitHub](https://github.com/neo4j/graph-data-science)

#### GenAI & Vector Features
- [Cypher Vector Functions](https://neo4j.com/docs/cypher-manual/current/functions/vector/)
- [GenAI Integrations](https://neo4j.com/docs/cypher-manual/current/genai-integrations/)
- [Vector Indexes](https://neo4j.com/docs/cypher-manual/current/indexes/semantic-indexes/vector-indexes/)
- [Embeddings Tutorial](https://neo4j.com/docs/genai/tutorials/embeddings-vector-indexes/)

#### GraphRAG
- [Neo4j GraphRAG Python](https://neo4j.com/docs/neo4j-graphrag-python/current/)
- [GraphRAG Python API](https://neo4j.com/docs/neo4j-graphrag-python/current/api.html)

#### General Resources
- [Cypher Manual](https://neo4j.com/docs/cypher-manual/current/introduction/cypher-neo4j/)
- [Graph Data Science Docs](https://neo4j.com/docs/graph-data-science/current/)

#### Fetching Latest Documentation with Context7
When users ask about Neo4j features, especially those released after the training cutoff, use the Context7 MCP server to fetch current documentation:

```python
# First, resolve the library ID
library_info = mcp__context7__resolve_library_id(
    libraryName="neo4j"
)

# Then fetch specific documentation
docs = mcp__context7__get_library_docs(
    context7CompatibleLibraryID="/neo4j/neo4j",  # or the ID from resolve
    topic="vector indexes",  # optional: focus on specific topic
    tokens=10000  # optional: amount of documentation to retrieve
)
```

Use Context7 for:
- Vector search capabilities and syntax
- Latest Cypher functions and procedures
- GenAI integration features
- New index types or constraints
- GraphRAG patterns and best practices

### Common Patterns
- Use async driver for all new features
- Vector dimensions: 1536 (current OpenAI text-embedding-3-small)
- Index naming: `{node_label}_embedding_index`

## Documentation-First Development
**IMPORTANT**: Technology evolves rapidly. When implementing any feature:
1. ALWAYS check official documentation first (links provided above)
2. Compare with existing code patterns second
3. Explain any differences between docs and local implementation
4. Trust latest documentation over:
   - My training data (likely outdated)
   - Existing codebase (may be legacy)
   - Examples in this file (may be from earlier versions)

## Architecture & Key Design Patterns

### Graph Schema (Session-Centric POLE Model)
The system uses a session-centric approach where `Session` nodes are the central entities, connected to:
- `Person` nodes via phones/emails that `PARTICIPATED_IN` sessions
- `Phone`, `Email`, `Device` nodes representing communication endpoints
- `Content` nodes containing message text with embeddings

Key relationships follow law enforcement ontologies:
- `(:Phone)-[:PARTICIPATED_IN]->(:Session)` - Phone participation in sessions
- `(:Person)-[:USES]->(:Phone|Email)` - Person-to-identifier mapping
- `(:Session)-[:HAS_CONTENT]->(:Content)` - Session content linkage

### Data Processing Pipeline
1. **Schema Creation**: Establishes constraints, indexes, and vector index (1536 dimensions)
2. **Data Import**: Processes NDJSON files, creating nodes/relationships while preserving raw data


### Query Patterns
The system supports extensive query types as demonstrated in the `queries/` directory:
- Semantic search using vector embeddings
- Full-text search on content
- Time-based filtering
- Entity association analysis
- Communication pattern detection
- Multi-hop relationship traversal

### Important Implementation Details
- **Database credentials**: `neo4j` / `Sup3rSecur3!` (intentionally hardcoded for local POC)

- **Data format**: NDJSON with session/involvement/product structure
- **Content extraction**: Handles data URIs for text content

## ⚠️ **CRITICAL: GitHub Workflow Rules**

### Branch Naming Convention (ENFORCED BY GITGUARD)
- **Pattern**: `^(feat|fix|docs|chore)/[0-9]+-[description]`
- **Rule**: Branch number MUST reference an existing GitHub issue
- **Examples**: 
  - ✅ `fix/27-post-merge-cleanup` (Issue #27 exists)
  - ❌ `fix/28-new-feature` (if Issue #28 doesn't exist)

### Issue-Branch-PR Workflow
1. **MUST verify**: Does the issue exist before creating branch?
2. **Continuation work**: Use original issue number (e.g., `fix/27-cleanup` for Issue #27 follow-up)
3. **New features**: Create issue first, then branch with same number
4. **NEVER assume**: Don't use "next number" without verifying issue exists

### Before Creating Any Branch:
```bash
# Check if issue exists first
gh issue view 28  # Verify issue exists before creating fix/28-*
```

**GitGuard Failure = Stop and Fix**: Never bypass security checks, always fix the root cause.

## Important Instructions

### 🚫 **GIT COMMIT RULE: NEVER use `git add` or `git commit` without explicit user request**
- **ONLY commit when user explicitly asks**: "commit this", "git add", "create a commit", etc.
- **DEFAULT behavior**: Make changes but DO NOT stage or commit them
- **User retains control**: Let the user decide what and when to commit

### Other Critical Instructions
- **NEVER sign commits or changes as Claude/AI** - use standard git authorship only
- NEVER use emojis in any files or documentation unless explicitly requested by the User
- Only create documentation files when explicitly requested
- Always prefer editing existing files to creating new ones
- NEVER write Cypher queries into files without first validating them using the MCP Neo4j server
- ALWAYS test Cypher queries with MCP Neo4j server before documenting them

### GDS (Graph Data Science) Algorithm Rules
When using GDS algorithms for investigations:

1. **ALWAYS check schema first** - Never assume relationship types exist
   ```cypher
   MATCH (n)-[r]-() RETURN DISTINCT labels(n), type(r), count(r)
   ```

2. **Use Cypher projection for indirect relationships** - Person nodes connect via Phone/Session
   ```cypher
   CALL gds.graph.project.cypher(
     'person-communication',
     'MATCH (p:Person) RETURN id(p) AS id',
     'MATCH (p1:Person)-[:USES]->(:Phone)-[:PARTICIPATED_IN]->(:Session)<-[:PARTICIPATED_IN]-(:Phone)<-[:USES]-(p2:Person)
      WHERE p1 <> p2
      RETURN DISTINCT id(p1) AS source, id(p2) AS target'
   )
   ```

3. **Common GDS algorithms for investigations**:
   - **Betweenness Centrality**: Find information brokers/bridges
   - **PageRank**: Identify influential nodes
   - **Community Detection (Louvain)**: Find criminal groups
   - **Shortest Path**: Trace connections between suspects

4. **Clean up projections** - Always drop after use
   ```cypher
   CALL gds.graph.drop('graph-name', false)
   ```

## Defensive Programming Requirements
**MANDATORY validation for EVERY code change**:

1. **Before editing**: Search for ALL occurrences (use `rg`, not grep/find)
2. **After editing**: Test what you modified (run scripts, execute queries, etc.)
3. **Before committing**: Verify all changes work and nothing broke
4. **Always report**: Tell user exactly what you validated

**NEVER claim success without proving it!**
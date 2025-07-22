# Neo4j Crime Investigator's Shell

A minimalist reference repository for crime investigators to query Neo4j databases using natural language through Claude Code. Built on the POLE (Person, Object, Location, Event) schema for law enforcement investigations.

[![Project Board](https://img.shields.io/badge/Project%20Board-Kanban-blue)](https://github.com/users/dzivkovi/projects/1)

## Purpose

This repository provides:
- Comprehensive Cypher query examples for investigative patterns
- Neo4j MCP integration for natural language database queries
- Documentation on graph-based crime analysis techniques
- Reference materials on POLE data standards and intelligence-led policing

## Primary Interface: Slash Commands

Start Claude Code (`claude` in your terminal) to access these built-in commands:

### `/investigate` - Query Neo4j Database
Use natural language to query the connected Neo4j instance:
```
/investigate "Who did John Smith call on March 15th?"
/investigate "Find all locations where suspicious activity was reported"
/investigate "Show communication patterns between Alice and Bob in the last month"
/investigate "Search for messages mentioning 'Miami' or 'travel plans'"
```

### `/note` - Document Findings
Save investigation results to the `analysis/` directory:
```
/note  # Saves the last Claude response to analysis/YYYY-MM-DD/HH-topic.md
/note "Key finding about money laundering network"  # Custom topic name
```

The `/note` command automatically:
- Creates dated folders (analysis/2025-01-21/)
- Names files with timestamp and topic (14-money-laundering-network.md)
- Preserves the full investigation context

## Neo4j Connection Methods

### 1. MCP Server (Recommended)
Configure Neo4j MCP and Context7 documentation server in Claude Code settings.
See [MCP Setup Guide](docs/mcp.md) for configuration details.

### 2. Docker Desktop CLI (Fallback)
When MCP fails, use Docker commands:
```bash
export CASENAME=gantry  # Default case name
export NEO_NAME="neo4j-${CASENAME}"
docker exec -i ${NEO_NAME} cypher-shell -u neo4j -p Sup3rSecur3! < queries/investigative.cypher
```

## Query Examples

The `queries/` directory contains battle-tested Cypher patterns for:

- **Entity Resolution** - Finding all identifiers used by a person
- **Communication Analysis** - Mapping contact networks and patterns  
- **Content Search** - Full-text and semantic search across messages
- **Temporal Analysis** - Time-based filtering and session patterns
- **Network Traversal** - Multi-hop relationship exploration

Key files:
- `investigative.cypher` - Core law enforcement queries
- `network-visualizations.cypher` - Communication network graphs
- `vector-search-verification.cypher` - Semantic search with embeddings

## Documentation

### Investigation Techniques
- [Neo4j for Criminal Investigations](docs/ai-docs/Neo4j_for_Criminal_Investigations.md)
- [Social Network Analysis](docs/ai-docs/Social_network_graphs_in_Data_Science.md)

### Reference Standards
- [POLE Data Standards](docs/references/National%20Police%20Chiefs%20Council%20-%20POLE%20Data%20Standards%20Dictionary.md)
- [Intelligence-Led Policing Guide](docs/references/OSCE%20Guidebook%20-%20Intelligence-Led%20Policing.md)
- [Fraud Detection Patterns](docs/references/Neo4j_WP-Fraud-Detection-with-Graph-Databases.md)

### Technical Guides
- [MCP Integration](docs/mcp.md) - Natural language Neo4j access
- [Graph Schema](docs/neo4j-graph-schema.mmd) - POLE entity relationships
- [Backup & Restore](docs/neo4j-backup-and-restore.md) - Database preservation

## Architecture

### Graph Schema
Session-centric POLE model where `Session` nodes connect to:
- `Person` nodes via phones/emails that `PARTICIPATED_IN` sessions
- `Phone`, `Email`, `Device` nodes as communication endpoints
- `Content` nodes containing message text with embeddings

### Key Features
- **Semantic Search** - OpenAI embeddings (1536 dims) with vector indexes
- **GenAI Integration** - Built-in Neo4j functions for embedding generation
- **GraphRAG Ready** - Supports AI-powered relationship analysis
- **Immutable Raw Data** - Preserves evidence chain and provenance

## For AI Assistants

See `CLAUDE.md` for:
- Neo4j MCP usage patterns
- Cypher query best practices
- Docker command reference
- Latest documentation links via Context7 MCP
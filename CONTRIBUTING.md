# Contributing to Neo4j Crime Investigator's Shell

Thank you for contributing to this investigative reference repository! This guide outlines how to contribute Cypher queries, documentation, and natural language query patterns for Claude Code.

## Focus Areas

Contributions typically involve:
1. **Cypher query patterns** - Adding investigative query examples to `queries/`
2. **Natural language patterns** - Improving investigative query examples
3. **Documentation** - Enhancing guides on graph-based crime analysis
4. **Reference materials** - Adding POLE model or intelligence-led policing resources

## Development Environment

### Prerequisites
- Claude Code with Neo4j MCP configured
- Docker Desktop (for fallback testing)
- Access to a Neo4j instance with POLE schema

### Neo4j MCP Setup
Configure both Neo4j and Context7 servers in Claude Code settings:
```json
{
  "mcpServers": {
    "neo4j": {
      "command": "uvx",
      "args": ["mcp-neo4j-cypher@0.2.2", "--transport", "stdio"]
    },
    "context7": {
      "url": "https://mcp.context7.com/mcp"
    }
  }
}
```

## Cypher Query Standards

### Query Development Workflow
1. **Test with MCP first** - Validate all queries using `mcp__neo4j__read_neo4j_cypher`
2. **Document parameters** - Use `$param` syntax, never concatenate strings
3. **Include comments** - Explain investigative purpose and expected results
4. **Follow POLE conventions** - Use standard node labels (Person, Phone, Session, etc.)

### Query Format Example
```cypher
// EVAL-001: Find all phones used by a specific person
// Purpose: Entity resolution for multi-identifier subjects
// Expected: Returns phone numbers with usage patterns
MATCH (p:Person {name: $personName})-[:USES]->(phone:Phone)
OPTIONAL MATCH (phone)-[part:PARTICIPATED_IN]->(s:Session)
RETURN phone.number AS phoneNumber,
       count(DISTINCT s) AS sessionCount,
       min(s.startTime) AS firstSeen,
       max(s.startTime) AS lastSeen
ORDER BY sessionCount DESC
```

### Testing Queries
Before adding to `queries/` directory:
```python
# Test via MCP
result = mcp__neo4j__read_neo4j_cypher(
    query=your_query,
    params={"personName": "John Smith"}
)
# Verify results match investigative expectations
```

## Natural Language Query Patterns

### Contributing Query Examples
1. Add examples to documentation showing natural language investigation patterns
2. Demonstrate how Claude Code interprets investigative requests
3. Include variety of investigation scenarios
4. Test patterns with real data

### Example Pattern Documentation
```markdown
## Investigation Examples

These natural language patterns demonstrate how investigators can query Neo4j:

- "Find all phones used by John Smith"
- "Show communication between Alice and Bob"
- "Search for drug-related conversations"

Claude Code interprets these requests and executes appropriate Cypher queries.
```

## Documentation Standards

### Adding Investigation Guides
- Place in `docs/ai-docs/` for technique documentation
- Use `docs/references/` for official standards/papers
- Include practical examples from real investigations
- Link to relevant Cypher queries in `queries/`

### Writing Style
- Clear, concise language for field investigators
- Avoid technical jargon where possible
- Include visual diagrams when helpful
- Provide step-by-step investigation workflows

## Pull Request Process

### Branch Naming
Follow GitGuard requirements:
```bash
# Check issue exists first
gh issue view 123

# Create branch with issue number
git checkout -b feat/123-new-query-pattern
# or: fix/456-query-bug
# or: docs/789-investigation-guide
```

### PR Checklist
- [ ] All Cypher queries tested with MCP
- [ ] Documentation updated if needed
- [ ] Examples demonstrate investigative value
- [ ] No hardcoded values (use parameters)
- [ ] Follows POLE schema conventions

### Commit Messages
```
feat: add money laundering detection queries
fix: correct person-phone relationship direction  
docs: add guide for analyzing burner phones
```

## Testing Philosophy

### Evaluation-First for Queries
1. **Define success criteria** - What should the query find?
2. **Create test scenarios** - Use representative data
3. **Validate results** - Ensure investigative accuracy
4. **Document edge cases** - Empty results, large datasets

### Query Validation
```cypher
// Test data setup (if needed)
CREATE (p:Person {name: "Test Subject"})
CREATE (ph:Phone {number: "+1234567890"})
CREATE (p)-[:USES]->(ph)

// Run query
[Your query here]

// Cleanup
MATCH (p:Person {name: "Test Subject"}) DETACH DELETE p
```

## Best Practices

### For Investigators
- Queries should answer real investigative questions
- Results should be actionable for law enforcement
- Consider performance on large datasets
- Include time-based filtering where relevant

### For Developers  
- Keep queries readable and maintainable
- Use descriptive variable names
- Avoid complex nested patterns when simpler works
- Comment any non-obvious graph traversals

---

Remember: This is a tool for investigators. Every contribution should make their work easier and more effective.
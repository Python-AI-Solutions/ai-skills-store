---
name: nl-to-cypher
description: Use this skill when converting natural language queries into Cypher queries for Neo4j databases. Invoke when the user asks about querying graph databases, needs help generating Cypher from natural language, wants to optimize graph queries, or is working on natural language query interfaces. Also use for debugging Cypher queries, optimizing query performance, or understanding graph query patterns.
allowed-tools: [Read, Grep, Bash]
---

# Natural Language to Cypher Expert

You are an expert in converting **natural language queries** into **Cypher queries** for Neo4j graph databases, with specialized knowledge of neuroscience data patterns.

## Core Expertise

### Cypher Query Language Mastery
- **Pattern matching**: Node and relationship patterns
- **Filtering**: WHERE clauses, property filters, logical operators
- **Traversal**: Path expressions, variable-length relationships
- **Aggregation**: COUNT, COLLECT, SUM, AVG, MAX, MIN
- **Sorting and limiting**: ORDER BY, SKIP, LIMIT
- **Advanced features**: OPTIONAL MATCH, UNION, subqueries
- **Performance**: Index usage, query optimization

### Natural Language Understanding
- **Intent classification**: Metadata, structure, content, relationship queries
- **Entity extraction**: Node types, properties, constraints
- **Constraint parsing**: Comparison operators, ranges, patterns
- **Synonym mapping**: User terms → graph schema terms
- **Context handling**: Implicit vs explicit queries

### Query Safety & Optimization
- **Query validation**: Syntax checking, injection prevention
- **Performance**: Proper index usage, efficient patterns
- **Safety limits**: Default LIMIT clauses, timeout settings
- **Error handling**: Graceful failures, helpful error messages

---

## Query Generation Process

### Step 1: Parse Natural Language
Extract key components from the user's question:

**Components to identify**:
1. **Query Type**: What/Which/How many/Show/Find/List/Count
2. **Target Entities**: Node types (e.g., "electrodes", "devices", "experimenters")
3. **Properties**: Attributes mentioned (e.g., "sampling rate", "location", "name")
4. **Constraints**: Filters (e.g., ">30kHz", "in visual cortex", "used by")
5. **Relationships**: Connections (e.g., "recorded with", "used by", "part of")
6. **Aggregation**: Count, average, sum, etc.
7. **Output Format**: What to return

**Example parsing**:
```
Query: "What devices were used by experimenters from Stanford?"

Parsed:
- Type: List/What (retrieval)
- Target: Devices
- Relationship: used_by → Experimenters
- Constraint: affiliation = "Stanford"
- Return: Device names/properties
```

---

### Step 2: Map to Graph Schema
Translate natural language terms to graph schema elements:

**Common mappings for neuroscience data**:
```
Natural Language          → Graph Schema
------------------        → ---------------
"electrodes"              → (:Electrode)
"devices" / "equipment"   → (:Device)
"researchers"             → (:Experimenter)
"experiments"             → (:NWBFile)
"recordings"              → (:ElectricalSeries) or (:TimeSeries)
"trials"                  → (:Trial)
"brain regions"           → location property or (:BrainRegion)
"sampling rate"           → sampling_rate property
"used by"                 → -[:USES_DEVICE]->
"recorded with"           → -[:RECORDED_WITH]->
"part of"                 → -[:CONTAINS]-> or -[:PART_OF]->
```

---

### Step 3: Generate Cypher Query

#### Basic Query Templates

**1. Simple Property Retrieval**
```
NL: "What is the sampling rate?"

Cypher:
MATCH (ts:TimeSeries)
RETURN ts.sampling_rate AS sampling_rate
LIMIT 100
```

**2. Filtered Retrieval**
```
NL: "Show electrodes with impedance greater than 1 MegaOhm"

Cypher:
MATCH (e:Electrode)
WHERE e.impedance > 1000000
RETURN e.id, e.impedance, e.location
ORDER BY e.impedance DESC
LIMIT 100
```

**3. Relationship Traversal**
```
NL: "Which devices were used in this experiment?"

Cypher:
MATCH (nwb:NWBFile)-[:USES_DEVICE]->(d:Device)
RETURN d.name, d.description, d.manufacturer
```

**4. Multi-Hop Traversal**
```
NL: "What brain regions were recorded using Device X?"

Cypher:
MATCH (d:Device {name: 'Device X'})<-[:USES_DEVICE]-(eg:ElectrodeGroup)<-[:PART_OF]-(e:Electrode)
RETURN DISTINCT e.location AS brain_region
```

**5. Aggregation**
```
NL: "How many trials are in this experiment?"

Cypher:
MATCH (t:Trial)
RETURN COUNT(t) AS trial_count
```

**6. Complex Filtering with Multiple Constraints**
```
NL: "Find electrodes in visual cortex with impedance between 0.5 and 2 MegaOhm"

Cypher:
MATCH (e:Electrode)
WHERE e.location CONTAINS 'visual cortex'
  AND e.impedance >= 500000
  AND e.impedance <= 2000000
RETURN e.id, e.location, e.impedance, e.x, e.y, e.z
ORDER BY e.impedance ASC
LIMIT 100
```

**7. Organizational Queries**
```
NL: "Which institutions were involved in this research?"

Cypher:
MATCH (nwb:NWBFile)-[:AFFILIATED_WITH]->(inst:Institution)
RETURN inst.name, inst.location
```

**8. Equipment and Experimenter Queries**
```
NL: "Who were the experimenters and what devices did they use?"

Cypher:
MATCH (nwb:NWBFile)-[:HAS_EXPERIMENTER]->(exp:Experimenter)
MATCH (nwb)-[:USES_DEVICE]->(d:Device)
RETURN exp.name AS experimenter, COLLECT(d.name) AS devices
```

---

### Step 4: Add Safety & Optimization

#### Always Include:
1. **LIMIT clause** (default: 100) unless aggregating
2. **Proper indexing hints** when filtering by ID or common properties
3. **DISTINCT** when preventing duplicates
4. **ORDER BY** for consistent results
5. **Property existence checks** when needed

#### Performance Patterns:
```cypher
// Good: Use indexed properties in WHERE
MATCH (e:Electrode)
WHERE e.id = 123
RETURN e

// Good: Specific relationship types
MATCH (d:Device)-[:USES_DEVICE]->(eg:ElectrodeGroup)

// Good: Limit early in the pattern
MATCH (t:Trial)
WHERE t.outcome = 'success'
WITH t LIMIT 100
MATCH (t)-[:HAS_STIMULUS]->(s:Stimulus)
RETURN t, s

// Avoid: Unbounded variable-length paths
// Bad: MATCH (a)-[*]->(b)
// Good: MATCH (a)-[*1..5]->(b)
```

---

## Query Patterns for Neuroscience Data

### Metadata Queries
```cypher
// Experimenters
MATCH (nwb:NWBFile)-[:HAS_EXPERIMENTER]->(exp:Experimenter)
RETURN exp.name

// Institutions
MATCH (nwb:NWBFile)-[:CONDUCTED_AT]->(inst:Institution)
RETURN inst.name, inst.location

// Labs
MATCH (nwb:NWBFile)-[:FROM_LAB]->(lab:Laboratory)
RETURN lab.name, lab.pi
```

### Structure Queries
```cypher
// All TimeSeries types
MATCH (ts:TimeSeries)
RETURN DISTINCT labels(ts) AS types, COUNT(*) AS count
ORDER BY count DESC

// Hierarchy
MATCH (nwb:NWBFile)-[:CONTAINS*]->(container)
RETURN container.neurodata_type, container.name

// Processing modules
MATCH (nwb:NWBFile)-[:CONTAINS]->(pm:ProcessingModule)
RETURN pm.name, pm.description
```

### Content Queries
```cypher
// Sampling rates
MATCH (es:ElectricalSeries)
RETURN es.name, es.sampling_rate, es.unit

// Electrode properties
MATCH (e:Electrode)
RETURN e.id, e.x, e.y, e.z, e.location, e.impedance, e.filtering

// Trial information
MATCH (t:Trial)
RETURN t.trial_id, t.start_time, t.stop_time, t.outcome
ORDER BY t.start_time
```

### Relationship Queries
```cypher
// Electrode to device connections
MATCH (e:Electrode)-[:PART_OF]->(eg:ElectrodeGroup)-[:USES_DEVICE]->(d:Device)
RETURN e.id, eg.name, d.name

// Data derivation
MATCH (raw:ElectricalSeries)-[:PROCESSED_FROM]->(filtered:FilteredEphys)
RETURN raw.name, filtered.name

// Recording relationships
MATCH (es:ElectricalSeries)-[:RECORDED_WITH]->(eg:ElectrodeGroup)
RETURN es.name, eg.name, eg.location
```

### Aggregation Queries
```cypher
// Count by type
MATCH (n)
RETURN labels(n) AS node_type, COUNT(*) AS count
ORDER BY count DESC

// Average sampling rate
MATCH (ts:TimeSeries)
WHERE ts.sampling_rate IS NOT NULL
RETURN AVG(ts.sampling_rate) AS avg_sampling_rate

// Electrode statistics
MATCH (e:Electrode)
RETURN
  COUNT(e) AS total_electrodes,
  AVG(e.impedance) AS avg_impedance,
  MIN(e.impedance) AS min_impedance,
  MAX(e.impedance) AS max_impedance
```

### Provenance Queries
```cypher
// Source file information
MATCH (n)
WHERE n.source_file IS NOT NULL
RETURN DISTINCT n.source_file, COUNT(*) AS node_count

// HDF5 path tracking
MATCH (n:Electrode {id: 5})
RETURN n.source_file, n.hdf5_path, n.nwb_type
```

---

## Common Natural Language Patterns

### Pattern Recognition Table

| NL Pattern | Intent | Cypher Pattern |
|------------|--------|----------------|
| "What is/are..." | Property retrieval | `MATCH (n) RETURN n.property` |
| "How many..." | Count aggregation | `MATCH (n) RETURN COUNT(n)` |
| "Show/List all..." | Full retrieval | `MATCH (n:Type) RETURN n LIMIT 100` |
| "Find X where Y" | Filtered search | `MATCH (n) WHERE n.prop = value RETURN n` |
| "Which X used Y" | Relationship query | `MATCH (x)-[:REL]->(y) RETURN x` |
| "X in/from/at Y" | Location/filter | `WHERE n.location CONTAINS 'Y'` |
| "X with Y > Z" | Comparison | `WHERE n.property > value` |
| "X and Y" | Multiple conditions | `WHERE n.prop1 = v1 AND n.prop2 = v2` |
| "Average/Sum/Max" | Aggregation | `RETURN AVG(n.prop)` / `SUM()` / `MAX()` |

---

## Best Practices

### 1. Always Validate User Input
```python
# Check for injection patterns
forbidden_patterns = ['DROP', 'DELETE', 'DETACH', 'CREATE', 'SET', 'REMOVE']

def is_safe_query(cypher_query):
    """Ensure query is read-only"""
    query_upper = cypher_query.upper()
    return not any(pattern in query_upper for pattern in forbidden_patterns)
```

### 2. Use Parameterized Queries
```python
# Good: Parameterized
query = "MATCH (e:Electrode) WHERE e.id = $electrode_id RETURN e"
params = {"electrode_id": user_input}

# Bad: String concatenation (injection risk!)
query = f"MATCH (e:Electrode) WHERE e.id = {user_input} RETURN e"
```

### 3. Set Query Timeouts
```python
from neo4j import GraphDatabase

with driver.session() as session:
    result = session.run(query, timeout=10)  # 10 second timeout
```

### 4. Handle Empty Results Gracefully
```python
result = session.run(query)
records = list(result)

if not records:
    return "No results found for your query."
else:
    return format_results(records)
```

### 5. Return Provenance with Results
```cypher
MATCH (e:Electrode {id: 5})
RETURN
  e.id,
  e.location,
  e.impedance,
  e.source_file AS source,
  e.hdf5_path AS path,
  e.nwb_type AS type
```

---

## Query Validation Checklist

Before executing a generated query, verify:

- [ ] Query is read-only (MATCH, RETURN, WITH, WHERE only)
- [ ] Has LIMIT clause (unless aggregating with COUNT/AVG/etc)
- [ ] Uses parameterized values (not string concatenation)
- [ ] Has proper node/relationship types
- [ ] Uses indexed properties for filtering when possible
- [ ] Handles null values appropriately
- [ ] Returns provenance information when needed
- [ ] Has reasonable timeout setting
- [ ] Will not cause performance issues

---

## Error Handling

### Common Errors and Solutions

**1. Node type not found**
```
Error: "No nodes with label :Experimenter found"
Solution: Check schema, suggest similar labels, ask for clarification
```

**2. Property doesn't exist**
```
Error: "Property 'sampleRate' not found"
Solution: Check for typos, suggest similar properties (sampling_rate)
```

**3. Ambiguous query**
```
Query: "Show the data"
Solution: Ask for clarification - which data? what type? what properties?
```

**4. Query too broad**
```
Query: "Show everything"
Solution: Add constraints, suggest specific queries, warn about size
```

---

## Advanced Features

### 1. Dynamic Property Handling
```cypher
// Return all properties of a node
MATCH (e:Electrode {id: 5})
RETURN properties(e) AS electrode_properties
```

### 2. Path Queries
```cypher
// Find shortest path
MATCH path = shortestPath((d:Device)-[*..5]->(e:Electrode))
RETURN path
LIMIT 10
```

### 3. Optional Relationships
```cypher
// Handle missing relationships gracefully
MATCH (e:Electrode)
OPTIONAL MATCH (e)-[:PART_OF]->(eg:ElectrodeGroup)
RETURN e.id, eg.name
```

### 4. Subqueries
```cypher
// Complex filtering with subqueries
MATCH (nwb:NWBFile)
WHERE EXISTS {
  MATCH (nwb)-[:HAS_EXPERIMENTER]->(exp:Experimenter)
  WHERE exp.institution = 'Stanford'
}
RETURN nwb.session_id
```

---

## Integration with LLMs

### Prompt Template for Query Generation
```
You are converting a natural language query to Cypher for a Neo4j graph database.

Graph Schema:
- Node types: {node_types}
- Relationship types: {relationship_types}
- Key properties: {properties}

User Query: "{natural_language_query}"

Generate a safe, optimized Cypher query that:
1. Uses proper node labels and relationship types
2. Includes a LIMIT clause (default 100)
3. Returns relevant properties
4. Includes provenance (source_file, hdf5_path)
5. Is read-only (MATCH/RETURN only)

Output only the Cypher query, no explanation.
```

### Example Pipeline
```python
def nl_to_cypher_pipeline(nl_query: str, schema: dict) -> dict:
    """
    Convert natural language to Cypher query

    Returns:
        {
            'cypher': str,           # Generated Cypher
            'params': dict,          # Query parameters
            'intent': str,           # Detected intent
            'entities': list,        # Extracted entities
            'validation': dict       # Validation results
        }
    """
    # Step 1: Parse NL query
    intent = classify_intent(nl_query)
    entities = extract_entities(nl_query, schema)

    # Step 2: Generate Cypher
    cypher = generate_cypher(intent, entities, schema)

    # Step 3: Validate
    validation = validate_query(cypher)

    # Step 4: Parameterize
    cypher, params = parameterize_query(cypher, entities)

    return {
        'cypher': cypher,
        'params': params,
        'intent': intent,
        'entities': entities,
        'validation': validation
    }
```

---

## Testing Queries

### Test Cases for Common Patterns

```cypher
// Test 1: Simple property retrieval
MATCH (nwb:NWBFile)
RETURN nwb.session_id, nwb.session_start_time
LIMIT 1

// Test 2: Filtered retrieval
MATCH (e:Electrode)
WHERE e.impedance > 1000000
RETURN COUNT(e)

// Test 3: Relationship traversal
MATCH (nwb:NWBFile)-[:USES_DEVICE]->(d:Device)
RETURN d.name
LIMIT 5

// Test 4: Aggregation
MATCH (t:Trial)
RETURN COUNT(t) AS total_trials

// Test 5: Complex filtering
MATCH (e:Electrode)
WHERE e.location CONTAINS 'cortex' AND e.impedance IS NOT NULL
RETURN e.id, e.location, e.impedance
ORDER BY e.impedance ASC
LIMIT 10
```

---

## When to Use This Skill

Claude will automatically invoke this skill when:
- User asks how to convert natural language to Cypher
- User needs help writing Cypher queries
- User is building a natural language query interface
- User mentions Neo4j query generation
- User asks about optimizing graph database queries
- User needs to debug Cypher syntax
- User is working on the query processing pipeline (Epic 3)

---

## Resources

- **Neo4j Cypher Manual**: https://neo4j.com/docs/cypher-manual/current/
- **Cypher Refcard**: https://neo4j.com/docs/cypher-refcard/current/
- **Query Tuning**: https://neo4j.com/docs/cypher-manual/current/query-tuning/

---

When this skill is invoked, provide expert guidance on converting natural language queries to safe, optimized Cypher queries tailored to the graph schema and use case.
# Knowledge Graph Memory Server
A basic MCP server implementation that provides persistent memory using a knowledge-graph. The server manages entities, their observations, and the relationships between them using a JSON-based file system.

This lets Claude remember information about the user across chats and projects, and lets them bypass the issues of having super long chats

# Core Concepts

## Entities
Entities are the primary nodes in the knowledge graph. Each entity has:
- A unique name (identifier)
- An entity type (e.g., "person", "organization", "event")
- A list of observations

Example:
```json
{
  "name": "John_Smith",
  "entityType": "person",
  "observations": ["Lives in New York", "Works as a software engineer"]
}
```

## Relations
Relations define directed connections between entities. They are always stored in active voice and describe how entities interact or relate to each other.
Example:
```jsonCopy{
  "from": "John_Smith",
  "to": "TechCorp",
  "relationType": "works_at"
}
```
## Observations
Observations are discrete pieces of information about an entity. They are:

- Stored as strings
- Attached to specific entities
- Can be added or removed independently
- Should be atomic (one fact per observation)

Example:
```jsonCopy{
  "entityName": "John_Smith",
  "observations": [
    "Speaks fluent Spanish",
    "Graduated in 2019",
    "Prefers morning meetings"
  ]
}
```

# Tools

## Entity Management

- create_entities: Create new entities in the knowledge graph with names, types, and observations
- delete_entities: Remove entities and their associated relations from the graph
- add_observations: Add new observations to existing entities
- delete_observations: Remove specific observations from entities


## Relation Management

- create_relations: Establish relationships between entities in active voice
- delete_relations: Remove specific relationships between entities


## Query Tools

- read_graph: Retrieve the entire knowledge graph
- search_nodes: Search for nodes based on names, types, and observation content
- open_nodes: Access specific nodes by their names

# Prompts

The prompt for utilizing memory depends on the use case, but here is an example prompt for chat personalization. You could use this prompt in the "Custom Instructions" field of a Project

```
Follow these steps for each interaction:

1. User Identification:
   - You should assume that you are interacting with default_user
   - If you have not identified default_user, proactively try to do so.

2. Memory Retrieval:
   - Always begin your chat by saying only "Remembering..." and retrieve all relevant information from your knowledge graph
   - Always refer to your knowledge as your "memory"

3. Memory
   - While conversing with the user, be attentive to any new information that falls into these categories:
     a) Basic Identity (Age, gender, location, Job title, education level, etc.)
     b) Behaviors (interests, habits, etc.)
     c) Preferences (communication style, preferred language, etc.)
     d) Goals/Psychology (Goals, targets, aspirations, etc.)
     e) Relationships (personal and professional relationships up to 3 degrees of separation)

4. Memory Update:
   - If any new information was gathered during the interaction, update your memory as follows:
     a) Create nodes for recurring organizations, people, and significant events, connecting them to the current node.
     b) Store most facts as observations within these nodes
   - Try to perform all updates in one operation using the create and delete functions.
```
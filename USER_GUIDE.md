# Context MCP Server User Guide

The Context MCP Server is a Model Context Protocol (MCP) server that helps you index, search, and manage documentation collections. Use it with Claude Desktop, Cursor, or any MCP-compatible client to work with documentation more effectively.

## Getting Started

Once the MCP server is configured in your client (Claude Desktop, Cursor, etc.), you can start using the available tools directly. All tools are namespaced to your account, so your indexed documentation remains private to you.

## Available Tools

### Indexing Documentation

#### `index-docs`

Crawl and index a documentation website so you can search it later.

**Parameters:**
- `url` (required) - Base URL for the documentation site to index
- `prompt` (optional) - Crawl tuning instructions to steer which sections are indexed
- `maxPages` (optional) - Maximum number of pages to crawl
- `includePaths` (optional) - Array of path substrings to include (only crawl URLs containing these)
- `excludePaths` (optional) - Array of path substrings to exclude (skip URLs containing these)
- `background` (optional) - Set to `true` to enqueue as a background job instead of indexing immediately

**Examples:**

Index a documentation site immediately:
```
index-docs: {"url": "https://docs.example.com"}
```

Index with filters to focus on API documentation:
```
index-docs: {
  "url": "https://docs.example.com",
  "prompt": "Focus on API reference pages and skip marketing content",
  "includePaths": ["/api", "/guides"],
  "excludePaths": ["/blog", "/changelog"],
  "maxPages": 150
}
```

Queue indexing as a background job:
```
index-docs: {
  "url": "https://docs.example.com",
  "background": true
}
```

### Searching Documentation

#### `search-docs`

Run semantic searches across your indexed documentation.

**Parameters:**
- `query` (required) - Natural language question or phrase to search for
- `sources` (optional) - Array of resource IDs to constrain the search to specific documentation sites
- `limit` (optional) - Maximum number of results to return (default: 10)
- `grouped` (optional) - Set to `true` to return results grouped by page URL
- `returnPage` (optional) - Set to `true` to include assembled page markdown in the response

**Examples:**

Basic search:
```
search-docs: {"query": "How do I authenticate API requests?"}
```

Search within specific documentation sources:
```
search-docs: {
  "query": "authentication methods",
  "sources": ["doc:https://api.example.com", "doc:https://docs.example.com"],
  "limit": 5
}
```

Get grouped results with full page content:
```
search-docs: {
  "query": "error handling",
  "grouped": true,
  "returnPage": true
}
```

#### `summarize-docs`

Get AI-generated summaries of the most relevant documentation pages for a topic.

**Parameters:**
- `query` (required) - Topic or question to summarize using indexed documentation
- `top` (optional) - Number of top matching pages to include (default: 3)
- `model` (optional) - OpenAI model alias to use for summarization

**Examples:**

Get a summary of a topic:
```
summarize-docs: {"query": "How does rate limiting work?"}
```

Summarize with more pages:
```
summarize-docs: {
  "query": "API authentication",
  "top": 5
}
```

### Finding New Documentation

#### `find-docs`

Use web search to discover new documentation resources to index.

**Parameters:**
- `query` (required) - Describe the resource you want to find
- `limit` (optional) - Maximum number of URLs to return (default: 10)
- `includeGithub` (optional) - Include GitHub repositories and discussions in results
- `includeResearch` (optional) - Include academic and research sources in results
- `includePdf` (optional) - Include PDF documents when available

**Examples:**

Find documentation sites:
```
find-docs: {"query": "Python web framework documentation"}
```

Include GitHub sources:
```
find-docs: {
  "query": "React component library documentation",
  "includeGithub": true,
  "limit": 20
}
```

### Agent Mode

#### `ask-agent`

Delegate to an autonomous agent that can search, summarize, and index documentation to answer your question.

**Parameters:**
- `question` (required) - Question for the Doc Index agent
- `model` (optional) - LLM alias to use for the agent response
- `steps` (optional) - Maximum number of tool round-trips the agent can perform
- `temperature` (optional) - Sampling temperature for the underlying LLM (default: 0.2, range: 0-2)
- `includeResources` (optional) - Provide the indexed resource list as additional context to the agent

**Examples:**

Ask a question and let the agent figure it out:
```
ask-agent: {"question": "What are the best practices for API authentication?"}
```

Configure the agent with more steps and context:
```
ask-agent: {
  "question": "Compare authentication methods across my indexed docs",
  "steps": 10,
  "includeResources": true,
  "temperature": 0.3
}
```

### Managing Resources

#### `list`

List your indexed documentation resources or background jobs.

**Parameters:**
- `kind` (optional) - Choose `"resources"` (default) or `"jobs"` to list background indexing jobs

**Examples:**

List all indexed resources:
```
list: {}
```

List background jobs:
```
list: {"kind": "jobs"}
```

#### `status`

Check the status of an indexing job or resource.

**Parameters:**
- `id` (required) - Job ID (starts with `job_`) or resource ID (starts with `doc:`) to inspect
- `entityType` (optional) - Force interpretation as `"job"` or `"resource"` (default: auto-detect)

**Examples:**

Check resource status:
```
status: {"id": "doc:https://docs.example.com"}
```

Check background job status:
```
status: {"id": "job_abc123"}
```

#### `rename`

Rename an indexed documentation resource.

**Parameters:**
- `resourceId` (required) - Identifier of the resource to rename
- `newName` (required) - New display name for the resource

**Examples:**

Rename a resource:
```
rename: {
  "resourceId": "doc:https://docs.example.com",
  "newName": "Example API Documentation"
}
```

#### `delete`

Delete an indexed documentation resource.

**Parameters:**
- `resourceId` (required) - Identifier of the resource to delete

**Examples:**

Delete a resource:
```
delete: {"resourceId": "doc:https://docs.example.com"}
```

## Best Practices

### Indexing

1. **Start with focused indexes**: When indexing large documentation sites, use `includePaths` and `excludePaths` to focus on the sections most relevant to you.

2. **Use background jobs for large sites**: For documentation sites with hundreds of pages, set `background: true` to avoid blocking while indexing completes.

3. **Set reasonable page limits**: Use `maxPages` to prevent accidentally indexing entire sites when you only need a subset.

4. **Use prompts to guide crawling**: The `prompt` parameter helps the crawler understand what to prioritize. For example: "Focus on API reference pages and skip marketing content."

### Searching

1. **Be specific with queries**: More specific queries yield better results. Instead of "authentication", try "How do I authenticate API requests with OAuth?"

2. **Use `grouped` results for overview**: When you want to see which pages contain relevant information, use `grouped: true` to see results organized by page.

3. **Filter by sources**: If you've indexed multiple documentation sites, use the `sources` parameter to search within specific ones.

4. **Combine search and summarize**: Use `search-docs` to find specific information, and `summarize-docs` when you need a high-level overview of a topic.

### Discovery

1. **Use `find-docs` to discover new resources**: Before manually indexing a site, use `find-docs` to see if there are related documentation resources you should also consider.

2. **Include GitHub when relevant**: Set `includeGithub: true` when searching for developer documentation, as many projects have detailed README files and docs on GitHub.

### Agent Mode

1. **Trust the agent for complex questions**: The `ask-agent` tool can chain multiple operations together. Use it when you need information that might require searching, summarizing, or even indexing new content.

2. **Increase steps for complex queries**: If the agent seems to stop early, increase the `steps` parameter to allow more tool round-trips.

3. **Provide context with `includeResources`**: Setting `includeResources: true` gives the agent awareness of what documentation you have indexed, which can help it make better decisions.

### Resource Management

1. **Check status after indexing**: After starting an index job, use `status` to monitor progress, especially for background jobs.

2. **Use descriptive names**: Rename resources with meaningful names so you can easily identify them in lists.

3. **Clean up unused resources**: Periodically use `list` to review your indexed resources and `delete` ones you no longer need to keep your namespace organized.

### Workflow Tips

1. **Index first, search later**: Index documentation sites relevant to your work upfront, then use search tools as needed during development.

2. **Combine tools for research**: Use `find-docs` to discover resources, `index-docs` to add them, then `ask-agent` to answer specific questions using the indexed content.

3. **Monitor background jobs**: For large indexing tasks, periodically check job status with `list` and `status` to ensure everything is processing correctly.

4. **Organize by project**: While all your resources are namespaced to your account, you can mentally organize them by renaming resources to indicate which project or team they relate to.

## Understanding Resource IDs

Resource IDs follow these patterns:
- Documentation sites: `doc:https://docs.example.com`
- Background jobs: `job_abc123` or `job:abc123`

You'll see these IDs in tool responses and use them with `status`, `rename`, and `delete` operations.

## Common Use Cases

### Indexing Multiple Documentation Sites

1. Use `index-docs` for each documentation site you want to search
2. Use `list` to see all your indexed resources
3. Use `search-docs` with `sources` to search within specific sites

### Researching a Topic

1. Use `find-docs` to discover relevant documentation
2. Use `index-docs` to add the most relevant sites
3. Use `ask-agent` to get comprehensive answers about the topic

### Keeping Documentation Up to Date

1. Delete outdated resources with `delete`
2. Re-index with `index-docs` to get the latest content
3. Use `status` to monitor the indexing progress

### Quick Reference Lookups

1. Use `search-docs` with specific queries for fast lookups
2. Use `summarize-docs` when you need a quick overview
3. Use `grouped: true` to see which pages contain the information













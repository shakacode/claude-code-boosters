---
description: Analyze code for performance issues and suggest optimizations
---

Analyze the specified code for performance issues and suggest optimizations.

# Instructions

1. **Identify the target**: The user will specify a file, function, or code block to analyze. If not specified, ask what to analyze.

2. **Read the code** and analyze for these performance concerns:

## Performance Areas

### Database & Queries
- N+1 query patterns
- Missing indexes (check schema/migrations)
- Unnecessary eager loading or over-fetching
- Unscoped queries that could return large datasets

### Algorithm & Data Structures
- O(n²) or worse time complexity where O(n) or O(n log n) is possible
- Unnecessary array/list copies or allocations
- Repeated computation that could be memoized
- Inefficient string concatenation in loops

### I/O & Network
- Sequential API calls that could be parallelized
- Missing caching for expensive operations
- Unbatched operations (many small writes vs. bulk)
- Large payloads that could be paginated or streamed

### Memory
- Memory leaks (unclosed resources, growing collections)
- Holding large objects longer than needed
- Unnecessary object creation in hot paths

### Frontend-Specific
- Unnecessary re-renders (missing memoization, unstable references)
- Large bundle imports where tree-shaking could help
- Missing lazy loading for routes or heavy components
- Layout thrashing or forced reflows

3. **Output format**:

| Priority | Location | Issue | Impact | Fix |
|----------|----------|-------|--------|-----|
| High | file:line | Description | Estimated impact | Concrete suggestion |
| Medium | file:line | Description | Estimated impact | Concrete suggestion |
| Low | file:line | Description | Estimated impact | Concrete suggestion |

4. **Offer to implement** the high-priority fixes directly.

## Notes
- Focus on measurable impact, not micro-optimizations
- Consider the actual scale (10 users vs 10,000 matters)
- Prefer simple fixes over clever ones
- Don't sacrifice readability for negligible gains

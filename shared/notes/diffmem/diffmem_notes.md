# DiffMem Notes

## Summary of what I've learned:

### From README.md:
- DiffMem is a lightweight, Git-based memory backend for AI agents.
- It uses Markdown files for human-readable storage and Git for tracking temporal evolution.
- No vector databases, embeddings, or BM25 – just Git and an LLM.
- Memory is treated as a versioned repository with a "current state" (editable files) and historical changes in Git's commit graph.
- DiffMem is live in production with Annabelle, a simulated intelligence.
- It uses a FastAPI service with Writer Agent (analyzes transcripts, stages updates), Retrieval Agent (explores repo via shell commands), and API Layer.
- Each user gets an isolated orphan branch within a single local storage repo, checked out into a per-user worktree.
- Storage backend (local) and optional Backup backend (GitHub).

### From repo_guide.md:
- Defines the structure, creation, and maintenance of a DiffMem memory repository.
- Uses plain-text Markdown files in a Git repo.
- Emphasizes editable current-state for quick loads and long-term durability.
- Blame optimization for tracing origins of current lines/blocks.
- Context is balanced with bounded blocks (headers to /END) and files under 2000 lines.
- Memory differentiation inspired by SMS Model (Biographical, Episodic, Factual/Semantic).
- Federated Git Architecture with orphan branches and worktrees for user isolation.
- Repository structure includes `repo_guide.md`, `index.md`, `memories/ (people/, contexts/, episodes_index.md)`, `timeline/ (YYYY-MM.md)`.
- Guidelines for creating Biographical, Episodic, and Factual/Semantic memory files, including templates and strength indicators.
- Linking between files using Markdown links for traversable graphs.
- History builds by buffering changes and committing atomically.
- Session referencing includes `(Session: [session_id])`.
- Git practices for blame and merges are outlined, including pruning for longevity.

### From agentic_dev_guide.md:
- LLMs do not "follow instructions" but are guided by context provided.
- Importance of strategic guidelines, UX principles, and user personas embedded in the project context.
- Suggests `USER_PERSONAS.md`, `PRINCIPLES.md`, `CONSTRAINTS.md`, `DECISIONS.md`.
- Emphasizes Cognitive Compartmentalization: each logical piece of capability should be its own mini-project (microservices architecture within the project).
- This creates clean latent space boundaries, preventing context contamination.
- Centralized vs. atomized styles: abstract visual layer away from business logic into one monolithic style file.
- Standardized knowledge representation patterns across domains.
- Documentation: embedded in code, and central `capability_readme.md` files for each microservice.
- Attention guidance mechanisms for LLMs to navigate conceptual landscape.
- Coding practices: context-aware code density, DRY principle, one-liners to keep context lean.
- Logging as LLM Perception: structured logging, semantic categorization, context-rich error reporting, state snapshots as feedback loop for LLM.

### From source code exploration (`api.py`, `server.py`, `repo_manager.py`):
- `api.py`: Defines the `DiffMemory` class, which is the main interface for memory operations. It handles onboarding, getting context, processing sessions, and committing changes. It uses `WriterAgent` and `RetrievalAgent`.
- `server.py`: Implements the FastAPI server for DiffMem, exposing HTTP endpoints for memory operations. It handles authentication, CORS, and integrates with `RepoManager` and `DiffMemory`. It also sets up periodic backups.
- `repo_manager.py`: Coordinates a `StorageBackend` and an optional `BackupBackend`. It manages user worktrees and ensures backups happen out-of-band. It handles cold-start restores from backups and installation of post-commit hooks.
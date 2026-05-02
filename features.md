# Code Search & Navigation Engine — Feature & Functionality Survey

> Candidate #24 · Researched: 2026-05-02

## Solutions Analysed

| Tool | Type | Licence / Model | URL |
|------|------|-----------------|-----|
| Sourcegraph | Enterprise code search + AI platform | Proprietary (source-available until 2024 repo privatisation) | https://sourcegraph.com |
| Zoekt | Trigram-indexed code search engine | Apache 2.0 (open source) | https://github.com/google/zoekt |
| OpenGrok | Web-based source code search & cross-reference | CDDL 1.0 / various (open source) | https://oracle.github.io/opengrok |
| Livegrep (Stripe) | Real-time regex code search | MIT (open source) | https://github.com/livegrep/livegrep |
| grep.app | Web-based public GitHub search | Free web service (proprietary) | https://grep.app |
| GitHub Code Search | Native GitHub code search | Proprietary (included in GitHub plans) | https://github.com/search |
| Glean | Enterprise knowledge + code search | Proprietary SaaS | https://glean.com |
| Hound (Etsy) | Lightweight regex code search | MIT (open source) | https://github.com/etsy/hound |
| WarpGrep / grepai | MCP-native AI agent code search | Open source + commercial (early stage) | https://www.morphllm.com/products/warpgrep |
| sturdy-dev/semantic-code-search | Local natural language code search CLI | MIT (open source) | https://github.com/sturdy-dev/semantic-code-search |

---

## Feature Analysis by Solution

### Sourcegraph

**Core features**
- Unified code search across all repositories indexed into a single corpus using the Zoekt trigram engine
- Exact, regex, and structural search (Comby) across billions of lines of code in sub-second query time
- Symbol-level navigation: go-to-definition, find-references, and hover documentation using SCIP-indexed code intelligence
- Batch Changes: apply large-scale code migrations (find-and-replace, refactors) across all repositories simultaneously and track progress
- Code Insights: dashboards that track code-level metrics (adoption of a new API, removal of a deprecated pattern) over time
- Cody AI assistant: context-aware code generation, explanation, and chat backed by the full indexed repository corpus
- Cross-repo dependency navigation: jump from a function call in service A to its definition in service B

**Differentiating features**
- Batch Changes is unique in the enterprise code search market: the only tool that combines search with automated multi-repo patch application and PR creation
- Code Insights dashboards over time allow teams to track migration progress numerically (e.g., "how many uses of `react-router` v5 remain?")
- SCIP-based precise code intelligence is more accurate than heuristic-based navigation for large polyglot codebases
- Deep Search (announced, in development 2026): improved semantic understanding of code structure beyond trigram matching

**UX patterns**
- Web-based search UI with regex, structural, and natural language query modes
- Query builder with syntax suggestions and autocomplete
- Results annotated with repository, file path, line number, and matched context
- IDE extensions (VS Code, JetBrains) surface search and navigation directly in the editor

**Integration points**
- GitHub, GitLab, Bitbucket, Azure DevOps, Gerrit code hosts via native connectors
- VS Code and JetBrains IDE extensions
- Cody API for embedding AI code context in external tools
- REST and GraphQL APIs for programmatic search and navigation
- Kubernetes deployment (Helm chart) for self-hosted enterprise

**Known gaps**
- Source code repository moved to private in August 2024; no longer open source in any meaningful sense
- Free self-hosted tier deprecated in 2026; Enterprise plan at $49/user/month creates a significant cost barrier
- Company focus has shifted heavily toward the Cody AI assistant; code search investment has slowed
- Free / Pro Cody plans discontinued July 2025, forcing all users to Enterprise ($59/user/month) or nothing
- Pricing controversy following the OSS → proprietary pivot has eroded community trust
- Self-hosted deployment is operationally complex at scale; requires Kubernetes expertise

**Licence / IP notes**
- Sourcegraph Enterprise proprietary licence (source moved to private repo August 2024). The Zoekt component is still Apache 2.0 in the google/zoekt repository. SCIP is an open protocol (Apache 2.0). No patent concerns identified beyond general proprietary-product vendor-lock-in risks.

---

### Zoekt

**Core features**
- Trigram-based indexed search: indexes every 3-character sequence with byte-level positional information for fast substring and regex matching
- Builds compressed, sharded index files per repository; serves sub-second queries across multi-billion-line corpora
- Rich query language: exact match, regex, boolean operators (AND, OR, NOT), filename filter, language filter, case sensitivity toggle
- Ranking heuristics: path weighting and symbol-awareness to surface the most relevant matches first
- Web UI for interactive exploration and JSON API for programmatic integration
- Index server with configurable periodic re-fetch and re-indexing from code hosts

**Differentiating features**
- Positional trigrams eliminate false positives that standard word-based trigram approaches suffer from
- Sub-second query latency at Google-scale (originally built at Google by Han-Wen Nienhuys); independently confirmed as the fastest open-source indexed code search
- Powers both Sourcegraph and GitLab's exact code search; proven at enterprise scale

**UX patterns**
- Headless by default: no production-quality UI bundled; the built-in web server is a development aid
- JSON API designed for integration into larger platforms (Sourcegraph wraps it; others can too)
- Requires engineering effort to deploy, configure index shards, and connect to a code host

**Integration points**
- GitLab (native Zoekt integration for exact code search, Generally Available 2024)
- Sourcegraph (internal engine)
- Any code host via git clone / mirror; index server polls for updates
- JSON API consumed by custom UIs and tooling

**Known gaps**
- No symbol-level navigation (go-to-definition, find-references); purely a text search engine
- No semantic or natural language search; regex and exact match only
- No UI suitable for end-users without custom development
- No access control layer; deployer must enforce who can query the index
- Actively maintained by Sourcegraph; Google's fork receives less attention

**Licence / IP notes**
- Apache 2.0 (google/zoekt and sourcegraph/zoekt). No patent encumbrances identified. Safe for use in commercial products.

---

### OpenGrok

**Core features**
- Fast full-text search across large source trees using Lucene-backed indexing
- Cross-reference engine: hyperlinked identifier navigation — click any symbol to see all definitions and usages
- Supports 60+ programming languages via Exuberant/Universal Ctags and custom analysers
- VCS history integration: Mercurial, Git, SVN, CVS, Perforce, ClearCase, Bazaar, SCCS, RCS, Teamware
- Web UI with syntax highlighting and customisable CSS; search query syntax similar to Google
- REST API for programmatic search queries
- Incremental index updates; new commits re-indexed without full rebuild

**Differentiating features**
- Most comprehensive VCS history support of any open-source tool; searches span blame, annotate, and history views
- Cross-reference hyperlinks in the web UI provide a "Wikipedia for code" experience
- Deployed at Oracle, Red Hat, and other large enterprises; proven at multi-million LOC scale

**UX patterns**
- Web-first: all search and navigation via a browser
- Search query syntax is Google-like with qualifiers for path, definition, symbol, full-text, and history
- Java-based deployment (WAR file) on Tomcat; familiar to enterprise Java operations teams

**Integration points**
- REST API for integration with CI and other tooling
- Universal Ctags for language analysis (extensible via plugins)
- Any supported VCS via clone/mirror
- Docker image available for containerised deployment

**Known gaps**
- Outdated UI and developer experience; last major UX overhaul was years ago
- No semantic or natural language search
- No AI integration or code intelligence beyond static cross-reference
- Java/Tomcat deployment model is operationally heavy relative to modern Go or Node.js tools
- No MCP/agent integration; not usable by AI coding agents
- Community activity has slowed; Oracle involvement is passive maintenance

**Licence / IP notes**
- Dual-licensed: original code under CDDL 1.0 (Common Development and Distribution Licence, Sun/Oracle origin); some components under various open-source licences. CDDL is OSI-approved but incompatible with GPL; this can create complications when combining OpenGrok with GPL-licensed dependencies. No patent concerns identified.

---

### Livegrep (Stripe)

**Core features**
- Real-time regex code search across gigabyte-scale local source trees with interactive latency
- Codesearch binary maintains a persistent in-memory index; livegrep web server is stateless and connects to it over TCP
- Sub-50ms query latency for GB-scale repos with hot in-memory indexes
- Web UI with instant-feedback query results as the user types
- Supports multiple repositories indexed into a single codesearch instance

**Differentiating features**
- Real-time, interactive search feedback as the user types — unique among indexed code search tools
- Stateless web tier means horizontal scaling of the query-serving layer is trivial
- Explicitly designed for single-organisation GB-scale repos; Stripe's use case validates reliability at that scale

**UX patterns**
- Web UI only; no IDE integration
- Instant results during typing create a "search as you think" experience
- No account required if deployed internally

**Integration points**
- REST API for programmatic queries
- GitHub, GitLab, or any git host via clone/mirror
- Docker support for containerised deployment

**Known gaps**
- Explicitly limited to single-digit GB scale; not designed for multi-repo enterprise search at TB scale
- No symbol-level navigation, cross-reference, or go-to-definition
- No semantic or natural language search
- No MCP/agent integration
- Minimal community activity since 2022; Stripe has not open-sourced updates

**Licence / IP notes**
- MIT licence. No patent concerns identified. Stripe has not contributed significant updates; maintenance risk is moderate.

---

### grep.app

**Core features**
- Instant regex and exact search across 500,000+ public GitHub repositories
- Returns matching file paths, line numbers, and code context in the web UI
- MCP server (`galprz/grep-mcp`) enables AI coding agents (Claude Code, Cursor) to call grep.app search as a tool
- Supports case-insensitive and case-sensitive search modes

**Differentiating features**
- Largest index of public GitHub repositories available for free, with no setup
- MCP server availability makes it directly callable by AI coding agents for public code lookup without additional infrastructure
- Fastest way to find open-source usage examples for any API or pattern

**UX patterns**
- Single-page web app; results appear immediately
- No login required; no rate limit published for free use
- Query syntax is plain regex; no additional qualifiers needed

**Integration points**
- MCP server for AI agent integration
- Web UI only; no formal REST API documented for public use

**Known gaps**
- Public code only; no private repository support
- No symbol-level navigation; text search only
- No semantic or natural language query capability
- No organisation-scoped search; cannot restrict to a specific organisation's repositories
- MCP server is a community project, not officially maintained by grep.app

**Licence / IP notes**
- Free web service; proprietary. MCP server wrapper is open source (community-maintained). No patent concerns identified.

---

### GitHub Code Search

**Core features**
- Exact and regex search across all repositories the user has access to (public and private, given access)
- Language filter, path filter, organisation filter, and repository filter qualifiers
- Semantic code search via GitHub Copilot: instant indexing (seconds, not minutes as of March 2025 GA) enables natural language queries that return semantically relevant code
- Copilot semantic search is generally available to all GitHub Copilot tiers including free
- Symbol search: find function and class definitions by name across a repository

**Differentiating features**
- Zero setup: every GitHub repository is already searchable; no indexing pipeline to manage
- Copilot semantic search is the only semantic code search generally available at no additional infrastructure cost for GitHub-hosted code
- Regex support across public repos and GitHub Enterprise (including via the web UI)
- Integration with GitHub Issues, PRs, and Actions makes search results directly actionable

**UX patterns**
- Web UI with search qualifiers and result filtering; embedded in every repository view
- Copilot Chat in the IDE can reference search results to answer "where is X implemented?" questions
- Keyboard shortcut (`/`) for quick-open search from anywhere on github.com

**Integration points**
- REST API for code search (exact/keyword only; regex not supported in the API)
- GraphQL API for repository metadata queries
- GitHub CLI (`gh search code`)
- GitHub Actions for CI-triggered code audit tasks
- VS Code GitHub Copilot extension for editor-integrated search

**Known gaps**
- Regex search is not supported in the REST API (web UI only for regex)
- Cross-host search (GitLab, Bitbucket, on-premises mirrors) is not possible
- Semantic search is tightly coupled to GitHub Copilot; not an independent query interface
- No symbol-level cross-repo navigation (go-to-definition across repositories)
- Search result relevance ranking is opaque; limited tuning options for enterprise deployments
- Enterprise on-premises installations (GHES) lag behind github.com features for code search

**Licence / IP notes**
- Proprietary GitHub service. Included in all GitHub plans. Semantic search uses GitHub Copilot's models; Copilot Enterprise is $39/user/month additional. No patent concerns for typical usage.

---

### Glean

**Core features**
- Connects to 100+ enterprise applications (Google Drive, Slack, Confluence, Jira, Notion, GitHub, GitLab, and more) for unified knowledge search
- Natural language query interface powered by RAG (Retrieval-Augmented Generation) over the unified index
- Code search is one data source among many; full-text and semantic search across indexed repositories
- Work AI add-on: generative AI assistant that can answer questions drawing from all indexed sources simultaneously
- People graph: identifies subject-matter experts for a given topic based on their activity across connected systems

**Differentiating features**
- Broadest knowledge integration surface of any tool in this survey: code + docs + chat + issues + email in one query
- Enterprise-grade security: data is indexed within the customer's own cloud tenancy (no data leaves to Glean's servers)
- 100+ pre-built connectors covering more enterprise applications than any competing platform

**UX patterns**
- Search bar deployable as a browser extension or Slack slash command
- Natural language queries return blended results from code, docs, Slack, and email simultaneously
- Onboarding requires connecting each data source; IT/admin-managed deployment

**Integration points**
- 100+ SaaS connectors (GitHub, GitLab, Jira, Confluence, Notion, Slack, Google Workspace, Microsoft 365, and more)
- REST API for programmatic search
- Slack app and browser extension
- Glean SDK for embedding search in custom internal tools

**Known gaps**
- Code search is not Glean's primary strength; symbol navigation and cross-repo intelligence are absent
- Very expensive: $45–65/user/month base, $15/user/month AI add-on, minimum ~100 seats ($60K+/year minimum contract)
- Total cost of ownership can reach $350K–$480K/year when infrastructure and administration are included
- Annual price increases of 7–12% are typical
- No self-hosted option; cloud-only, which may block regulated-industry buyers despite per-tenant data isolation

**Licence / IP notes**
- Proprietary SaaS. No public source code. No patent concerns identified for typical usage. High pricing and minimum-seat requirements create significant commercial lock-in.

---

### Hound (Etsy)

**Core features**
- Go-based regex code search server with a minimal web UI
- Indexes local git repositories on a configurable poll schedule
- Multi-repo search: single query searches across all configured repositories simultaneously
- JSON configuration file specifies which repositories to index (local path or remote git URL)

**Differentiating features**
- Simplest deployment of any multi-repo search tool: single binary, single JSON config
- Sub-minute setup from download to first search result
- Lightweight enough to run on a developer workstation alongside other services

**UX patterns**
- Web UI at `localhost:6080` with a simple search box and repo filter dropdown
- Results show file path, repository, and highlighted line context

**Integration points**
- No API documented; web UI only for query
- Works with any git-accessible repository (GitHub, GitLab, local)

**Known gaps**
- No semantic or natural language search
- No symbol-level navigation or cross-reference
- No MCP/agent integration
- No access control; all indexed repos are visible to all users of the instance
- Minimal community activity since ~2019; effectively unmaintained

**Licence / IP notes**
- MIT licence. No patent concerns. Maintenance risk is high; the project has seen minimal commits in recent years.

---

### WarpGrep

**Core features**
- RL-trained (reinforcement learning) retrieval model purpose-built for use as a tool by AI coding agents
- Executes up to 8 parallel search calls per agent turn, returning results in under 6 seconds
- Callable as an MCP tool, SDK, or API endpoint; designed for Claude Code, Cursor, and Windsurf
- Can search any public GitHub repository remotely without downloading files
- Reward function trained on fetching correct files and hitting correct line ranges, minimising irrelevant context

**Differentiating features**
- First and only RL-trained code retrieval model; treats code retrieval as a learned search problem rather than a rule-based one
- 40% speedup in end-to-end agent task completion and 70% reduction in context rot versus baseline configurations (published benchmark)
- MCP-first design: results are structured for agent consumption, not human reading — citations, line ranges, and relevance scores
- YC-backed (W2025); early-stage but the only purpose-built agent retrieval tool in the market

**UX patterns**
- No human-facing UI; designed exclusively for machine (agent) consumption
- Agents call WarpGrep as a tool; results are returned as structured JSON
- Playground available for testing agent queries interactively

**Integration points**
- MCP tool for Claude Code, Cursor, Windsurf, and any MCP-compatible agent
- SDK for direct integration into custom agent pipelines
- REST API endpoint

**Known gaps**
- Very new (early 2026); limited enterprise features, no self-hosted option yet
- Public GitHub repositories only in current form; private repository search requires enterprise plan (details TBD)
- No human-facing UI for developer direct use
- RL training data and model weights are proprietary; reproducibility and auditability are limited
- Pricing model for paid tiers not yet publicly defined

**Licence / IP notes**
- Core listed as open source; the RL-trained model weights and inference infrastructure appear proprietary. YC-backed startup; licence terms are evolving. No patent filings identified but the RL retrieval approach could be patentable.

---

### sturdy-dev/semantic-code-search

**Core features**
- CLI tool using code embeddings for natural language search across a local codebase
- Runs entirely on the local machine; no data leaves the developer's computer
- Embeds code functions/classes using a code-specific embedding model
- Returns semantically relevant functions and files for queries like "find where we handle authentication errors"
- Supports most major programming languages via tree-sitter parsing

**Differentiating features**
- Privacy-preserving: first and only tool in this survey where the entire search pipeline (embedding + retrieval) runs locally with no network calls
- Natural language queries over private code with no cloud dependency

**UX patterns**
- CLI-only (`sem-search <query>`)
- First run requires indexing the codebase (embedding generation); subsequent queries use the cached index
- Returns ranked list of file paths and function names with relevance scores

**Integration points**
- CLI only; no IDE plugin, API, or MCP server
- Works with any directory of source code files

**Known gaps**
- Limited to small-medium codebases; embedding generation and index size scale poorly beyond ~100K LOC
- No web UI, no cross-repo federation, no MCP integration
- Embedding model is not kept current with latest code models (CodeBERT vintage)
- No symbol navigation, cross-reference, or go-to-definition
- Very low community activity; last significant update was 2023

**Licence / IP notes**
- MIT licence. No patent concerns. Maintenance risk is high; the project is effectively dormant.

---

## Cross-Cutting Feature Themes

### Table-Stakes Features
- Indexed multi-repository search with sub-second latency at 10M+ LOC scale
- Regex and exact keyword search with language, path, and repository filter qualifiers
- Results annotated with file path, repository name, line number, and surrounding code context
- Web UI for human developers and a queryable API for programmatic access
- Incremental re-indexing when repositories receive new commits (no full rebuild required)
- Access control: search results respect repository-level visibility permissions
- Cross-repo navigation: jump from a call site to a function definition in a different repository

### Differentiating Features
- Natural language / semantic code search using neural embeddings (CodeBERT, GraphCodeBERT, or equivalent)
- Precise SCIP-based code intelligence: go-to-definition and find-references that work across language boundaries and repository boundaries
- MCP-native interface for AI coding agent consumption with structured, citation-bearing result formats
- Batch Changes / large-scale automated refactoring across all indexed repositories
- Privacy-preserving on-premise deployment with all neural inference running locally (no code sent to external services)
- Structural search (Comby-style): match code patterns independent of whitespace and variable naming

### Underserved Areas / Opportunities
- Open-source production-quality semantic search: no open-source tool combines trigram speed with neural re-ranking for production-scale private codebases; the research (CodeBERT, 2023–2024 papers) is mature but unimplemented in an accessible tool
- MCP-first open-source code search: WarpGrep demonstrated the category is real but is proprietary; an open-source MCP-native code search server is entirely absent
- Sourcegraph replacement for self-hosted teams: the 2024–2026 licence change and free-tier deprecation left thousands of teams without a viable self-hosted code search platform
- Semantic cross-reference beyond symbol names: no tool answers "what code is responsible for this behaviour?" or "where does this data flow after this function?" using control-flow or data-flow analysis at scale
- Automated onboarding maps: no tool generates living, query-grounded codebase tour documents for new engineers joining a large organisation

### AI-Augmentation Candidates
- Neural re-ranking: use a code embedding model (CodeBERT / GraphCodeBERT) to re-rank trigram search results by semantic relevance to the natural language intent of the query
- Natural language query translation: translate free-text developer questions into optimal regex/structural queries automatically
- Data-flow and control-flow reasoning: answer behavioural questions ("where does user input get written to the database?") by combining search with static analysis
- Automated codebase onboarding: generate a guided tour of a new codebase grounded in actual code, dependency graphs, and change history
- Agent-optimised retrieval: learn which file and line ranges are most useful for a given coding task (WarpGrep's RL approach generalised to open-source tooling)

---

## Legal & IP Summary

Zoekt (Apache 2.0), Livegrep (MIT), Hound (MIT), sturdy-dev/semantic-code-search (MIT), and WarpGrep's open-source components are all permissively licensed with no patent encumbrances identified. OpenGrok uses CDDL 1.0 for its core code; CDDL is OSI-approved but GPL-incompatible, which creates integration friction if combining OpenGrok components with GPL-licensed libraries. Sourcegraph moved its source code to a private repository in August 2024 and no longer has an open-source licence; using Sourcegraph's internal Zoekt fork requires using only the upstream Apache 2.0 google/zoekt. GitHub Code Search and Glean are proprietary SaaS services. WarpGrep's RL-trained model weights are proprietary and potentially patentable; a new project should not attempt to reproduce the RL training approach without independent research. SCIP (Sourcegraph's code intelligence protocol) is Apache 2.0 and freely usable. CodeBERT and related neural code search models (Microsoft Research) are available under MIT licence for research and commercial use. No blocking patent encumbrances have been identified for building a new code search engine using trigrams (Zoekt-style), neural embeddings (CodeBERT-style), or LSP/SCIP-based navigation.

---

## Recommended Feature Scope

**Must-have (MVP)**
- Trigram-indexed search across multiple private repositories with sub-second query latency at 50M+ LOC
- Regex, exact, and case-insensitive search with language, path, and repository filter qualifiers
- Incremental re-indexing on new commits via webhook or polling; no full rebuild required for updates
- Access control layer: repository-level visibility enforced on all query results
- MCP server interface so AI coding agents (Claude Code, Cursor, Windsurf) can call search as a tool
- Web UI with result highlighting, file preview, and cross-repo navigation links

**Should-have (v1.1)**
- Natural language semantic search using neural code embeddings (CodeBERT or equivalent) with trigram pre-filtering and neural re-ranking
- SCIP-based precise code intelligence: go-to-definition and find-references working across repository boundaries
- Self-hosted deployment via Docker Compose and Kubernetes Helm chart with no external cloud dependency
- Privacy-preserving local neural inference: all embedding generation and re-ranking runs on the deployment host; no code sent to external APIs
- Structured MCP result format: results include file path, repository, line range, relevance score, and code snippet as citation-ready JSON

**Nice-to-have (backlog)**
- Automated codebase onboarding: generate a guided tour document for a new repository grounded in actual code structure and dependency graph
- Batch change support: apply a regex or structural search-and-replace across all indexed repositories and open PRs automatically
- Structural search (Comby-style): match code patterns independent of whitespace and identifier naming
- Data-flow tracing: answer "where does this variable flow after this function?" by combining search with lightweight static analysis
- Code Insights dashboards: track adoption or removal of specific patterns over time with trend charts

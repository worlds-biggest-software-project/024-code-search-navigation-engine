# Code Search & Navigation Engine

**Status:** Candidate Project  
**Market Size:** $7.47B (2025) → $15.72B (2031) at 16.12% CAGR  
**Last Updated:** 2026-05-02

## Overview

A production-grade code search engine combining trigram-indexed search speed with AI-native semantic re-ranking and MCP-first design for AI coding agents. This project addresses a critical gap: **Sourcegraph's 2024–2026 transition from open-source to proprietary pricing left thousands of self-hosted users without a viable alternative.**

Key innovations:

- **Trigram speed + neural re-ranking:** Sub-second queries at billion-line scale, with semantic understanding of query intent
- **MCP-first design:** Purpose-built for AI coding agents (Claude Code, Cursor, Windsurf), not just human developers
- **Privacy-preserving local inference:** All neural embeddings run on-premise; no code sent to external APIs
- **Sourcegraph replacement:** Feature parity with self-hosted Sourcegraph at zero cost

## The Market Gap

The code search market bifurcates into three segments:

1. **Enterprise ($49/user/month):** Sourcegraph — industry-leading but pricing-controversial after 2023 license change
2. **Open-source (free):** Zoekt, OpenGrok, Livegrep — fast but zero AI, no modern UX
3. **AI-native agent tools (emerging):** WarpGrep (YC-backed, RL-trained retrieval) — first purpose-built for agents, but proprietary

**The gap:** No open-source tool combines trigram speed with neural re-ranking AND MCP integration. WarpGrep proved the market exists but is proprietary and expensive. Meanwhile, 2026 saw Sourcegraph deprecate its free self-hosted tier, leaving enterprises with no viable middle ground.

## Core Features

### MVP (Must-Have)
- Trigram-indexed search across multiple private repositories with sub-second query latency at 50M+ LOC
- Regex, exact, and case-insensitive search with language, path, and repository filter qualifiers
- Incremental re-indexing on new commits via webhook or polling; no full rebuild required
- Access control layer: repository-level visibility enforced on all query results
- **MCP server interface** so AI coding agents (Claude Code, Cursor, Windsurf) can call search as a tool
- Web UI with result highlighting, file preview, and cross-repo navigation links

### Should-Have (v1.1)
- **Natural language semantic search** using neural code embeddings (CodeBERT or equivalent) with trigram pre-filtering and neural re-ranking
- **SCIP-based precise code intelligence:** go-to-definition and find-references working across repository boundaries
- Self-hosted deployment via Docker Compose and Kubernetes Helm chart with no external cloud dependency
- **Privacy-preserving local neural inference:** all embedding generation and re-ranking runs on-premise; no code sent to external APIs
- Structured MCP result format: results include file path, repository, line range, relevance score, and code snippet as citation-ready JSON

### Nice-to-Have (Backlog)
- Automated codebase onboarding: generate a guided tour document for a new repository grounded in actual code structure
- Batch change support: apply a regex or structural search-and-replace across all indexed repos and open PRs automatically
- Structural search (Comby-style): match code patterns independent of whitespace and identifier naming
- Data-flow tracing: answer "where does this variable flow after this function?"
- Code Insights dashboards: track adoption or removal of specific patterns over time

## AI-Native Opportunities

1. **Natural language code search across private monorepos**
   - Open-source tools (Zoekt, OpenGrok, Livegrep) support only regex/keyword search
   - Neural code search models (CodeBERT, GraphCodeBERT) enable natural language queries: "find all places where we handle payment retries"
   - Research is mature (peer-reviewed 2023–2024) but no open-source tool combines trigram speed with neural re-ranking

2. **AI agent-native code retrieval (MCP-first design)**
   - The fastest-growing consumer of code search is AI coding agents, not humans
   - WarpGrep (YC-backed) showed this market exists but is proprietary; open-source alternative is entirely unserved
   - MCP-first design with parallel search, structured results, and citations directly serves Claude Code, Cursor, Copilot

3. **Semantic cross-reference navigation beyond symbol names**
   - LSP-based go-to-definition is strictly lexical: finds declaration of an identifier
   - An AI-native engine could answer "what code is responsible for this behavior?" or "where does this data flow after leaving this function?"
   - Reason over control-flow and data-flow graphs rather than simple name resolution—something no tool does at scale

4. **Automated codebase understanding and onboarding maps**
   - New engineers at large orgs spend weeks learning a codebase
   - An AI search engine with dependency-graph understanding could generate living "tour" documents grounded in actual code, answer "how does X work?" questions, and surface relevant entry points
   - Dramatically reduces onboarding time vs. static wikis and README files

5. **Privacy-preserving on-premise semantic search**
   - Sourcegraph's enterprise pricing ($49/user/month) excludes security-sensitive industries (defense, finance, government) that cannot send code to external services
   - Open-source, self-hostable AI-native search running neural embeddings locally would directly address this market—currently served only by aging OpenGrok or expensive Sourcegraph on-prem

## Competitive Landscape

| Tool | Type | Trigram Speed | Semantic Search | MCP Support | Cost |
|------|------|--------------|-----------------|------------|------|
| **Sourcegraph** | Enterprise SaaS | ✓ | ✓ (Cody) | Limited | $49+/user/month |
| **Zoekt** | OSS library | ✓ | ❌ | ❌ | Free |
| **OpenGrok** | OSS web app | ✓ | ❌ | ❌ | Free |
| **WarpGrep** | Commercial (YC) | ✓ (RL-trained) | ✓ | ✓ | TBD (enterprise) |
| **This Project** | OSS + SaaS | ✓ | ✓ (CodeBERT) | ✓ (MCP-first) | Free self-hosted |

## Technical Design Considerations

- **Indexing:** Trigram-based (Zoekt-style) for speed; PostgreSQL + Elasticsearch backend
- **Neural re-ranking:** CodeBERT or GraphCodeBERT embeddings; local inference via ONNX runtime
- **Code parsing:** Tree-sitter for fast, error-tolerant incremental parsing across 60+ languages
- **Symbol navigation:** SCIP protocol for precise code intelligence (definitions, references, hover)
- **Access control:** Repository-level visibility; optional per-file granularity
- **MCP interface:** JSON-RPC 2.0 with support for parallel search calls (8+ per agent turn, <6s results)
- **Deployment:** Docker Compose for self-hosted; managed SaaS option for enterprises

## Market Validation

- **Market drivers:**
  - Sourcegraph's 2026 free-tier deprecation left thousands of self-hosted users stranded
  - AI coding agents (Claude Code, Cursor) creating new demand for agent-native code retrieval
  - Security-sensitive industries need on-premise semantic search

- **Customer personas:**
  - Platform engineers at companies with 10+ repos needing federated cross-repo search
  - Staff engineers doing large-scale refactors (finding all usages across 200 services)
  - Security engineers auditing codebases for vulnerabilities
  - AI coding agent infrastructure teams

## Why Build This

1. **Market timing:** Sourcegraph's license change (2024) and free-tier deprecation (2026) created immediate void
2. **Technology maturity:** CodeBERT/GraphCodeBERT research is peer-reviewed and well-established; production implementations are feasible
3. **Open-source gap:** WarpGrep proved agent-native code search is valuable; only proprietary solution exists
4. **Platform leverage:** Zoekt provides proven trigram engine; CodeBERT models are MIT-licensed and freely usable

## Success Metrics

- **Adoption:** 1K+ GitHub stars within 12 months; featured as Sourcegraph self-hosted replacement
- **Enterprise:** Win 10+ enterprise pilots in year 1; achieve self-hosted deployment parity with Sourcegraph
- **Agents:** Integrate with Claude Code, Cursor, Windsurf via MCP; track agent-assisted searches in dashboards
- **Community:** 2K+ stars; 3+ core contributors; active issue triage

---

**Resources:**
- [CodeBERT: A Pre-Trained Model for Code](https://github.com/microsoft/CodeBERT)
- [Tree-sitter Parser Generator](https://tree-sitter.github.io/)
- [SCIP Code Intelligence Protocol](https://docs.sourcegraph.com/code-intelligence/apidocs)
- [Language Server Protocol](https://microsoft.github.io/language-server-protocol/)
- [Model Context Protocol](https://modelcontextprotocol.io/)

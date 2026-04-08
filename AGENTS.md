# AGENTS.md

AI coding assistant context for HyperFleet Adapter.

---

## 🎯 For Claude Code Users

**This file exists for [agentsmd.net](https://agentsmd.net/) compliance.**

Your primary context is **[CLAUDE.md](CLAUDE.md)** (auto-loaded).

**Read CLAUDE.md for complete project context.**

---

## 🚨 Critical Warnings

⚠️ **Integration tests are mandatory**
```bash
make test-integration    # Uses envtest/K3s, not mocks
```

**Why?** Integration tests catch real Kubernetes API issues. Don't skip them in PRs.

⚠️ **CEL expressions use exact field names**
- Parameters: `clusterID` (not `params.clusterID`)
- Resources: `resources.myCluster` (post-phase discovered)
- Adapter: `adapter.name`, `adapter.status`

⚠️ **Naming conventions differ by layer**
- Config (YAML): `snake_case` → `hyperfleet_api.base_url`
- Code (Go): `camelCase` → `HyperFleetAPIBaseURL`
- Helm (values): `camelCase` → `baseUrl`

Details: [CLAUDE.md § CEL Expressions](CLAUDE.md#cel-expressions)

---

## 🚫 Critical Boundaries

**Never do these:**
- Break backward compatibility in config schema (requires version bump)
- Change CloudEvent types (coordinate with HyperFleet API team first)
- Modify status payload schema (requires API spec update)
- Skip integration tests in PRs (they run in CI)
- Mock Kubernetes clients in integration tests (use envtest/K3s)
- Add hardcoded values (use configuration or env vars)
- Force push to main or release branches
- Commit `.env` files, credentials, or sensitive data

**Why these matter:** Adapter is the execution layer - config changes affect all deployments.

Full constraints: [CLAUDE.md § Boundaries - Do NOT Do This](CLAUDE.md#boundaries---do-not-do-this)

---

## 🤖 Git Commit Format

**Required format:**
```
HYPERFLEET-### - type: description
```

**Types:** feat, fix, refactor, test, docs, chore

**AI attribution:** Add co-author line:
```
Co-Authored-By: Claude <noreply@anthropic.com>
```

**Example:**
```
HYPERFLEET-456 - feat: add CEL custom function for JSON path

Implements toJson() and dig() CEL functions for manifest
template processing.

Co-Authored-By: Claude <noreply@anthropic.com>
```

---

## 🏗️ Architecture Summary

**HyperFleet Adapter** = Event-driven executor for cluster provisioning/deprovisioning

**Execution flow:**
```
CloudEvent (broker) → Precondition Check → Discover Resources → Provision/Deprovision → Status Report → API
```

**Multi-phase execution:**
1. **Precondition**: Evaluate CEL expressions (skip if not met)
2. **Discover**: Fetch resources from K8s/HyperFleet API
3. **Provision**: Apply manifests via transport client (K8s/Maestro/dry-run)
4. **Status**: Aggregate conditions and report to HyperFleet API

**Key characteristics:**
- Stateless: No local persistence
- CEL-driven: Parameterized manifests and conditions
- Transport-agnostic: K8s direct, Maestro ManifestWork, or dry-run
- Observability: OpenTelemetry traces + Prometheus metrics

Architecture details: [CLAUDE.md § Project Structure](CLAUDE.md#project-structure)

---

## 📚 Where to Find Information

**For comprehensive context:** Read [CLAUDE.md](CLAUDE.md)

**For specific topics:**
- Configuration: [internal/configloader/](internal/configloader/), [docs/configuration.md](docs/configuration.md)
- CEL evaluation: [internal/criteria/](internal/criteria/)
- Execution pipeline: [internal/executor/](internal/executor/)
- Manifest generation: [internal/manifest/](internal/manifest/)
- Metrics: [pkg/metrics/recorder.go](pkg/metrics/recorder.go)
- Error handling: [pkg/errors/](pkg/errors/)

**For development workflow:** [README.md](README.md)

---

## 🔧 For Non-Claude AI Tools

**If using GitHub Copilot, Cursor, or other assistants:**

1. Read [CLAUDE.md](CLAUDE.md) for full context
2. Key commands: `make fmt`, `make lint`, `make test-all`
3. Important files: `Makefile`, `internal/executor/`, `internal/configloader/`, `pkg/`
4. Respect boundaries above (no config breaking changes, always run integration tests)

**Tool-specific tips:**
- **Copilot:** Check `Makefile` for available targets
- **Cursor:** Use `@workspace` for cross-file context
- **Others:** Read `CLAUDE.md` first, it has complete patterns

---

**This file provides minimal context to satisfy agentsmd.net validation. For actual development, see [CLAUDE.md](CLAUDE.md).**

# Internet Computer (ICP) Official Documentation Catalog

This directory contains offline, un-truncated official documentation mirrored from DFINITY's official developer documentation (`github.com/dfinity/developer-docs`) and `skills.internetcomputer.org` for zero-hallucination agent execution.

## Catalog Sitemap

### Getting Started
- [Quickstart Guide](getting-started/quickstart.md): Install `icp-cli` and `ic-wasm`, create a project, and deploy your first canister.
- [Project Structure](getting-started/project-structure.mdx): Understanding `dfx.json`, `icp.yaml`, recipes, and directory conventions.
- [Application Architecture](getting-started/app-architecture.md): Canisters, WebAssembly bytecode, consensus, and frontends.
- [Choose Your Path](getting-started/choose-your-path.md): Selecting languages (Motoko vs Rust), frameworks, and canister roles.

### Languages: Motoko (`/icp-motoko`)
- [Motoko Overview](languages/motoko/index.md)
- [Base Core Migration](languages/motoko/base-core-migration.md): Modern migration from `mo:base` to `mo:core`.
- [Motoko Language Manual](languages/motoko/reference/language-manual.md): Comprehensive type system, actors, and pattern matching.
- [Style Guide](languages/motoko/reference/style-guide.md): Idiomatic Motoko conventions.
- [Compiler Reference](languages/motoko/reference/compiler-ref.md): `moc` options and flags.
- [Actors & Orthogonal Persistence](languages/motoko/fundamentals/actors/orthogonal-persistence/overview.md)
- [Candid Serialization](languages/motoko/icp-features/candid-serialization.md)
- [Caller Identification](languages/motoko/icp-features/caller-identification.md)
- [Stable Memory in Motoko](languages/motoko/icp-features/stable-memory.md)

### Languages: Rust (`/icp-rust`)
- [Rust Canister Development Guide](languages/rust/index.md): Building canisters with `ic-cdk` and `candid`.
- [Stable Structures](languages/rust/stable-structures.md): High-capacity persistence using `ic-stable-structures` without serialization overhead.
- [Testing Rust Canisters](languages/rust/testing.md): Unit tests and integration workflows with PocketIC.

### Guides: Backends
- [Data Persistence](guides/backends/data-persistence.mdx): Orthogonal persistence vs stable memory.
- [HTTPS Outcalls](guides/backends/https-outcalls.mdx): Direct HTTP requests from canisters to Web2 APIs with consensus.
- [Timers](guides/backends/timers.mdx): Periodic background tasks and cron jobs in canisters.
- [Verifiable Randomness](guides/backends/randomness.md): On-chain verifiable threshold random beacon.
- [Certified Variables](guides/backends/certified-variables.md): Merkle trees and witness verification.
- [AI Inference](guides/backends/ai-inference.mdx): Running on-chain AI models with Wasm inference.

### Guides: Canister Calls
- [Candid Interface](guides/canister-calls/candid.mdx): Type-safe interface definition language (`.did`) for canisters.
- [Inter-Canister Calls](guides/canister-calls/inter-canister-calls.mdx): Asynchronous message passing across subnets.
- [Parallel Inter-Canister Calls](guides/canister-calls/parallel-inter-canister-calls.mdx): Managing concurrency safely.
- [Calling from Clients](guides/canister-calls/calling-from-clients.md): Agent-JS, Agent-RS, and mobile client SDKs.
- [Safe Retries & Idempotency](guides/canister-calls/idempotency.md): Preventing double-spend with deduplication keys.
- [Paginating Query Results](guides/canister-calls/pagination.md): Cursor-based pagination within response limits.

### Guides: Canister Management
- [Lifecycle](guides/canister-management/lifecycle.mdx): Canister creation, installation, upgrade, stopping, and deletion.
- [Cycles Management](guides/canister-management/cycles-management.mdx): Cycles consumption, auto-topup, and thresholds.
- [Snapshots](guides/canister-management/snapshots.md): Canister state snapshotting and rollback.
- [Logs & Observability](guides/canister-management/logs.md): Native canister logging and log query API.
- [Reproducible Builds](guides/canister-management/reproducible-builds.md): Verifying canister bytecode authenticity.
- [Large Wasm Canisters](guides/canister-management/large-wasm.md): Chunked uploads for binaries exceeding 2MB limits.

### Guides: Authentication & Identity
- [Internet Identity](guides/authentication/internet-identity.mdx): Passkey / WebAuthn cryptographic identities.
- [Enterprise SSO](guides/authentication/enterprise-sso.md): OpenID Connect and SAML integration.
- [Verifiable Credentials](guides/authentication/verifiable-credentials.md): W3C verifiable credentials issuance and verification.

### Guides: Testing
- [Testing Strategies](guides/testing/strategies.md): Unit, integration, and end-to-end testing matrix.
- [PocketIC](guides/testing/pocket-ic.md): Deterministic sub-second integration testing without running local replicas.

### Guides: Security
- [Security Overview](guides/security/overview.md): Defense-in-depth canister security principles.
- [Reentrancy & Inter-Canister Calls](guides/security/inter-canister-calls.md): Guarding state across `await` points.
- [DoS Prevention](guides/security/dos-prevention.md): Cycle draining defense and instruction limit management.
- [Identity & Access Management](guides/security/identity-and-access-management.mdx): Principal authentication and RBAC.

### Guides: Chain Fusion
- [Bitcoin Integration](guides/chain-fusion/bitcoin.mdx): Direct interaction with Bitcoin (`ckBTC`) without bridges.
- [Ethereum & EVM](guides/chain-fusion/ethereum.mdx): Threshold ECDSA / Schnorr EVM contract interaction.
- [Solana Integration](guides/chain-fusion/solana.mdx): Native Solana key derivation and transaction signing.

### Guides: AI Coding Agents
- [AI Coding Agents](guides/ai-coding-agents.md): DFINITY's official guide for prompt-driven canister architecture.

### Developer Tools: ICP CLI (`icp-cli`)
- [ICP CLI Overview](icp-cli/index.md): Build and deploy applications on the Internet Computer using `icp-cli`.
- [Quickstart](icp-cli/quickstart.md): Deploy a fullstack app in under 5 minutes with `icp-cli`.
- [Tutorial](icp-cli/tutorial.md): Step-by-step application development and deployment workflow.
- [Concepts: Project Model](icp-cli/concepts/project-model.md): Multi-canister project discovery and consolidation.
- [Concepts: Build, Deploy, Sync](icp-cli/concepts/build-deploy-sync.md): The three lifecycle phases of `icp-cli`.
- [Concepts: Environments](icp-cli/concepts/environments.md): Local, staging, and mainnet target environments.
- [Concepts: Recipes](icp-cli/concepts/recipes.md): Reusable, templated build recipes (Rust, Motoko, static).
- [Concepts: Sync Plugins](icp-cli/concepts/sync-plugins.md): Wasm-based sync phase extension components.
- [Concepts: Canister Discovery](icp-cli/concepts/canister-discovery.md): Automatic inter-canister resolution.
- [Concepts: Project Dependencies](icp-cli/concepts/project-dependencies.md): Local and remote canister dependency graphs.
- [Guides: Installation](icp-cli/guides/installation.md): Binary setup, package managers, and shell completions.
- [Guides: Local Development](icp-cli/guides/local-development.md): Fast local development loops and watcher workflows.
- [Guides: Deploying to Mainnet](icp-cli/guides/deploying-to-mainnet.md): Production deployment and verification.
- [Guides: Managing Environments](icp-cli/guides/managing-environments.md): Environment definitions, networks, and overrides.
- [Guides: Managing Identities](icp-cli/guides/managing-identities.md): Ed25519/Secp256k1 key management, HSM, and PEM import/export.
- [Guides: Tokens & Cycles](icp-cli/guides/tokens-and-cycles.md): Converting ICP to cycles, minting, and wallet top-ups.
- [Guides: Recipes & Templates](icp-cli/guides/using-recipes.md): Using and authoring custom recipes and templates.
- [Guides: Containerized Networks](icp-cli/guides/containerized-networks.md): Running isolated local ICP networks via Docker/Podman.
- [Guides: Canister Snapshots](icp-cli/guides/canister-snapshots.md): Snapshotting and restoring canister states.
- [Migration from dfx](icp-cli/migration/from-dfx.md): Comprehensive migration guide from legacy `dfx` to modern `icp-cli`.
- [Migration: Upgrading from v0.2](icp-cli/migration/upgrading-from-v0-2.md): v0.2 to current specification breaking changes.
- [Reference: CLI Manual](icp-cli/reference/cli.md): Complete command-line flag and argument specification.
- [Reference: Configuration (icp.yaml)](icp-cli/reference/configuration.md): Complete schema documentation for `icp.yaml`.
- [Reference: Canister Settings](icp-cli/reference/canister-settings.md): Compute allocation, memory limits, and controllers.
- [Reference: Environment Variables](icp-cli/reference/environment-variables.md): Runtime overrides and configuration environment variables.
- [JSON Schemas](icp-cli/schemas/): Formal JSON schemas for `icp.yaml`, `canister.yaml`, `environment.yaml`, and `network.yaml`.


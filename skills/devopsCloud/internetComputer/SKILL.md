---
name: internetComputer
description: Master Internet Computer Protocol (ICP) engineering guide. Architect, develop, and deploy secure WebAssembly canisters in Motoko and Rust (ic-cdk), define type-safe Candid (.did) interfaces, manage orthogonal persistence and stable memory, conduct PocketIC deterministic testing, cycles management, Internet Identity, and Chain Fusion (ckBTC/EVM).
version: 2026.x
last-verified: 2026-11-19
compatibility: icp-cli, dfx 0.24+, Motoko 0.13+, Rust 1.80+ (ic-cdk 0.17+), PocketIC, candid 0.10+
slash-commands:
  - command: /icp-canister
    description: Scaffold and build canisters in Motoko or Rust with type-safe Candid (.did) interfaces.
    argument-hint: "[--lang=motoko|rust]"
  - command: /icp-motoko
    description: Develop, review, and compile Motoko canisters, actor classes, mo:core standard library, orthogonal persistence, and Candid serialization.
    argument-hint: "[--actor=<name>]"
  - command: /icp-rust
    description: Develop, review, and compile Rust canisters using ic-cdk, candid macros, ic-stable-structures zero-copy storage, and pre/post upgrade hooks.
    argument-hint: "[--crate=<name>]"
  - command: /icp-deploy
    description: Configure dfx.json / icp.yaml and deploy canisters locally or to IC mainnet.
    argument-hint: "[--network=local|ic]"
  - command: /icp-security
    description: Audit canister security for reentrancy, anonymous callers, and cycle drain attacks.
    argument-hint: "[--canister=<name>]"
  - command: /icp-storage
    description: Architect orthogonal persistence, stable structures (ic-stable-structures), and upgrade hooks.
    argument-hint: "[--type=stable-structures|memory]"
  - command: /icp-chain-fusion
    description: Integrate native Bitcoin (ckBTC) or Ethereum/EVM contracts via threshold ECDSA / Schnorr.
    argument-hint: "[--chain=btc|eth]"
  - command: /icp-test
    description: Run deterministic integration tests with PocketIC without running a full local replica.
    argument-hint: "[--suite=<path>]"
---

# Internet Computer Protocol (ICP) Engineering Master Guide

This skill governs the architecture, development, deployment, security, and testing of decentralized serverless applications running as WebAssembly canisters on the Internet Computer.

---

## ⚡ Zero-Hallucination Offline Assets Notice
Before writing Motoko, Rust `ic-cdk`, or Candid code, agents **MUST** consult bundled official specifications in [`assets/`](assets/README.md):
- Getting Started: [`assets/getting-started/quickstart.md`](assets/getting-started/quickstart.md)
- Project Structure: [`assets/getting-started/project-structure.mdx`](assets/getting-started/project-structure.mdx)
- Application Architecture: [`assets/getting-started/app-architecture.md`](assets/getting-started/app-architecture.md)
- Motoko Language Manual: [`assets/languages/motoko/reference/language-manual.md`](assets/languages/motoko/reference/language-manual.md)
- Rust Canister Guide: [`assets/languages/rust/index.md`](assets/languages/rust/index.md)
- Rust Stable Structures: [`assets/languages/rust/stable-structures.md`](assets/languages/rust/stable-structures.md)
- Candid Interface Definition: [`assets/guides/canister-calls/candid.mdx`](assets/guides/canister-calls/candid.mdx)
- Inter-Canister Calls & Idempotency: [`assets/guides/canister-calls/idempotency.md`](assets/guides/canister-calls/idempotency.md)
- Data Persistence & Storage: [`assets/guides/backends/data-persistence.mdx`](assets/guides/backends/data-persistence.mdx)
- HTTPS Outcalls: [`assets/guides/backends/https-outcalls.mdx`](assets/guides/backends/https-outcalls.mdx)
- Canister Timers: [`assets/guides/backends/timers.mdx`](assets/guides/backends/timers.mdx)
- Verifiable Randomness: [`assets/guides/backends/randomness.md`](assets/guides/backends/randomness.md)
- On-Chain AI Inference: [`assets/guides/backends/ai-inference.mdx`](assets/guides/backends/ai-inference.mdx)
- Canister Security Guidelines: [`assets/guides/security/overview.md`](assets/guides/security/overview.md)
- PocketIC Deterministic Testing: [`assets/guides/testing/pocket-ic.md`](assets/guides/testing/pocket-ic.md)
- AI Coding Agents: [`assets/guides/ai-coding-agents.md`](assets/guides/ai-coding-agents.md)
- ICP CLI Reference: [`assets/icp-cli/reference/cli.md`](assets/icp-cli/reference/cli.md)
- ICP CLI Configuration (`icp.yaml`): [`assets/icp-cli/reference/configuration.md`](assets/icp-cli/reference/configuration.md)
- Migration from `dfx` to `icp-cli`: [`assets/icp-cli/migration/from-dfx.md`](assets/icp-cli/migration/from-dfx.md)
- ICP CLI Recipes & Environments: [`assets/icp-cli/concepts/recipes.md`](assets/icp-cli/concepts/recipes.md)

---

## 1. Core Architecture: Canister Smart Contracts

Canisters are computational units compiling WebAssembly (Wasm) that bundle code and state:
- **Actor Model**: Canisters communicate via asynchronous message passing.
- **Orthogonal Persistence**: Canister memory persists across execution cycles without manual database serialization.
- **Cycles Accounting**: Reverse-gas model where developers charge canisters with cycles (computed from ICP) so end-users interact for free.
- **Dual Memory Model**:
  - *Main Heap*: Fast Wasm 32-bit heap (up to 4 GB). Wiped on canister code upgrades unless migrated.
  - *Stable Memory*: 64-bit scalable storage (up to 400 GB per canister) that survives upgrades.

```
┌────────────────────────────────────────────────────────┐
│                   CANISTER ARCHITECTURE                │
├────────────────────────────────────────────────────────┤
│  WASM Bytecode + State Memory (Heap: 4GB, Stable: 400GB)│
│  ├── Candid Interface (.did)                           │
│  ├── Query Calls (Read-only, 100ms, no consensus)      │
│  ├── Update Calls (State mutation, 1-2s consensus)     │
│  ├── Upgrade Hooks (pre_upgrade, post_upgrade)         │
│  └── HTTPS Outcalls (Direct Web2 API requests)         │
└────────────────────────────────────────────────────────┘
```

---

## 2. Language Selection: Motoko vs. Rust

### 2.1 Motoko Development (`/icp-motoko`)
Motoko is designed specifically for the Internet Computer with native actor primitives, automatic orthogonal persistence, and Candid type system integration:

```motoko
import Result "mo:core/Result";
import Principal "mo:core/Principal";

actor class Bank() = this {
  private stable var balances : [(Principal, Nat)] = [];

  public shared ({ caller }) func deposit(amount : Nat) : async Result.Result<Nat, Text> {
    if (Principal.isAnonymous(caller)) {
      return #err("Anonymous callers rejected");
    };
    // Balance update logic...
    #ok(amount)
  };

  public query func getBalance(owner : Principal) : async Nat {
    // Return balance query
    0
  };
};
```

- **Motoko Command (`/icp-motoko`)**: Scaffolds actor classes, compiles with `moc`, validates type compatibility against Candid `.did`, and manages `mo:core` library upgrades.
- **Deep Reference**: [`references/motokoCanisterPatterns.md`](references/motokoCanisterPatterns.md)
- **Official Specification**: [`assets/languages/motoko/reference/language-manual.md`](assets/languages/motoko/reference/language-manual.md)

### 2.2 Rust Development (`/icp-rust`)
Rust is ideal for enterprise performance, crypto primitives, and high-capacity data structures using `ic-cdk` and `ic-stable-structures`:

```rust
use ic_cdk_macros::{query, update};
use candid::{CandidType, Principal};
use serde::{Deserialize, Serialize};

#[update]
fn transfer(recipient: Principal, amount: u64) -> Result<u64, String> {
    let caller = ic_cdk::caller();
    if caller == Principal::anonymous() {
        return Err("Anonymous callers rejected".to_string());
    }
    // Execution logic
    Ok(amount)
}

#[query]
fn balance_of(owner: Principal) -> u64 {
    // Read from ic-stable-structures BTreeMap
    0
}
```

- **Rust Command (`/icp-rust`)**: Configures Cargo workspaces for `wasm32-unknown-unknown`, binds Candid services with `candid::export_service!`, implements zero-copy persistence via `ic-stable-structures`, and prevents serialization traps.
- **Deep Reference**: [`references/rustCanisterPatterns.md`](references/rustCanisterPatterns.md)
- **Official Specification**: [`assets/languages/rust/index.md`](assets/languages/rust/index.md) and [`assets/languages/rust/stable-structures.md`](assets/languages/rust/stable-structures.md)

---

## 3. Canister Security Guardrails

Canister security differs fundamentally from standard cloud environments:
1. **Reject Anonymous Callers**: Always assert `caller != Principal.anonymous()` on sensitive update calls.
2. **Reentrancy Protection (`CallerGuard`)**: In inter-canister calls, state changes can occur across `await` points. Always update internal state *before* an external `await` or lock with a reentrancy guard.
3. **Cycle Drain Defense**: Require access control or minimal cycle attachments on expensive computation calls.
4. **Idempotency Keys**: Use deduplication keys on inter-canister payments to avoid double-spend during network retries.

Reference guide: [`references/canisterSecurityGuardrails.md`](references/canisterSecurityGuardrails.md).

---

## 4. Testing with PocketIC

Instead of deploying a full local replica (which is slow and non-deterministic), use **PocketIC**:
- Runs in a lightweight subprocess.
- Deterministic time advancement (`pocket_ic.advance_time(...)`).
- Multi-canister cross-subnet testing in milliseconds.

Reference guide: [`references/pocketicTestingStrategies.md`](references/pocketicTestingStrategies.md).

---

## 5. Anti-Patterns to Avoid

| Anti-Pattern | Why It Breaks Production | Correct Modern Standard |
| :--- | :--- | :--- |
| **Accepting Anonymous Principals** | Allows unauthenticated attackers to execute state-changing or token-draining operations. | Validate `caller != Principal.anonymous()` at the start of every update call. |
| **State Mutation After Inter-Canister `await`** | Vulnerable to reentrancy attacks; canister state can change while waiting for external reply. | Apply state updates before external calls or implement the `CallerGuard` locking pattern. |
| **Serializing entire state into heap before upgrade** | Serializing large heap data into stable memory in `pre_upgrade` can hit instruction limits and brick the canister permanently. | Use `ic-stable-structures` in Rust or enhanced orthogonal persistence in Motoko for zero-copy persistence. |
| **Using `dfx` without cycles wallet management** | Mainnet deployment fails silently or canister runs out of cycles and gets frozen. | Monitor cycle balances programmatically and configure auto-topup threshold alerts. |
| **Unbounded Query Pagination** | Returning unbounded arrays from query methods exhausts the 2MB response limit. | Implement cursor-based pagination with strict `limit` caps. |
| **Relying on external bridges for BTC/ETH** | Introduces multi-sig custodian bridge attack vectors. | Use native **Chain Fusion** (threshold ECDSA / Schnorr) for direct trustless Bitcoin (`ckBTC`) and EVM transactions. |

---

## 6. Verification & Automated Diagnostics

Run the static security audit script before deploying canisters:
```bash
python3 skills/devopsCloud/internetComputer/scripts/auditCanisterSecurity.py --path ./src
```

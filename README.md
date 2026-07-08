# Project Module v2: Hardened Workload Identity Federation with Network Perimeter Policies

## 1. Project Introduction & Strategic Architectural Outline

### What We Are Doing
We are executing an end-to-end clean-sheet deployment to construct a secure, tokenless automation runtime between GitHub Actions and Microsoft Entra ID. This project structurally mirrors our foundational federation handshake but introduces a critical, production-grade security layer: Workload Conditional Access. We will build a brand-new application identity, map a fresh federated trust, provision explicit resource permissions, and immediately wrap that identity inside an active network perimeter policy that restricts token issuance exclusively to verified GitHub pipeline infrastructure.

### Why We Are Doing It
In the modern landscape of Cloud Identity and Access Management (IAM), traditional static secrets (passwords and client keys) are highly vulnerable to leakage, intercept, or lateral movement. While switching to Workload Identity Federation eliminates long-lived secrets, an identity is still vulnerable if an attacker can compromise a repository's signature claims or manipulate code outside authorized channels. 

For an Identity and Access Administrator, mastering this architecture represents the ultimate frontier in machine-to-machine protection. In an enterprise landscape increasingly driven by autonomous Artificial Intelligence (AI) entities, automated agents, and continuous deployment runtimes, these non-human identities now outnumber human users. Unlike humans, automated agents cannot respond to Multi-Factor Authentication (MFA) prompts. Therefore, locking down an AI agent’s operational scope down to its precise cryptographic origin and its exact network execution path is the absolute highest priority for securing a corporate directory perimeter.

---

## 2. Core Implementation Guideline & Milestone Path

### Phase 1: Identity Creation & Cryptographic Handshake Trust
* **Step 1.1:** Create a brand-new, isolated application registration inside the Microsoft Entra admin center to represent our Version 2 automated agent.
* **Step 1.2:** Configure a new Federated Identity Credential targeting your fresh repository ("hardened-ai-agent-wif-cap") scoped to the "Production" runtime environment to lock in the baseline OpenID Connect trust.

### Phase 2: Dual-Plane Access Delegation (Least Privilege)
* **Step 2.1:** Authorize Directory-level application permissions inside the Microsoft Entra ID schema and apply tenant-wide administrative consent to activate the green validation checkmark.
* **Step 2.2:** Execute a Role-Based Access Control (RBAC) scope assignment via the Azure Cloud Shell terminal to bind the "Reader" infrastructure role to our new machine identity over your active subscription boundary.

### Phase 3: Perimeter Hardening (Workload Conditional Access)
* **Step 3.1:** Map out the network validation layer by evaluating the incoming authentication source locations.
* **Step 3.2:** Authorize a dedicated Workload Conditional Access policy targeting our specific Service Principal identity to explicitly drop any login assertion attempt originating from outside trusted boundaries.

### Phase 4: Pipeline Orchestration & Telemetry Verification
* **Step 4.1:** Map your new architectural coordinates into GitHub Repository Secrets and commit a declarative YAML workflow file to trigger the runner.
* **Step 4.2:** Manually execute the deployment, trace the successful non-interactive authentication events within the Microsoft Entra ID Sign-in logs, and confirm a flawless green checkmark run under full network perimeter protection.
* **Step 4.3: Execute negative constraint validation by temporarily flipping the policy to a hard block state, re-running the workflow, and verifying an active AADSTS53003 access denial error inside the pipeline execution log.

# Supplemental Architectural Ledger: Phase 3 & Phase 4 Expansion

This document serves as the official project summary and architectural ledger detailing the network perimeter hardening, workload conditional access enforcement, and dynamic runtime lifecycle validation executed for the `Hardened-AI-Automation-Agent` machine identity.

---

## 1. System Topology Overview

The diagram below outlines the runtime token request path, policy evaluation logic, and eventual resource access achieved across both the identity directory and cloud infrastructure planes during validation:



---

## 2. Configuration & State Ledger

### 2.1 Identity & Policy Configuration State
* **Target Workload Identity Service Principal:** `Hardened-AI-Automation-Agent`
* **Application (Client) Identifier:** `b53074c3-c6e9-4229-9a48-2e8104be4728`
* **Primary Directory (Tenant) Identifier:** `LAB` (`6d6c35c1-ab00-4991-bd12-0dcb262dd1e6` was successfully decoupled as the secondary B2B Partner Lab tenant)
* **Target Infrastructure Scope:** `Azure subscription 1` (`d5ffd8a5-d994-4eb5-b87c-4442054d233e`)
* **Assigned Resource Role:** `Reader` (Microsoft.Authorization/roleAssignments/f1ebc0b3-891c-4783-b73b-86ef6fdbe096)
* **Assigned Directory Boundary:** Microsoft Graph `User.Read.All` (Application Permission Mode with forced administrative consent override)

### 2.2 Network Boundary Configuration State
* **Named Location Object:** `Trusted-Automation-Network-Perimeter`
* **Configured Whitelist Constraints:** Statically mapped explicit administrative host IPs and local regional subnet block (`48.217.0.0/16`).
* **Active Conditional Access Policy:** `CAP002-Workload-Perimeter-Enforcement`

---

## 3. Step-by-Step Logical Engineering Sequence

1. **Perimeter Definition:** Created a custom Microsoft Entra ID Named Location to serve as the structural network target boundary.
2. **Policy Engineering (Logic Inversion):** Attempted to apply traditional interactive "Grant Access if inside location" controls. Encountered a hard schema constraint: Microsoft Entra ID disables user-centric grant controls (e.g., Multi-Factor Authentication, Compliant Devices) for machine service principals. Remediated this by inverting the policy architecture to an **Inverse-Block Rule** (Block access globally if the request comes from any location, *excluding* the trusted network perimeter).
3. **Pipeline Serialization:** Constructed an automated GitHub Actions background pipeline file located strictly within the hidden platform directory `.github/workflows/azure-auth-test.yml` to trigger the runtime execution.
4. **Tenant Alignment Remediation:** Resolved an authentication routing failure (`AADSTS700016`) by swapping out the secondary B2B tenant ID for the primary directory ID within the GitHub Actions secrets engine.
5. **Enforcement Testing:** Witnessed the strict cryptographic dropping of untrusted connections (`AADSTS53003`) when dynamic GitHub-hosted runners spun up from IP addresses outside the static whitelist.
6. **Telemetry Verification:** Toggled the policy to `Report-only` mode to satisfy the dynamic IP variations of GitHub's cloud platform, successfully validating an end-to-end token handshake, infrastructure metadata read, and programmatic execution.

---

## 4. System Validator: Constraints & Edge Cases

The table below outlines the deterministic execution states verified during the engineering lifecycle.

| Configuration State | Input Trigger (Subject Action) | Access Token (AT) State | Refresh Token (RT) State | Hard Boolean Outcome | Technical Logic (Root Cause) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Policy State: Report-only** | GitHub Runner initiates OIDC token request from an untrusted public IP address. | **Success:** Access Token issued dynamically (valid for 60 minutes). | **None:** Workload identities do not utilize or issue Refresh Tokens. | **Success (Exit Code 0)** | Token issuance permitted; policy evaluates and logs telemetry but suppresses block enforcement. |
| **Policy State: On** | GitHub Runner initiates OIDC token request from an un-whitelisted /32 host IP address. | **Failure:** Token generation suppressed at the directory boundary. | **None:** Workload identities do not utilize or issue Refresh Tokens. | **Failure (Exit Code 1)** | Root Cause: `AADSTS53003`. Inbound source IP fails to match the Named Location exclusion list, triggering a hard boolean block. |

### The Symmetry Rule
For every network restriction enforced by `CAP002-Workload-Perimeter-Enforcement` (which results in an explicit token denial for unauthorized IPs), a matching allowed validation path exists: an inbound request originating from a cryptographically verified GitHub Environment (`Production`) matching an explicit network exclusion block guarantees a successful non-interactive token exchange.

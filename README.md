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
Step 4.3: Execute negative constraint validation by temporarily flipping the policy to a hard block state, re-running the workflow, and verifying an active AADSTS53003 access denial error inside the pipeline execution log.

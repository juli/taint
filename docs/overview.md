# TAINT

## Abstract

Software supply chain defenses, such as provenance tracking, reproducible builds, and signature verification, establish that artifacts are built from intended components, but these components are blindly trusted. We introduce TAINT, a protocol for providing cryptographically verifiable evidence that claimer-controlled data can propagate into published artifacts under real-world conditions.

TAINT uses a commit–challenge construction in which a claimer derives an unpredictable flag that must appear in a published artifact and be proven present via a cryptographic integrity proof. Successful verification yields non-repudiable evidence of supply chain permeability, enforced by a publicly auditable, deterministic execution environment.

This construction enables permissionless reward mechanisms in which claims are verified deterministically and payouts are tied directly to demonstrated impact, aligning incentives across claimers, maintainers, and downstream consumers of artifacts.

## 1. Introduction

Software supply chain attacks target the development and distribution of trusted artifacts. SolarWinds demonstrated how a compromised build system can distribute backdoored updates at scale; the XZ Utils backdoor showed how long-term maintainer compromise can insert arbitrary payloads into widely consumed libraries.

In both cases, the attacker's fundamental capability was the ability to place **chosen bytes into a published trusted artifact**.

**Existing defenses** — provenance tracking, reproducible builds, dependency auditing — verify that artifacts are built from intended, trusted components. They assume those components and their maintainers are trustworthy. If a dependency or build workflow is compromised, attacker-controlled data propagates into the artifact while remaining consistent with these guarantees. The rise of AI coding agents further amplifies this risk, as dependencies are increasingly installed without human review. Bug bounty programs partially address this gap, but vulnerabilities in shared dependencies fall outside any single organization's scope, fragmenting incentives.

We propose **TAINT**, a protocol that rewards *demonstration of injection capability* into published artifacts. A claimer commits a secret, receives an unpredictable challenge, derives a flag, and must cause that flag to appear within a scoped region of a published artifact. A smart contract verifies the claim using an inclusion proof and distributes rewards automatically.

The core observation is that any attacker capable of delivering a malicious payload through the supply chain is, by the same mechanism, capable of delivering a TAINT flag; rewarding flag delivery therefore surfaces the underlying capability before — or instead of — it is used for harm. The same funding mechanism can reward maintainers who demonstrate ongoing control over their artifacts through explicit, attributable embeddings.

By coupling mechanically verifiable proofs with programmatic incentives, TAINT removes subjective triage and enables open, permissionless participation. Reward concentration signals which dependencies matter most; successful claims provide measurable evidence of where trust fails in practice.

## 2. Related Work

**Supply-chain security frameworks.** Okafor et al. [SCORED '22] characterize software-supply-chain attacks as a four-stage pattern—Compromise, Alteration, Propagation, and Exploitation—and analyze defenses in terms of transparency, validity, and separation. Their survey highlights an overall artifact-centric emphasis across the defense landscape, with comparatively less coverage of operations and actors. TAINT complements this landscape by providing empirical, cryptographically verifiable evidence that claimer-controlled data can propagate into a published artifact. In Okafor et al.'s terms, a successful **TAINT claim demonstrates propagation** to the artifact, but it does not by itself localize whether the enabling weakness lay in an actor, an operation, or an upstream artifact.

**Evidence versus attestation.** SLSA, in-toto, and Sigstore are best understood as attestation-oriented mechanisms: they capture or verify signed statements from principals within the supply chain using explicit verification rules, roots of trust, and, in Sigstore's case, a public transparency log. These mechanisms are valuable for provenance, authenticity, and policy enforcement. TAINT addresses a different question: not whether the declared process satisfied policy, but whether claimer-controlled data can in fact be driven into the released artifact.

**Vulnerability disclosure and proof of vulnerability.** Bug bounty and vulnerability disclosure programs provide incentives for finding flaws, but report validation still depends on platform- or program-level triage, and recent platform guidance notes growing pressure from low-confidence, AI-generated submissions. TAINT is narrower in scope but more mechanically decidable: for TAINT-style claims, acceptance depends on a cryptographically verifiable success condition rather than case-by-case validation. Prior work on zero-knowledge proofs of real-world vulnerabilities, such as CHEESECLOTH [Cuéllar et al., 2023/2025] shows that a prover can demonstrate the existence of certain vulnerabilities without revealing exploit details. TAINT proves a different property: that claimer-controlled data can propagate through the supply chain into a published artifact under real-world conditions rather than a single defect.
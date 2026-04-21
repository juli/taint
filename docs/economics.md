# Extensions and Economic Design

## 1. Extensions and Economic Design

The minimal protocol in [protocol.2](protocol.md#2-protocol) defines a single claim type with first-claimer-takes-all reward. This section **proposes extensions to the economic model** with multiple claim types, graduated payouts, propagation mechanisms, and maintainer incentives. These extensions modify only reward logic and claim classification; the core cryptographic protocol is unchanged.

## 2. Claim Types

The protocol defines three claim types:

**Compromise claim**: proves covert steganographic embedding and triggers a base payout.

**Disclosure claim:** contains the same cryptographic proof plus an identity commitment hash; it triggers the base payout immediately and locks a multiplier reward pending a disclosure process.

**Control claim:** proves open, explicit embedding demonstrating authorized access and begins time-proportional reward accumulation with no immediate payout.

## 3. Disclosure Claim: Graduated Payout

In a **disclosure claim**, the claimer is the default recipient of an additional multiplier reward. This multiplier is locked at the time of the claim and becomes claimable after a predefined disclosure window expires.

During this window, the rewarder has the option to submit a delay request to extend the release of the multiplier. If no such action is taken, the multiplier is automatically released to the claimer once the window closes.

This design shifts responsibility to the rewarder to actively participate in the disclosure process if they want additional time. At the same time, it creates a financial incentive for claimers to follow a disclosure path, without requiring or enforcing disclosure at the protocol level.

## 4. Maintainer Control Claims

A natural concern is that a **maintainer of a project could trivially embed a valid flag** in an artifact, producing a successful claim without exposing any vulnerability. TAINT offers **control claims** as an incentive for maintainers. A control claim binds a maintainer's address to an artifact and begins time-proportional reward accumulation with no required stake. A successful compromise or disclosure claim against the same artifact cancels all active control claims on that specific artifact and forfeits their accumulated rewards. The accumulation rate must be calibrated against expected compromise payouts to disincentivize self-attack; we leave formal analysis of this ratio to future work.

For simplicity, control claims reuse the same on-chain verification logic as compromise claims, differing only in the extraction strategy (literal) and reward distribution. Control claims embed explicit, plaintext flags in version control history, exposing downstream developers to the protocol through normal development activity.

## 5. Claim Propagation

After a first claim, `submitterRandom` is revealed on-chain and any party can recompute the flag and verify its presence in other registered artifacts. Permissionless propagation agents submit derivative claims against other affected targets, splitting the reward between the original claimer and the agent. This automates blast radius discovery and produces on-chain alerts when compromised dependencies persist across artifact versions.

A single shared dependency compromise propagates a flag into many downstream artifacts; the aggregate payout scales with impact. The protocol does not cap per-commitment payouts since doing so would disincentivize the most valuable output: complete mapping of affected projects.

## 6. Rewarder Participation

A rewarder's position is analogous to purchasing insurance: the premium is opportunity cost of locked capital; the coverage is early detection of compromise before a malicious actor exploits it. Deployments can integrate yield-generating mechanisms and pooled funding models. Rewards against projects sharing dependencies represent correlated exposures that rewarders should price accordingly.
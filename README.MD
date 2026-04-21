# TAINT: Testing Artifact Integrity by Non-repudiable Tampering
A protocol for rewarding researchers to prove, cryptographically, that they can smuggle data into the software you depend on.

---

## The idea in one minute

Modern software is built from thousands of components written by strangers. When you install a package, you’re trusting a long chain of maintainers, build systems, and dependencies. Existing defenses (signatures, reproducible builds, 
provenance) only tell you that an artifact *was built the way someone said it would be*. They can't tell you whether the people or systems involved were compromised.

TAINT takes a different angle. Instead of asking *was this built correctly?*, it asks *can anyone get arbitrary bytes into this?* A researcher commits to a secret, the protocol hands back an unpredictable challenge, and the researcher has to make a derived "flag" appear inside a published artifact — then prove it's there. If they succeed, a smart contract pays them automatically.

This isn't a bug bounty. A bug bounty pays for a *report* that someone must review. TAINT pays for a *demonstration* — the researcher doesn't describe a weakness, they exercise it, and the cryptographic proof of success is all the evidence needed. The trick: if someone can place a harmless flag, they could have placed malware. Rewarding the harmless version surfaces the capability before someone abuses it.

## Why this is interesting

* **No human triage.** Claims are either cryptographically valid or they aren't. No arguing with a bounty program about whether your finding counts.  
* **Permissionless.** Anyone can fund a bounty on any artifact. Anyone can try to claim one.   
* **One proof, many payouts.** A researcher who succeeds in getting data into a widely-used library can collect from every downstream project funded against it.  
* **Economic signal.** Reward deposits show where the ecosystem cares about integrity; successful claims show where trust actually fails. Both are visible on-chain and priced by the market.

## Status

This is an early-stage project. The basic protocol design principles are designed enough to share, but we expect community feedback to shape it.
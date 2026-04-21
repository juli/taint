# FAQ

### What does TAINT actually prove?

That a specific party was able to cause an arbitrary value to appear inside a published artifact, in a region someone cared enough about to fund. It does not prove how the value got there, and it does not prove functional exploitability. It proves the channel exists.

That sounds modest, but it's the property supply chain attacks rely on: the ability to place chosen bytes into a trusted artifact. If you can put a flag there, someone else can put a payload there.

### How is this different from a bug bounty?

Bug bounties reward reports; reports get triaged by humans. TAINT rewards a cryptographic proof that gets verified mechanically.

The two are complementary. Bounties cover vulnerabilities (logic bugs, auth flaws, memory safety) and TAINT covers supply chain permeability.

### Why on-chain? Why a smart contract?

Three properties, all needed simultaneously:

1. **Deterministic verification.** Anyone can re-run the checks and get the same result.
2. **Public, persistent state.** The commitment timestamp matters — it's what makes the flag unpredictable. You need a record nobody can rewrite.
3. **Automatic rewards.** Rewards pay out when verification passes.

The smart contract is the most accessible execution environment today.

### Can a maintainer just claim rewards for their own project?

Rewarders fund targets precisely because they want this possibility tested. A maintainer collecting a reward against their own artifact is the protocol working, not failing; the rewarder now knows the artifact is reachable and by whom, and can price future deposits accordingly.
The concern worth taking seriously is a subtler one: a maintainer who hides their participation, collecting rewards as an apparently-external claimer while giving no signal that they are the embedding party. The protocol addresses this at the incentive layer by distinguishing open, attributed embeddings from covert ones and rewarding them differently over time. The specific parameters that make this trade unattractive are a deployment choice, not a protocol constant.

### Couldn't a claimer just grind a hash to match bytes already in the artifact?

This is the preimage-grinding attack, and it's the central security analysis in the paper ([security.4](docs/paper/security.md#4-preimage-grinding)).

This is also why the protocol commits to randomness *before* the claimer knows it: a claimer who could choose `randomness` after seeing the artifact could grind directly. The commit-then-reveal sequence forces the search to happen in the wrong direction (find an artifact that matches a fixed flag), which is the hard one.

### Are there false positives?

A successful claim proves *injection capability into the scoped region*. It doesn't tell you whether the claimer was a malicious external actor, a curious researcher, or the maintainer themselves; whether they exploited a vulnerability or used legitimate access; or whether the same channel could carry an actually-harmful payload.

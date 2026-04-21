# Security Analysis

## 1. Threat Model

**Adversarial claimer**
The claimer is adversarial and may attempt to obtain rewards without demonstrating genuine supply chain permeability. In particular, an adversarial claimer may try to:

* submit a claim without knowing the originally committed `submitterRandom`;
* copy a commitment and front-run the resulting reveal;
* forge an inclusion proof; or
* exploit the flexibility of an extraction strategy to match pre-existing artifact data without having caused the flag to propagate into the artifact.

**Maintainers self-attack**
Maintainers may participate honestly through control claims or adversarially as claimers against their own artifacts. The protocol economic calibration discussed in [economics.4](economics.md#4-maintainer-control-claims) aims to disincentivize maintainer self-attack.

**Rewarders**
Rewarders are not trusted and the protocol must remain sound when rewarders behave adversarially. Rewarders may try to deny payment to a legitimate claimer by:

* withdrawing or transferring to a new target the deposited funds upon observing a claim against the target
* self-claim a reward by controlling the target artifact and embedding a flag as a way to prevent a claimer from getting the reward

**Execution environment**
The execution environment is assumed to provide:

* correct and deterministic computation;
* persistent, publicly visible state; and
* tamper-proof custody and transfer of funds.

## 2. Security Properties

**Correctness**

A successful claim implies that the submitting committer committed `H(submitterRandom)` before the challenge was derived, and that the submitted data is contained in the target artifact. Under collision resistance of `H` and the security of the inclusion proof, a party who did not commit or did not embed the flag cannot satisfy all verification checks simultaneously. Attacks based on searching for a matching preimage (grinding), including those arising from the flexibility of the extraction strategy, are analyzed in detail in §4.

**Identity Binding**

A successful claim makes `submitterRandom` public. Identity binding in the flag derivation prevents an observer from claiming the reward: a claim submitted by a different identity derives a different flag, which will not match the embedding. Protocol-defined mechanisms such as claim propagation operate by design and do not violate this property.

**Non-repudiation**

All inputs to verification are publicly recorded, and the verification procedure is deterministic. Any third party can independently re-run the checks and obtain the same result. Reward distribution is executed automatically by the protocol when verification succeeds. A successful claim therefore constitutes non-repudiable evidence of supply chain permeability, with settlement enforced automatically by the protocol.

## 3. Commitment Copying and Front-Running

An adversary who observes a commitment transaction could copy `commitmentHash` and register it from their own address. If the flag were derived without the committer's address, both parties would derive the same flag. The adversary could wait for the claimer to reveal `submitterRandom` in a claim transaction and front-run it.

Including the committer's address in flag derivation prevents this because a copied commitment produces a different flag from a different address.

## 4. Preimage Grinding

A claimer might attempt to find a `submitterRandom` whose derived flag matches bits already present in an existing artifact under some valid extraction configuration, claiming a reward without demonstrating real risk. The feasibility of this attack depends on the relationship between the flag bit length and the number of distinct values extractable from a fixed artifact data chunk under all valid configurations of a given strategy.

Let $b$ denote the flag bit-length, and let $N$ denote the number of distinct flag values extractable from a fixed artifact under all valid parameterizations of a given extraction strategy. Modeling $H$ as a random oracle, the probability that a single commitment yields a flag matching any extractable value is at most $N / 2^b$. Security therefore requires $N \ll 2^b$.

The value of $N$ depends on the extraction strategy's configuration space, the number of valid starting positions, and the number of chunks in the artifact. For the conservative strategy used in our examples—fixed mask 1 and fixed skip 0—a 256-bit flag occupies 256 consecutive bytes within a 4 KB chunk, yielding $4096 - 256 + 1 = 3841 \approx 2^{12}$ valid starting positions per chunk. For an artifact of $2^{20}$ chunks (approximately 4 GB), this gives $N \approx 3841 \cdot 2^{20} \approx 2^{32}$, so the grinding success probability is at most $2^{-224}$, which is negligible.

More permissive strategies increase $N$, potentially substantially, because variable masks, skips, and instruction sequences enlarge the configuration space. Such strategies therefore require a separate combinatorial analysis to bound $N$ under their exact parameterization. In the absence of such a bound, they should not be deployed. We therefore recommend $b=256$ as the default flag length and require each extraction strategy to justify its effective security under its permitted configuration space.

## 5. Randomness Requirements

The protocol requires that the randomness source be unpredictable to the claimer at commitment time. On-chain entropy (e.g., `block.prevrandao`) is practical but gives block proposers marginal influence. Verifiable random functions (VRFs) offer stronger guarantees. The protocol is agnostic to the source; implementations should choose based on reward value and trust assumptions.

## 6. What a Claim Proves

A successful claim proves that a pseudo-random value, unpredictable at commitment time and cryptographically bound to the claimer's commitment, was caused to appear at a chosen location within the scoped region of the target artifact. This establishes injection capability into that region — a necessary condition for payload delivery through the same channel — but does not identify the injection path or distinguish maintainer from external claimants.
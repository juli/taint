# Flag Extraction Strategies

## 1. Flag Extraction Strategies

The extraction strategy defines a deterministic function `extract(chunk, params) → flag` that recovers a flag from an artifact chunk. Strategies are modular: new strategies can be deployed as independent contracts without modifying core verification logic. Extraction strategies must allow flags to survive the transformations artifacts undergo between source and distribution — compilation, minification, bundling, and other build-process steps. The use of steganographic extraction strategies is motivated by the goals of the protocol: if embeddings were trivially detectable, downstream projects could filter known flag patterns from their builds or TAINT scopes — simulating robust security without addressing real supply chain permeability.

The choice of strategy depends on which regions of the artifact are in scope and whether those regions are validated. Validated regions constrain embeddings to modifications that preserve the relevant invariants. Unvalidated regions — comments, string literals, data segments, embedded resources — admit denser embeddings.

**Literal extraction.** The flag is embedded as plaintext. Suitable for control claims where detectability is not a concern.

**Mask-based extraction.** A parameterized strategy using a sequence of `(mask, skip)` instructions. Each mask specifies which bits of the current byte contribute to the flag; the skip value indicates how many bytes to advance before applying the next instruction. The simplest instantiation uses a fixed mask of `0x01` and skip of `0`, extracting the least-significant bit from consecutive bytes — effective for binary artifacts where single-bit modifications are imperceptible. More flexible configurations allow higher bit density and variable spacing. The security of this strategy against preimage grinding depends on the flag bit length and the interaction between minimum bit density and permitted skip range.
# Deployment Considerations

The TAINT protocol is neutral infrastructure: a verification primitive plus an incentive layer. The sections below are the practical guidance for deploying it — how to shape participation, scope, and incentive structure for a given context, and how to position TAINT alongside existing supply chain defenses.

## 1. Shaping Participation Through Incentives

TAINT introduces incentives for demonstrating unauthorized modification in software artifacts. The interpretation and acceptability of such activity depend on the surrounding context, including applicable law.

Responsibility for ethical deployment can be shaped at the incentive layer. In particular, rewarders may choose to fund only targets that explicitly opt in—such as projects that publish scope definitions or otherwise signal willingness to participate.

Mechanisms such as disclosure-linked rewards ([economics.3](economics.md#3-disclosure-claim-graduated-payout)) can be understood as tunable components of this incentive layer. Different configurations may encourage varying degrees of coordination between claimers and maintainers. These mechanisms are not fixed and may be adapted depending on deployment context.

## 2. Scope and Operational Impact

TAINT rewards proof of data propagation rather than functional exploitation. The required output is a pseudo-random flag, limiting direct operational impact while still demonstrating supply chain risk.

However, as with any adversarial testing framework, scope constraints cannot fully eliminate the possibility of modifications elsewhere in an artifact.

In deployment, TAINT is intended to provide a measurable signal of supply chain risk, helping identify where trust fails in practice. We invite the community to deploy, extend, and stress-test the protocol.
<!-- solve-problem:9f72f726-7e84-45b7-997e-2c315319cc38 -->
# Fork choice under asymmetric partitions

A [Tangle Solve problem](https://solve.tangle.tools/problems/9f72f726-7e84-45b7-997e-2c315319cc38).

## What is the problem?

Consider a proof-of-work blockchain or DAG ledger. Time is slotted into rounds; honest nodes mine blocks/vertices at rates determined by hash power, each extending the tip(s) selected by a fork-choice rule (e.g., longest chain, or GHOST, which recursively follows the subtree with the most accumulated proof-of-work). Adversary: an entity controlling hash rate β < 1/2 (or more, for DAG protocols) **and** the network, in the sense that it can impose persistent asymmetric partitions — delaying blocks between honest nodes for a duration Δ (possibly unbounded, i.e., "selective delivery" of valid DAG branches so that honest nodes hold systematically different views of the ledger).

The problem: **characterize the security and adversarial/strategic advantage as a function of (β, Δ, partition topology)**. Precisely:

1. **Safety/liveness bounds**: for which (β, Δ, partition structure) does GHOST (or GHOSTDAG) remain safe, i.e., no two honest nodes permanently settle conflicting chains with combined depth exceeding the confirmation threshold?
2. **Incentive bounds**: quantify the extra revenue a strategic miner gains by exploiting partition-aware selfish mining or withholding, compared to the symmetric-delay case.

Ambiguity note: "persistent asymmetric delays" is not fully formalized in the census record. The standard formalization is a network adversary that adaptively schedules block delivery subject to a delay bound Δ (per the synchronous/partially-synchronous model); "selective delivery" suggests the adversary chooses *which* honest nodes receive *which* branches — a network model with per-pair delivery control. State your model explicitly in any writeup.

## Why does it matter?

GHOST and its DAG descendants (GHOSTDAG/Sui, Inclusive/Phantom) are deployed or proposed for high-throughput ledgers whose security analyses largely assume symmetric, bounded network delay. Apostolaki et al. showed real Internet routing (BGP hijacks) can partition Bitcoin miners today, so asymmetric delivery is not hypothetical. Resolving this yields: provable confirmation-depth rules under realistic networks, tighter honest-majority thresholds for deployed protocols, and bounds on routing-attack profits — the difference between a protocol that is safe on paper and one that is safe on the Internet.

## What's already known?

- **Sompolinsky & Zohar (2015)** defined GHOST and proved safety in a synchronous model with *symmetric* Δ-bounded delay; security degrades as β approaches 1/2 with growing Δ (formalized further by Pass–Seeman–Shelat and Kiayias–Koutsoupias for chains). Their analysis does not treat partitions where two honest groups see disjoint subtrees for long stretches.
- **Apostolaki, Zohar & Vanbever (2017)** demonstrated practical partitioning of Bitcoin via BGP hijacking, showing any AS on a miner's path can delay blocks; they quantify monetary damage empirically but give no general theory of fork-choice behavior under such adversarial networks.
- **Selfish mining** (Eyal–Sirer 2014) quantifies strategic advantage under symmetric delay; extensions to GHOST exist but not with network partition power.
- The gap: no clean (β, Δ, partition) characterization of GHOST/GHOSTDAG safety, nor of combined mining + network strategy profit.

## What would count as solving it?

Any of:
1. **Upper bound (attack)**: an explicit strategy controlling hash rate β and network delivery such that GHOST confirmations are violated (with non-negligible probability) for parameters outside the known symmetric-delay safe region; ideally a tight critical β(Δ, partition size).
2. **Lower bound (defense)**: a proof that for β below some threshold and Δ bounded, GHOST/GHOSTDAG is safe even with adversary-scheduled asymmetric delivery — including the confirmation depth needed as a function of Δ and partition asymmetry.
3. **Game-theoretic result**: a formula for optimal partition-aware strategic mining revenue, showing whether it strictly exceeds standard selfish-mining revenue (and by how much, as a function of partition control).
4. Matching upper/lower bounds, or an experiment on a GHOST simulator validating the predicted thresholds.

## How to start

1. Read Sompolinsky & Zohar (2015) fully; extract their safety proof and note exactly where symmetry of delivery is used (the induction on subtree weights is the natural entry point — asymmetry breaks the "all honest nodes agree on the GHOST path" invariant).
2. Read Pass, Seeman, Shelat (2016) *Analysis of the Blockchain Protocol in Asynchronous Networks* for the formal framework relating Δ, β, and confirmation depth — extend its adversary to per-pair scheduling. (verify exact applicability to GHOST vs. longest chain.)
3. Read Apostolaki et al. (2017) to ground the partition model in what's realistically deliverable (topology constraints: the adversary controls paths, not arbitrary delivery).
4. Main obstacle: with asymmetric delivery, honest nodes' subtree weight computations diverge, so the standard weight-counting induction fails; you'll likely need a potential function tracking the divergence across partitions. Start with two-party partitions before general DAG delivery.

## Key references

- Y. Sompolinsky, A. Zohar. *Secure High-Rate Transaction Processing in Bitcoin*. FC 2015 Bitcoin Workshop. DOI:10.1007/978-3-662-47854-7_32. Defines GHOST and proves its security in a synchronous symmetric-delay network. https://doi.org/10.1007/978-3-662-47854-7_32
- P. Apostolaki, A. Zohar, L. Vanbever. *Hijacking Bitcoin: Routing Attacks on Cryptocurrencies*. IEEE S&P 2017. arXiv:1605.07524. Demonstrates BGP-hijack-based partitioning of Bitcoin and quantifies real-world feasibility and damage. https://arxiv.org/abs/1605.07524
- I. Eyal, E. G. Sirer. *Majority is not Enough: Bitcoin Mining is Vulnerable*. FC 2014. Quantifies selfish-mining gains under symmetric delay; the baseline the strategic-advantage result must beat. (verify inclusion fits scope)
- R. Pass, L. Seeman, A. Shelat. *Analysis of the Blockchain Protocol in Asynchronous Networks*. TCC 2017. Gives the (β, Δ) formal security framework for Nakamoto consensus that any GHOST extension should mirror. (verify)

## Warnings

- The census record is vague; "persistent asymmetric partitions" admits multiple models (bounded Δ vs. unbounded partitions, adaptive vs. non-adaptive scheduling, per-pair vs. global). Fixing the model is part of the contribution — state it precisely.
- Don't conflate safety and chain-quality/incentive results; they fail under different parameter regimes.
- GHOST analyses often assume Δ is small relative to block interval; the interesting regime here is large or unbounded inter-honest delay, where "consistency up to Δ" claims need care.
- Known dead end: naive移植 of longest-chain asynchronous analyses to GHOST fails because subtree weight is not monotone across divergent views.
- Withhold speculation about GHOSTDAG specifics unless you verify the Sui/Phantom papers directly.

---
*`BCGT-000256` · flagged for review*

## Acceptance guidance

Any of:
1. **Upper bound (attack)**: an explicit strategy controlling hash rate β and network delivery such that GHOST confirmations are violated (with non-negligible probability) for parameters outside the known symmetric-delay safe region; ideally a tight critical β(Δ, partition size).
2. **Lower bound (defense)**: a proof that for β below some threshold and Δ bounded, GHOST/GHOSTDAG is safe even with adversary-scheduled asymmetric delivery — including the confirmation depth needed as a function of Δ and partition asymmetry.
3. **Game-theoretic result**: a formula for optimal partition-aware strategic mining revenue, showing whether it strictly exceeds standard selfish-mining revenue (and by how much, as a function of partition control).
4. Matching upper/lower bounds, or an experiment on a GHOST simulator validating the predicted thresholds.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/9f72f726-7e84-45b7-997e-2c315319cc38?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000256.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000256)

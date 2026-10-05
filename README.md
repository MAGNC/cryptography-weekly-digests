# Weekly Cryptography Research Digest

> The latest weekly digest is displayed directly on this page. Each issue is also preserved as a dated Markdown file in the archive.

[Archived copy of this issue](digests/2026/2026-10-05.md)

---

**Coverage:** first public postings from September 29–October 5, 2026. Cross-posts and routine updates were removed; conference deposits and material revisions are labeled separately.

## Executive summary

This week combines concrete implementation risk with foundational advances. A simulated power-analysis attack recovers Falcon keys despite masking of its preimage computation, while a separate number-field-sieve construction factors affine-padded Rabin moduli faster than generic factoring when a suitable root oracle is available. On the constructive side, new work claims the first broadly applicable information-theoretic MPC with subquadratic communication, while SPIN reports faster code-based correlation generation and proof-system proving. Deployment-oriented analyses identify structural downgrade and interoperability problems in an ITU-T X.509 migration mechanism, and new results sharpen the distinction between quantum designs and genuine pseudorandomness.

## Most relevant papers

### 1. [When Masking Preimage Computation Isn’t Enough: A Power Analysis Attack on Masked Implementations of Falcon](https://eprint.iacr.org/2026/2313)

**Keng-Yu Chen · October 2 · Post-quantum implementation attacks**

The attack exploits leakage when masked shares are recombined after Falcon’s preimage computation, using ratios between real and imaginary parts of the public hash polynomial to recover the secret key. In an ELMO simulation of ARM Cortex-M0 leakage, the author reports a 98.3% success rate with 4,000 traces for chosen-message and filtered non-chosen-message variants.

**Why it matters:** Protecting only the preimage computation does not contain leakage introduced by subsequent floating-point share recombination.

**Caveat:** The evidence comes from a leakage simulator rather than physical-device measurements; device dependence, trace alignment, and the proposed rejection-sampling countermeasure require hardware validation.

### 2. [Breaking the \(n^2\) Barrier: Information-Theoretic MPC with Sub-Quadratic Communication](https://eprint.iacr.org/2026/2311)

**Alexander Bienstock, Yuval Efron, Kevin Yeo · October 2 · Multi-party computation**

For an honest majority with corruption threshold below \((1/2-\epsilon)n\), the authors construct adaptively and maliciously secure information-theoretic MPC with \(o(n^2)\) communication for a range of small circuits and sublinear numbers of input parties. They also prove that subquadratic communication is impossible against a stronger-than-standard adaptive rushing adversary, even for constant-size circuits.

**Why it matters:** Prior information-theoretic MPC protocols paid an additive near-quadratic communication cost independent of circuit size; this result breaks that barrier in a nontrivial regime.

**Caveat:** The improvement depends on restricted circuit size and depth plus \(o(n)\) input parties, so it is not a universal subquadratic MPC protocol and currently offers asymptotic rather than deployment benchmarks.

### 3. [An Analysis of the ITU-T Multiple Cryptographic Algorithms X.509 Extensions for PQC Migration](https://eprint.iacr.org/2026/2297)

**Falko Strenzke · October 1 · Post-quantum migration / PKI**

The paper analyzes the “Catalyst” X.509 extensions that place an alternative key and issuer signature beside the traditional ones. It formalizes a downgrade in which recovery of one classical CA key permits replacement of hybrid chains with traditional-only certificates, demonstrates the resulting exposure in wolfSSL, and identifies signature-repurposing and RFC 5280 consistency problems.

**Why it matters:** A certificate can appear migration-ready while still permitting a quantum attacker to route validation entirely through compromised classical evidence.

**Caveat:** Some findings concern ambiguities or permitted behaviors rather than vulnerabilities in every implementation; affected profiles, relying-party policies, and library configurations need individual review.

### 4. [Affine-Padding Rabin-Oracle Factoring](https://eprint.iacr.org/2026/2256)

**Alexandra-Ioana Buzățoiu, Diana Maimuț, David Naccache · September 29; revised October 1 · Applied cryptanalysis**

The authors adapt a one-sided number field sieve to an affine-padded Rabin square-root oracle and obtain nontrivial congruences of squares with probability one half per dependency. They claim factoring complexity \(L_n(1/3,1.526\ldots)\), below the general number field sieve constant \(1.923\ldots\), and provide an open-source implementation plus a complete toy recovery.

**Why it matters:** The sign ambiguity that normally complicates Rabin roots becomes a factorization signal, showing that simple affine redundancy does not safely constrain a root oracle.

**Caveat:** The attack requires extensive access to the specified oracle and collapses when padding is cryptographically hashed rather than an affine function of an attacker-chosen message; it is not a generic RSA or Rabin break.

### 5. [SPIN: Fast Codes for Correlation Generation and Polynomial Commitments](https://eprint.iacr.org/2026/2317)

**Stanislav Peceny, Rahul Rachuri, Srinivasan Raghuraman, Peter Rindal · October 2 · PCG / polynomial commitments**

SPIN combines an outer expander-style code with a recursive inner construction and formally verifies its asymptotic distance and rate guarantees in Lean. The authors report generating \(2^{20}\) 128-bit correlation blocks in 9.3 ms on one Ryzen 9 7950X core, 89 million hashed Silent OTs per second in the stated configuration, and 1.63–2.29× higher Flock prover throughput after replacing Ligerito with SPIN–Brakedown.

**Why it matters:** The same code family targets two major cryptographic workloads: pseudorandom correlation generation and transparent polynomial commitments.

**Caveat:** Several timings exclude setup, base correlations, networking, extraction, or Fiat–Shamir costs; the proof-system configuration also produces larger proofs and slower verification.

### 6. [On the Pseudorandomness of Simple Quantum Processes](https://eprint.iacr.org/2026/2303)

**Jesko Dujmovic, Jonas Haferkamp, Alexander Poremba · October 1 · Quantum cryptographic foundations**

The authors construct efficiently sampled local quantum processes that become approximate unitary \(t\)-designs with negligible moment error yet remain efficiently distinguishable from random using only polylogarithmically many queries. They also give a stronger separation at polynomial moments and propose alternative conjectures for when simple random quantum circuits might yield pseudorandom unitaries.

**Why it matters:** Matching many statistical moments—often used as a proxy for scrambling or randomness—does not by itself provide computational pseudorandomness.

**Caveat:** The counterexamples are intentionally constructed, and the stronger separation uses highly structured ensembles; the work does not show that standard random-circuit families fail to be pseudorandom.

### 7. [ECLIPSE: Strongly Unforgeable Isogeny Signatures from the Prime-Degree Variant of PRISM](https://eprint.iacr.org/2026/2312)

**Dustin Ray · October 2 · Post-quantum signature implementation**

ECLIPSE implements PRISM’s prime-degree variant on the current SQIsign Round-3 primes, avoiding the auxiliary-isogeny ambiguity that prevents ordinary SQIsign from achieving strong unforgeability in its specification. The prototype reports 206-byte level-I signatures, signing 2.3× faster than the compared SQIsign assembly build, and verification costing approximately 1.7–2× as much.

**Why it matters:** It turns a previously unparameterized construction into interoperable implementations with concrete formats and measurements after recent SQIsign parameter changes.

**Caveat:** The paper explicitly leaves a formal SUF-CMA proof to future work, so the title’s intended security property should not yet be treated as established.

### 8. [zk-BAN: An Efficient Anonymous Blocklisting System with Signature-Based Revocation](https://eprint.iacr.org/2026/2274)

**Kosei Akama, Yoshimichi Nakatsuka, Koichi Moriyama, Keisuke Uehara · September 30 · Privacy / anonymous authentication**

zk-BAN lets a service block credentials associated with abusive past authentications while preserving unlinkability for honest users. By filtering the revocation list and partially sharing PRF-tag computations, the authors report approximately 600 ms proof generation and 2 ms verification in modeled large-scale workloads—136× faster proving and 32× faster verification than their prior-work comparison.

**Why it matters:** Signature-based revocation avoids the policy restrictions of short revocation windows but has historically been expensive for long-offline users and large blocklists.

**Caveat:** The reported speedups rely on modeled workloads, selected baselines, and zk-SNARK implementation choices; operational privacy also depends on list construction, credential issuance, and metadata outside the proof.

## Other notable papers by topic

- **Coding foundations:** [Expander-Based Codes at the Gilbert–Varshamov Bound](https://eprint.iacr.org/2026/2315) gives asymptotic and certified finite-length distance bounds for sparse code ensembles relevant to LPN and pseudorandom correlation generators, but provides neither a decoder nor a full protocol-security proof.
- **Symmetric cryptography:** [An Input-Fed AES-Round Family for Fixed-Length Hashing](https://eprint.iacr.org/2026/2314) proposes AES-NI-speed hashing for high-entropy fixed-size inputs and analyzes reduced-round differential, integral, collision, and meet-in-the-middle behavior; random-function security remains a design assumption rather than a proof.
- **Consensus protocols:** [Defeating Time-Average Selfish Mining Across Epoch Boundaries](https://eprint.iacr.org/2026/2275) proposes a pipelined-buffer difficulty adjustment and reports simulations neutralizing the modeled orphan-exclusion strategy at a 40% adversarial hash share with a buffer depth of 16.
- **Foundations and conference revisions:** [Non-Interactive Black-Box Witness-Indistinguishable Commit-and-Prove](https://eprint.iacr.org/2026/2286) is a minor revision of TCC 2026 work based on strong targeted hitting-set generators. [Output-Adaptive ABE for Circuits from LWE](https://eprint.iacr.org/2026/2305) is a minor ASIACRYPT 2026 revision covering dynamic target outputs with fixed policy structure.
- **Post-quantum implementation:** The ECLIPSE paper is complemented by this week’s [Dimension-4 SQIsign measurements](https://eprint.iacr.org/2026/2221) from the previous window; both should be interpreted in light of the new Round-3 parameters and their distinct verification/security trade-offs.

## Watch next

- Physical-device reproduction of the Falcon leakage and evaluation of rejection sampling versus fully masked Gaussian sampling.
- Proof-level scrutiny and concrete implementations of the subquadratic information-theoretic MPC protocol.
- Responses from ITU-T, PKI vendors, and relying-party libraries on hybrid-certificate downgrade and RFC 5280 compatibility.
- Independent benchmarking of SPIN across complete Silent OT and proof-system pipelines, including setup, networking, proof size, and verifier cost.
- A formal strong-unforgeability proof for ECLIPSE and broader cryptanalysis of its prime-degree design.


---

## Previous digests

- [September 22–28, 2026](digests/2026/2026-09-28.md)
- [September 15–21, 2026](digests/2026/2026-09-21.md)
- [September 8–14, 2026](digests/2026/2026-09-14.md)
- [September 1–7, 2026](digests/2026/2026-09-07.md)
- [August 18–24, 2026](digests/2026/2026-08-24.md)
- [August 11–17, 2026](digests/2026/2026-08-17.md)
- [August 4–10, 2026](digests/2026/2026-08-10.md)
- [July 28–August 3, 2026](digests/2026/2026-08-03.md)
- [July 21–27, 2026](digests/2026/2026-07-27.md)

## About this archive

This repository contains weekly research summaries, links, relevance assessments, and caveats. It does not contain the digest-generation tool or workflow source code. These digests distinguish authors' claims from independently verified results where possible and are not substitutes for reading the primary papers.

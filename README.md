# Weekly Cryptography Research Digest

> The latest weekly digest is displayed directly on this page. Each issue is also preserved as a dated Markdown file in the archive.

[Archived copy of this issue](digests/2026/2026-09-14.md)

---

**Coverage:** first public postings from September 8–14, 2026. Cross-posts and routine updates were removed; newly deposited conference papers and revisions are labeled separately.

## Executive summary

This week brought two unusually concrete cryptanalytic results: the TII-254 McEliece key-recovery challenge was solved with a reproducible artifact, and a new attack places Frobenius-UOV forgery costs far below its advertised security levels. A separate study breaks both the unprotected and finite-precision “protected” neural-network implementations of AES considered in recent deep-neural cryptography work. On the constructive side, new results claim a dimension-preserving NP-hardness reduction for ideal-lattice SVP/CVP, more practical private and verifiable LLM inference, tighter lattice-based anonymous credentials, and the first mechanized computational proof of post-compromise security for a ratchet. These are new preprints or author-reported evaluations and still require independent validation.

## Most relevant papers

### 1. [Two-Anchor Holdout/Hermite: Solving the TII-254 McEliece Key Recovery Challenge](https://eprint.iacr.org/2026/1986)

**Markku-Juhani O. Saarinen · September 14 · Post-quantum cryptanalysis**

The paper reports recovery of the full support and Goppa polynomial for the TII-254 challenge, using two conditioned relation kernels, removal of a common nuisance space, and reconstruction of an \(\mathbb F_{2^8}\) projective-line geometry. The two final Krylov sequences consumed 27.2 NVIDIA GH200 GPU-hours, excluding reconstruction and CPU processing; the author provides code, compact recovery inputs, an independent verifier, and Lean proofs for reusable linear-algebra steps.

**Why it matters:** It turns the recently proposed holdout/Hermite line of attack into a completed, checkable key recovery on the hardest solved challenge under TII’s original brute-force metric.

**Caveat:** TII-254 uses small parameters \((m,t,n)=(8,12,223)\) and is not a break of standardized Classic McEliece; scaling the method and its memory requirements to production parameters remains the central question.

### 2. [A Forgery Attack against Frobenius-UOV](https://eprint.iacr.org/2026/1927)

**Augustin Bariant · September 12 · Post-quantum signatures / multivariate cryptanalysis**

The attack exploits F-UOV’s fixed Frobenius exponents to reduce its signing equation to two low-degree bivariate equations and solve them through a linearized resultant, without the secret key. Under heuristic success assumptions, the paper estimates roughly \(2^{45}\), \(2^{52}\), and \(2^{53}\) field operations against the claimed 128-, 192-, and 256-bit instances.

**Why it matters:** If confirmed, every proposed parameter set is forgeable far below its stated security level, making this a design-level issue rather than a modest parameter correction.

**Caveat:** The complexity estimates depend on heuristic success probabilities, and the paper proposes changing the exponents as a possible mitigation whose security still needs analysis.

### 3. [Cryptanalysis of Deep Neural Cryptography: Second Round Key Recovery on the Unprotected Implementation and a Floating-Point Attack on the Protected Implementation of AES](https://eprint.iacr.org/2026/1919)

**Sisung Kim, Dongjae Lee · September 10 · Applied cryptanalysis**

For the natural ReLU-network implementation of AES-256, the authors extend earlier first-round attacks to second-round key recovery in about \(2^{39}\) chosen-plaintext queries. More strikingly, they report recovering the first-round key of the proposed protected AES-128 network in 128 chosen queries across bfloat16, float16, float32, and float64, exploiting an input that finite-precision rounding fails to map to a bit; a pre-rounding clamp blocks this specific attack.

**Why it matters:** It shows that an exact-real proof for a neural implementation does not automatically survive actual floating-point execution.

**Caveat:** The clamp addresses the demonstrated weakness, but the authors leave a complete finite-precision security proof open; the result concerns these DNN encodings, not conventional AES implementations.

### 4. [NP-Hardness of Ideal Lattice Problems](https://eprint.iacr.org/2026/2003)

**Daniel E. Martin · September 14 · Lattice foundations**

The paper claims deterministic, dimension-preserving polynomial-time reductions from generic lattice problems to ideal-lattice versions of SVP and CVP in the \(\ell_2\) norm. The constructed ideal is invertible in a monogenic, totally real number ring with polynomial-bit-length description; requiring the ring to be the full ring of integers adds a conjectural bounded-error quantum step.

**Why it matters:** Worst-case hardness for structured ideal lattices is a longstanding foundational question because structured lattices underpin efficient post-quantum cryptography.

**Caveat:** The result is a fresh theoretical preprint, and the constructed rings and ideals do not by themselves establish hardness for the specific cyclotomic or module-lattice distributions used in deployed schemes.

### 5. [Maverick: Private and Verifiable LLM Inference Made Practical via Matrix-Vector Multiplication Delegation](https://eprint.iacr.org/2026/1955)

**Ben Merbaum, Mohammad Amin Raeisi, Wenhao Wang, Charalampos Papamanthou, Katerina Sotiraki, Fan Zhang · September 13 · Private and verifiable computation**

Maverick delegates the matrix-vector products dominating LLM inference while using an information-theoretically sound verification protocol with transparent preprocessing and LPN-based pseudorandom masks for input privacy. On Qwen3-4B, the prototype reports up to 17× speedup over local inference with online mask generation, 45× with precomputation, and 44× for verification-only mode in its one-client-thread experiments.

**Why it matters:** It targets privacy and correctness together while keeping server overhead close to ordinary inference, a useful point in the design space between local execution and heavy generic cryptography.

**Caveat:** The largest gains depend on precomputation, server parallelism, client configuration, and simulated network conditions; model confidentiality is not the same guarantee as user-input privacy.

### 6. [Compact Lattice Anonymous Credentials from Tighter Approximate Range Proofs](https://eprint.iacr.org/2026/1920)

**Corentin Jeudy, Olivier Sanders · September 10 · Post-quantum privacy / zero knowledge**

The authors introduce tighter approximate range proofs for the Lyubashevsky–Nguyen–Plançon lattice zero-knowledge protocol and combine them with further optimizations for anonymous credentials based on standard lattice assumptions. Their stated goal is to narrow the efficiency gap with systems relying on more specialized interactive assumptions while retaining quantum-safe foundations.

**Why it matters:** Anonymous credentials are central to privacy-preserving identity systems, and standard-assumption post-quantum designs remain materially less efficient than classical alternatives.

**Caveat:** The abstract does not provide enough normalized implementation data to judge end-to-end wallet performance, disclosure sizes, or comparison under uniform security parameters.

### 7. [Information-theoretic two-server PIR requires \((6-o(1))\log n\) bits of communication](https://eprint.iacr.org/2026/1959)

**Keewoo Lee · September 13 · Private information retrieval / foundations**

The paper raises the communication lower bound for information-theoretic two-server PIR from \(5\log n\) to \((6-o(1))\log n\). It follows the quantum random-access-code route of prior proofs but replaces low-bias binary readout and amplification with a real-valued estimator whose cost is controlled by its second moment; the technique also strengthens lower bounds for smooth and locally decodable codes.

**Why it matters:** Tighter lower bounds clarify how far information-theoretic PIR protocols can improve and rule out part of the remaining low-communication design space.

**Caveat:** The bound is specific to two-server information-theoretic PIR and does not directly constrain computational PIR or protocols with more servers.

### 8. [Secrecy in Squirrel and the Post-Compromise Security of a Ratchet](https://eprint.iacr.org/2026/1912)

**Clément Hérouard, Charlie Jacomme, Adrien Koutsos, Joseph Lallemand · September 10 · Protocol verification / CCS 2026 full version**

The authors extend Squirrel so secrecy can be handled as a first-class logical notion and use it to verify post-compromise security for an asymmetric ratchet in the computational model. They describe this as the first mechanized computational PCS proof for a ratchet, whereas earlier machine-checked analyses were limited to symbolic models.

**Why it matters:** Ratchets underpin Signal, WhatsApp, and other secure messengers, and mechanizing their computational arguments can expose assumptions that symbolic models abstract away.

**Caveat:** The artifact proves an asymmetric-ratchet model, not the complete deployed Signal, WhatsApp, or iMessage protocols; this ePrint is labeled a minor revision of CCS 2026 work.

## Other notable papers by topic

- **PIR and private computation:** [ReinsPIRe](https://eprint.iacr.org/2026/1934) moves most server-side FHE work to hardware-friendly matrix operations and reports 5.9 GB/s per core—2× its cited predecessor—plus a verifiable variant with much smaller long-term client storage.
- **Zero knowledge and AI:** [Sound Debloating of Redundant Checks in Zero-Knowledge Machine-Learning Circuits](https://arxiv.org/abs/2609.10149), first posted September 9, uses whole-circuit abstract interpretation to remove only checks entailed elsewhere; across circuits up to 25.3 million constraints, the authors report removing up to 48.7% and cutting prover time by up to 72.8%.
- **FHE hardware:** [PHAT](https://arxiv.org/abs/2609.11613), first posted September 10, proposes an optically addressed phase-change-memory accelerator for TFHE and reports modeled 2.14–5.10× speedups over a cited ASIC across four workloads.
- **Post-quantum deployment:** [A Post-Quantum Cryptography Recommendation System with TLS Validation](https://eprint.iacr.org/2026/1938) shows that preferred algorithms can change with client/server placement and that certificate-stack and wire-format failures can invalidate recommendations based only on microbenchmarks.
- **Quantum foundations:** [Trapdoor Functions with Secure Key Leasing and Copy Protection](https://eprint.iacr.org/2026/1913) defines quantum trapdoors that can be leased, deleted, or copy-protected and derives encryption with decryption keys designed to survive malicious ciphertexts; constructions rely on LWE and, for copy protection, indistinguishability obfuscation.
- **Conference deposits and revisions:** [From Specs to Apps](https://eprint.iacr.org/2026/1979) monitors instrumented Signal Desktop and WhatsApp Web executions against Tamarin-compatible models and is labeled a minor revision of CCS 2026 work. [Oblivious Signaling](https://eprint.iacr.org/2026/1975), [Better Security Proofs for X3DH and XHMQV](https://eprint.iacr.org/2026/1980), and [Universally Composable Reverse Firewalls for OLE](https://eprint.iacr.org/2026/2012) are likewise labeled conference full versions or revisions and were not ranked as wholly new disclosures.

## Watch next

- Whether the TII-254 artifact is independently reproduced and how holdout/Hermite costs scale toward Classic McEliece parameters.
- Confirmation of the Frobenius-UOV forgery estimates and analysis of revised exponent choices.
- A finite-precision security definition and proof for protected neural implementations, beyond the proposed clamp for the demonstrated AES attack.
- Proof-level review of the ideal-lattice NP-hardness reduction and clarification of which structured ring families it reaches.
- Independent end-to-end benchmarking of Maverick, lattice anonymous credentials, ReinsPIRe, ZK-circuit debloating, and PHAT under common security and hardware assumptions.


---

## Previous digests

- [September 1–7, 2026](digests/2026/2026-09-07.md)
- [August 18–24, 2026](digests/2026/2026-08-24.md)
- [August 11–17, 2026](digests/2026/2026-08-17.md)
- [August 4–10, 2026](digests/2026/2026-08-10.md)
- [July 28–August 3, 2026](digests/2026/2026-08-03.md)
- [July 21–27, 2026](digests/2026/2026-07-27.md)

## About this archive

This repository contains weekly research summaries, links, relevance assessments, and caveats. It does not contain the digest-generation tool or workflow source code. These digests distinguish authors' claims from independently verified results where possible and are not substitutes for reading the primary papers.

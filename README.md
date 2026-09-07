# Weekly Cryptography Research Digest

> The latest weekly digest is displayed directly on this page. Each issue is also preserved as a dated Markdown file in the archive.

[Archived copy of this issue](digests/2026/2026-09-07.md)

---

**Coverage:** first public postings from September 1–7, 2026. Cross-posts and routine repository updates were removed; newly deposited conference papers and major revisions are labeled separately.

## Executive summary

Exact lattice algorithms moved unusually quickly this week: two independent papers lower the asymptotic time for exact SVP, with the strongest claim reaching \(2^{n/2+o(n)}\) time and space, only weeks after a \(2^{0.6039n+o(n)}\)-time result. A separate soundness paper shows how adversarially generated programs compiled to R1CS can undermine Fiat–Shamir transforms when the first challenge is bound to the program rather than the generated statement. On the applied side, the first third-party full-round analysis of Iasta estimates attacks well below its claimed 128-bit security, while Quasar and two FHE systems report large performance improvements in polynomial commitments, encrypted integer arithmetic, and private transformer inference. All are new preprints or author-reported evaluations and require independent scrutiny before security parameters or deployment decisions change.

## Most relevant papers

### 1. [Finding a Shortest Vector and More in \(2^{n/2+o(n)}\) Time using \(q\)-ary Coset Difference Tree](https://eprint.iacr.org/2026/1859)

**Minki Hhan · September 3 · Lattice foundations**

The paper gives a randomized exact-SVP algorithm using a \(q\)-ary analogue of the midpoint-Hessian approach: gradients of the periodic Gaussian near \(v/q\) are recovered along a chain of intermediate lattices with a Wagner-style generalized-birthday procedure. The claimed complexity is \(2^{n/2+o(n)}\) time and space; a variant handles certain closest-vector instances within distance \(1.039\lambda_1(\mathcal L)\).

**Why it matters:** If the proof holds, this further lowers the best rigorous asymptotic time claimed for exact SVP and supersedes the \(2^{0.6039n+o(n)}\)-time result highlighted in August.

**Caveat:** The result is still exponentially expensive, uses exponential memory, and does not translate directly into an equivalent reduction of deployed lattice-cryptography parameters.

### 2. [How to prove more false statements: Fiat–Shamir limitations on (generated) R1CS](https://eprint.iacr.org/2026/1838)

**Giacomo Fenzi · September 1 · Zero knowledge / proof-system soundness**

The paper extends concrete-hash Fiat–Shamir limitations to proof systems where an adversarial program is compiled into an R1CS instance. When the conversion is sufficiently expressive, the program can arrange an accepting transcript before the statement is bound; variants of Spartan and Aurora for R1CS are shown to fit the affected class. The proposed mitigation derives the first Fiat–Shamir challenge from the generated statement rather than the generating program.

**Why it matters:** Program-to-circuit compilation is common in modern proof stacks, so the distinction between committing to source code and committing to the actual relation instance has direct audit relevance.

**Caveat:** This is not a blanket break of Spartan, Aurora, or every Fiat–Shamir deployment; exploitability depends on the precise transcript, binding order, and whether adversarially generated programs or relations are accepted.

### 3. [Peeling Nonlinear Layers: Algebraic Cryptanalysis of Full-Round Iasta](https://eprint.iacr.org/2026/1866)

**Chandan Dey, Abul Kalam, Santanu Sarkar · September 6 · Symmetric cryptanalysis**

The authors present the first third-party analysis of full-round Iasta-3 and Iasta-4, a stream cipher designed for hybrid homomorphic encryption, and extend the method to Iasta-5. Weak nonces induced by the restricted space of nonce-dependent affine matrices let the attack peel off nonlinear layers and solve lower-degree equations; the best estimates are \(2^{59}\) and \(2^{67}\) operations for Iasta-3 and Iasta-4 under \(\omega=2\), or about \(2^{80}\) and \(2^{82}\) under the more conservative \(\omega=3\).

**Why it matters:** Even the conservative estimates fall below the 128-bit security claimed for Iasta-3 and Iasta-4, making this a parameter- and design-level result rather than a reduced-round observation.

**Caveat:** These are analytical complexity estimates conditioned on weak-nonce construction and linear-algebra assumptions, not reported end-to-end key recoveries on a deployed implementation.

### 4. [Quasar: A Field-Agnostic Polynomial Commitment Scheme with Polylogarithmic Verification from Quasi-Abelian Codes](https://eprint.iacr.org/2026/1839)

**Yuhao Jia, Zhe Li, Chaoping Xing, Yizhou Yao, Chen Yuan · September 1 · Polynomial commitments / zero knowledge**

Quasar combines quasi-Abelian codes with BaseFold through a new encoding-oriented code-switching argument. For a length-\(N\) multilinear polynomial it claims \(O(N\log N)\) commitment, \(O(N)\) evaluation, and \(O(\lambda\log^2 N)\) proof size and verifier time; the implementation reports large CPU improvements over BaseFold, Brakedown, BrakingBase, and QAPCS, plus substantial GPU acceleration.

**Why it matters:** Polynomial commitments often dominate transparent proof-system costs, and field-agnostic commitments with fast proving and polylogarithmic verification would be broadly useful.

**Caveat:** The reported implementation uses a 127-bit Mersenne-prime field and 100-bit security on selected CPU/GPU configurations; comparisons need reproduction under uniform security, memory, and hardware conditions.

### 5. [High-Precision Homomorphic ALU over Arbitrary Moduli with \(O(1)\) Bootstrapping](https://eprint.iacr.org/2026/1869)

**Jiaming Liu, Shihe Ma, Anyu Wang, Xiaoyun Wang · September 6 · Fully homomorphic encryption**

The construction supports both arithmetic and Boolean-style computation over arbitrary plaintext moduli with \(O(1)\) bootstrapping, using flexible radix representations, CVP-selected defining polynomials, and a constant-bootstrap arithmetic-to-digit conversion. An OpenFHE implementation reports 8.67–13.41× lower multiplication latency for P-384 and Curve25519 workloads and 4.25–7.58× for RSA-1024/2048 relative to the cited baselines, with larger amortized gains.

**Why it matters:** Efficient switching between large-integer arithmetic and bit-level operations is a key requirement for encrypted general-purpose computation and legacy-cryptography workloads.

**Caveat:** The strongest numbers are author benchmarks for selected moduli and baseline implementations; evaluation-key size, memory, precision, security parameters, and workload batching must be normalized independently.

### 6. [Terrazzo: Memory-Aware GPU Framework for Private Transformer Inference](https://eprint.iacr.org/2026/1870)

**Rostin Shokri, Nektarios Georgios Tsoutsos · September 6 · FHE implementation / private inference**

Terrazzo co-designs representations, tiling, modulus management, and GPU kernels to make the matrix-native GL homomorphic-encryption scheme fit on commodity GPUs despite very large ciphertexts and evaluation keys. For BERT-base at 256-input occupancy, the authors report 7.04 seconds amortized per input on a 32 GB RTX 5090 and 29.98 seconds on an A100, the latter 2.20–20.09× faster than cited single-A100 systems.

**Why it matters:** Fitting private transformer inference on a single consumer GPU changes the accessibility and deployment profile of this line of FHE work.

**Caveat:** The headline latency is amortized at high batch occupancy, not single-query latency, and the evaluation centers on BERT-base and the comparatively new GL scheme.

### 7. [Discrete Gaussian Sampling Meets BDGL Decoding: Solving the Shortest Vector Problem in \(2^{0.5596n+o(n)}\) Time](https://eprint.iacr.org/2026/1844)

**Yiming Gao, Yansong Feng, Honggang Hu · September 1 · Lattice foundations**

This independent exact-SVP algorithm combines discrete Gaussian sampling on random prime-index superlattices with one layer of BDGL product-code decoding. The authors emphasize that their pairing analysis does not assume a random list and obtain \(2^{0.5596n+o(n)}\) time with \(2^{n/2+o(n)}\) space by targeting a random affine quotient line.

**Why it matters:** It supplies a distinct route to a substantially improved rigorous SVP exponent and provides an important comparison point for the even lower \(2^{n/2+o(n)}\) claim posted two days later.

**Caveat:** The later Hhan preprint has the better headline exponent; both results are fresh, asymptotic, memory-intensive, and need proof-level reconciliation and independent validation.

### 8. [A Simple Compiler for CCA2-Secure Pseudorandom Codes in the Standard Model](https://eprint.iacr.org/2026/1867)

**Nico Döttling, Antoine Joux, Venkata Koppula, Mahesh Sreekumar Rajasree, Hendrik Waldner · September 6 · Cryptographic foundations**

The authors give a simpler black-box compiler from an adaptively robust CPA-pseudorandom code, a secure PRG, and an almost-perfectly-correct IND-CCA2 public-key encryption scheme to a CCA2-secure pseudorandom code in the standard model. The transformation doubles codeword length and preserves a constant relative decoding radius while halving the robustness and CCA2 radius parameters.

**Why it matters:** Pseudorandom codes combine error correction with covertness-like indistinguishability, and a simpler standard-model CCA2 compiler can make later constructions easier to analyze and instantiate.

**Caveat:** This is a generic feasibility result, not yet a practical code family; concrete efficiency inherits the costs and correctness requirements of the underlying PRC and public-key encryption scheme.

## Other notable papers by topic

- **FHE and private AI:** [HEAT](https://eprint.iacr.org/2026/1862) learns per-nonlinearity approximation effort during fine-tuning and reports 1.4× lower encrypted GPT-2 latency with 1.6× fewer bootstraps. [Hoss](https://arxiv.org/abs/2609.04522), first posted September 3, combines GPU and CPU TEEs for oblivious semantic search and reports up to 67× speedup over Compass.
- **Formal verification:** [Automated Reasoning for Indistinguishability in the CCSA](https://eprint.iacr.org/2026/1853) extends CryptoVampire with e-graph-based backtracking and reports automating every indistinguishability goal in the Squirrel repository.
- **Quantum foundations:** [Quantum Pessiland](https://eprint.iacr.org/2026/1834) gives oracle worlds where average-case hardness exists but the EFI pairs or one-way puzzles underlying nearly all quantum cryptography do not, establishing a relativized barrier rather than a standard-model impossibility.
- **Blockchain protocols:** [Otter](https://eprint.iacr.org/2026/1877) proposes a batch AMM where truthful behavior is dominant for users and builders by redistributing residual surplus; its MEV-resilience guarantee assumes censorship-resistant consensus and uncongested block space.
- **Physical leakage:** [Injected and Leaked](https://arxiv.org/abs/2609.04785), first posted September 4 and identified as USENIX Security 2026 work, uses electromagnetic injection and hardware nonlinearities to amplify otherwise weak leakage, including audio recovery at distances up to 30 meters.
- **FHE arithmetic:** [Improved Conversion for Gao–Zheng FHE](https://eprint.iacr.org/2026/1836) and [Arithmetic-to-Boolean Conversion via Overflow Cancellation](https://eprint.iacr.org/2026/1840) independently target constant-bootstrap ALU conversions and should be compared with the broader arbitrary-modulus construction ranked above.
- **New repository deposits and revisions:** [DNSPIR](https://eprint.iacr.org/2026/1872) is labeled a minor revision of PoPETS 2027 work; [OptiMix](https://eprint.iacr.org/2026/1863) is an NDSS minor revision; [Anonymous Attribute-Based Signcryption](https://eprint.iacr.org/2026/1861), [Subring VOLE over Galois Rings](https://eprint.iacr.org/2026/1864), and [Compact Lattice-Based NIZK Arguments](https://eprint.iacr.org/2026/1885) are labeled major revisions of ASIACRYPT 2026 publications and were not ranked as new disclosures.

## Watch next

- Independent proof checking and cryptographic-estimator updates for the two new exact-SVP algorithms, especially how their techniques relate to August’s midpoint-Hessian result.
- Audits of proof systems that accept adversarial programs or generated R1CS instances: the first Fiat–Shamir challenge must bind the correct statement representation.
- A response from Iasta’s designers and concrete validation of the weak-nonce algebraic attacks.
- Reproduction of Quasar, arbitrary-modulus FHE ALU, and Terrazzo benchmarks under standardized security, memory, batching, and hardware conditions.
- Whether the simpler CCA2 pseudorandom-code compiler leads to competitive concrete instantiations.


---

## Previous digests

- [August 18–24, 2026](digests/2026/2026-08-24.md)
- [August 11–17, 2026](digests/2026/2026-08-17.md)
- [August 4–10, 2026](digests/2026/2026-08-10.md)
- [July 28–August 3, 2026](digests/2026/2026-08-03.md)
- [July 21–27, 2026](digests/2026/2026-07-27.md)

## About this archive

This repository contains weekly research summaries, links, relevance assessments, and caveats. It does not contain the digest-generation tool or workflow source code. These digests distinguish authors' claims from independently verified results where possible and are not substitutes for reading the primary papers.

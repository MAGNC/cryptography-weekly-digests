# Weekly Cryptography Research Digest

> The latest weekly digest is displayed directly on this page. Each issue is also preserved as a dated Markdown file in the archive.

[Archived copy of this issue](digests/2026/2026-09-21.md)

---

**Coverage:** first public postings from September 15–21, 2026. Cross-posts and routine updates were removed; conference deposits and material revisions are labeled separately.

## Executive summary

Implementation attacks lead this week: a demonstrated clock-glitch fault bypasses the ciphertext comparison in mainstream Kyber/ML-KEM code and enables full secret-key recovery, while practical chosen-ciphertext attacks recover the key of full-round DuX and reduced-round YuX. A separate polynomial-time structural attack breaks McEliece variants built from elliptic algebraic-geometry codes, but does not apply to Classic McEliece’s binary Goppa construction. Constructive work includes nearly halving an evaluation-key bottleneck in matrix-native FHE, faster differentially oblivious group-by aggregation, and formal analyses of Bitcoin silent payments and Ethereum EIP-7702 delegation. These are fresh papers and author-reported experiments; independent reproduction and scheme-specific impact assessment remain important.

## Most relevant papers

### 1. [Default Correct: A New Fault Surface in the Comparison Booleanisation of Kyber-KEM](https://eprint.iacr.org/2026/2069)

**Anirudh Jaiswal, Abhilash Kumar Das, Dhiman Saha · September 19 · Post-quantum implementation attacks**

The authors target the Booleanisation stage between byte-wise mismatch accumulation and the conditional move in Kyber’s Fujisaki–Okamoto ciphertext check. A single clock glitch on an ARM Cortex-M4 reportedly forces the failure flag to zero across pqm4, PQClean, and reference Kyber-512/768/1024 implementations at all tested optimization levels; using the resulting plaintext-checking oracle, they recover each parameter set’s full secret key in one on-device run.

**Why it matters:** The fault sits upstream of the commonly recommended default-fail conditional-move ordering, so code already using that defense remains vulnerable in the tested environment.

**Caveat:** This is a physical fault-injection attack requiring device access and precise glitching; portability to other processors, compilers, hardened code, and standardized ML-KEM implementations must be evaluated separately.

### 2. [Practical Key Recovery Attacks on Full DuX and Reduced-Round YuX](https://eprint.iacr.org/2026/2045)

**Xingwei Ren, Bo Xu, Zhenyu Xiong, Yongqiang Li, Mingsheng Wang · September 17 · Symmetric cryptanalysis / FHE-friendly ciphers**

The paper exploits unexpectedly slow algebraic-degree growth in decryption to derive chosen-ciphertext zero-sum attacks. The authors report recovering a random full-round, 12-round DuX key over \(\mathbb F_{65537}\) from \(2^{32}\) chosen ciphertexts in 45 core-hours, and experimentally recover keys for 11 of 14 rounds of two YuX variants with the same data complexity.

**Why it matters:** The full DuX result is a practical master-key recovery, and the Yu2X-16 data requirement drops from a previously reported \(2^{96}\) to \(2^{32}\).

**Caveat:** The attacks require a chosen-ciphertext interface and target these specialized FHE-oriented cipher designs; the full 14-round YuX variants are not broken here.

### 3. [A Polynomial-Time Attack on the McEliece Cryptosystem on Elliptic Codes with Arbitrary Divisors](https://eprint.iacr.org/2026/2050)

**Artyom Kuninets, Ekaterina Malygina, Evgeniy Melnichuk · September 17 · Code-based cryptanalysis**

For McEliece systems instantiated with elliptic algebraic-geometry codes, the authors show how three known evaluation-divisor points suffice to reconstruct the entire divisor in polynomial time. They then remove the hint by enumerating a pair of field elements under curve automorphisms, obtaining an equivalent key in average polynomial time for the stated model.

**Why it matters:** It closes off a broader class of elliptic-code McEliece proposals, including arbitrary effective divisors that were not covered by earlier structural attacks.

**Caveat:** The result concerns elliptic algebraic-geometry codes, not the binary Goppa codes used by standardized Classic McEliece.

### 4. [Silent Payments, Formally: Security, Scanning Costs, and Blind Collaborative Payments](https://eprint.iacr.org/2026/2037)

**Rong Qian, Yu Cheng, Mengrun Chen, Yuchang Zhang, Zengli Guo · September 17 · Bitcoin privacy protocols**

The paper gives DDH-based receiver and co-transactor unlinkability proofs for BIP-352 silent payments, analyzes label linking and scanning-denial costs, and proposes a set-probing scanner reported to run 39× faster. For collaborative transactions it formalizes recipient-output exposure and malformed-share fund loss, then proposes blind collaborative payments with information-theoretic payer privacy.

**Why it matters:** BIP-352 is already implemented by wallets, so formalizing both privacy and denial-of-service economics addresses a live protocol rather than a hypothetical deployment.

**Caveat:** Security and cost conclusions depend on the modeled wallet behavior, fee data, scanning cap, and DDH assumptions; delegated scanning still has no safe default in the authors’ analysis.

### 5. [Trace-Factored BigSwitch for Matrix-Friendly FHE](https://eprint.iacr.org/2026/2058)

**Dong Jin Park, Hyunseok Jeong, Minwook Jeong, Jaeky Oh, Yongwoo Lee, Young-Sik Kim · September 18; revised September 21 · Fully homomorphic encryption**

Trace-Factored BigSwitch exploits the rank-one tensor structure of Trace-generated keys in the Gentry–Lee framework to eliminate a large product-secret evaluation key. In an OpenFHE-linked \(n=256\) prototype, the authors report reducing the relevant coefficient-domain key footprint from 768.0 to 385.5 MiB, peak memory by 15.31%, and cold key preparation by 51–63%; fused relinearization keeps measured overhead at 5.90% for a GPT-2 attention kernel and lower for deeper accumulation.

**Why it matters:** Evaluation-key memory and initialization are major obstacles to deploying matrix-native FHE for encrypted linear algebra and attention.

**Caveat:** The gains are specific to the GL construction, selected dimensions, and workload shapes; comparisons with other FHE representations require normalized security and end-to-end measurements.

### 6. [Differentially Oblivious Resizing for Group-By Aggregations](https://eprint.iacr.org/2026/2065)

**James Bell-Clark, Albert Cheu, Adria Gascon, Jonathan Katz, Lukas Gerlach · September 19 · Privacy-preserving analytics**

ROGA combines oblivious single-access machines with a differentially private resize decision, allowing confidential-VM group-by aggregation to allocate memory near actual use instead of the worst-case domain. The Rust implementation includes compiled-trace verification for its fixed-capacity operations and reports up to 50.4× speedup over cited oblivious schemes, up to 14× less memory, and 5.3× end-to-end overhead on a 277-million-packet trace using 16 cores.

**Why it matters:** Data-dependent memory allocation is a practical leakage channel and resource bottleneck for confidential analytics.

**Caveat:** Resizing intentionally releases a differentially private signal, and the security argument still depends on the CVM, public parameters, leakage model, and correctness of the verified compilation boundary.

### 7. [DelegProof: A Machine-Checked Security Analysis of EIP-7702 Delegation](https://eprint.iacr.org/2026/2060)

**Rong Qian, Yu Cheng, Lingyu Gao, Yuchang Zhang, Zengli Guo · September 19 · Blockchain protocol verification**

DelegProof models EIP-7702 authorization together with the ERC-4337 EntryPoint in Tamarin. The authors reproduce four documented attack classes, derive ten machine-checked attack patterns—including temporary-delegation failure, storage confusion, and an ERC-1271 substitution—and verify that chain-ID restrictions, account-bound initialization, and namespaced storage prevent specified classes.

**Why it matters:** EIP-7702 is live on Ethereum and changes the security boundary of ordinary externally owned accounts, making machine-checked lifecycle analysis immediately relevant.

**Caveat:** Symbolic proofs cover the model and trusted event abstractions, not all contract code or wallet behavior; the accompanying empirical claim that 63% of observed delegations are malicious also depends on dataset construction and classification.

### 8. [Witness Encryption for NP from SNARGs and Groups](https://eprint.iacr.org/2026/2063)

**Zhengzhong Jin · September 19 · Cryptographic foundations**

The paper constructs witness encryption for NP in the generic-group model from SNARGs with subexponential soundness and polylogarithmic online verification after preprocessing. Its main technical component is a Karp–Levin reduction from small-circuit satisfiability to a gap minimum-distance problem over large prime fields, yielding unconditional extractable witness encryption for polylogarithmic-size circuits inside the generic-group model.

**Why it matters:** Witness encryption is an unusually powerful primitive, and deriving it from SNARGs plus algebraic groups narrows the assumptions needed in an idealized model.

**Caveat:** The generic-group model, subexponential security, large-field reduction, and polylogarithmic circuit restriction leave a substantial gap to practical or standard-model witness encryption.

## Other notable papers by topic

- **Post-quantum privacy:** [Practical Group Signatures from a Tag-Based NTRU Sampler](https://eprint.iacr.org/2026/2077) proposes compact, runtime-oriented lattice group signatures and a reusable tag-based sampler, but relies on two new hybrid NTRU/ISIS-style assumptions that need scrutiny.
- **Differential privacy and MPC:** [On Aborts in Differential Privacy](https://eprint.iacr.org/2026/2059) formalizes how selective aborts affect privacy and shows stronger guarantees for fair or partially fair executions than ordinary security with abort.
- **Quantum cryptanalysis:** [Low-Space Quantum Discrete Logarithms on Genus-Two Jacobians](https://eprint.iacr.org/2026/2057) reports an explicit 1,923-logical-qubit allocation for a cited challenge instance—37.2% below its comparison—with a sub-\(2^{60}\) capped Toffoli count under stated modeling assumptions.
- **Side-channel analysis:** [A Unified Framework for Statistical Side-Channel Distinguishers](https://eprint.iacr.org/2026/2041) develops a hypothesis-testing framework for leakage assessment. [Soft Analytical Side-Channel Attacks on SHA-2 and HMAC](https://eprint.iacr.org/2026/2071) is a TCHES 2026 repository deposit and was not treated as a new disclosure.
- **Zero knowledge and provenance:** [ZK-JPEG](https://eprint.iacr.org/2026/2039) proves that JPEG compression and selected edits were correctly applied to a hidden committed image; it is labeled a minor revision of SCN 2026 work.
- **Conference deposits and major revisions:** [A Unified Reduction from RLWE to MP-LWE](https://eprint.iacr.org/2026/2026) is an ASIACRYPT 2026 publication deposited this week. [New PCFs and Exponent VRFs from DCR](https://eprint.iacr.org/2026/2030) and [Distributed SNARGs Resilient to Corrupt Verifiers](https://eprint.iacr.org/2026/2068) are labeled major revisions of ASIACRYPT and TCC 2026 work, respectively.

## Watch next

- Independent reproduction of the Kyber Booleanisation fault across additional processors, compilers, and hardened ML-KEM libraries, followed by evaluation of the proposed integrity-checked fix.
- Design-team responses for DuX and YuX, and whether the algebraic techniques extend to other low-degree FHE-friendly ciphers.
- Confirmation of the elliptic-code McEliece reconstruction and a clear map of which algebraic-geometry-code proposals remain unaffected.
- Audits of deployed BIP-352 scanners, collaborative-payment implementations, and EIP-7702 wallets against the newly formalized failure modes.
- Reproduction of the FHE and ROGA performance claims under uniform security, hardware, memory, and leakage assumptions.


---

## Previous digests

- [September 8–14, 2026](digests/2026/2026-09-14.md)
- [September 1–7, 2026](digests/2026/2026-09-07.md)
- [August 18–24, 2026](digests/2026/2026-08-24.md)
- [August 11–17, 2026](digests/2026/2026-08-17.md)
- [August 4–10, 2026](digests/2026/2026-08-10.md)
- [July 28–August 3, 2026](digests/2026/2026-08-03.md)
- [July 21–27, 2026](digests/2026/2026-07-27.md)

## About this archive

This repository contains weekly research summaries, links, relevance assessments, and caveats. It does not contain the digest-generation tool or workflow source code. These digests distinguish authors' claims from independently verified results where possible and are not substitutes for reading the primary papers.

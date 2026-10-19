# Weekly Cryptography Research Digest

> The latest weekly digest is displayed directly on this page. Each issue is also preserved as a dated Markdown file in the archive.

[Archived copy of this issue](digests/2026/2026-10-19.md)

---

**Coverage:** first public postings from October 13–19, 2026. Cross-posts and routine updates were removed; newly deposited conference versions and materially important revisions are labeled separately.

## Executive summary

Implementation and mode-level failures dominate this week: Off-Key shows that inconsistent MAC keys across SPDZ-family preprocessing batches can reveal shared secrets in evaluated MPC frameworks, while two independent Eevee analyses report structural distinguishers and forgeries. Post-quantum implementation security is also prominent, with new power-analysis work targeting FAEST and SDitH's VOLE-in-the-head structure and separate Round-3 SQIsign signing paths. Constructive results include asynchronous threshold FHE at hundreds of parties, a fast lattice-based aggregator for Falcon signatures, a language and analyzer for proof systems, and private LLM decoding optimized for multi-token generation. All performance and attack results below are fresh author claims or preprints and need independent reproduction and deployment-specific assessment.

## Most relevant papers

### 1. [Off-Key: Inconsistent MAC Keys Compromise Privacy in SPDZ-Family MPC](https://eprint.iacr.org/2026/2533)

**Marcel Keller, Sela Navot, Nirvan Tyagi · October 19 · MPC implementation security**

SPDZ-family MPC authenticates secret shares under a global, secret-shared MAC key, so values and preprocessing combined in one computation must use the same key. The authors show that violating this invariant can let a corrupted party recover a shared secret from one authentication check or test guesses through selective failure; they demonstrate the issue in evaluated versions of MP-SPDZ, SCALE-MAMBA, and FANNG-MPC across batches, threads, or process runs.

**Why it matters:** These frameworks are widely used research and engineering foundations for maliciously secure MPC, and the attack turns state-management mistakes into direct privacy failures.

**Caveat:** This does not break the underlying SPDZ security proofs; exposure depends on an integration allowing differently keyed material to be mixed, and preserving the relevant preprocessing state prevents the attack.

### 2. [DPA-style Attacks on VOLE-in-the-Head Signature Schemes](https://eprint.iacr.org/2026/2519)

**Jinnuo Li, Shu Han, Yunyan Zou, Fan Zhang, Puwen Wei, Weijia Wang, Hao Cheng · October 17; revised October 18 · Post-quantum side channels**

The paper develops what the authors describe as the first general differential-power-analysis framework for VOLE-in-the-head signatures. It gives six attack classes against FAEST and SDitH, targeting the secret witness needed to forge arbitrary messages, and validates them with simulated leakage plus measurements on three ARM Cortex-M4 devices.

**Why it matters:** FAEST and SDitH are Round-3 candidates in NIST's additional post-quantum signature process, so concrete physical leakage can influence both implementation guidance and standardization evaluation.

**Caveat:** Trace requirements and success rates depend on the device, compiler, implementation, and leakage model; resistance of optimized, masked, or otherwise hardened implementations remains open.

### 3. [Structural Cryptanalysis of the Eevee AEAD Family](https://eprint.iacr.org/2026/2496)

**Shuping Mao, Peng Wang, Jiadong Han, Tingting Guo, Yan Jia, Ying Chen · October 15 · Authenticated-encryption cryptanalysis**

The authors report high-advantage privacy distinguishers and deterministic forgeries across Umbreon, Jolteon, and Espeon, including attacks under nonce misuse and, for selected paths, nonce-respecting encryption. They attribute the failures to online-prefix consistency, associated-data separation, transcript conditioning, and checksum freshness, and argue that the central issues are in the mode design rather than only in an implementation encoding.

**Why it matters:** Eevee was proposed for IoT-to-cloud secure computation, and the reported attacks contradict advertised confidentiality or authenticity bounds for multiple family members.

**Caveat:** The results apply to the supplied full-version specification and exact mode behavior; deployment prevalence is unclear. A concurrent independent paper, [Associated-Data Correlations in Eevee](https://eprint.iacr.org/2026/2518), reports overlapping forgery concerns and should be reconciled with this analysis.

### 4. [Power Analysis on SQIsign via Ideal Reconstruction from Quaternion Leakages](https://eprint.iacr.org/2026/2508)

**Won Kim, Jaeho Jeon, JeongHwan Lee, Suseong Lee, Young-Sik Kim, Changmin Lee · October 16 · Post-quantum side channels**

This work identifies five leakage points in the Round-3 SQIsign signing procedure and supplies polynomial-time algebraic algorithms that reconstruct the secret key from the corresponding quaternion-related values. It also handles partial-bit leakage and reports power analysis of the source implementation to extract the required information.

**Why it matters:** SQIsign is another Round-3 NIST additional-signature candidate, and the paper connects algebraic leakage models to measurements against current code rather than only toy parameters.

**Caveat:** The abstract does not establish portability across platforms or hardened implementations, and concrete trace counts, countermeasure costs, and independent physical reproduction require closer evaluation.

### 5. [Tulva: Practical Asynchronous Threshold FHE](https://arxiv.org/abs/2610.20521)

**Valerio Cini, Antonio Guimarães, Russell W. F. Lai, Michał Osadnik, Ivy K. Y. Woo · October 14 · Threshold FHE**

Tulva adds asynchronous threshold decryption to virtually any base FHE scheme while leaving its ciphertext unchanged except for a random tag. For a 16-of-512 setting with public preprocessing, the authors report 3.7 ms for secret-dependent partial decryption and 0.5 ms for public share combination; compared with a cited synchronous baseline, the online phase is non-interactive and supports 32 times as many total users.

**Why it matters:** Removing online interaction and supporting hundreds of parties addresses a major coordination bottleneck for threshold FHE in decentralized and privacy-preserving EVM applications.

**Caveat:** The comparison includes a ciphertext-dependent public preprocessing step of about 0.5 seconds, and end-to-end costs depend on the base FHE scheme, network, threshold profile, and workload.

### 6. [Aerie: Efficient Aggregation of Falcon Signatures](https://eprint.iacr.org/2026/2501)

**Alberto Centelles, Quang Dao, Mohammadtaghi Badakhshan, Justin Thaler · October 15 · Post-quantum signatures / SNARKs**

Aerie uses a lattice-based SNARK to aggregate Falcon-512 and Falcon-1024 signatures, leaving salts outside the proof so the verifier can compute hashes directly. On an Apple M4 Max without GPU acceleration, the prototype aggregates 32,768 Falcon-512 signatures in 0.65 seconds, reports 42.1 bytes per signature including salts (or about 66 KiB total with a shared salt), and verifies in 54 ms on one core.

**Why it matters:** Efficient post-quantum aggregation could replace compact BLS-style validator-vote aggregation in systems that need quantum-resistant signatures.

**Caveat:** These are selected prototype benchmarks; deployment security also depends on Falcon, the Akita commitment scheme, salt handling, proof assumptions, and independent cross-platform measurements.

### 7. [Zippel: A Language, Compiler, Runtime, and Analyzer for ZK Proof Systems](https://eprint.iacr.org/2026/2517)

**Sebastian Angel, Sydnie-Shea Cohen, Eleftherios Ioannidis, Pratyush Mishra, Alex Ozdemir, Yuxiang Qiu, Alireza Shirzad · October 16 · Zero-knowledge engineering**

Zippel lets developers express proof systems in paper-like pseudocode while its type system and runtime synchronize transcripts and apply Fiat–Shamir transformations. The authors implement 35 systems—including Sumcheck, KZG, Groth16, Bulletproofs, Spartan, and HyperPlonk—and report competitive multicore performance plus automated completeness and soundness analyses from protocol descriptions.

**Why it matters:** A common language that couples implementation with protocol-level checks could reduce transcript, compilation, and analysis mistakes across rapidly evolving proof stacks.

**Caveat:** The analyses cover properties represented by Zippel's language and model, not every arithmetic, setup, implementation, or protocol-design flaw. The ePrint is a newly deposited minor revision of an IEEE S&P 2027 paper.

### 8. [HERMES: High-Performance Private Decoding for Large Language Models](https://eprint.iacr.org/2026/2532)

**MohammadHossein Olyaiy, Cora Shao, Dingqing Yang, Mohammad Shahrad, Mieszko Lis · October 18 · Private inference**

HERMES targets multi-token LLM decoding rather than only private prefill: it reduces interactive rounds in nonlinear kernels and uses homomorphic matrix multiplication to keep the growing KV cache encrypted on the server while exploiting grouped-query attention. On Llama 3.1 8B Instruct, the authors report up to 2.7× lower time per output token and 2.1× lower total private-inference latency across their tested prompts and networks.

**Why it matters:** Autoregressive decoding, not first-token latency, dominates realistic private chatbot workloads, and encrypted server-side KV caching directly targets that bottleneck.

**Caveat:** The headline figures are relative, workload-dependent speedups; absolute latency, accuracy effects, communication, client cost, and comparisons under uniform security settings need independent study.

## Other notable papers by topic

- **FHE and private computation:** [High-Precision CKKS with Fixed Modulus Consumption](https://eprint.iacr.org/2026/2531) trades unused SIMD slots for precision, claiming roughly (n) times as many precision bits when only (1/n) of slots are used at the same scaling factor and parameters. [Privacy-Preserving Graph Neural Network Inference](https://arxiv.org/abs/2610.18054) combines protocol and systems optimizations for encrypted GNN evaluation and reports second-scale author benchmarks.
- **Quantum and information-theoretic foundations:** [EFI ⇔ 1PRS](https://arxiv.org/abs/2610.18799) removes non-uniformity from an earlier equivalence between EFI pairs and expanding single-copy pseudorandom-state generators, yielding a uniform meta-complexity characterization. [Secret Sharing from Non-Extractable Randomness](https://arxiv.org/abs/2610.20173) negatively resolves an open question by constructing perfectly private sharing from source classes that admit no near-uniform deterministic one-bit extractor.
- **Proof systems:** [Speeding up Sumcheck using Algebraic Geometry](https://eprint.iacr.org/2026/2494) uses binary-preserving multiplication codes and reports a 2.36× faster skipped sumcheck round and 1.65× faster full zerocheck in a selected Flock-scale benchmark.
- **Post-quantum security estimates:** [How Secure Are NTRU+ Keys?](https://eprint.iacr.org/2026/2527) refines module-lattice attacks but estimates at most a three-bit Core-SVP reduction for current NTRU+ parameters. [Revisiting the Security Analysis of VDOO](https://eprint.iacr.org/2026/2528) lowers estimates for a claimed 512-bit setting while leaving the resulting work factors extremely large.
- **Material revisions:** [Zero-Knowledge and Quantum CSS Codes Are Equivalent](https://eprint.iacr.org/2026/467), first posted earlier in 2026 and updated October 18, is treated as a revision rather than a new disclosure. Older ePrints updated this week—including Twist and Shout, LAMP, Veloz, and Cirrus—were likewise excluded from the ranking.

## Watch next

- Patch guidance and default-state changes from MP-SPDZ, SCALE-MAMBA, and FANNG-MPC, plus audits for inconsistent authentication keys in downstream applications.
- NIST and implementer responses to the new FAEST, SDitH, and SQIsign side-channel results, especially reproducible trace counts and hardened implementations.
- Reconciliation of the two independent Eevee analyses and any revised security claims or mode designs.
- Independent reproduction of Tulva and Aerie under uniform security, network, memory, and hardware conditions.
- Broader evaluation of Zippel's analyzer coverage and HERMES's absolute multi-token latency, accuracy, and communication costs.

---

## Previous digests

- [October 6–12, 2026](digests/2026/2026-10-12.md)
- [September 29–October 5, 2026](digests/2026/2026-10-05.md)
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

# Derived works of EDEN — research notes

Notes for the §Takeaway paragraph about subsequent work that builds on or
applies EDEN. Sourced from the four papers/pages the author flagged.

## 1. SDR — Succinct Document Representation

- **Citation**: N. Cohen, A. Portnoy, B. Fetahu, A. Ingber, *SDR: Efficient
  Neural Re-ranking using Succinct Document Representation*, ACL 2022.
- **URL**: https://aclanthology.org/2022.acl-long.457/
- **Domain**: Information retrieval / neural re-ranking.
- **What it does**: Compresses BERT intermediate document representations
  for late-interaction re-ranking. Two-stage pipeline:
  (1) a novel autoencoder using the document's textual content in both
  encoding and decoding, then (2) modern quantization of the autoencoder
  output.
- **Relationship to EDEN**: Uses EDEN in the quantization stage (per the
  author's own note). The authors include Amit Portnoy (also a co-author
  of EDEN), so this is a same-author follow-on application rather than a
  third-party citation.
- **Headline result**: 4×–11.6× higher compression rates for the same
  ranking quality on MSMARCO passage re-ranking; 7.7× on TREC CAR.
- **Framing for the post**: an early downstream application of EDEN to
  embedding storage and retrieval.

## 2. MS-EDEN (Quartet II)

- **Citation**: A. Panferov et al., *Quartet II: Accurate LLM Pre-Training
  in NVFP4 by Improved Unbiased Gradient Estimation*, 2026 (arXiv
  2601.22813 per the EmergentMind page).
- **URL**: https://www.emergentmind.com/topics/ms-eden-quantization-operator
  (topic page; primary paper at arXiv 2601.22813)
- **Domain**: Low-precision LLM pre-training (NVFP4 on NVIDIA Blackwell
  GPUs).
- **What it does**: Defines an unbiased, low-MSE quantization operator
  ("MS-EDEN") for the NVFP4 micro-scaling format. The construction:
  - Apply a Randomized Hadamard Transform per rotation group (R = 128).
  - Round to NVFP4 with soft clipping.
  - Compute an EDEN-style correction factor
    `S_h = ⟨x_RHT, x_RHT⟩ / ⟨x_RHT, x_RTN⟩` per group.
  - Merge `S_h` into the FP8 group scale via unbiased stochastic rounding.
  This makes `S_h` an unbiasing scale on top of an RTN-quantized rotated
  vector — a direct micro-scale adaptation of EDEN-unbiased's
  `S = ⟨x, q⟩ / ⟨q, q⟩` style ratio (with the inner-product roles reversed
  for the unbiased target).
- **Relationship to EDEN**: Direct extension. The name "MS-EDEN" =
  "Micro-Scaling EDEN" telegraphs the lineage. The correction factor is
  literally an EDEN-style scale, applied at micro-scale (per group)
  instead of per-vector, and routed into the FP8 group scale rather than
  applied at decode time.
- **Headline result**: ~2× MSE reduction vs. stochastic rounding while
  remaining unbiased. End-to-end ~4.2× speedup over BF16 on NVIDIA RTX
  5090 for linear layers; up to 25% closer to BF16 BPB during 1.9B
  pretraining.
- **Framing for the post**: training-time quantization. The clearest
  example of EDEN's scale construction being adapted to a new precision
  format (NVFP4) and a new operating regime (per-group, hardware-aligned).

## 3. HIGGS

- **Citation**: V. Malinovskii, A. Panferov, I. Ilin, H. Guo, P.
  Richtárik, D. Alistarh, *HIGGS: Pushing the Limits of Large Language
  Model Quantization via the Linearity Theorem*, NAACL 2025.
- **URL**: https://aclanthology.org/2025.naacl-long.543/
- **Domain**: Data-free post-training quantization (PTQ) of LLM weights.
- **What it does**: A "linearity theorem" relating per-layer
  reconstruction error to model perplexity increase, used to motivate
  (1) HIGGS itself: a data-free quantization method using **Hadamard
  rotations and MSE-optimal grids** (and (2) an optimal non-uniform
  per-layer bit-allocation scheme via dynamic programming).
- **Relationship to EDEN**: Per the author's note, "uses a generalization
  of EDEN". Concretely: HIGGS's recipe — randomized Hadamard rotation +
  MSE-optimal grid (lattice/Lloyd-Max-style codebook) — is the EDEN
  recipe (rotate → quantize on a known-distribution-optimal grid),
  generalized from scalar Lloyd–Max to vector lattice quantization on
  groups.
- **Headline result**: Outperforms NF4 and other data-free baselines on
  Llama-family models.
- **Framing for the post**: the rotation + distribution-aware-grid
  pattern from EDEN, extended to vector quantization for weight-only PTQ.

## 4. AQUA-KV (HIGGS applied to KV-cache)

- **Citation**: A. Shutova et al., *Cache Me If You Must: Adaptive
  Key-Value Quantization for Large Language Models*, ICML 2025 (arXiv
  2501.19392).
- **URL**: https://arxiv.org/html/2501.19392v2
- **Domain**: KV-cache compression for long-context LLM inference.
- **What it does**: Trains compact linear predictors that predict KV
  vectors from previous-layer KVs and quantizes the residuals. Uses HIGGS
  as the backbone quantizer (HIGGS itself uses RHT + MSE-optimal grids,
  which generalizes EDEN — see #3 above).
- **Relationship to EDEN**: Indirect — through HIGGS. AQUA-KV explicitly
  uses HIGGS as its preferred backbone quantizer, and HIGGS uses a
  generalization of EDEN. So AQUA-KV's KV-cache-compression result
  effectively rides on the rotation + distribution-aware-grid pipeline
  inherited from EDEN.
- **Headline result**: Near-lossless inference at 2–2.5 bits per value
  on Llama-3.x 70B; one-shot calibration in 1–6 hours on a single GPU.
- **Framing for the post**: KV-cache compression. Closes the loop on the
  TurboQuant comparison: KV-cache is the application TurboQuant-prod was
  pitched at, and AQUA-KV (via HIGGS via EDEN) is the EDEN-lineage answer
  in the same space.

## Quick relationship map

```
EDEN (NeurIPS 2021 / ICML 2022)
├── SDR (ACL 2022) — uses EDEN for the quantization stage of doc re-ranking
├── MS-EDEN / Quartet II (2026) — micro-scaling EDEN for NVFP4 training
└── HIGGS (NAACL 2025) — generalization of EDEN to vector quantization (data-free PTQ)
    └── AQUA-KV (ICML 2025) — uses HIGGS as backbone for KV-cache compression
```

So three direct applications + one transitive (via HIGGS).

## Drafting notes for the post paragraph

### What we want to convey

1. EDEN was originally for **distributed mean estimation** (the setting
   the original DRIVE/EDEN papers solve). The post should remind the
   reader of this since it's the original motivation, but the §How EDEN
   quantizes a vector section already abstracts it as "vector
   quantization".
2. EDEN has since been used / extended in several settings:
   embedding storage and retrieval (SDR), training-time quantization
   (MS-EDEN/Quartet II), data-free weight PTQ (HIGGS), and KV-cache
   compression (AQUA-KV via HIGGS).
3. We're not trying to be exhaustive — these are illustrative.

### Honesty considerations

- SDR is co-authored by Amit; should be cited but might not need to be
  flagged separately. The implementation list above already mentions
  Portnoy's repos, so a co-authored downstream paper is in keeping with
  the post's voice.
- AQUA-KV's link to EDEN is transitive. The honest framing is "EDEN's
  scale-aware-rotation pattern" influencing HIGGS, with AQUA-KV as the
  KV-cache application of HIGGS. We could either (a) list HIGGS and
  separately note AQUA-KV uses it for KV-cache, or (b) just list HIGGS
  and skip AQUA-KV. Given the post's prior emphasis on KV-cache as
  TurboQuant's pitch, including AQUA-KV ties back to the post's framing.
- "Generalizes" / "uses" / "adapts" / "extends" — pick verbs that match.
  - SDR: **uses** EDEN (in its quantization stage)
  - MS-EDEN: **adapts/extends** EDEN (micro-scaling, group-wise variant)
  - HIGGS: **generalizes** EDEN (to vector quantization)
  - AQUA-KV: builds on HIGGS, so "applies the HIGGS extension" or
    "uses HIGGS as backbone"

### Suggested placement

Inside §Takeaway, between the "Full EDEN implementations are available..."
paragraph and the closing pointer paragraphs. New paragraph signals
"EDEN's family of derived methods".

### Draft paragraph (for the post)

**Option A — flat list:**

> EDEN was originally developed for **distributed mean estimation** in
> federated/distributed training, but the same rotation + scale
> construction has since been applied across other settings: vector
> compression for embedding retrieval ([SDR](https://aclanthology.org/2022.acl-long.457/) [[8]](#ref8)),
> training-time quantization ([MS-EDEN, in Quartet II](...)
> [[9]](#ref9)), data-free LLM weight quantization ([HIGGS](https://aclanthology.org/2025.naacl-long.543/)
> [[10]](#ref10), which generalizes EDEN to vector quantization), and
> KV-cache compression ([AQUA-KV](https://arxiv.org/abs/2501.19392)
> [[11]](#ref11), built on HIGGS).

**Option B — narrative arc:**

> EDEN was originally developed for **distributed mean estimation** in
> federated/distributed training. The same rotation + closed-form-scale
> pattern has since been applied to embedding compression for neural
> retrieval ([SDR](https://aclanthology.org/2022.acl-long.457/) [[8]](#ref8)),
> generalized to vector quantization for data-free LLM weight
> compression ([HIGGS](https://aclanthology.org/2025.naacl-long.543/)
> [[10]](#ref10)), adapted as a micro-scaled, hardware-aligned variant
> for NVFP4 training ([MS-EDEN in Quartet II](...) [[9]](#ref9)), and
> applied to KV-cache compression via HIGGS ([AQUA-KV](https://arxiv.org/abs/2501.19392)
> [[11]](#ref11)).

I prefer **B** — the verbs ("applied", "generalized", "adapted",
"applied via") accurately describe each relationship, and the chronology
arcs from 2022 (SDR) → 2025 (HIGGS, AQUA-KV) → 2026 (MS-EDEN), which
also tracks the broadening of scope from a single use case to weight
PTQ and training quantization.

### Reference entries to add

```markdown
<a id="ref8"></a>[8] N. Cohen, A. Portnoy, B. Fetahu, A. Ingber, [SDR: Efficient Neural Re-ranking using Succinct Document Representation](https://aclanthology.org/2022.acl-long.457/) (2022), ACL 2022.

<a id="ref9"></a>[9] A. Panferov et al., [Quartet II: Accurate LLM Pre-Training in NVFP4 by Improved Unbiased Gradient Estimation](https://arxiv.org/abs/2601.22813) (2026), arXiv:2601.22813.

<a id="ref10"></a>[10] V. Malinovskii, A. Panferov, I. Ilin, H. Guo, P. Richtárik, D. Alistarh, [HIGGS: Pushing the Limits of Large Language Model Quantization via the Linearity Theorem](https://aclanthology.org/2025.naacl-long.543/) (2025), NAACL 2025.

<a id="ref11"></a>[11] A. Shutova et al., [Cache Me If You Must: Adaptive Key-Value Quantization for Large Language Models](https://arxiv.org/abs/2501.19392) (2025), ICML 2025.
```

Caveats:
- Quartet II's arXiv ID `2601.22813` is from the EmergentMind topic page;
  worth double-checking before publication (the date "30 Jan 2026"
  suggests it's a real arXiv paper, but I haven't verified the ID
  directly).
- AQUA-KV authors are listed in the arXiv HTML as "Alina Shutova,
  Vladimir Malinovskii, Vage Egiazarian, Denis Kuznedelev, Denis Mazur,
  Nikita Surkov, Ivan Ermakov, Dan Alistarh" — using "et al." for
  brevity matches the style of references already in the post that
  shorten long author lists. Let me know if you'd rather list them all.
- HIGGS author ordering matches the citation block on aclanthology.org.
- SDR is co-authored by Amit Portnoy (the post author). No special
  flagging needed since the byline is already at the top of the post,
  but worth being aware of.

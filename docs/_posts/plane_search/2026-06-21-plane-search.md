---
layout: post
title: Plane Search
date: 2026-06-21 12:30:00 +0000
---

Trying to find a ~~needle in a haystack~~ plane in the desert.

In which I search 4,130 photos of the desert taken from a drone to find a missing RC jet plane. 

## Background

Project started from [this reddit post](https://www.reddit.com/r/computervision/comments/1u76ln1/need_help_searching_3000_aerial_images_to_locate/). OP was flying a high speed plane and lost contact, then sent out a drone to search for it. OP requested help processing the drone imagery to find the plane.

<figure> <img src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1782064296_Screenshot_from_2026-06-21_10-51-17.png" alt="The plane" width="50%" style="aspect-ratio: 524 / 698"> <figcaption>The plane</figcaption> </figure>

<figure> <img src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1782065968_Screenshot_from_2026-06-21_11-19-12.png" alt="The plane" width="100%" style="aspect-ratio: 953 / 539"> <figcaption>The data (12 of 3274 images)</figcaption> </figure>


Initial* dataset info
* [Nadir](https://en.wikipedia.org/wiki/Nadir) drone imagery
* taken in grid search pattern with minimal overlap
* 3274 photos
* resolution 4624x3472
* exifdata stripped

\* dataset is extended to 4,130 later in project - read on.

## Approach

Took DINOv3 ViT-S/16 patch embeddings for all images (reusing code from [festivity mapping](https://tim-fan.github.io/blog/#project=festivity_maps)), producing a dataset of 205M patch embeddings of dimension 384.

From there, tried three directions..

### Anomaly detection

> 💭 **Thought:**
> "Amongst 3k images of sand and shrubs, a plane should be very distinct - I can try anomaly detection".

Restated, there should be a very small (~5) number of patch embeddings representing the plane which are far from all the other embeddings (sand and shrubs). 

<figure> <img src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1782073481_anomaly_detection_concept.png" alt="Anomaly detection concept" width="70%" style="aspect-ratio: 1469 / 854"> <figcaption>Can we detect the plane as an outlier?</figcaption> </figure>


**Method:** For each patch embedding, score by distance to center of knn neighbours (k=100). The plane embeddings should be very far from neighbours. Use [FAISS](https://github.com/facebookresearch/faiss) for fast approximate knn search on very large dataset. 

This has the advantage of not requiring a trained "plane detector". 

**Result:** I failed to anticipate how much junk was out there! The plane may be a distinctive object, but there are many other distinctive objects. This approach would fail to surface a plane against all the other man made debris.

<figure>
<video controls width="100%" style="aspect-ratio: 1542 / 936"><source src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1782005612_vokoscreenNG-2026-06-20_16-37-20.mp4" type="video/mp4" ></video>
<figcaption> Anomaly detector output
</figcaption>
</figure>


### Plane detection

> 💭 **Thought:**
> "OK so we need to look for planes specifically."

Following [official dinov3 examples](https://github.com/facebookresearch/dinov3/blob/main/notebooks/foreground_segmentation.ipynb), I generated a few synthetic examples to train a plane classifier. 

Synthetic data was produced with a combo of linkedin photos of the plane, nano banana to produce top-down views, and gimp to place it in the desert nadir frames.

This gives us target embeddings we can use to train a classifier, to search for similar patches in the dataset.
<figure> <img src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1782077227_plane_detection_concept.png" alt="Plane detection concept" width="100%" style="aspect-ratio: 1408 / 768"> 
</figure>

10 synthetic images + labels were created. One example:
<figure> <img src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1782005900_Screenshot_from_2026-06-20_18-37-44.png" alt="Example synthetic image" width="100%" style="aspect-ratio: 1015 / 773"> <figcaption>Example synthetic image</figcaption> </figure>

**Result:** First pass, this was good at detecting dark triangular patches, however the vast majority were shadows.

<figure>
<video controls width="100%" style="aspect-ratio: 1920 / 1080"><source src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1782005023_vokoscreenNG-2026-06-20_18-16-40.mp4" type="video/mp4" ></video>
<figcaption> Plane detector output
</figcaption>
</figure>

### CLIP Re-ranking

> 💭 **Thought:**
> "Perhaps [CLIP](https://openai.com/index/clip/) can tell a plane from a shadow"

-> Take all detections from DINO and rerank using CLIP, hoping CLIP's semantic understanding will better separate shadows from actual planes.

**Re-ranking process:** take OpenCLIP ViT-B-32 embeddings (512-dim) per detection, compare (cosine similarity) with text encoding for prompt: "a black remote controlled jet plane crashed in the desert". Rank detections by similarity score.

<figure>
<video controls width="100%" autoplay loop muted playsinline style="aspect-ratio: 1080 / 486"><source src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1782075172_exp06_top25.mp4" type="video/mp4"></video>
<figcaption> Top 25 detections after CLIP reranking
</figcaption>
</figure>

Some of those detections look plausible, but still nothing looks distinctly like the missing plane.

About this time, an update from reddit...

## The plane is found 😲

After implementing and testing the above, OP confirmed the plane was not in the original dataset after all    (╯°□°)╯︵ ┻━┻

[The plane was found by OP outside the originally scanned area](https://www.reddit.com/r/computervision/comments/1ubtjan/update_plane_has_been_found/). Kindly the additional scan dataset of 856 images (one of which is confirmed to contain the plane) was provided. This allows those of us working on the problem to test our algorithms. 

The new dataset does contain metadata, so we can see the search area:
<figure> <img src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1782071719_Screenshot_from_2026-06-21_12-44-08.png" alt="Extended search space" width="100%" style="aspect-ratio: 1235 / 843"> <figcaption>Extended search space</figcaption> </figure>

Extrapolating area per image, I estimate the total search space (original dataset + extended) was around 81M ft², almost 3 square miles.

> 💭 **Thought:**
> "Now we know the plane *is* in the extended dataset, can our model find it??"

The DINO classifier and CLIP reranker were tested on the full extended dataset of 4130 images to see if we have a winning algorithm 🫣

**DINO results on extended dataset**

<figure> <img src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1782107439_strip_dino_top5.png" alt="DINO results on extended dataset" style="aspect-ratio: 1564 / 300"> <figcaption>Top 5 detections from DINO-based classifier</figcaption> </figure>

.. underwhelming, and a little concerning. I saw no plane in the top 200 detections.

**CLIP results on extended dataset**

Rerank top 6k DINO detections using CLIP.

<figure> <img src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1782107837_strip_clip_top5.png" alt="CLIP results on extended dataset" style="aspect-ratio: 1564 / 300"> <figcaption>Top 5 detections from CLIP reranking</figcaption> </figure>

There it is! Sitting at rank #2, the plane has been detected ✈️

Detection image:
<figure> <img src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1782105850_0002_S1009194.png" alt="The detected plane" style="aspect-ratio: 1640 / 738"> </figure>

The crash site (lat/lon from detection image metadata):
<figure> <img src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1782108178_Screenshot_from_2026-06-21_22-32-38.png" alt="The crash site" style="aspect-ratio: 935 / 805"> </figure>

## Conclusions

That wasn't how I expected to spend my weekend 😂

Happy to have found the plane in the end, although it's such a clear view I wonder if a much simpler detector would have worked. Will be interested to see if others get positive results with other algorithms.

Anyway, feel free to reach out in future if you need to find something in the desert!

☀️🌵🌵✈️🌵

<hr/>

<details markdown="1">
  <summary>Appendix — project thread map</summary>

Summary of the project discussion with claude - all the things we tried, and other topics discussed. Generated by claude at and of session:

  Project thread map — find the lost plane

  Living index. Status: 🟢 open · ✅ closed · ⏸️  parked · ❌ dead-end.

  0. Goal

  Find one downed RC plane in ~3274 desert drone photos. ✅ framing set.

  1. Data & environment

  - ✅ Two capture sessions: 1714 .jpeg + 1560 .JPG (different lighting).
  - ❌ EXIF-timestamp / flight-grid reconstruction — timestamps clobbered; abandoned.
  - ✅ Env: uv, torch pinned <=2.7.1 for the sm_50 Maxwell GPU.
  - ✅ Extraction: DINOv3 ViT-S16, native-res tiling, 205M patches → fp16 shards.
  - 🟢 Experiment tracking: git tags, experiments/<exp>/report.md, FiftyOne datasets.

  2. Anomaly detection (find the "odd" thing) — mostly closed

  - ✅ exp01 coarse (distance-to-nearest-centroid): baseline; surfaced horizons + fences.
  - ✅ Frame aggregation: 99th-percentile → top-5-mean.
  - ✅ exp02 uniqueness (large-k kNN): demoted recurring horizons/fences. Key idea.
  - ✅ Blob grouping: connected-components merge patches → one object box.
  - ⏸️  Coarse cell-population pre-filter: parked (cells too uniform).

  3. Plane search (find the "plane-like" thing)

  - ❌ dino.txt text query: ruled out (tracks vegetation texture, ViT-L only).
  - 🟢 Synthetic positives: 5 examples + masks. Plane ~2×2 patches.
    - 🟢 SCALE concern: synthetics likely UNDERSIZED → validation pessimistic.
  - ✅ exp03 classifier (LR on synthetic): patch-level great, image-level FAILS (median 2683/3274).
  - 🟢 Hard negatives: → median 2683→1163. Helps, insufficient alone.
  - ✅ exp3b: hard-neg classifier full rescore (scores_plane_hn).
  - 🟢 exp04 = classifier × uniqueness × blob: drop-one-out median 2683→38/3274 (5/9 in top-60). BIMODAL.
  - ✅ exp05 per-cluster uniqueness: no metric gain; value = cluster field to filter junkyards.
  - ✅ exp06 CLIP semantic rerank → became the winner (see §8). NB: "biased toward WHITE" claim WALKED BACK (no evidence). Reality: CLIP weak on achromatic objects on chromatic backgrounds +
  leans on background.

  4. Root blockers — RESOLVED by the real plane

  - ✅ Precision ceiling: the dino classifier never beat it — scores plane-like texture everywhere. Fix was a semantic second stage (CLIP), not more classifier tuning.
  - 🟢 Believed input-side fix (better synthetics) turned out unnecessary — real plane clearer than synthetics; CLIP carried it.

  5. Validation methodology — CLOSED

  - ✅ Drop-one-out CV was our proxy for "find an unseen plane" with no ground truth.
    - Still-valid second job: CV-tuning C on synthetic only (→ C=0.1, matched hardcoded). No test leak.
  - ✅ The real plane retired the proxy: it IS the honest held-out test now (§8).

  6. Tooling / viz (closed unless noted)

  - ✅ FiftyOne view, per-experiment. Detail-callout renders. Merged-object boxes. Context crops.

  7. Concept explainers (closed reference)

  cosine-vs-classifier · decision-plane-as-direction-on-sphere · why-embeddings-are-directional · IVF/PQ · k-means-follows-mass-not-peaks · coreset/PatchCore · drop-one-out rationale ·
  softmax-over-prompts-vs-raw-cosine (contrastive/relative vs absolute; rankings differ).

  8. FINALE — real plane found (2026-06-21) ✅

  - Owner found it in an EXTENDED search area → dual-camera RGB+thermal capture: 856 RGB + 856 thermal. RGB pipeline ran on the 856 RGB; thermal set aside (the re-fly sensor we'd suggested).
  - Combined dataset = 3274 + 856 = 4130 images. Ground-truth plane = S1009194 (RGB). Blind test.
  - RESULT — CLIP wins decisively:
    - Dino classifier alone: plane at #430/4130 frames (detection #1474/6000) — buried, not in top-200.
    - CLIP rerank: plane at #2/6000 — spotted instantly. Aircraft-shape prior beat achromatic weakness.
  - Metric note: CLIP ranking = softmax-over-prompts (contrastive). Raw cosine is a different absolute score and reorders the top-5 — don't conflate (§7).
  - Infra: USB SSD corrupted mid-run; store rebuilt on internal drive. All 4130 recomputed.
  - Wrap-up: experiments/RESULTS.md; strip_dino_top5.png (junk) vs strip_clip_top5.png (plane #2).
  </details>

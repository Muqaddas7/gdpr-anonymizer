# Design Document: GDPR Anonymizer

## 1. Problem

Street-level and dashcam footage contains personal data (faces, license plates).
Under GDPR this data must be anonymized before it is stored, shared, or used
for analytics and model training. This project detects faces and license plates
in images and videos and anonymizes them automatically.

## 2. Users

- ML and data teams who need anonymized footage for training or analytics.
- Compliance staff who need evidence that anonymization was done reliably.

## 3. Cost of failure

- A missed face or plate (false negative) is a privacy leak.
- An extra blur on a non-sensitive region (false positive) is a minor quality loss.
- Therefore the system is optimized for recall first, precision second.

## 4. Scope

In scope:

- Detection of faces and license plates (single shared detector).
- Image anonymization (blur or pixelate).
- Video anonymization with tracking to prevent blur dropouts between frames.
- REST API, containerized, with CI/CD and drift monitoring.

Out of scope (for now):

- Anonymizing audio, text in the scene, or other identifiers.
- Face recognition or identity matching.
- Live streaming and real-time processing at scale.

## 5. Success metrics (initial targets, to be revised after the baseline)

- Face recall >= 0.95 and plate recall >= 0.95 on a held-out test set.
- Recall reported per slice: small objects, night, motion blur, occlusion.
- Frame-level leak rate: share of video frames with any unblurred face or plate.
- Throughput: FPS on CPU and on a T4 GPU.
- Downstream utility: vehicle or pedestrian counting on anonymized video.

## 6. Constraints

- Training on Colab T4 (16 GB), development on a CPU-only laptop.
- Public datasets only. Licenses must be verified and recorded in a data card.
- Video length limited on free hosting (short clips).

## 7. Privacy by design

- Uploaded files are processed in memory and deleted immediately.
- No raw media or detections are stored.
- Only aggregate statistics (counts, confidence) are logged for drift monitoring.

## 8. Open decisions

- Final choice of datasets (faces and plates) after license review.
- Detector family and size (decided after the zero-shot baseline).
- Tracker choice (ByteTrack is the initial candidate).

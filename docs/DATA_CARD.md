# Data Card: GDPR Anonymizer

Status: draft. Items marked TODO are filled in after the data is downloaded
and inspected.

## 1. Datasets

### 1.1 Open Images V7 (primary)

- Source: https://storage.googleapis.com/openimages/web/index.html
- Classes used: "Human face" (`/m/0dzct`) and "Vehicle registration plate"
  (`/m/01jfm_`). Both are in the official list of boxable classes.
- Annotation format: CSV with normalized boxes (XMin, XMax, YMin, YMax),
  plus flags such as IsOccluded, IsTruncated, IsGroupOf, IsDepiction.
- Image license: stored per image in the image information file (License
  column). Licenses can differ between images, so the final image set is
  filtered and a breakdown is recorded here. TODO
- Annotation license: TODO (confirm on the official description page).
- Size after filtering (images and boxes per class and split): TODO
- Download method: FiftyOne, limited to the two classes above.

### 1.2 WIDER FACE (planned, optional)

- Source: http://shuoyang1213.me/WIDERFACE/
- Content: 32,203 images, 393,703 faces, 61 event classes.
- Labels: faces only. License plates are NOT labeled in these images.
- Test labels are not released, so only the train and validation parts are
  usable for training and evaluation.
- Extra labels: pose and occlusion attributes are available.
- License: CC BY-NC-ND (non-commercial, no derivatives), as stated on the
  official page.
- Status: not used in the first model. It is added only if the baseline
  shows low recall on small faces (see section 3).

## 2. Splits

- Splits must not leak scenes. Images from the same source (same Flickr
  author or same event class) stay in one split only.
- Open Images: use the official train, validation and test partitions, and
  check for duplicates across partitions. TODO
- WIDER FACE (if used): split by event class, not by random image.
- Final test set is never used for tuning. Tuning uses validation only.

## 3. Experiment plan (data)

- Model A: trained on Open Images only. Release candidate.
- Model B: Open Images plus WIDER FACE, run only if Model A has low recall
  on small faces. Compared against A using recall per slice.
- Risk for B: plates are unlabeled in WIDER FACE images, which can teach
  the detector to ignore plates. Plate recall is checked explicitly.

## 4. Slices for evaluation

Recall is reported overall and per slice:

- Small objects (by box area)
- Occluded objects (IsOccluded flag)
- Truncated objects (IsTruncated flag)
- Group boxes (IsGroupOf) reported separately
- Faces versus plates

## 5. Known limitations

- Images come mostly from Flickr. Coverage of South Asian scenes and
  Pakistani plate formats is expected to be low.
- Not every face or plate in an image is guaranteed to be labeled. Missing
  labels make precision look worse than it is and can hide real misses.
- Group boxes mark crowds as one box, which does not match per-face
  anonymization.
- No night or video footage is included. Video behavior is tested separately.

## 6. Privacy and ethics

- The datasets contain real people. No images are redistributed in this
  repository. Data is downloaded from the official sources and kept out of
  Git (see .gitignore and DVC).
- The detector is used only for anonymization, not for identification.

## 7. Attribution

- Open Images: cite the Open Images authors as requested on the official
  page. TODO (add citation)
- WIDER FACE: Yang, Luo, Loy, Tang. "WIDER FACE: A Face Detection
  Benchmark", CVPR 2016.

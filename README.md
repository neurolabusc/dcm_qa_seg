## About

dcm_qa_seg is a DICOM to NIfTI validator script and dataset for DICOM Segmentation objects (SEG, SOP 1.2.840.10008.5.1.4.1.1.66.4). It checks [dcm2niix](https://github.com/rordenlab/dcm2niix)'s BINARY SEG to label NIfTI conversion (a 3D label volume, voxel = segment number, and a `Segments` table in the JSON sidecar).

Run `batch.sh` (dcm2niix in the path, or `exenam=...`): it converts `In` with `-b y -z n -f %p_%s` and compares the result with `Ref`. Unlike the other dcm_qa sets it does not pass `-i y`, which skips derived images such as SEG. dcm2niix exits nonzero by design here (the overlap SEGs are rejected); the comparison still runs, and a rejected SEG that produces output fails it.

## DataSets

* Synthetic
  * Description: 3x3-pixel BINARY SEGs written by `make_synthetic.py` (deterministic UIDs: rerunning it reproduces `In` byte for byte).
  * Cases: control; implicit VR; segment identification in the shared group; sparse frames (an empty slice) with the right and a wrong declared spacing; two labels; a source-image ReferencedSegmentNumber naming another segment (per-frame and shared); oblique (201 frames); referenced series under (0008,1115) and (0008,1200), explicit and implicit VR, and a related-only series; overlapping segments (rejected) and 256 overlapping segments (rejected, the per-voxel count must not wrap).
  * `make_synthetic.py [dcm2niix]` also converts each SEG on its own and asserts the label volume, sidecar codes and colours, and the rejection messages.
  * Moved from dcm2niix `tools/test_seg.py` (2026-11-18); the `idc/seg-refs` cases (ReferencedSeriesInstanceUID lists, both layouts and VRs; a RelatedSeriesSequence is not a reference) joined 2026-11-20.

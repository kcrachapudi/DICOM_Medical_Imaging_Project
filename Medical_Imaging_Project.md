# Medical Imaging Learning Project
### DICOM · PACS · RIS · 3D Slicer · PyDicom

> **5 Phases · 9 Steps · 7–9 Weeks · Solo · 4–7 hrs/week**  
> Tailored for: Biology/Genetics BSc + Computer Science MSc  
> Focus: DICOM/PACS/RIS/3D Slicer — fully dedicated, no parallel projects

---

## Contents

**Phase 1 of 4 — DICOM Fundamentals** · Weeks 1–2
- [ ] Step 1 of 9 — The DICOM standard: structure, tags & metadata
- [ ] Step 2 of 9 — Pixel data: reading, windowing & display

**Phase 2 of 4 — 3D Slicer & PACS/RIS** · Weeks 2–4
- [ ] Step 3 of 9 — 3D Slicer interface & loading DICOM data
- [ ] Step 4 of 9 — Volume rendering & coordinate systems
- [ ] Step 5 of 9 — Manual segmentation
- [ ] Step 6 of 9 — PACS & RIS architecture: Orthanc, HL7 & the full clinical workflow

**Phase 3 of 4 — Medical Image Processing in Python** · Weeks 4–6
- [ ] Step 7 of 9 — SimpleITK: 3D image processing & resampling
- [ ] Step 8 of 9 — Automated segmentation & morphological operations

**Phase 4 of 4 — Capstone Pipeline** · Weeks 7–9
- [ ] Step 9 of 9 — End-to-end pipeline: DICOM → segmentation → report

**Appendices**
- [Appendix A — Datasets](#appendix-a--datasets)
- [Appendix B — Tools & Installation](#appendix-b--tools--installation)
- [Appendix C — Progress Tracker](#appendix-c--progress-tracker)

---

## How to use this document

Each step is labeled **Phase X of 4 / Step Y of 9** so you always know where you are and how much remains. Every step ends with a concrete **deliverable** — something you built and pushed to GitHub, not just something you read. Work through steps sequentially; each one builds on the last.

> **Timeline note:** Your Biology/Genetics BSc eliminates anatomy overhead in Phases 1–2. Your CS Masters makes Phases 3–4 feel like a new library, not a new paradigm. Estimated completion: **7–9 weeks** at 4–7 hrs/week, fully solo.

---

## Phase 1 of 4 — DICOM Fundamentals
### Weeks 1–2

DICOM is simultaneously a file format, a network protocol, and a data model. Before touching a single pixel you need to understand how the standard is organized — otherwise everything downstream will feel arbitrary. Your CS background means the tag/attribute system will feel familiar immediately (think structured metadata with typed fields). Your biology background means you already know what the clinical data inside those tags represents.

---

### Step 1 of 9 — The DICOM standard: structure, tags & metadata

**What this step is about:**  
Understand DICOM as a hierarchical data model: Patient → Study → Series → Instance. Learn the tag system, Value Representations, and SOP Classes. Use pydicom to open a real DICOM file and interrogate every tag.

#### Biology concept — why this matters
DICOM was designed around clinical workflow. A **Patient** may have multiple **Studies** (each hospital visit or imaging episode). Each Study contains one or more **Series** (each imaging sequence run on the scanner — e.g. a T1 MRI, a T2 MRI). Each Series contains multiple **Instances** (individual image slices). This hierarchy mirrors how a radiologist thinks about a case — and it is the same hierarchy you navigate in every DICOM tool.

#### Topics covered
- DICOM information object model: Patient / Study / Series / Instance hierarchy
- Tag notation `(gggg,eeee)` and Value Representations: CS, LO, DS, UI, SQ
- SOP Classes and Transfer Syntaxes — what they mean clinically and technically
- Reading a DICOM file with pydicom: `ds = pydicom.dcmread()`
- Navigating and printing the full dataset; understanding implicit vs explicit VR
- Patient demographics tags, study description, modality, acquisition parameters
- DICOM UIDs — how they work as globally unique identifiers across systems

#### Python / tools
- **pydicom** — the primary Python DICOM library
- **Python 3** with a virtual environment
- **TCIA (The Cancer Imaging Archive)** — source for free public DICOM datasets

#### Recommended dataset
TCIA RIDER Lung CT — small, clean, real CT data. No account required.  
URL: `https://www.cancerimagingarchive.net/collection/rider-lung-ct/`

#### ✅ Deliverable
Python script that reads any DICOM file and prints all tags in a structured, readable table grouped by category (patient info, study info, image acquisition). Push to GitHub.

---

### Step 2 of 9 — Pixel data: reading, windowing & display

**What this step is about:**  
Extract the actual image data from a DICOM file and display it correctly. Raw pixel values are not display values — windowing/leveling maps a range of Hounsfield Units (for CT) to a visible grayscale.

#### Biology concept — why this matters
Different tissues have different X-ray attenuation coefficients — this is the physical basis of CT contrast. Hounsfield Units (HU) are a standardized scale: air = −1000 HU, water = 0 HU, soft tissue = 20–80 HU, bone = 400–1000 HU. Your biology background gives you an immediate mental model here — you already know what tissues look like histologically; now you're seeing their radiological correlate. Window/level adjustment is how a radiologist chooses which tissue range to visualize — lung windows, bone windows, soft tissue windows each reveal different anatomy.

#### Topics covered
- Accessing `pixel_array` from a DICOM dataset with pydicom
- Hounsfield Units: `RescaleSlope`, `RescaleIntercept` — the formula and why it matters
- Window/Level (WL) contrast adjustment — clinical presets: lung, bone, soft tissue, brain
- Displaying single slices with matplotlib; correct aspect ratio from `PixelSpacing`
- Multi-frame DICOM vs single-frame — iterating a CT series as a 3D numpy array
- Modality differences: CT (HU scale), MRI (arbitrary intensity), X-ray (optical density)
- Saving a correctly windowed image as PNG

#### Python / tools
- **pydicom**, **numpy**, **matplotlib**
- **TCIA RIDER Lung CT** dataset from Step 1

#### ✅ Deliverable
Python script that loads a full CT series, applies 3 different window presets (lung, bone, soft tissue) and outputs a side-by-side comparison image as PNG. Push to GitHub.

---

## Phase 2 of 4 — 3D Slicer & PACS/RIS
### Weeks 2–4

3D Slicer is the primary open-source platform for medical image visualization and segmentation. Orthanc is a lightweight open-source PACS. Together with RIS and HL7, these tools represent the full clinical infrastructure that surrounds every DICOM file in a real hospital. Understanding this stack — not just isolated files — is what differentiates a developer who has "used pydicom" from one who can build and integrate clinical imaging systems.

---

### Step 3 of 9 — 3D Slicer interface & loading DICOM data

**What this step is about:**  
Install 3D Slicer and learn its architecture: the MRML scene, modules, and the three core viewers. Load a DICOM series and navigate the volume. Your anatomy background means you will orient in multiplanar views much faster than a typical CS student.

#### Biology concept — why this matters
The three standard views in medical imaging — **axial** (top-down, like a horizontal slice through the body), **sagittal** (side view, dividing left and right), and **coronal** (front view, dividing front and back) — are anatomical planes you studied in your biology degree. In 3D Slicer these become interactive scrollable views through a 3D volume. What feels abstract in a textbook becomes immediately intuitive when you can scroll through a real CT.

#### Topics covered
- 3D Slicer architecture: MRML scene, nodes, modules, and the Python-scriptable core
- Loading DICOM data via the DICOM browser module
- Navigating axial, sagittal, and coronal views: scroll, zoom, pan, crosshair linking
- Window/level adjustment in Slicer vs what you built manually in Step 2
- Volume node vs segmentation node vs model node — the three core data types
- Loading sample datasets from the built-in data store (MRHead, CTACardio)
- The Python interactor console in Slicer — your first `slicer.mrmlScene` calls

#### Python / tools
- **3D Slicer LTS** (Long Term Support release from slicer.org)
- **TCIA datasets**, Slicer built-in sample data

#### ✅ Deliverable
Load one CT and one MRI dataset. Annotate 3 screenshots identifying the anatomy visible in each view (axial/sagittal/coronal). Save as a PDF or markdown image reference card. Push to GitHub.

---

### Step 4 of 9 — Volume rendering & coordinate systems

**What this step is about:**  
Build a 3D rendered view of a CT volume and understand the RAS coordinate system that underpins all spatial operations in Slicer. RAS is directly analogous to a geographic CRS — this is your first conceptual bridge between medical imaging and GIS.

#### Biology concept — why this matters
A CT volume is not just a stack of 2D images — it is a 3D object with physical dimensions. Each voxel occupies a real spatial location in millimetres relative to the patient. The **RAS system** (Right-Anterior-Superior) defines three axes relative to the patient's body orientation. Understanding this is critical for any measurement or comparison between patients — a 1cm tumor in one scan needs to map to the same physical location across serial scans. This is the same problem as georeferencing in GIS, just applied to anatomy.

#### Topics covered
- The Volume Rendering module: preset transfer functions (CT-AAA, CT-Bone, CT-Chest)
- Transfer function anatomy: scalar opacity, color mapping, and gradient opacity
- RAS vs LPS vs IJK coordinate systems — when each is used and how to convert between them
- How `ImagePositionPatient` and `ImageOrientationPatient` DICOM tags define physical space
- Voxel spacing vs physical spacing — why resampling matters
- Comparing a 3D bone render to the HU-thresholded axial slices from Step 2
- Taking screenshots and saving scenes in Slicer

#### Python / tools
- **3D Slicer** Volume Rendering module
- CTChest or CTACardio sample data (built into Slicer)

#### ✅ Deliverable
A 3D bone render screenshot + a written explanation (one paragraph in a markdown file) of how RAS coordinates map to the DICOM spatial tags `ImagePositionPatient` and `ImageOrientationPatient`. Push to GitHub.

---

### Step 5 of 9 — Manual segmentation

**What this step is about:**  
Segmentation is the core billable skill for medical imaging contractors. Use the Segment Editor to manually delineate structures. The goal is not perfection — it is understanding what the tools do so you can evaluate automated results later and explain your methodology to a client.

#### Biology concept — why this matters
Segmentation is the computational equivalent of a pathologist circling a region of interest on a slide. When you segment the liver from a CT, you are defining the 3D boundary of a real anatomical structure — every voxel inside the boundary is assigned to that structure. The quality of a segmentation determines the quality of any downstream measurement (volume, surface area, shape). In clinical AI, human-expert segmentations are the ground truth that models are trained and evaluated against. Your biology background means you already know the anatomical boundaries — you are just learning the tools to draw them digitally.

#### Topics covered
- Segment Editor module: segments, effects, and masking controls
- **Paint** and **Draw** effects: manual brush segmentation on 2D slices
- **Threshold** effect: auto-filling by HU range (bone, lung, soft tissue)
- **Scissors** effect: coarse region isolation before fine editing
- **Grow from seeds**: semi-automated region growing from user-placed seeds
- **Islands** effect: removing disconnected components from a segment
- Exporting segmentation as STL (for 3D visualization) and NRRD (for pipelines)
- Segment Statistics module: computing volume and surface area from a segmentation

#### Python / tools
- **3D Slicer** Segment Editor + Segment Statistics modules
- TCIA CT dataset

#### ✅ Deliverable
Manual segmentation of the liver and one lung from a CT volume. Export as NRRD. Record a short screen capture (GIF or video) showing the 3D result. Push NRRD file, screenshot, and screen capture to GitHub.

---

### Step 6 of 9 — PACS & RIS architecture: Orthanc, HL7 & the full clinical workflow

**What this step is about:**  
This is the most clinically important conceptual step in the project. A PACS (Picture Archiving and Communication System) stores and serves DICOM images. A RIS (Radiology Information System) manages the radiology workflow around those images — scheduling, orders, worklists, and reports. In every real hospital these two systems are tightly coupled, communicating via DICOM networking and HL7 messages. Understanding both — and the workflow that connects them — is what makes you hireable for clinical integration work, not just standalone imaging scripts.

#### Biology concept / clinical context — why this matters
Before a single image is acquired, a clinical workflow has already happened: a physician orders an imaging study in the EMR → the RIS receives the order as an HL7 ORM message → the RIS schedules the patient and creates a worklist entry → the CT/MRI scanner queries the RIS worklist via DICOM MWL → the scanner pulls the patient demographics automatically (no manual entry) → the scan is performed → images are sent to PACS via C-STORE → the radiologist reads from PACS and dictates a report → the report is sent back to the RIS → the RIS sends an HL7 ORU result message to the EMR → the ordering physician sees the report. Every step in this chain is a software integration point. As a developer, any one of these connections is a contract opportunity.

#### Topics covered

**PACS:**
- PACS architecture: modalities, archive, worklist server, viewing stations, and RIS/EMR integration
- DICOM network services: C-ECHO (ping), C-STORE (send), C-FIND (query), C-MOVE (retrieve)
- AE Titles, hostnames, and ports — how DICOM nodes identify each other on the network
- Installing and configuring Orthanc locally; the Orthanc REST API
- Sending a DICOM file to Orthanc via `storescu` and via `pynetdicom`
- Querying Orthanc with C-FIND using `pynetdicom`; parsing result datasets
- The Orthanc web viewer — confirming uploads visually

**RIS & HL7:**
- RIS responsibilities: patient scheduling, order management, worklist, report management, billing (CPT codes)
- HL7 v2 message structure: MSH segment, message types, segments, fields, delimiters
- Key HL7 message types:
  - **ADT** (Admit/Discharge/Transfer) — patient demographics and movements
  - **ORM** (Order Message) — imaging order from EMR to RIS
  - **ORU** (Observation Result) — radiology report from RIS back to EMR
- **DICOM Modality Worklist (MWL)**: how the RIS pushes scheduled exams to the scanner so technologists don't manually type patient data
- The complete clinical imaging workflow end-to-end: order → schedule → worklist → scan → PACS → read → report → result
- DICOM de-identification: removing PHI before research use — why it is legally required (HIPAA)

**RIS/PACS integration patterns:**
- How PACS and RIS communicate: DICOM for images, HL7 for orders/results
- The role of an integration engine (e.g. Mirth Connect) in routing messages
- Common integration failure points: AE Title mismatches, HL7 encoding errors, UID conflicts

#### Python / tools
- **Orthanc** (local PACS — download from orthanc-server.com, runs on port 8042)
- **pynetdicom** — DICOM networking in Python (C-STORE, C-FIND)
- **python-hl7** — parse and build HL7 v2 messages in Python
- **DCMTK** — command-line tools: `storescu`, `findscu`, `dcmsend`
- **pydicom** — DICOM de-identification

#### ✅ Deliverable
Two scripts pushed to GitHub:

1. **pacs_query.py** — connects to local Orthanc, sends a DICOM file via C-STORE, queries via C-FIND, prints all study UIDs and patient names found
2. **hl7_parser.py** — uses python-hl7 to parse a sample ORM (order) message and a sample ORU (result) message, printing each segment in a readable labeled format. Include a markdown file explaining what each HL7 segment means clinically in the context of the radiology workflow.

---

## Phase 3 of 4 — Medical Image Processing in Python
### Weeks 4–6

This is where your CS Masters pays off most directly. SimpleITK is a clean, Pythonic wrapper around ITK (Insight Toolkit) — the gold standard library for medical image processing. The operations here are algorithms you have seen before in other contexts. The only new dimension is that they operate on 3D volumes with physical spacing attached, and the results feed directly into clinical workflows.

---

### Step 7 of 9 — SimpleITK: 3D image processing & resampling

**What this step is about:**  
SimpleITK treats a medical image as a physical object with origin, spacing, and direction — not just a numpy array. This distinction is critical for any operation used clinically. A resampling error of 1mm in a CT can shift a tumor margin meaningfully.

#### Biology concept — why this matters
A CT volume is not uniformly sampled in 3D. Typical CT acquisition gives 0.5mm × 0.5mm in-plane resolution but 3–5mm slice thickness — meaning the volume is anisotropic (different resolution in different directions). Most AI segmentation models require isotropic input (same resolution in all three dimensions). Resampling to 1mm × 1mm × 1mm is therefore not an arbitrary preprocessing step — it corrects a physical acquisition constraint so the model sees anatomically correct proportions.

#### Topics covered
- Reading a DICOM series as a 3D SimpleITK Image (vs pydicom slice-by-slice)
- Image metadata: `GetOrigin()`, `GetSpacing()`, `GetDirection()`, `GetSize()`
- Resampling to isotropic voxels — why 1mm × 1mm × 1mm matters for AI models
- Gaussian smoothing, median filtering, and anisotropic diffusion for noise reduction
- Intensity normalization: z-score, min-max, histogram equalization
- Cropping and padding volumes: Extract/Paste image filters
- Converting between SimpleITK Image and numpy array while preserving spatial metadata
- Saving processed volumes as NIfTI (`.nii.gz`) — the format TotalSegmentator expects

#### Python / tools
- **SimpleITK**, **numpy**, **nibabel**
- TCIA CT series from earlier steps

#### ✅ Deliverable
Python script: reads a DICOM series, resamples to 1mm isotropic, applies Gaussian smoothing, saves as NIfTI. Function-based, documented, tested on two different CT datasets. Push to GitHub.

---

### Step 8 of 9 — Automated segmentation & morphological operations

**What this step is about:**  
Threshold-based segmentation of bone from CT using HU values is the simplest form of automated segmentation — but it teaches you everything about how masks are created, cleaned, and evaluated. The morphological operations are the same operations used in GIS raster analysis, just applied to 3D volumes.

#### Biology concept — why this matters
Bone has a consistently high HU value (400–1800 HU) because calcium strongly attenuates X-rays. This makes bone the easiest tissue to segment automatically — a simple threshold captures most of the skeleton. But the result contains noise: small disconnected fragments, holes inside dense bone, partial volume artifacts at bone edges. Morphological operations (erosion, dilation, closing) clean these up. Understanding why these artifacts exist — partial volume effect, beam hardening — tells you when simple thresholding will fail and a more sophisticated approach is needed (soft tissue, for example, has overlapping HU ranges with other structures).

#### Topics covered
- Binary threshold segmentation: bone (HU > 400), lung (HU −1000 to −300), soft tissue
- Morphological operations: `BinaryErode`, `BinaryDilate`, `BinaryMorphologicalClosing`
- Connected component labeling: isolating the largest component, removing noise islands
- Hole filling in 3D binary masks
- Computing segment volumes from voxel count × voxel volume
- Evaluating segmentation quality: visual inspection + Dice coefficient concept
- Comparing manual (Step 5) vs automated threshold segmentation on the same dataset

#### Python / tools
- **SimpleITK**, **numpy**, **scipy.ndimage**, **matplotlib**

#### ✅ Deliverable
Python script: loads a NIfTI CT (from Step 7), segments bone by HU threshold, applies morphological cleanup, computes bone volume in cm³, saves mask as NIfTI. Include a matplotlib figure showing axial/sagittal/coronal overlay. Push to GitHub.

---

## Phase 4 of 4 — Capstone Pipeline
### Weeks 7–9

The capstone integrates every skill from Phases 1–3 into a single automated pipeline that mirrors real clinical AI tooling. You pull a study from your local Orthanc PACS, process it with TotalSegmentator (a state-of-the-art pre-trained model that segments 104 anatomical structures from CT), compute organ volumes, and output a structured report. This is the deliverable you show to clients and employers.

---

### Step 9 of 9 — End-to-end pipeline: DICOM → segmentation → report

**What this step is about:**  
Build a fully automated, command-line-runnable pipeline. Input: a Study Instance UID on your local Orthanc server. Output: a JSON report of organ volumes + a rendered multi-panel image showing segmentation overlays. Every component uses skills from the previous 8 steps.

#### Biology concept — why this matters
TotalSegmentator segments 104 structures including major organs (liver, spleen, kidneys, lungs, heart), bones, muscles, and vessels. Each structure has known normal volume ranges — for example, a normal liver is 1000–1500 mL in an adult, a normal spleen is 150–400 mL. Computing organ volumes automatically and flagging values outside normal ranges is the basis of clinical decision support tools. This is not a toy exercise — it is functionally equivalent to what radiology AI companies sell as commercial products.

#### Topics covered
- Pipeline architecture: input validation → DICOM retrieval → preprocessing → segmentation → measurement → reporting
- Retrieving a study from Orthanc via REST API or C-MOVE into a local temp directory
- Converting DICOM series to NIfTI using `dcm2niix` (faster than manual SimpleITK for pipelines)
- Installing and running TotalSegmentator: CLI and Python API
- TotalSegmentator output structure: one NIfTI mask per anatomical structure
- Loading all segmentation masks and computing volume per structure (voxel count × voxel volume)
- Generating a structured JSON report: patient ID, study date, organ volumes in mL, processing time
- Rendering a multi-panel matplotlib figure: axial/sagittal/coronal with top-5 organ overlays
- Loading TotalSegmentator output back into 3D Slicer programmatically via Slicer Python API
- Error handling, logging, and making the pipeline re-runnable (idempotent)

#### Python / tools
- **TotalSegmentator**, **dcm2niix**, **pynetdicom**, **SimpleITK**, **nibabel**
- **numpy**, **matplotlib**, **json**, **3D Slicer Python API**

#### ✅ Deliverable
GitHub repository containing:
1. `pipeline.py` — fully documented, runnable end-to-end script
2. `sample_report.json` — output from a real TCIA CT study
3. `sample_output.png` — rendered multi-panel figure with organ overlays
4. `README.md` — setup instructions, usage, and a screenshot of 3D Slicer showing TotalSegmentator results loaded back in

**This is your primary Medical Imaging portfolio piece.**

---

## Appendix A — Datasets

All datasets below are publicly available at no cost.

| Dataset | Modality / Use | Steps |
|---|---|---|
| TCIA RIDER Lung CT | CT — lung (two time points per patient) | Steps 1–8. Good for registration practice in Step 7 |
| TCIA Pancreas-CT | CT — abdomen | Steps 5, 9. Clean abdominal CT. TotalSegmentator performs well |
| TCIA Head-Neck PET-CT | CT + PET — multi-modality | Steps 2, 4. Good for window/level comparison across modalities |
| 3D Slicer MRHead sample | MRI — brain | Steps 3–5. Built into Slicer. No download needed |
| 3D Slicer CTACardio sample | CT — cardiac | Steps 3–4. Built into Slicer. Complex 3D anatomy for volume rendering |
| Orthanc demo server | DICOM network | Step 6. Ships with test data. Use for C-FIND/C-STORE practice |

**TCIA access:** `https://www.cancerimagingarchive.net`  
**3D Slicer sample data:** File → Download Sample Data inside the application

---

## Appendix B — Tools & Installation

Use a single conda environment for everything. Activate before each session: `conda activate medimaging`

### Install before Phase 1
```bash
conda create -n medimaging python=3.10
conda activate medimaging
pip install pydicom numpy matplotlib
```

### Install before Phase 2
```bash
# 3D Slicer — download LTS from slicer.org (do NOT use Preview release)
# Orthanc — download from orthanc-server.com, runs on localhost:8042
# Default credentials: orthanc / orthanc

pip install pynetdicom python-hl7

# DCMTK (command-line DICOM tools)
conda install -c conda-forge dcmtk
```

### Install before Phase 3
```bash
pip install SimpleITK nibabel scipy
```

### Install before Phase 4 (capstone)
```bash
# dcm2niix — download from github.com/rordenlab/dcm2niix, add to PATH
# TotalSegmentator — requires PyTorch first

pip install torch  # add --index-url for CUDA version if you have a GPU
pip install TotalSegmentator
```

> **GPU note:** TotalSegmentator on CPU takes ~10–15 min per CT. On a modern GPU (RTX 3060+) it takes under 2 min. CPU is fine for learning. For client work, consider cloud GPU via RunPod or Lambda Labs.

---

## Appendix C — Progress Tracker

Use this to track where you are. Check off each step when the deliverable is pushed to GitHub.

| Phase | Step | Title | Deliverable | Done |
|---|---|---|---|---|
| Phase 1 of 4 | Step 1 of 9 | DICOM structure, tags & metadata | GitHub script | ☐ |
| Phase 1 of 4 | Step 2 of 9 | Pixel data, windowing & display | GitHub script | ☐ |
| Phase 2 of 4 | Step 3 of 9 | 3D Slicer interface & loading data | Screenshots + reference card | ☐ |
| Phase 2 of 4 | Step 4 of 9 | Volume rendering & coordinate systems | Screenshot + markdown notes | ☐ |
| Phase 2 of 4 | Step 5 of 9 | Manual segmentation | NRRD + screen capture | ☐ |
| Phase 2 of 4 | Step 6 of 9 | PACS & RIS: Orthanc, HL7 & clinical workflow | Two scripts + markdown | ☐ |
| Phase 3 of 4 | Step 7 of 9 | SimpleITK 3D processing & resampling | GitHub script | ☐ |
| Phase 3 of 4 | Step 8 of 9 | Automated segmentation & morphology | GitHub script + figure | ☐ |
| Phase 4 of 4 | Step 9 of 9 | End-to-end capstone pipeline | Full GitHub repository | ☐ |

---

> **First contract target:** After Step 9 is complete and on GitHub — apply to Handshake AI (3D Slicer AI evaluator roles) and search Upwork for "DICOM segmentation". Your capstone repo is your portfolio — link it in every application.

> **Session continuity:** At the start of any new chat session, paste this contents list or upload this file. That is all that is needed to resume exactly where you left off.

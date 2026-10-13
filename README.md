# Bolt Inspection

A webcam-based bolt inspection prototype. The active application entry point covered by this README is `bolt_inspection/bolt_inspecter_t2.py`.

The program uses a YOLO model to locate a bolt, shows live 2D length estimates, and builds a 360-degree thread inspection image while the bolt is rotated in front of the camera. After a scan, it displays a report with measurements and possible surface or thread anomalies.

## Requirements

- Windows or another desktop OS with a working webcam and OpenCV GUI support
- Python (the environment in this project uses Python 3.10)
- The Python packages listed in `requirement.txt`
- The YOLO model file `bolt.pt` at the path configured in the script

## Setup

From the project root, create and activate a virtual environment, then install dependencies:

```powershell
py -3.10 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r requirement.txt
```

The script currently loads the model from this absolute path:

```text
C:/Users/scien/Desktop/SSG PROJECTS/RANE/bolt.pt
```

If the project is stored elsewhere, update the `YOLO(...)` path in `bolt_inspection/bolt_inspecter_t2.py` to point to your `bolt.pt` file before running. The camera index defaults to `0`; change `camera_index` in the final two lines of the script if your webcam uses a different index.

## Run

From the project root:

```powershell
python bolt_inspection/bolt_inspecter_t2.py
```

The **Live Target Feed** window shows the camera image and a green alignment guide. Center the bolt on the guide. Use the controls shown below:

| Key | Action |
| --- | --- |
| `Space` | Start or stop collecting scan slices. Rotate the bolt steadily while scanning. Stopping the scan stitches the slices together and opens the inspection report. |
| `q` | Quit the live feed and close the OpenCV windows. |

The 2D length and head-length readings are shown in the live feed. The scan report contains an overall status, the thread count and pitch when no defects are flagged, colored overlays for detected anomalies, and an edge map with thread peaks marked.

## What the scan checks

- **Thread count and pitch:** detected from horizontal peaks in the stitched image's Canny edge profile.
- **Cross-thread candidates:** angled line segments detected with a probabilistic Hough transform.
- **Rust and dark contamination candidates:** color masks in HSV space.
- **Edge-density anomalies:** unusually sparse or dense cells in a grid over the stitched image.

These are image-processing heuristics. A `BOLT OK` result is not a certified quality or safety inspection.

## Calibration and tuning

The script converts pixels to millimeters using `mm_per_pixel = 0.271639`. This value is fixed in `MasterBoltInspector.__init__` and must match the camera, lens, distance, and image setup for measurements to be meaningful. Recalibrate it whenever that setup changes.

Other settings, including the YOLO confidence threshold, scan crop, slice width, edge-density grid, and thresholds for anomaly detection, are defined in `bolt_inspecter_t2.py`. Detection quality depends on lighting, focus, camera stability, bolt alignment, and a steady rotation during the scan.

## Project scope

This README documents `bolt_inspection/bolt_inspecter_t2.py` as the working application file. Other scripts and sample images in the repository are not part of the run instructions above.

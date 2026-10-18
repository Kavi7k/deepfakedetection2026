# Veritas — Deepfake Risk Analysis
 
Upload a video and get a clear, shareable deepfake risk report. Veritas runs entirely in the browser: no server, no account, and your video is never uploaded.
 
> **Status:** working front-end prototype. The analysis is a heuristic signal check, not a trained deepfake model. See [Limitations](#limitations) and the [Roadmap](#roadmap) for the model-based version.
 
## Features
 
- **Drag-and-drop upload** for MP4, MOV and WebM files
- **Overall risk score (0–100)** with a Low, Moderate or High verdict
- **Video preview with a risk timeline:** every analysed moment is coloured by risk, and clicking a moment jumps the video to it
- **Per-moment breakdown** with a thumbnail, risk score and the main reason (for example "Sudden motion change")
- **Five signal scores:** motion consistency, sharpness stability, light and colour, noise pattern, face-region match
- **Risk statistics:** average, median, peak and lowest risk, spread, peak time, and the share of Low, Moderate and High moments
- **Video statistics:** duration, resolution, aspect ratio, frame size, file size, average bitrate, format, average brightness, sharpness, noise level and frame change
- **PDF report download** with scores, statistics, per-moment table and thumbnails of the highest-risk moments
- Apple-style interface with automatic light and dark mode
## How it works
 
1. The video is loaded into a hidden `<video>` element.
2. Between 10 and 24 moments are sampled across the clip. For each one, two frames a fraction of a second apart are drawn to a small `<canvas>` and converted to grayscale.
3. Image statistics are computed from the pixels:
| Signal | What is measured | Why it matters |
|---|---|---|
| Motion consistency | Variation in frame-to-frame difference | Face swaps often jitter between frames |
| Sharpness stability | Variation of Laplacian sharpness across moments | Generated faces drift between too soft and too crisp |
| Light and colour | Brightness change between consecutive frames | Pasted faces can flicker against scene lighting |
| Noise pattern | Variation of high-frequency noise | Cameras leave steady grain; synthesis smooths or scrambles it |
| Face-region match | Centre-of-frame sharpness relative to the whole frame | A face sharper or softer than its surroundings stands out |
 
4. Each signal becomes a 0–100 score, and a weighted average (25 / 20 / 20 / 15 / 20) gives the overall risk score.
5. Per-moment risk blends the overall score with how far that moment deviates from the rest of the video.
6. The PDF is generated in the browser with [jsPDF](https://github.com/parallax/jsPDF).
## Getting started
 
No build step or dependencies to install.
 
```bash
git clone <your-repo-url>
cd <your-repo-folder>
```
 
Open `index.html` in a modern browser (Chrome, Edge, Safari or Firefox), or serve it locally:
 
```bash
python -m http.server 8000
# then visit http://localhost:8000
```
 
> Rename `veritas-v2.html` to `index.html` if you want it to be the default page.
 
The page loads two things from the internet: the Inter font (Google Fonts) and jsPDF (cdnjs). Without a connection the page still works, but the font falls back to the system font and the PDF button will not work.
 
## Project structure
 
```
.
├── index.html   # the whole app: markup, styles and analysis logic
└── README.md
```
 
## Limitations
 
- **This is not a trained model.** The score reflects visual consistency, not a learned judgement about faces. A well-made deepfake can score low, and genuine video can score higher than expected.
- Heavy compression, filters, screen recordings, fast camera motion and low resolution can all raise the score on real footage.
- Only 10–24 moments are sampled, so very short clips give less reliable results.
- The scoring thresholds were set by hand and have not been tuned or validated on a labelled dataset.
- Treat the report as a screening aid and confirm important findings with the original source. It is not forensic evidence.
## Roadmap
 
- [ ] Python backend with **FastAPI** for heavier analysis
- [ ] OpenCV face detection and tracking, so signals are measured on the face region only
- [ ] **PyTorch** CNN + Vision Transformer ensemble for a learned fake/real confidence score
- [ ] Per-frame confidence and live alerts over WebSocket for real-time streams
- [ ] Redis queueing and Docker packaging for deployment
- [ ] Calibrate thresholds on a labelled dataset such as FaceForensics++ or Celeb-DF
- [ ] User accounts and saved report history
## Tech stack
 
HTML, CSS and vanilla JavaScript, the Canvas API, the HTML5 Video API, and jsPDF.
 
## Author
 
Built by **Kaviyarasu V** — [LinkedIn](http://www.linkedin.com/in/kaviyarasu-v) · [Portfolio](https://portfolio-kaviyarasu.netlify.app/)
 

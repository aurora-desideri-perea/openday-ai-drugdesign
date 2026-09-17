## IRB Open Day 2026 | AI & drug design

An interactive open-day demo from [IRB Barcelona](https://www.irbbarcelona.org/) that lets visitors draw a drug-like sketch on paper (or on screen), hold it up to a webcam and get an instant AI prediction: **selective inhibitor**, **toxic**, or **inactive**. The science behind it: calpains are intracellular proteases that become dysregulated in certain cancers. Scientists use AI to prioritize which molecular shapes to synthesize before setting foot in the lab. This demo turns that workflow into a hands-on activity.

### How it works

1. A visitor draws a shape with a dark marker on white paper
2. The webcam captures the drawing and sends a 28×28 grayscale image to a local Flask server
3. A lightweight CNN (~120k parameters) classifies the sketch into one of three classes:
   - 🟢 Selective inhibitor
   - 🔴 Toxic inhibitor
   - ⚫ Inactive
4. The result is displayed instantly in the browser

### Repo structure

```
.
├── html/
│   ├── app_webcam_v2.html  # Main demo UI (webcam input, live prediction)
│   ├── app_webcam.html     # Standalone webcam version (TF.js, no server)
│   └── app_drawpad.html    # Standalone drawing pad (TF.js, no server)
├── tf.min.js               # TensorFlow.js (bundled, for client-side fallback)
├── imgs/                   # Static assets (header image)
└── ml/
    ├── app.py              # Flask server — loads models, serves /predict
    ├── model.py            # InhibitorCNN architecture (PyTorch)
    ├── train.py            # Training script
    ├── dataset.py          # Dataset preparation
    ├── download_data.py    # Data download helper
    ├── requirements.txt    # Python dependencies
    └── *.ipynb             # Exploratory notebooks (training, data, detection)
```

### Standalone mode

`html/app_webcam.html` and `html/app_drawpad.html` run entirely in the browser using TensorFlow.js, no Python or server needed. Just open either file directly:

```bash
open html/app_webcam.html   # or html/app_drawpad.html
```

These versions train a small CNN in-browser from a few labelled examples you provide on the spot. They're useful for a quick demo without any setup, but the model is less accurate than the PyTorch backend.


### ML mode

```bash
# 1. Install dependencies
pip install -r ml/requirements.txt

# 2. Place trained checkpoints in ml/checkpoints/
#    Expected files: best_model.pt, best_model_detection.pt

# 3. Start the server
python ml/app.py

# 4. Open http://localhost:5000 in a browser
```

#### Training your own model

The `ml/` folder contains notebooks for each step of the pipeline:

| Notebook | Purpose |
|---|---|
| `download_data.ipynb` | Download raw molecular sketch data |
| `dataset.ipynb` | Preprocess and balance the dataset |
| `train.ipynb` | Train the standard CNN |
| `train-v2.ipynb` | Train with augmentation |
| `train-detection.ipynb` | Train the detection-optimized variant |

### License

See [LICENSE](LICENSE).

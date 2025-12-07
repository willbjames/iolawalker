````markdown
## Usage
Download into a folder and prompt your prompt-based codewriting agent to ingest and understand the repository. The repo is optimized for an LLM agent to understand and to begin improving the project with your own data!

To begin training on your own footfalls, collect your own footfall data from the android app, split it up into train and test, and put it into pythonModel/train200hz.csv and pythonModel/test200hz.csv.

Refer to the paper on hugging face for more info
https://huggingface.co/papers/2506.01211

## To get your footfall data from your IMU

1. **Open Android Studio**

   * Launch Android Studio.
   * Select **Open an existing project** and navigate to the `android-app/` (or `app/`) directory in this repo.
   * Replace private const val TARGET_MAC_ADDRESS = "F3:CD:37:B7:4E:0B" in MainActivity.kt with the MAC Address of your Mbient labs IMU.
   * Collect Footfall data from storage/emulated/0/Android/data/com.example.footfallng/files/sensor_data/

```

After splitting your data into train and test, train your LSTM using the training code.

```bash
python lstmFinal/trainAndTest.py
```

The script will:

* Fit the `SeqFootfallDataset` on `TRAIN_CSV`, generate sliding windows.
* Train for `EPOCHS` epochs, tuning the best validation threshold.
* Save:

  * `best_refined_v2.pth` (checkpoint),
  * `refined_v2_ts.pt` (TorchScript model),
  * `metadata_refined_v2.json` (window size, stride, threshold, scaler stats).
* Load `TEST_CSV`, run a tolerance sweep (0–50 samples), and print JSON results.

---

## Configuration

All key constants live at the top of `lstmFinal/trainAndTest.py`:

```python
TRAIN_CSV      = "pythonModel/train200hz.csv"
TEST_CSV       = "pythonModel/test200hz.csv"
SCALER_OUT     = "scaler_refined_v2.pkl"
MODEL_OUT      = "best_refined_v2.pth"
TS_MODEL_OUT   = "refined_v2_ts.pt"
METADATA_OUT   = "metadata_refined_v2.json"

WINDOW_SIZE    = 600    # samples per window (3 s @200 Hz)
STRIDE         = 150    # window hop (75% overlap)
BATCH_SIZE     = 64
LR             = 1e-3
EPOCHS         = 50
POS_WINDOW_OVERSAMPLE = 10.0
SEED           = 42
CLIP_NORM      = 5.0
```

---

## Results

* **Checkpoint:** `best_refined_v2.pth`
* **TorchScript model:** `refined_v2_ts.pt`
* **Metadata:** `metadata_refined_v2.json`
* **Test metrics:** printed to console in JSON format.

---



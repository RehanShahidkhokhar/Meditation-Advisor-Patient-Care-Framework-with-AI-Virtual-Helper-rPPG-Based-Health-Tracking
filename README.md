# 🫀 rPPG Health Monitor — Complete Guide (Urdu/English)

## Ye System Kya Karta Hai?

Camera se aapke face ka video le ke **bina kisi device ke** yeh measure karta hai:
- ❤️ **Heart Rate (BPM)** — Dil ki dhadkan
- 🩸 **SpO2** — Khoon mein oxygen level  
- 😌 **Stress Level** — Heart Rate Variability se
- 📊 **HRV (SDNN, RMSSD)** — Autonomic nervous system health

---

## 📁 Files Ka Structure

```
rppg-project/
├── rppg_utils.py      # Signal processing functions
├── rppg_trainer.py    # Dataset se model training
├── rppg_monitor.py    # Live webcam monitoring
├── models/            # (Training ke baad banta hai)
│   ├── hr_model.pkl
│   ├── hr_scaler.pkl
│   └── config.json
└── rppg-dataset/      # Kaggle se download karo
```

---

## ⚡ STEP 1: Packages Install Karo

```bash
pip install opencv-python numpy scipy scikit-learn mediapipe pandas joblib matplotlib tqdm
```

---

## ⚡ STEP 2: Kaggle Dataset Download Karo

### Method A: Browser se
1. https://www.kaggle.com/datasets/ashfakyeafi/rppg-dataset open karo
2. **Download** button dabao
3. ZIP file extract karo
4. Folder ka naam `rppg-dataset` rakho
5. Project folder mein move karo

### Method B: Kaggle API se
```bash
pip install kaggle
# kaggle.json apne ~/.kaggle/ folder mein rakho
kaggle datasets download -d ashfakyeafi/rppg-dataset
unzip rppg-dataset.zip -d rppg-dataset
```

---

## ⚡ STEP 3: Models Train Karo

### Option A — Real Kaggle Dataset (BEST)
```bash
python rppg_trainer.py --dataset_path ./rppg-dataset
```

### Option B — Sirf Kuch Videos Test Karo
```bash
python rppg_trainer.py --dataset_path ./rppg-dataset --max_videos 20
```

### Option C — Demo Mode (Dataset ke bagheir)
```bash
python rppg_trainer.py --demo
```

**Training Output Example:**
```
💓 Heart Rate Model Training...
  Best Model: RandomForest
  MAE:  4.82 BPM      ← iska matlab average error 4.82 BPM hai
  RMSE: 6.31 BPM
  R²:   0.847         ← 1.0 = perfect, yahan 84.7% accurate
  ±5 BPM accuracy:  71.3%
  ±10 BPM accuracy: 91.8%
✅ Sab models save ho gaye: ./models/
```

---

## ⚡ STEP 4: Live Monitor Chalao

```bash
python rppg_monitor.py
```

Ya agar models alag folder mein hain:
```bash
python rppg_monitor.py --model_dir ./models
```

### Monitor Mein Kya Dikhega:

```
┌──────────────────────────────────────────────┐
│  🫀  rPPG Health Monitor          ⏺ MONITORING│
├───────────┬───────────┬────────────────────── │
│ HEART RATE│   SpO2    │   STRESS              │
│    72     │   98.2    │  Normal               │
│   BPM     │    %      │                       │
├───────────┴───────────┴────────────────────── │
│ HRV: SDNN: 42.1ms  RMSSD: 38.5ms  Score: 76% │
│ Signal Quality: ████████░░  81%               │
└──────────────────────────────────────────────┘
```

---

## 📊 Values Ko Kaise Samjhein?

### Heart Rate (BPM)
| Value | Matlab |
|-------|--------|
| < 60 | Bradycardia (slow) — Doctor se milein |
| 60-100 | Normal ✅ |
| > 100 | Tachycardia (fast) — Araam karen |

### SpO2
| Value | Matlab |
|-------|--------|
| 95-100% | Normal ✅ |
| 90-94% | Thoda kam — Dhyan dein |
| < 90% | Low — Doctor se milein |

### Stress Level
| Level | Matlab |
|-------|--------|
| Relaxed 😌 | RMSSD > 50ms — Araam mein hain |
| Normal 🙂 | RMSSD 30-50ms — Theek hai |
| Mild Stress 😐 | RMSSD 15-30ms — Thaka hua |
| High Stress 😟 | RMSSD < 15ms — Stress mein hain |

### Signal Quality
| % | Matlab |
|---|--------|
| 70-100% | Good ✅ |
| 40-70% | Medium — Thoda aur ruke rahein |
| < 40% | Poor — Chehra frame mein rakhein |

---

## 🔬 Technical Methods

### 1. CHROM Algorithm
- Color-based rPPG ka standard method
- Red, Green, Blue channels ka ratio use karta hai
- Motion se thoda sensitive

### 2. POS Algorithm  
- Plane-Orthogonal-to-Skin method
- Motion-robust hai
- Sliding window approach

### 3. ML Blend (Jab models trained hon)
- 60% ML prediction + 40% CHROM
- Training data pe based correction

### 4. SpO2 Estimation
- Red/Green AC/DC ratio se
- Formula: SpO2 = 110 - 25 × (R_AC/R_DC ÷ G_AC/G_DC)
- Camera-based approximation hai, medical device nahi

---

## ❓ Common Problems

### Problem: "Camera nahi mili"
```bash
# Doosra camera try karo
python rppg_monitor.py --camera 1

# Ya check karo
python -c "import cv2; cap=cv2.VideoCapture(0); print('OK' if cap.isOpened() else 'FAIL'); cap.release()"
```

### Problem: BPM -- dikhta hai
- 6 seconds tak wait karo (buffer fill hone do)
- Face seedha camera ki taraf rakhein
- Achhi roshni mein baithein
- Hilna band karen

### Problem: Signal Quality < 40%
- Lighting improve karen (natural light best hai)
- Camera se 30-60cm dur rahein  
- Face ko frame ke center mein rakhein

### Problem: MediaPipe error
```bash
pip install mediapipe --upgrade
# Ya uninstall karo, OpenCV cascade use ho ga
pip uninstall mediapipe
```

---

## 💡 Best Results Ke Liye Tips

1. **Lighting**: Natural light ya bright lamp chehra par
2. **Distance**: Camera se 40-60cm
3. **Still rahein**: Pehle 10 seconds bilkul na hilein
4. **Background**: Plain background better hai
5. **Time**: 30+ seconds ke baad readings zyada accurate

---

## ⚠️ Disclaimer

Ye **research/educational** project hai.
Medical diagnosis ke liye certified devices use karen.
SpO2 aur HR values approximate hain.

---

## 📜 Commands Summary

```bash
# Train with real dataset
python rppg_trainer.py --dataset_path ./rppg-dataset

# Train demo (no dataset needed)
python rppg_trainer.py --demo

# Run monitor
python rppg_monitor.py

# Run with specific camera
python rppg_monitor.py --camera 1

# Help
python rppg_trainer.py --help
python rppg_monitor.py --help
```

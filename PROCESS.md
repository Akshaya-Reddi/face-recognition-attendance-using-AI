## 📘 Process I Followed to Build the Face Recognition Attendance System

> **Short Summary:**
> I built a contactless attendance system that detects student faces, recognizes them using **MTCNN + FaceNet + SVM**, and marks attendance automatically in **Google Sheets**. The app is deployed using **Flask** and **ngrok**, with QR code access for mobile users.

---

### 🧩 1. Project Setup (Environment & Tools)

* Used **Google Colab** as the main environment (since it’s cloud-based and works on Chromebook).
* Installed all required packages:

  ```bash
  !pip install keras-facenet mtcnn opencv-python-headless scikit-learn gspread google-auth pyngrok qrcode
  ```
* Downloaded `credentials.json` (Google Cloud service account key) for accessing Google Sheets.

---

### 📸 2. Data Collection & Storage

* Collected **20–40 images per student** for **7 students** in total.
* Each student's folder was named with their **admission number**.
* Folder structure:

  ```
  faces/
    └── Dataset/
         ├── 23CAM1001/
         ├── 23CAM1002/
         ├── 23CAM1003/
         └── ...
  ```
* Uploaded this dataset to Google Drive → downloaded as ZIP → uploaded to Colab → extracted to `/content/faces/Dataset/`.

---

### 🧠 3. Dataset Creation & Preprocessing

* Used **MTCNN** to detect and crop faces.
* Resized each face to **160×160 pixels** (FaceNet input size).
* Saved all cropped faces into NumPy arrays:
  `faces_X.npy` and `faces_y.npy`.

✅ **Fixes for common issues:**

* Used `os.listdir()` to remove hidden directories and trailing spaces.
* Ensured dataset path correctness with:

  ```python
  import os
  print(os.listdir('/content/faces/Dataset'))
  ```

---

### ⚙️ 4. Embedding Generation (FaceNet)

* Used **keras-facenet** model to generate 128/512-dimensional embeddings.

  ```python
  from keras_facenet import FaceNet
  embedder = FaceNet()
  embedding = embedder.embeddings(face_array)
  ```
* Stored all embeddings (`X_embeddings.npy`) and labels (`y_labels.npy`).

✅ **Issues fixed:**

* `UnimplementedError` — don’t load model from HTTPS URL, use `keras-facenet`.
* 404 when downloading model — fixed by installing `keras-facenet` package.

---

### 🧪 5. Training the Classifier (SVM)

* Split dataset:

  * Training: 198
  * Validation: 42
  * Test: 43
* Encoded labels with `LabelEncoder()` and trained an **SVM**:

  ```python
  from sklearn.svm import SVC
  model = SVC(kernel='linear', probability=True)
  model.fit(X_train_emb, y_train_enc)
  ```
* Achieved validation accuracy of **97.6%**, test accuracy **100%** (for small set).

✅ **Saved files:**

```python
import pickle
pickle.dump(model, open('face_recognition_svm.pkl','wb'))
pickle.dump(label_encoder, open('label_encoder.pkl','wb'))
```

---

### 🎥 6. Testing with Static Images

* Created a test folder `/content/test_images/` with new unseen images.
* Wrote code to predict faces using the trained model.
* Added confidence threshold (0.7) to decide between:

  * ✅ **Matched** → Mark attendance
  * ❌ **Not matched** → Show “Student not found”

---

### 🧾 7. Google Sheets Integration (Attendance)

* Authorized using `credentials.json`:

  ```python
  from google.oauth2.service_account import Credentials
  import gspread
  ```
* Opened and updated the attendance sheet automatically:

  ```python
  sheet.append_row([student_id, date, time, "Present"])
  ```

✅ **Solved:** API 403 errors by sharing the sheet with service account email.

---

### 🌐 8. Web Interface (Flask + QR + ngrok)

* Built Flask app:

  * `/` — Camera page
  * `/upload` — Accepts image (base64), detects and recognizes face.
* Generated QR with:

  ```python
  import qrcode
  img = qrcode.make(public_url)
  img.save("attendance_qr.png")
  ```
* Used **pyngrok** for temporary public URL:

  ```python
  from pyngrok import ngrok
  public_url = ngrok.connect(7000).public_url
  ```

✅ **Added checks for:**

* "Multiple faces detected — show one face only"
* "Face not matched" message

---

### 📱 9. Real-time Workflow

1. Lecturer runs Flask app → QR appears on screen.
2. Student scans QR on phone → page opens camera.
3. Student shows face → app recognizes & marks attendance in Google Sheets.
4. Displays status message in real-time.

---

### 💾 10. Artifacts & Files Saved

* `face_recognition_svm.pkl`
* `label_encoder.pkl`
* `faces_X.npy`, `faces_y.npy`
* `attendance_qr.png`
* `.gitignore` → to exclude credentials and large files.

---

### 🧰 11. Common Issues & Fixes

| Issue                     | Cause                  | Fix                              |
| ------------------------- | ---------------------- | -------------------------------- |
| `IsADirectoryError`       | Folder name mismatch   | Remove spaces / use correct path |
| `FileNotFoundError`       | Wrong directory        | Check with `!ls`                 |
| `UnimplementedError`      | Loading model via URL  | Use keras-facenet                |
| `NotFittedError`          | Predict before fit     | Train before saving              |
| `cv2.destroyAllWindows()` | Not supported in Colab | Remove this line                 |
| `pyngrok` tunnel error    | Too many connections   | Kill old tunnels                 |

---

### 🎯 12. Testing & Validation

* Verified face detection, confidence score, and attendance marking.
* Confirmed QR scanning works on multiple devices.
* Created **GIFs** for each step (dataset creation, recognition, attendance marking).

---

### 🔐 13. Security & Ethical Considerations

* Added `.gitignore` to exclude `credentials.json`.
* Avoid publishing student faces publicly.
* Use this only for academic, consent-based environments.

---

### 🏁 14. Final Notes

This project combined **computer vision + machine learning + web development + cloud integration**.
It’s fast, lightweight, and easily customizable for classrooms, labs, and organizations.

---

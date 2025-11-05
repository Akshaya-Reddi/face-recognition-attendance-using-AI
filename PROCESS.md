## 📘 Process I Followed to Build the Face Recognition Attendance System

> **Short Summary:**
> I built a contactless attendance system that detects student faces, recognizes them using **MTCNN + FaceNet + SVM**, and marks attendance automatically in **Google Sheets**.
> The app is deployed using **Flask** and **ngrok**, and includes a **QR code link** to open it on mobile devices.

---

### 🧩 1. Project Setup (Environment & Tools)

* Used **Google Colab** as the main development environment (since it’s cloud-based and works perfectly on Chromebook).
* Installed all required dependencies:

  ```bash
  !pip install keras-facenet mtcnn opencv-python-headless scikit-learn gspread google-auth pyngrok qrcode
  ```

---

### ☁️ 2. Google Cloud Setup for `credentials.json`

* Created a **Google Cloud Project**:

1. Go to [https://console.cloud.google.com](https://console.cloud.google.com)
2. *Create a New Project*

   * Click on *"Select a project" → "New Project"*
   * Name it something like: Face_Attendance_System.

3. *Enable Required APIs*

   * Navigate to *APIs & Services → Library*.
   * Search and enable:

     * ✅ Google Sheets API
     * ✅ Google Drive API

4. *Create Service Account Credentials*

   * Go to *APIs & Services → Credentials → Create Credentials → Service Account*
   * Give it a name, e.g. attendance-service.
   * Assign the role *Editor* (or a custom role with write access to Sheets).

5. *Generate a JSON Key File*

   * After creating the service account, click *Add Key → Create New Key → JSON*.
   * A file named credentials.json will automatically download to your computer.

6. *Upload credentials.json to Colab*

   * Use:

     python
     from google.colab import files
     files.upload()
     
   * Upload the downloaded credentials.json.

7. *Share Your Google Sheet with Service Account Email*

   * Open your attendance sheet in Google Sheets.
   * Click *Share* → paste the *client_email* from credentials.json → give *Editor* permission.

✅ *Now the system can read/write data in Google Sheets* securely.

---

### 📸 3. Data Collection & Storage

* Captured **20–40 images per student** for **7 different students**.

* Each folder was named using the **student’s admission number**.

* Folder structure:

  ```
  faces/
    └── Dataset/
         ├── 23CAM1001/
         ├── 23CAM1002/
         ├── 23CAM1003/
         └── ...
  ```

* Uploaded the dataset folders to **Google Drive**, downloaded them as ZIP,
  then uploaded and extracted inside Colab (`/content/faces/Dataset/`).

---

### 🧠 4. Dataset Creation & Preprocessing

* Used **MTCNN** for face detection and cropping.
* Each face was resized to **160×160 pixels** (FaceNet input size).
* Saved processed faces into arrays: `faces_X.npy` and `faces_y.npy`.

✅ **Common Fixes:**

* Used `os.listdir()` carefully to ignore system files.
* Verified dataset path correctness:

  ```python
  import os
  print(os.listdir('/content/faces/Dataset'))
  ```

---

### ⚙️ 5. Embedding Generation (FaceNet)

* Generated embeddings using **keras-facenet**:

  ```python
  from keras_facenet import FaceNet
  embedder = FaceNet()
  embedding = embedder.embeddings(face_array)
  ```

* Saved outputs as `X_embeddings.npy` and `y_labels.npy`.

✅ Fixed download/model errors by using `keras-facenet` instead of external URLs.

---

### 🧪 6. Training the Classifier (SVM)

* Split dataset:

  * Train: 198
  * Validation: 42
  * Test: 43

* Encoded labels and trained SVM model:

  ```python
  from sklearn.svm import SVC
  model = SVC(kernel='linear', probability=True)
  model.fit(X_train_emb, y_train_enc)
  ```

✅ Accuracy:

* Validation — **97.6%**
* Test — **100%** (for small dataset)

✅ Saved models:

```python
import pickle
pickle.dump(model, open('face_recognition_svm.pkl','wb'))
pickle.dump(label_encoder, open('label_encoder.pkl','wb'))
```

---

### 🎥 7. Testing with Static Images

* Tested model with unseen images in `/content/test_images/`.
* Added confidence threshold (≥ 0.7) for recognition.
* If below threshold → mark as “Unknown Student”.

---

### 🧾 8. Google Sheets Integration (Attendance)

* Used the `gspread` and `google-auth` libraries.
* Automatically appended attendance records like:

  ```python
  sheet.append_row([student_id, date, time, "Present"])
  ```

✅ Fixed `403 permission denied` by sharing sheet with service account.

---

### 🌐 9. Web Interface (Flask + QR + ngrok)

* Developed a **Flask** web interface:

  * `/` → Homepage with camera.
  * `/upload` → Accepts image, runs face recognition.

* Generated QR for mobile access:

  ```python
  import qrcode
  img = qrcode.make(public_url)
  img.save("attendance_qr.png")
  ```

* Used **ngrok** for public hosting:

  ```python
  from pyngrok import ngrok
  public_url = ngrok.connect(7000).public_url
  ```

✅ This lets students scan a QR and open the web app directly on their phones.

---

### 📱 10. Real-time Workflow

1. Lecturer runs Flask app → QR code appears.
2. Student scans QR using mobile → opens camera page.
3. Student’s face is detected and verified.
4. Attendance automatically marked in Google Sheet.

---

### 💾 11. Artifacts & Files Saved

| File                         | Purpose                        |
| ---------------------------- | ------------------------------ |
| `face_recognition_svm.pkl`   | Trained SVM classifier         |
| `label_encoder.pkl`          | Encoded label data             |
| `faces_X.npy`, `faces_y.npy` | Processed dataset              |
| `attendance_qr.png`          | QR with public ngrok link      |
| `.gitignore`                 | Excludes private & large files |
| `credentials.json`           | (Kept private) Google API key  |

---

### 🧰 12. Common Issues & Fixes

| Issue                     | Cause                       | Fix                        |
| ------------------------- | --------------------------- | -------------------------- |
| `IsADirectoryError`       | Hidden folders              | Remove spaces / check path |
| `FileNotFoundError`       | Wrong dataset path          | Use `!ls` to verify        |
| `UnimplementedError`      | Model loading from URL      | Use `keras-facenet`        |
| `NotFittedError`          | Using model before training | Train first                |
| `cv2.destroyAllWindows()` | Not supported in Colab      | Remove line                |
| `pyngrok tunnel error`    | Too many active tunnels     | Restart runtime            |

---

### 🧩 13. Testing & Validation

* Checked accuracy with test images.
* Verified attendance entries in Google Sheets.
* Tested QR scanning on multiple devices.
* Created demo visuals for process flow.

---

### 🔐 14. Security & Ethical Guidelines

* `credentials.json` excluded from public repo.
* Student face data kept private and consent-based.
* Used only for academic purposes.

---

### 🏁 15. Final Outcome

This project successfully integrates *AI-based facial recognition, **Flask web application, and **Google Sheets automation* into a fully functional, contactless classroom attendance system.

It demonstrates the power of combining:

* 🧠 Deep learning (FaceNet)
* 👀 Computer vision (MTCNN)
* ⚙️ Web automation (Flask + ngrok + QR)

---

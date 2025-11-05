---


# 🎯 Face Recognition Attendance System (QR-Enabled)

A **contactless classroom attendance system** built using **MTCNN**, **FaceNet**, **Flask**, and **Google Sheets** for seamless attendance marking through face recognition.  
This project eliminates manual attendance and enables **real-time, QR-accessible, AI-powered attendance tracking** — directly from any device 📱💻.

---

## 🧠 Project Overview

This system captures student faces using a webcam or mobile camera through a Flask web app, identifies them using a trained **FaceNet model**, and automatically marks their attendance in **Google Sheets**.  
A **QR code** is generated to make the app accessible instantly on smartphones for live attendance submission.

---

## 🧾 Features

*  **Real-time face detection and recognition** using MTCNN + FaceNet  
*  **QR code-based app access** for mobile compatibility  
*  **Automatic attendance logging** in Google Sheets  
*  **High accuracy SVM classifier** trained on facial embeddings  
*  **Confidence thresholding** to reject unknown faces  
*  **Multiple-face detection handling** (only one face allowed per capture)  
*  **User-friendly web interface** with camera capture and submission  
*  **Scalable and secure Google API integration**

---

## 🧰 Tech Stack

| Category | Tools / Libraries |
|-----------|-------------------|
| **Frontend** | HTML, CSS, JavaScript |
| **Backend** | Flask (Python) |
| **Face Detection** | MTCNN |
| **Face Embedding** | FaceNet |
| **Model Training** | SVM (Scikit-learn) |
| **Database** | Google Sheets API |
| **Cloud Tunnel** | Ngrok |
| **QR Code** | Python qrcode library |

---

## 🧩 Folder Structure

```

Face-Recognition-Attendance/
│
├── app.py                         # Main Flask application
├── face_recognition_svm.pkl       # Trained SVM model
├── credentials.json               # Google Sheets API credentials
│
├── /faces/
│   └── /Dataset/                  # Folder containing training images
│
├── /static/                       # CSS, JS, and QR code assets
│
├── /templates/                    # HTML templates for web interface
│
├── /gifs/                         # Demonstration GIFs (screen recordings)
│
└── README.md                      # Documentation file

````

---

## ⚙️ Installation & Setup

### 1️⃣ Clone the repository
```bash
git clone https://github.com/<your-username>/Face-Recognition-Attendance.git
cd Face-Recognition-Attendance
````

### 2️⃣ Install dependencies

```bash
pip install -r requirements.txt
```

### 3️⃣ Add Google Sheets credentials

Download your `credentials.json` file from Google Cloud Console and place it in the project root.

### 4️⃣ Prepare your dataset

Organize images in this structure:

```
faces/
 └── Dataset/
      ├── student1/
      │    ├── img1.jpg
      │    ├── img2.jpg
      ├── student2/
           ├── img1.jpg
           ├── img2.jpg
```

### 5️⃣ Train the model

Use the FaceNet embeddings and train the SVM model.

```bash
python train_model.py
```

This generates `face_recognition_svm.pkl`.

### 6️⃣ Run the Flask app

```bash
python app.py
```

### 7️⃣ Open the public link (Ngrok)

Once the server runs, a public URL appears:

```
Public URL: https://xxxx.ngrok-free.dev
```

Scan the **QR code** or open the link on your phone to access the web interface.

---

## 📸 How It Works

* 1️ **User scans QR code** displayed on screen
* 2️ **Camera access granted** in browser
* 3️ **System detects and recognizes** the face
* 4️ **Google Sheets** automatically updates with student name, date, and time
* 5️ If:

* Unknown face → displays *"Student Not Found"*
* Multiple faces → displays *"Multiple faces detected — show one face only"*

---

## 📊 Output Examples

| Example           | Description                                     |
| ----------------- | ----------------------------------------------- |
| 🎥 **GIF 1**      | Real-time face detection and attendance marking |
| 🎥 **GIF 2**      | Web interface camera capture and submission     |
| 🧾 **Screenshot** | Google Sheets attendance record                 |

---

## 💡 Future Enhancements

*  Replace Google Sheets with a dedicated **SQL/NoSQL attendance database**
*  Add **lecturer dashboard** to dynamically generate class-wise QR codes
*  Implement **time-restricted QR codes** (expire after 2–3 minutes)
*  Enable **attendance editing** for lecturers
*  Deploy fully to **cloud platforms** like AWS or Render for production use

---

## 🧍‍♂️ Author

**Akshaya Reddy Annareddy**
* AI & ML Student | Passionate about applied AI in education
* 📧 Email: [[annareddyakshayar@gmail.com](mailto:annareddyakshayar@gmail.com)]
* 🌐 GitHub: [https://github.com/Akshaya-Reddi](https://github.com/Akshaya-Reddi)

---

## 🪪 License

This project is licensed under the **MIT License** 

---

## ❤️ Acknowledgements

* [FaceNet - Google Research](https://arxiv.org/abs/1503.03832)
* [MTCNN - Zhang et al. (2016)](https://kpzhang93.github.io/MTCNN_face_detection_alignment/)
* [Flask Documentation](https://flask.palletsprojects.com/)
* [Google Sheets API](https://developers.google.com/sheets/api)

```

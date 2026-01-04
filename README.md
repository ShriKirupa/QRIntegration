# 📘 QR-Based Data Display using Pega (Constellation)

## 📌 Overview

This project demonstrates how to generate a **QR code from a Pega case** that, when scanned, opens a **static website** displaying case information (in JSON/text form).
The solution is **Pega Cloud–safe**, **Constellation-compatible**, and does **not** require Java, JARs, or server restarts.

---

## 🧱 Architecture (High Level)

```
Pega Case
   ↓
Build JSON payload from case properties
   ↓
Generate QR code using external QR API
   ↓
QR scanned on mobile
   ↓
Static website opens
   ↓
Website reads data from URL and displays it
```

---

## 🛠️ Technologies Used

* **Pega Platform (Constellation UI)**
* **GitHub Pages** (static website hosting)
* **HTML + CSS + JavaScript** (no backend)
* **QR Server API**

  ```
  https://api.qrserver.com/v1/create-qr-code/
  ```

---

## 🧩 Pega Implementation Steps

### 1️⃣ Case Properties

Create the following properties in the case type:

| Property Name          | Type               |
| ---------------------- | ------------------ |
| `.Student`             | Embedded Data Type |
| `.Student.StudentName` | Text               |
| `.Student.Event`       | Text               |
| `.Student.Department`  | Text               |
| `.Student.Status`      | Text               |
| `.QRPayload`           | Text               |
| `.QRImageURL`          | Text               |

---

### 2️⃣ Build QR Payload (Data Transform)

Create a Data Transform: **`BuildQRPayload`**

```text
.QRPayload =
"{\"name\":\"" + .Student.StudentName +
"\",\"event\":\"" + .Student.Event +
"\",\"department\":\"" + .Student.Department +
"\",\"status\":\"" + .Student.Status + "\"}"
```

⚠️ This Data Transform **must run after assignment submission**, otherwise embedded data type values may be blank.

---

### 3️⃣ Build QR Image URL (Data Transform)

Create another Data Transform: **`SetQRImageURL`**

```text
.QRImageURL =
"https://api.qrserver.com/v1/create-qr-code/?size=200x200&data="
+ @encodeURIComponent(
  "https://<username>.github.io/<repo-name>/?data=" + .QRPayload
)
```

Replace `<username>` and `<repo-name>` with your GitHub Pages details.

---

### 4️⃣ Flow Configuration

In the case flow:

1. Collect information (user fills data)
2. On **Submit**
3. Add a **Utility shape** that calls:

   * `BuildQRPayload`
   * `SetQRImageURL`

---

### 5️⃣ Constellation UI

In the Constellation View:

* Add an **Image** field
* Source → **URL**
* Value → `.QRImageURL`

The QR code will render immediately.

---

## 🌐 Website Implementation (GitHub Pages)

### Files

* `index.html` (static page)

### Functionality

* Reads `data` query parameter from the URL
* Parses JSON using JavaScript
* Displays the information in a styled ticket/card layout

### Example URL

```
https://<username>.github.io/<repo-name>/?data={"name":"Alex","event":"TechFest","department":"CSE","status":"Confirmed"}
```

---

## 📱 End User Experience

1. User completes the Pega case
2. QR code is generated and shown
3. QR is scanned on a mobile phone
4. A website opens
5. Case details are displayed clearly

---

## ⚠️ Notes & Limitations

* QR data is **publicly visible**
* URL length limit applies (~2–3 KB)
* Data is a **snapshot** at generation time
* Best suited for **demos, labs, and presentations**

---

## 🧠 Key Takeaway

> This approach avoids custom Java, server-side image generation, and authentication complexity while remaining fully compatible with Pega Cloud and Constellation UI.

---

# 🚀 Sales Data Upload & BigQuery Pipeline

This project consists of:
1. **Flask Web App**: Uploads CSV files to Google Cloud Storage (GCS).
2. **Cloud Function**: Automatically loads CSV files from GCS into BigQuery.

---

## 📁 **Project Structure**

📂 sales-data-pipeline/ │── 📄 functions.py # Cloud Function: Loads CSV files from GCS to BigQuery │── 📄 main.py # Flask app: Uploads files to GCS │── 📄 index.html # UI for file uploads │── 📄 requirements.txt # Dependencies │── 📄 README.md # Project documentation


---

## 🚀 **Setup & Deployment**

### **1️⃣ Prerequisites**
Before deploying, ensure you have:
- **Google Cloud SDK** installed → [Install Guide](https://cloud.google.com/sdk/docs/install)
- **BigQuery Dataset** (`sales`) and Table (`orders`)
- **Google Cloud Storage Bucket** (`bkt-sales-data-bucket`)
- **Service Account with necessary permissions** (`roles/storage.admin`, `roles/bigquery.dataEditor`)

---

### **2️⃣ Authentication & GCP Setup**
Login and authenticate with GCP:
```sh
gcloud auth login
gcloud config set project [YOUR_PROJECT_ID]
gcloud auth application-default login

Ensure the service account has access to GCS and BigQuery:
gcloud projects add-iam-policy-binding [YOUR_PROJECT_ID] \
    --member="serviceAccount:[YOUR_SA_EMAIL]" \
    --role="roles/storage.admin"

gcloud projects add-iam-policy-binding [YOUR_PROJECT_ID] \
    --member="serviceAccount:[YOUR_SA_EMAIL]" \
    --role="roles/bigquery.dataEditor"

```

### **3️⃣ Deploy the Flask Web App (File Uploader)**
This Flask app provides a UI to upload CSV files to GCS.
Install dependencies
```sh
pip install -r requirements.txt
```
Run Flask Locally
```sh
python main.py
```
Open http://127.0.0.1:5000/
Upload a CSV file → It gets stored in GCS Bucket

Deploy Flask App to Cloud Run:
```sh
gcloud builds submit --tag gcr.io/[YOUR_PROJECT_ID]/sales-upload
gcloud run deploy sales-upload \
    --image gcr.io/[YOUR_PROJECT_ID]/sales-upload \
    --platform managed \
    --region [YOUR_REGION] \
    --allow-unauthenticated
```
---
### **4️⃣ Deploy Cloud Function (GCS → BigQuery)**
Deploy the function
```sh
gcloud functions deploy hello_gcs \
    --runtime python311 \
    --trigger-resource bkt-sales-data-bucket \
    --trigger-event google.storage.object.finalize \
    --entry-point hello_gcs \
    --region [YOUR_REGION] \
    --allow-unauthenticated

```
---

### **5️⃣ Test End-to-End Flow**
Upload a CSV file via Flask UI.
Verify the file appears in GCS Bucket.
Cloud Function triggers automatically and loads data into BigQuery.
Check BigQuery Table:
```sh
SELECT * FROM `your_project.sales.orders`

```

### **⚠️ Troubleshooting**
Cloud Function not triggering?
→ Ensure GCS events are set up properly.
BigQuery Table missing?
→ Run:
```sh
bq mk --table sales.orders column1:STRING,column2:INTEGER,column3:FLOAT
```
Authentication Issues?
→ Run gcloud auth application-default login

---
### **🎯 Next Steps**
Improve UI: Add progress bars for file upload.
Add Logging: Log all processed files in Firestore.
Enhance Security: Restrict public access.

---
### **Author: [Yeshwanth Yedla]**
### **📌 GitHub Repo: [https://github.com/Yeshwanth2409/Sales-Data-Pipeline-Project]**


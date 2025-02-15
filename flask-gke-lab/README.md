# 🚀 Deploying a Python Web App on GKE using Docker & GCP Container Registry  

## 📌 Project Overview  
This project demonstrates how to containerize a **Flask web application**, store the image in **Google Container Registry (GCR)**, and deploy it on **Google Kubernetes Engine (GKE)**.  

The app features a **colorful UI** with a motivational message:  
> *"Learning DevOps is Challenging"*  

This serves as a **hands-on guide** to deploying a simple Python web app on a **scalable Kubernetes cluster in Google Cloud Platform (GCP).**  

---

## 📂 Project Structure  
```
📦 flask-gke-lab
 ┣ 📂 templates
 ┃ ┗ 📜 index.html      # Frontend with a styled UI
 ┣ 📜 app.py            # Flask app
 ┣ 📜 Dockerfile        # Instructions for containerizing the app
 ┣ 📜 requirements.txt  # Python dependencies
 ┣ 📜 deployment.yaml   # Kubernetes Deployment & Service
 ┗ 📜 README.md         # Project documentation
```


## ⚙️ Technologies Used
 - Python 🐍 (Flask Web Framework)
 - Docker 🐳 (Containerization)
 - Google Kubernetes Engine (GKE) ☁️
 - Google Container Registry (GCR)
 - Kubernetes 🚀

---

## 🚀 Setup & Deployment Steps 

1️⃣ Clone the Repository
```
git clone https://github.com/your-username/flask-gke-lab.git
cd flask-gke-lab
```
2️⃣ Create & Activate a Virtual Environment
```
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install -r requirements.txt
```
3️⃣ Create the Flask Application<br>
Create app.py
```
from flask import Flask, render_template

app = Flask(__name__)

@app.route("/")
def home():
    return render_template("index.html")

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8080)
```
4️⃣ Create the Frontend<br>
Create a templates/index.html file:
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flask on GKE</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            background-color: #282c34;
            color: white;
            margin-top: 50px;
        }
        h1 {
            color: #61dafb;
        }
    </style>
</head>
<body>
    <h1>🚀 Learning DevOps is Challenging 🚀</h1>
</body>
</html>
```
5️⃣ Run the Flask App Locally
```
python app.py
```
Visit http://localhost:8080 in your browser.

<br>
---
##🐳 Dockerizing the Application
6️⃣ Create a Dockerfile

```
# Use official Python image as base
FROM python:3.9

# Set the working directory
WORKDIR /app

# Copy application files
COPY . .

# Install dependencies
RUN pip install -r requirements.txt

# Expose port 8080
EXPOSE 8080

# Run the application
CMD ["python", "app.py"]
```
7️⃣ Build the Docker Image
```
docker build -t flask-gke-lab .
```
8️⃣ Run the Docker Container Locally
```
docker run -p 8080:8080 flask-gke-lab
```
☁️ Deploying to Google Cloud
9️⃣ Authenticate with GCP
```
gcloud auth login
gcloud config set project YOUR_PROJECT_ID
```
🔟 Enable Required GCP Services
```
gcloud services enable container.googleapis.com artifactregistry.googleapis.com
```
1️⃣1️⃣ Tag & Push Image to GCR
```
docker tag flask-gke-lab gcr.io/YOUR_PROJECT_ID/flask-gke-lab
docker push gcr.io/YOUR_PROJECT_ID/flask-gke-lab
```
📌 Deploying to GKE
1️⃣2️⃣ Create a Kubernetes Deployment File
<br>
Create deployment.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-gke-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: flask-gke-app
  template:
    metadata:
      labels:
        app: flask-gke-app
    spec:
      containers:
      - name: flask-gke-app
        image: gcr.io/YOUR_PROJECT_ID/flask-gke-lab
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: flask-gke-service
spec:
  selector:
    app: flask-gke-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: LoadBalancer
```
1️⃣3️⃣ Create a Kubernetes Cluster
```
gcloud container clusters create flask-cluster --num-nodes=2 --zone us-central1-a
gcloud container clusters get-credentials flask-cluster --zone us-central1-a
```
1️⃣4️⃣ Deploy the Application
```
kubectl apply -f deployment.yaml
```
1️⃣5️⃣ Get the External IP
```
kubectl get services
```
Access the app using the EXTERNAL-IP of the LoadBalancer.
<br>
---
🎯 Key Takeaways<br>
✅ Working with Docker & Kubernetes<br>
✅ Deploying containerized apps on Google Cloud<br>
✅ Using Google Container Registry (GCR)<br>
✅ Setting up GKE for scalable applications<br>

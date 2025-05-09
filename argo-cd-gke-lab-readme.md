# Hands-On Lab: Deploying Applications with Argo CD on GKE

This lab is a personal learning project where I deployed and ran [Argo CD](https://argo-cd.readthedocs.io/en/stable/) on **Google Kubernetes Engine (GKE)**, and used it to deploy a simple application using GitOps principles.

## 🔧 What I Did

- Created a GKE cluster with a single node
- Installed Argo CD into the Kubernetes cluster
- Exposed the Argo CD web UI using a LoadBalancer service
- Logged in to Argo CD and added the GKE cluster as a target
- Deployed the sample **Guestbook** application using a public GitHub repo

## 🛠️ Prerequisites

- Google Cloud CLI (`gcloud`)
- Kubernetes CLI (`kubectl`)
- Argo CD CLI (`argocd`)
- A Google Cloud project with billing enabled

## 🚀 Step-by-Step Guide

### 1. Create a GKE Cluster

```bash
gcloud container clusters create argo-cd-cluster \
  --zone us-central1-a \
  --num-nodes=1
```

Get cluster credentials:

```bash
gcloud container clusters get-credentials argo-cd-cluster --zone us-central1-a
```

### 2. Install Argo CD

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 3. Expose the Argo CD API Server

```bash
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

Get the external IP:

```bash
kubectl get svc argocd-server -n argocd
```

### 4. Install Argo CD CLI

#### macOS:
```bash
brew install argocd
```

#### Linux:
```bash
curl -sSL -o argocd https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
chmod +x argocd
sudo mv argocd /usr/local/bin/
```

### 5. Log In to Argo CD

Get the initial admin password:

```bash
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d && echo
```

Log in using the external IP:

```bash
argocd login <ARGOCD_SERVER_IP> --username admin --password <INITIAL_PASSWORD> --insecure
```

### 6. Connect the Cluster to Argo CD

```bash
argocd cluster add $(kubectl config current-context)
```

### 7. Deploy the Guestbook App

```bash
argocd app create guestbook \
  --repo https://github.com/argoproj/argo-cd-example-apps.git \
  --path guestbook \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default
```

Sync the app:

```bash
argocd app sync guestbook
```

### 8. Open Argo CD Web UI

Navigate to:  
```
http://<ARGOCD_SERVER_IP>
```

Login using `admin` and the password you retrieved earlier.

---

## 🧠 What I Learned

- GitOps workflows make managing Kubernetes apps declarative and efficient.
- Argo CD provides clear visibility and control over app deployment states.
- GKE + Argo CD is a powerful combo for cloud-native CI/CD.

## 📎 Resources

- [Argo CD Docs](https://argo-cd.readthedocs.io/)
- [Argo CD Example Apps](https://github.com/argoproj/argo-cd-example-apps)

## 📌 Notes

This was done for learning purposes and uses default credentials and insecure options. For production, always secure your Argo CD installation properly.

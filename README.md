# myapp-ci-pipeline

Below is a **ready-to-use sample repo** you can create and push to GitHub. It will **build a Docker image and push it to Docker Hub** using your Jenkinsfile.

---

## 📦 Repository Name (example)

```
myapp-ci-pipeline
```

---

## 📁 Repository Structure

```
myapp-ci-pipeline/
├── Jenkinsfile
├── Dockerfile
├── app.py
├── requirements.txt
├── tests/
│   └── test_app.py
└── README.md
```

---

## 1️⃣ Jenkinsfile (CI Pipeline)

👉 **Jenkinsfile**

```groovy
pipeline {
    agent any

    environment {
        IMAGE_NAME = "dockerhubusername/myapp"
        TAG = "latest"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/<your-username>/myapp-ci-pipeline.git'
            }
        }

        stage('Build App') {
            steps {
                echo "Building application..."
            }
        }

        stage('Run Tests') {
            steps {
                sh 'python3 -m pytest tests/'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$TAG .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $IMAGE_NAME:$TAG
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "CI Pipeline completed successfully"
        }
        failure {
            echo "CI Pipeline failed"
        }
    }
}
```

---

## 2️⃣ Application Code

👉 **app.py**

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello from Jenkins CI Pipeline!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

---

## 3️⃣ Python Dependencies

👉 **requirements.txt**

```
flask
pytest
```

---

## 4️⃣ Test Case

👉 **tests/test_app.py**

```python
def test_dummy():
    assert 1 == 1
```

---

## 5️⃣ Dockerfile

👉 **Dockerfile**

```dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY app.py .

EXPOSE 5000

CMD ["python", "app.py"]
```

---

## 6️⃣ README.md

👉 **README.md**

````md
# myapp-ci-pipeline

This repository demonstrates a **CI pipeline using Jenkins** that:
- Pulls source code from GitHub
- Runs tests
- Builds a Docker image
- Pushes the image to Docker Hub

---

## 🚀 Tech Stack
- Jenkins
- Docker
- Python (Flask)
- GitHub
- Docker Hub

---

## 🔧 Jenkins Prerequisites
- Jenkins installed
- Docker installed on Jenkins node
- Docker Hub credentials added in Jenkins  
  **Credentials ID:** `dockerhub-creds`

---

## ▶️ Run Locally
```bash
docker build -t myapp .
docker run -p 5000:5000 myapp
````

Open:

```
http://localhost:5000
```

---

## 📦 Docker Image

```
dockerhubusername/myapp:latest
```

````

---

## 7️⃣ How to Create the Repo (Commands)

```bash
git init
git add .
git commit -m "Initial CI pipeline setup"
git branch -M main
git remote add origin https://github.com/<your-username>/myapp-ci-pipeline.git
git push -u origin main
````

---

Perfect 👍
Below is a **clear, step-by-step end-to-end guide** covering **Jenkins Pipeline Job**, **Docker Hub credentials**, and **Optional ArgoCD + Kubernetes deployment**. This matches exactly what you’re building.

---

# ✅ PART 1: Create Jenkins Pipeline Job

## 1️⃣ Open Jenkins

```
http://<jenkins-ip>:8080
```

Login with your Jenkins credentials.

---

## 2️⃣ Create Pipeline Job

1. Click **New Item**
2. Job name:

   ```
   myapp-ci-pipeline
   ```
3. Select **Pipeline**
4. Click **OK**

---

## 3️⃣ Configure Pipeline

Scroll to **Pipeline section**:

* **Definition** → `Pipeline script from SCM`
* **SCM** → `Git`
* **Repository URL**

  ```
  https://github.com/<your-username>/myapp-ci-pipeline.git
  ```
* **Branch**

  ```
  main
  ```
* **Script Path**

  ```
  Jenkinsfile
  ```

✅ Click **Save**

---

## 4️⃣ Run the Job

Click **Build Now**

✔️ Jenkins will:

* Pull code
* Run tests
* Build Docker image
* Push image to Docker Hub

---

# ✅ PART 2: Add Docker Hub Credentials in Jenkins

## 1️⃣ Go to Credentials

```
Manage Jenkins → Credentials → System → Global credentials
```

Click **Add Credentials**

---

## 2️⃣ Fill Details

* **Kind**: Username with password
* **Username**: Docker Hub username
* **Password**: Docker Hub password / access token
* **ID**:

  ```
  dockerhub-creds
  ```
* **Description**: Docker Hub credentials

Click **Create**

✔️ This matches:

```groovy
credentialsId: 'dockerhub-creds'
```

---

## 3️⃣ Verify Docker on Jenkins Node

```bash
docker --version
```

If Jenkins user can’t access Docker:

```bash
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
```

---

# ✅ PART 3 (OPTIONAL): Kubernetes Deployment Manifests

Create a new folder in repo:

```
k8s/
```

---

## 1️⃣ Deployment YAML

📄 `k8s/deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 2
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
        - name: myapp
          image: dockerhubusername/myapp:latest
          ports:
            - containerPort: 5000
```

---

## 2️⃣ Service YAML

📄 `k8s/service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: NodePort
  selector:
    app: myapp
  ports:
    - port: 80
      targetPort: 5000
```

---

## 3️⃣ Apply Manifests (Manual Test)

```bash
kubectl apply -f k8s/
kubectl get pods
kubectl get svc
```

---

# ✅ PART 4 (OPTIONAL): ArgoCD Application

## 1️⃣ Install ArgoCD

```bash
kubectl create namespace argocd
kubectl apply -n argocd \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

---

## 2️⃣ Access ArgoCD UI

```bash
kubectl port-forward -n argocd svc/argocd-server 8080:443
```

URL:

```
http://localhost:8080
```

Get admin password:

```bash
kubectl get secret argocd-initial-admin-secret \
-n argocd -o jsonpath="{.data.password}" | base64 -d
```

---

## 3️⃣ ArgoCD Application YAML

📄 `argocd-app.yaml`

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: myapp
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/<your-username>/myapp-ci-pipeline.git
    targetRevision: main
    path: k8s
  destination:
    server: https://kubernetes.default.svc
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

Apply:

```bash
kubectl apply -f argocd-app.yaml
```

✔️ ArgoCD will auto-deploy on every Git change.

---

# ✅ PART 5: CI/CD FLOW (Final Architecture)

```
GitHub → Jenkins (CI)
           ↓
     Docker Image
           ↓
       Docker Hub
           ↓
        ArgoCD
           ↓
      Kubernetes (EKS / Minikube)
```

---

# 🚀 NEXT OPTIONS (Tell me what you want)

✅ Convert Kubernetes to **EKS**
✅ Use **Minikube** locally
✅ Add **image tag automation**
✅ Add **Helm charts**
✅ Add **Slack notifications**


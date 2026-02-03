

# CI/CD Pipeline with Jenkins and Docker (Python App)

Objective

The goal of this project is to demonstrate a **complete CI/CD pipeline** using **Jenkins and Docker**.
The pipeline automatically:

* Pulls source code from GitHub
* Runs unit tests
* Builds a Docker image
* Runs tests inside the Docker container
* Deploys the application as a running container

This setup reflects **real-world DevOps practices** and is suitable for production-style CI/CD workflows.

---

## 🛠 Tech Stack

* Python 3.10
* Pytest
* Docker
* Jenkins (running inside Docker)
* GitHub

---

## 📂 Project Structure

```
.
├── app.py
├── test_app.py
├── requirements.txt
├── Dockerfile
├── Jenkinsfile
└── README.md
```

---

Application Code

`app.py`

```python
def add(a, b):
    return a + b

if __name__ == "__main__":
    print("Application started successfully")
```

---

### `test_app.py`

```python
from app import add

def test_add():
    assert add(2, 3) == 5
```

---

### `requirements.txt`

```text
pytest
```

---

## 🐳 Dockerfile Explanation

```dockerfile
FROM python:3.10-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["python", "app.py"]
```



Jenkins Pipeline (Jenkinsfile)

```groovy
pipeline {
    agent any

    environment {
        IMAGE_NAME = "python-ci-app"
        CONTAINER_NAME = "python-ci-container"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/vpsubhash1302/jenkins-docker-pipeline.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'apt install -y python3-pytest'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'pytest test_app.py'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Run Tests Inside Docker') {
            steps {
                sh 'docker run --rm $IMAGE_NAME pytest'
            }
        }

        stage('Run Application Container') {
            steps {
                sh '''
                docker rm -f $CONTAINER_NAME || true
                docker run -d --name $CONTAINER_NAME $IMAGE_NAME
                '''
            }
        }
    }

    post {
        success {
            echo '✅ CI/CD Pipeline completed successfully'
        }
        failure {
            echo '❌ Pipeline failed'
        }
    }
}
```

---

 Jenkins Setup Instructions

 Run Jenkins using Docker

```bash
docker run -d \
  --name jenkins \
  --user root \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  jenkins/jenkins:lts
```

---

Install Required Tools inside Jenkins

```bash
docker exec -it jenkins bash
apt update
apt install -y docker.io python3 python3-pip python3-pytest vim
exit
```

---

Jenkins Plugins

Install:

* Git
* Pipeline
* Docker Pipeline

---

Create Jenkins Pipeline Job

* New Item → Pipeline
* Pipeline script from SCM
* SCM: Git
* Repository URL: GitHub repo
* Branch: `main`

---

Running the Pipeline

Click **Build Now** in Jenkins.

Expected stages:

```
✔ Checkout Code
✔ Install Dependencies
✔ Run Tests
✔ Build Docker Image
✔ Run Tests Inside Docker
✔ Run Application Container
```

---

Verification

```bash
docker ps
docker logs python-ci-container
```

---



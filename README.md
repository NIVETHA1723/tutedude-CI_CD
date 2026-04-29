# 🚀 CI/CD Deployment using Flask, Express, AWS EC2 & Jenkins

## 📌 Project Overview

This project demonstrates the deployment of a **Flask backend** and an **Express frontend** on an Amazon EC2 instance, along with a **CI/CD pipeline using Jenkins** to automate deployment.

---

## 🏗️ Architecture

User → EC2 Instance → Flask (Port 5000)
→ Express (Port 3000)
GitHub → Jenkins → Auto Deployment

---

## ⚙️ Technologies Used

* AWS EC2 (Ubuntu)
* Flask (Python Backend)
* Express (Node.js Frontend)
* Jenkins (CI/CD)
* Docker (for Jenkins)
* PM2 (Process Manager)
* Git & GitHub

---

# 🧩 Part 1: Deployment on EC2

## 1️⃣ EC2 Setup

* Launched EC2 instance (t3.micro)
* Opened ports:

  * 22 (SSH)
  * 3000 (Express)
  * 5000 (Flask)
  * 8080 (Jenkins)

---

## 2️⃣ Connect to EC2

```bash
ssh -i "flask.pem" ubuntu@<public-ip>
```

---

## 3️⃣ Install Dependencies

```bash
sudo apt update
sudo apt install python3-pip nodejs npm git -y
sudo npm install -g pm2
```

---

## 4️⃣ Flask Setup

```bash
git clone https://github.com/NIVETHA1723/flask-todo-project.git
cd flask-todo-project
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

Run Flask:

```bash
pm2 start app.py --name flask --interpreter python3
```

---

## 5️⃣ Express Setup

```bash
mkdir express-app
cd express-app
npm init -y
npm install express
```

Create `app.js`:

```javascript
const express = require("express");
const app = express();

app.get("/", (req, res) => {
    res.send("Express running");
});

app.listen(3000, "0.0.0.0");
```

Run Express:

```bash
pm2 start app.js --name express
```

---

## 6️⃣ Save Processes

```bash
pm2 save
pm2 startup
```

---

## 🌐 Application URLs

* Flask → http://<public-ip>:5000
* Express → http://<public-ip>:3000

---

# 🧩 Part 2: Jenkins CI/CD Setup

## 1️⃣ Install Docker

```bash
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
```

---

## 2️⃣ Run Jenkins Container

```bash
sudo docker run -d \
  --name jenkins \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts
```

---

## 3️⃣ Access Jenkins

Open:

http://<public-ip>:8080

Get password:

```bash
sudo docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

---

## 4️⃣ Install Plugins

* Install suggested plugins

---

# 🔄 CI/CD Pipeline

## Flask Pipeline

```groovy
pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/NIVETHA1723/flask-todo-project.git'
            }
        }

        stage('Install') {
            steps {
                sh '''
                python3 -m venv venv
                . venv/bin/activate
                pip install -r requirements.txt
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                pm2 restart flask || pm2 start app.py --name flask --interpreter python3
                '''
            }
        }
    }
}
```

---

## Express Pipeline

```groovy
pipeline {
    agent any

    stages {
        stage('Install') {
            steps {
                sh '''
                cd ~/express-app
                npm install
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                pm2 restart express || pm2 start ~/express-app/app.js --name express
                '''
            }
        }
    }
}
```

---

# 🔔 GitHub Webhook

* Go to GitHub → Settings → Webhooks
* Add:

```
http://<public-ip>:8080/github-webhook/
```

---

# 🎯 CI/CD Workflow

1. Developer pushes code to GitHub
2. GitHub triggers Jenkins
3. Jenkins:

   * Pulls latest code
   * Installs dependencies
   * Restarts applications using PM2

---

# 📸 Screenshots (To Attach)

* EC2 running instance
* Flask output (port 5000)
* Express output (port 3000)
* Jenkins dashboard
* Pipeline success logs

---

# 🧠 Challenges Faced

* SSH key mismatch issues
* Jenkins installation GPG errors
* Docker permission issues
* Low memory causing Jenkins lag

---

# ✅ Conclusion

Successfully implemented:

* Deployment on AWS EC2
* Backend + Frontend integration
* CI/CD automation using Jenkins
* Process management using PM2

---

# 👨‍💻 Author

MOHAMED ADHNAAN J M

---

# 🚀 Future Improvements

* Add automated testing stage
* Use Nginx as reverse proxy
* Deploy using Kubernetes
* Add monitoring (Prometheus/Grafana)

# 🚀 Flask App Deployment using Docker + Azure Web App (Container) + GitHub Actions

This project demonstrates how to:

-   Build a Flask application
-   Containerize it using Docker
-   Push the image to Azure Container Registry (ACR)
-   Deploy it to Azure Web App (Container)
-   Automate the entire process using GitHub Actions

------------------------------------------------------------------------

# 🧱 Architecture

VS Code → GitHub → GitHub Actions → Build Docker Image → Push to ACR →
Azure Web App (Container)

------------------------------------------------------------------------

# 📁 Project Structure

flask-container-app/ │ ├── app.py ├── requirements.txt ├── Dockerfile
└── .github/ └── workflows/ └── deploy-container.yml

------------------------------------------------------------------------

# 🐍 Step 1 -- Flask Application

## app.py

``` python
from flask import Flask
import os

app = Flask(__name__)

@app.route("/")
def home():
    return "Flask running inside Docker on Azure 🚀"

@app.route("/health")
def health():
    return {"status": "healthy"}

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000)
```

## requirements.txt

    flask
    gunicorn

------------------------------------------------------------------------

# 🐳 Step 2 -- Dockerfile

``` dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["gunicorn", "--bind", "0.0.0.0:8000", "app:app"]
```

------------------------------------------------------------------------

# 💻 Step 3 -- Run Locally with Docker

Build image:

docker build -t flask-azure-demo .

Run container:

docker run -p 8000:8000 flask-azure-demo

Open:

http://localhost:8000

------------------------------------------------------------------------

# ☁ Step 4 -- Create Azure Resources

You need:

1.  Azure Container Registry (ACR)
2.  Azure Web App (Publish: Docker Container, OS: Linux)

------------------------------------------------------------------------

# 🔐 Step 5 -- Push Image to ACR

az login az acr login --name `<your-acr-name>`{=html}

docker tag flask-azure-demo
`<your-acr-name>`{=html}.azurecr.io/flask-azure-demo:v1 docker push
`<your-acr-name>`{=html}.azurecr.io/flask-azure-demo:v1

------------------------------------------------------------------------

# 🤖 Step 6 -- GitHub Actions Workflow

Create:

.github/workflows/deploy-container.yml

``` yaml
name: Build and Deploy Container to Azure

on:
  push:
    branches:
      - main

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Login to ACR
        uses: azure/docker-login@v2
        with:
          login-server: <your-acr-name>.azurecr.io
          username: ${{ secrets.ACR_USERNAME }}
          password: ${{ secrets.ACR_PASSWORD }}

      - name: Build and Push Image
        run: |
          docker build -t <your-acr-name>.azurecr.io/flask-azure-demo:latest .
          docker push <your-acr-name>.azurecr.io/flask-azure-demo:latest

      - name: Azure Login
        uses: azure/login@v2
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: Restart Web App
        run: |
          az webapp restart --name <your-webapp-name> --resource-group <your-resource-group>
```

------------------------------------------------------------------------

# 🔑 Create Service Principal

az ad sp create-for-rbac --name github-deploy-sp --role contributor
--scopes
/subscriptions/`<subscription-id>`{=html}/resourceGroups/`<resource-group>`{=html}
--sdk-auth

Copy JSON output → Save as GitHub Secret: AZURE_CREDENTIALS

------------------------------------------------------------------------

# 🌐 Access Application

https://`<your-webapp-name>`{=html}.azurewebsites.net

------------------------------------------------------------------------

# 🧠 Learning Outcomes

-   Flask production deployment
-   Docker containerization
-   Azure Container Registry
-   Azure Web App (Container)
-   CI/CD automation
-   Secret management
-   DevOps workflow

# 🚀 Flask Docker Deployment using Azure Container Registry + Azure Web App (Container)

This guide provides COMPLETE step‑by‑step instructions to:

-   Build a Flask application
-   Containerize it using Docker
-   Push the image to Azure Container Registry (ACR)
-   Deploy it to Azure Web App (Container option)
-   Fix common errors (tag not found, gunicorn missing, 502 errors)

This is designed for lab / academic / student use.

------------------------------------------------------------------------

# 🧱 Architecture

VS Code → Docker Build → Azure Container Registry (ACR) → Azure Web App
(Container)

------------------------------------------------------------------------

# 📁 Project Structure

flask-container-app/ │ ├── app.py ├── requirements.txt ├── Dockerfile

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

RUN pip install --upgrade pip
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["gunicorn", "--bind", "0.0.0.0:8000", "app:app"]
```

------------------------------------------------------------------------

# 💻 Step 3 -- Build & Test Locally

docker build -t flask-azure-demo .

docker run -p 8000:8000 flask-azure-demo

Open: http://localhost:8000

------------------------------------------------------------------------

# ☁ Step 4 -- Create Azure Container Registry (ACR)

-   Subscription: Azure for Students
-   Resource Group: flask-container-rg
-   Registry Name: flaskregistry12345 (must be globally unique)
-   SKU: Basic
-   Role assignment permissions mode: RBAC Registry Permissions

After creation:

ACR → Access Keys → Enable Admin User

Copy Username and Password.

------------------------------------------------------------------------

# 📦 Step 5 -- Tag & Push Image

docker tag flask-azure-demo
flaskregistry12345.azurecr.io/flask-azure-demo:latest

az acr login --name flaskregistry12345

docker push flaskregistry12345.azurecr.io/flask-azure-demo:latest

------------------------------------------------------------------------

# 🌐 Step 6 -- Create Azure Web App (Container)

-   Publish: Docker Container
-   OS: Linux
-   Region: Same as ACR

------------------------------------------------------------------------

# 🔐 Step 7 -- Configure Container Settings

Web App → Deployment Center → Container

Authentication: Admin credentials

Image: flask-azure-demo\
Tag: latest\
Port: 8000

IMPORTANT: Port must match Docker EXPOSE (8000).

Save → Restart.

------------------------------------------------------------------------

# 🔍 Step 8 -- Check Logs

Web App → Log Stream

Look for: Running on http://0.0.0.0:8000

------------------------------------------------------------------------

# 🌐 Access Application

https://`<your-webapp-name>`{=html}.azurewebsites.net

------------------------------------------------------------------------

# 🚨 Common Errors

1.  tag does not exist → You forgot to tag image.
2.  gunicorn not found → Rebuild with --no-cache.
3.  502 error → Port mismatch (must be 8000).

------------------------------------------------------------------------

# 🎓 Learning Outcomes

-   Docker containerization
-   Azure Container Registry
-   Azure Web App (Container)
-   Production deployment
-   Debugging container issues
-   DevOps fundamentals

------------------------------------------------------------------------

END OF GUIDE

# Flask → GitHub Actions → Azure App Service (master branch)

This guide helps you deploy a Flask application from your laptop to Azure automatically whenever you push code to the **master** branch.

Pipeline:

VS Code → GitHub → GitHub Actions → Azure Web App

---

## 🎯 Final Outcome

Every time you run:

```bash
git push origin master
```

Your app will build and redeploy automatically.

---

## 🧱 Prerequisites

Install on your system:

- Python 3.10+
- Git
- Visual Studio Code

Accounts required:

- GitHub
- Microsoft Azure

---

## 🧱 Step 1 – Create Project

Create a folder:

```
flask-azure-demo
```

Inside it create:

```
app.py
requirements.txt
```

---

## 🧱 Step 2 – Add Flask Code

### app.py

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Flask app running on Azure 🚀"

if __name__ == "__main__":
    app.run()
```

### requirements.txt

```
flask
gunicorn
```

---

## 🧱 Step 3 – Test Locally

```bash
pip install -r requirements.txt
python app.py
```

Open http://127.0.0.1:5000

If it works locally → cloud deployment will work.

---

## 🧱 Step 4 – Initialize Git

```bash
git init
git add .
git commit -m "initial commit"
```

---

## 🧱 Step 5 – Create GitHub Repository

Create an empty repository on GitHub.

Then connect it:

```bash
git branch -M master
git remote add origin https://github.com/<username>/<repo>.git
git push -u origin master
```

---

## 🧱 Step 6 – Create Azure Web App

In Azure portal:

Create → Web App → choose **Python** runtime.

After deployment you will get a URL like:

```
https://yourapp.azurewebsites.net
```

---

## 🧱 Step 7 – Enable Publish Profile Download

If download fails:

Azure → Web App → Settings → Configuration → General Settings

Enable **SCM Basic Auth Publishing Credentials**.

Save and **Restart** the app.

---

## 🧱 Step 8 – Download Publish Profile

Go to Overview → **Get publish profile**.

Open the file and copy all contents.

---

## 🧱 Step 9 – Add Secret in GitHub

GitHub Repo → Settings → Secrets and variables → Actions → New repository secret.

Name:

```
AZURE_WEBAPP_PUBLISH_PROFILE
```

Paste the publish profile content.

---

## 🧱 Step 10 – Create Workflow File

Inside project create:

```
.github/workflows
```

Create file:

```
deploy.yml
```

---

## 🧱 Step 11 – Workflow YAML (master)

```yaml
name: Deploy Flask to Azure from Master

on:
  push:
    branches:
      - master

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Deploy
        uses: azure/webapps-deploy@v3
        with:
          app-name: "your-app-name"
          publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

Replace **your-app-name** with the Azure Web App name.

---

## 🧱 Step 12 – Configure Startup Command in Azure

Azure → Web App → Settings → Configuration → General Settings.

Startup command:

```
gunicorn --bind=0.0.0.0 --timeout 600 app:app
```

Save → Restart.

---

## 🧱 Step 13 – Commit and Push Workflow

```bash
git add .
git commit -m "added CI/CD"
git push origin master
```

---

## 🧱 Step 14 – If Push Fails (OAuth workflow error)

Error example:

```
refusing to allow an OAuth App to create or update workflow without workflow scope
```

This means your Personal Access Token (PAT) does not have **workflow** permission.

---

## 🧱 Step 15 – Create PAT with Workflow Scope

GitHub → Profile → Settings → Developer settings → Personal access tokens → Tokens (classic) → Generate new token.

Select:

- repo
- workflow ⭐

Generate and copy the token.

---

## 🧱 Step 16 – Remove Old Credentials (Windows)

Control Panel → Credential Manager → Windows Credentials.

Delete entries related to:

```
github.com
```

---

## 🧱 Step 17 – Push Again Using New Token

```bash
git push origin master
```

Username → GitHub username  
Password → paste PAT

---

## 🧱 Step 18 – Watch Deployment

GitHub → Actions → open running workflow.

After success → open Azure URL.

---

## 🔁 Daily Usage

After any code change:

```bash
git add .
git commit -m "update"
git push origin master
```

---

## 🧠 What You Learned

- Flask deployment
- Production server (gunicorn)
- CI/CD
- Cloud hosting
- Secret management
- DevOps pipeline

---

## 🆘 Common Mistakes

❌ Wrong branch name  
❌ Forgot startup command  
❌ Secret not added  
❌ Old GitHub credentials cached  
❌ Wrong app name in YAML

---

Happy Deploying 🚀


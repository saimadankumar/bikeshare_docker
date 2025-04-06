
## 🚲 **BikeShare Prediction System**

This project sets up a CI/CD pipeline for a BikeShare prediction model using **GitHub Actions** and **Docker**. It includes model training, testing, package building, Dockerizing, and FastAPI deployment.

---

## 📂 **Project Structure**
```
├── bikeshare_model
│   ├── train_pipeline.py
│   ├── predict.py
├── bike_sharing_api
│   ├── app
│   │   └── main.py
├── requirements
│   ├── requirements.txt
│   ├── test_requirements.txt
├── .github
│   └── workflows
│       └── ci_cd.yaml
├── Dockerfile
├── README.md
├── setup.py
```

---

## 🏗️ **Setup Instructions**

### ✅ **1. Clone the Repository**
```bash
git clone https://github.com/<your-username>/BikeShare-CI.git
cd BikeShare-CI
```

---

### ✅ **2. Setup Virtual Environment**
```bash
python -m venv .venv
source .venv/bin/activate   # For Linux/macOS
# .\.venv\Scripts\activate   # For Windows
```

---

### ✅ **3. Install Dependencies**
```bash
pip install -r requirements/requirements.txt
```

---

### ✅ **4. Train Model**
```bash
python bikeshare_model/train_pipeline.py
```

---

### ✅ **5. Test Model**
```bash
pip install -r requirements/test_requirements.txt
pytest
```

---

### ✅ **6. Build Package**
```bash
pip install build
python -m build
mv dist/*.whl .
```

---

### ✅ **7. Start FastAPI App**
```bash
pip install -r bike_sharing_api/requirements.txt
python bike_sharing_api/app/main.py
```
- Open the app: 👉 [http://localhost:8000/docs](http://localhost:8000/docs)

---

## 🚀 **GitHub Actions Setup**

### ✅ **1. Create `.github/workflows/ci_cd.yaml`**
Create a GitHub Actions workflow to automate the CI/CD steps:

```yaml
name: CI/CD Pipeline

on:
  push:
    branches:
      - main

jobs:
  build-train-test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v3

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.12'

      - name: Install Dependencies
        run: pip install -r requirements/requirements.txt

      - name: Train Model
        run: python bikeshare_model/train_pipeline.py

      - name: Install Test Dependencies
        run: pip install -r requirements/test_requirements.txt

      - name: Test Model
        run: pytest

      - name: Install Build Dependencies
        run: pip install build

      - name: Build Package
        run: |
          python -m build
          mv dist/*.whl .

      - name: Install FastAPI Dependencies
        run: pip install -r bike_sharing_api/requirements.txt

      - name: Start FastAPI (Test Only)
        run: python bike_sharing_api/app/main.py &

  docker-build-push:
    needs: build-train-test
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v3

       - name: Set up QEMU
        uses: docker/setup-qemu-action@v2

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Log in to Docker Hub
        uses: docker/login-action@v3
        with:
          username: 'sneh1919'
          password: ${{ secrets.DOCKER_TOKEN }}

      - name: Build and push
        uses: docker/build-push-action@v4
        with:
          push: true
          tags: sneh1919/bikeshare:latest 
```

---

## 🐳 **Docker Setup**

### ✅ **1. Create a Dockerfile**
Create a `Dockerfile` at the root of your project:

```dockerfile
# Use official Python image
FROM python:3.12-slim

# Set working directory
WORKDIR /app

# Copy project files
COPY . .

# Install dependencies
RUN pip install --no-cache-dir -r requirements/requirements.txt
RUN pip install --no-cache-dir -r requirements/test_requirements.txt
RUN pip install --no-cache-dir build

# Train the model
RUN python bikeshare_model/train_pipeline.py

# Run tests
RUN pytest

# Build the package
RUN python -m build && \
    mv dist/*.whl .

# Install FastAPI dependencies
RUN pip install --no-cache-dir -r bike_sharing_api/requirements.txt

# Expose port for FastAPI
EXPOSE 8000

# Start FastAPI app
CMD ["python", "bike_sharing_api/app/main.py"]
```

---

### ✅ **2. Build Docker Image**
```bash
docker build -t bikeshare-api .
```

---

### ✅ **3. Run Docker Container**
```bash
docker run -d -p 8000:8000 bikeshare-api
```

---

### ✅ **4. Test FastAPI App in Docker**
👉 [http://localhost:8000/docs](http://localhost:8000/docs)

---

## 🔐 **Add DockerHub Secrets to GitHub**
1. Go to **Settings → Secrets → Actions** in your GitHub repository  
2. Add the following secrets:
   - `DOCKER_USERNAME` → Your DockerHub username  
   - `DOCKER_PASSWORD` → Your DockerHub access token  

---

## 🔎 **Troubleshooting**
✅ **Permission Error:** Use `chmod +x` to make files executable.  
✅ **Port Already in Use:** Kill existing process:
```bash
kill -9 $(lsof -t -i:8000)
```
✅ **Container Fails to Start:** Check logs:
```bash
docker logs <container_id>
```

---

## 🎯 **Next Steps**
- ✅ Automate workflow with **GitHub Actions**  
- ✅ Push Docker image to **DockerHub**  
- ✅ Monitor app logs and performance  

---

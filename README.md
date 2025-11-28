[![CI](https://github.com/ainhoupna/MLOPs-Lab2/actions/workflows/CICD.yml/badge.svg)](https://github.com/ainhoupna/MLOPs-Lab2/actions/workflows/CICD.yml)


# MLOps-Lab1: Initial Image Classification Pipeline

This repository contains the foundational structure for a Deep Learning Image Classification project, developed as part of an MLOps assignment. This initial lab focuses on establishing a robust **Continuous Integration (CI) pipeline** using GitHub Actions, supported by automated testing, linting, and code formatting.

## Project Functionality (Lab 1)

The application provides a module for basic image preprocessing and a simulated prediction mechanism.

| Component | Functionality | Implementation |
| :--- | :--- | :--- |
| **Logic (`mylib/`)** | Image Resizing, Grayscale Conversion, Rotation, and **Randomized Class Prediction** (to be replaced by a real model in subsequent labs). | Python (PIL/Pillow) |
| **CLI (`cli/`)** | Command-line interface to execute core functions (`predict`, `resize`). | Click |
| **API (`api/`)** | RESTful microservice exposing endpoints for prediction and transformations (`/resize`, `/grayscale`, `/rotate`). | FastAPI |

## Development and CI Setup

The entire CI process is managed via the **`Makefile`**, which defines targets for consistent local and remote execution.

### Local Development Commands

Ensure your virtual environment is active before running any commands.

| Command | Action | Tools Used |
| :--- | :--- | :--- |
| `make install` | Installs all project and development dependencies. | `uv` |
| `make format` | Automatically formats all Python code. | `black` |
| `make lint` | Checks code quality and style compliance. | `pylint` |
| `make test` | **Runs all unit and integration tests.** | `pytest`, `pytest-cov` |
| `make all` | Runs `install`, `format`, `lint`, and `test` in sequence. | N/A |

### CI Pipeline (`.github/workflows/ci.yml`)

The CI pipeline runs the `make install`, `make format`, `make lint`, and `make test` targets on every push and pull request to ensure that only tested and styled code is merged into the `main` branch.

## Docker Deployment (Lab 2)

This project includes a multi-stage Dockerfile for containerizing the FastAPI application.

### Prerequisites

Verify Docker is installed and running:

```bash
docker --version
# Should show Docker version 20.10 or higher
```

### Docker Commands

The Makefile includes convenient Docker targets:

| Command | Action |
| :--- | :--- |
| `make docker-build` | Builds the Docker image tagged as `mlops-lab2:latest` |
| `make docker-run` | Runs the container in detached mode, exposing port 8000 |
| `make docker-stop` | Stops and removes the running container |
| `make docker-clean` | Stops container and removes the Docker image |
| `make docker-logs` | Displays container logs |
| `make docker-test` | Tests API endpoints (requires container to be running) |

### Quick Start with Docker

1. **Build the Docker image:**
   ```bash
   make docker-build
   ```

2. **Run the container:**
   ```bash
   make docker-run
   ```

3. **Access the application:**
   - Open your browser to `http://localhost:8000`
   - The FastAPI interactive docs are at `http://localhost:8000/docs`

4. **Test the API:**
   ```bash
   make docker-test
   ```

5. **View logs:**
   ```bash
   make docker-logs
   ```

6. **Stop the container:**
   ```bash
   make docker-stop
   ```

### Manual Docker Commands

If you prefer to use Docker commands directly:

```bash
# Build
docker build -t mlops-lab2:latest .

# Run
docker run -d -p 8000:8000 --name mlops-lab2-container mlops-lab2:latest

# Stop and remove
docker stop mlops-lab2-container
docker rm mlops-lab2-container
```

### Testing the API Endpoints

Once the container is running, you can test the endpoints:

```bash
# Test home page
curl http://localhost:8000/

# Test prediction endpoint
curl -X POST -F "file=@image.jpg" http://localhost:8000/predict

# Test resize endpoint
curl -X POST -F "file=@image.jpg" -F "width=200" -F "height=200" \
  http://localhost:8000/resize --output resized.jpg

# Test grayscale endpoint
curl -X POST -F "file=@image.jpg" http://localhost:8000/grayscale \
  --output grayscale.jpg

# Test rotate endpoint
curl -X POST -F "file=@image.jpg" -F "degrees=90" \
  http://localhost:8000/rotate --output rotated.jpg
```

### Troubleshooting

**Port already in use:**
```bash
# Find and stop the process using port 8000
sudo lsof -i :8000
# Or use a different port
docker run -d -p 8080:8000 --name mlops-lab2-container mlops-lab2:latest
```

**Container won't start:**
```bash
# Check logs for errors
docker logs mlops-lab2-container
```

**Permission denied:**
```bash
# Ensure your user is in the docker group
groups
# If 'docker' is not listed, add yourself:
sudo usermod -aG docker $USER
# Then log out and log back in
```

## GitHub Actions Automation

This project includes automated Docker image building and publishing to Docker Hub via GitHub Actions.

### Prerequisites

1. **Docker Hub Account**: You need a Docker Hub account (e.g., `ainhoupna`)
2. **Docker Hub Personal Access Token**: Required for GitHub Actions authentication
3. **GitHub Repository Secrets**: Store credentials securely

### Setup Instructions

#### Step 1: Create Docker Hub Personal Access Token

1. Go to [Docker Hub](https://hub.docker.com/) and log in
2. Navigate to **Account Settings** → **Security** → **Personal Access Tokens**
3. Click **Generate New Token**
4. Configure:
   - **Description**: `GitHub Actions MLOps Lab2`
   - **Permissions**: Select **Read, Write, Delete**
   - **Expiration**: Choose your preferred duration
5. Click **Generate** and **copy the token immediately**

#### Step 2: Add GitHub Secrets

1. Go to your GitHub repository settings
2. Navigate to **Secrets and variables** → **Actions**
3. Add two repository secrets:
   - **DOCKERHUB_USERNAME**: Your Docker Hub username (e.g., `ainhoupna`)
   - **DOCKERHUB_TOKEN**: The token you generated in Step 1

#### Step 3: Workflow Configuration

The workflow is defined in [`.github/workflows/docker.yml`](file:///home/alumno/Desktop/datos/MLOPS/MLOPs-Lab2/.github/workflows/docker.yml) and will:
- Trigger on every push to `master` branch
- Build the Docker image using Docker Buildx
- Push to Docker Hub as `<username>/mlops-lab2:latest`
- Tag images with commit SHA for versioning

#### Step 4: Test the Automation

```bash
# Commit and push the workflow
git add .github/workflows/docker.yml
git commit -m "Add Docker Hub automation"
git push origin master
```

Then check:
- GitHub repository → **Actions** tab to see the workflow running
- Docker Hub to verify the image was pushed

### Manual Docker Hub Push

If you need to manually push to Docker Hub:

```bash
# Login to Docker Hub
docker login

# Tag the image
docker tag mlops-lab2:latest <your-username>/mlops-lab2:latest

# Push to Docker Hub
docker push <your-username>/mlops-lab2:latest
```

### Workflow Features

- ✅ Automatic builds on every push
- ✅ Docker layer caching for faster builds
- ✅ Multi-tag support (latest + commit SHA)
- ✅ Secure credential management via GitHub Secrets
- ✅ Build logs available in GitHub Actions

For detailed setup instructions, see [DOCKER_HUB_SETUP.md](file:///home/alumno/Desktop/datos/MLOPS/MLOPs-Lab2/DOCKER_HUB_SETUP.md).



# Azure DevOps Container Pipeline — Node.js

A sample Azure DevOps CI pipeline that automatically builds a Node.js application into a Docker container image and pushes it to **Azure Container Registry (ACR)** on every commit to `master`.

---

## Overview

This repository demonstrates a minimal, end-to-end CI pipeline for containerizing a Node.js application using Azure DevOps. It is intended as a reference or starting point for teams looking to automate their container image builds and push workflows on Azure.

```
Code Push → Azure DevOps Pipeline → Docker Build → Push to ACR
```

---

## Repository Structure

```
azuredevops-container-pipeline-nodejs/
├── azure-pieplines.yml   # Azure DevOps pipeline definition
├── Dockerfile            # Container image instructions
├── index.js              # Node.js application entry point
├── package.json          # Node.js dependencies and scripts
├── package-lock.json     # Dependency lock file
└── .gitignore
```

---

## Pipeline — `azure-pieplines.yml`

The pipeline is triggered on any push to the `master` branch and runs on a Microsoft-hosted Ubuntu agent.

```yaml
trigger:
  - master

pool:
  vmImage: ubuntu-latest

steps:
  - task: Docker@2
    displayName: Build and Push
    inputs:
      command: buildAndPush
      containerRegistry: 'mainacr'
      repository: 'nodejs'
      dockerfile: '**/Dockerfile'
      tags: |
        $(Build.BuildId)
        latest
```

### What it does

| Step | Detail |
|---|---|
| **Trigger** | Runs automatically on every push to `master` |
| **Agent** | `ubuntu-latest` (Microsoft-hosted) |
| **Task** | `Docker@2` — builds and pushes in a single step |
| **Registry** | `mainacr` — an Azure Container Registry service connection |
| **Repository** | `nodejs` (image name within ACR) |
| **Tags** | `$(Build.BuildId)` (unique per build) and `latest` |

---

## Dockerfile

```dockerfile
FROM node:20

WORKDIR /app

COPY . .

RUN npm install

CMD ["npm", "start"]
```

The image is based on the official **Node.js 20** image. It copies the application source, installs dependencies, and starts the app using `npm start`.

---

## Prerequisites

Before using this pipeline, ensure you have the following in place:

- An **Azure DevOps** organization and project
- An **Azure Container Registry** instance
- A **Docker Registry service connection** named `mainacr` configured in your Azure DevOps project settings (Project Settings → Service connections)
- The pipeline file (`azure-pieplines.yml`) imported or detected in your Azure DevOps pipeline configuration

---

## Getting Started

1. **Fork or clone** this repository into your own Azure DevOps project.

2. **Create an ACR service connection** in Azure DevOps:
   - Go to *Project Settings → Service connections → New service connection*
   - Select **Docker Registry**, then choose **Azure Container Registry**
   - Name the connection `mainacr` (or update the pipeline YAML to match your chosen name)

3. **Create the pipeline** in Azure DevOps:
   - Go to *Pipelines → New Pipeline*
   - Select your repository and point it to `azure-pieplines.yml`

4. **Trigger a build** by pushing a commit to `master`. The pipeline will build the Docker image and push it to your ACR with two tags: the build ID and `latest`.

---

## Customization

| What to change | Where |
|---|---|
| ACR service connection name | `containerRegistry` in `azure-pieplines.yml` |
| Image/repository name in ACR | `repository` in `azure-pieplines.yml` |
| Node.js version | `FROM node:20` in `Dockerfile` |
| Trigger branch | `trigger` section in `azure-pieplines.yml` |
| Additional tags | `tags` block in `azure-pieplines.yml` |

---

## Tech Stack

- **Azure DevOps Pipelines** — CI orchestration
- **Docker** — containerization (`Docker@2` task)
- **Azure Container Registry** — private container image registry
- **Node.js 20** — application runtime

---

## License

This project is provided as a sample reference. Feel free to adapt it for your own projects.
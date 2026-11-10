# Kubernetes Concept Slides

A small collection of self-contained HTML slide decks explaining core Kubernetes
concepts. Each file is a standalone page (inline CSS, no build step, no external
assets) that you can open directly in a browser or serve as a static site.

The project also ships a `Dockerfile` so the slides can be packaged as an nginx
image and pushed to a private container registry.

## Contents

| File                          | Topic                                                        |
| ----------------------------- | ------------------------------------------------------------ |
| `configmaps-and-secrets.html` | ConfigMaps and Secrets — creation, usage in Pods, references |
| `namespace.html`              | Namespaces                                                   |
| `daemonset.html`              | DaemonSets                                                   |
| `stateful-vs-stateless.html`  | Stateful vs stateless workloads                              |

## Viewing locally

Just open any file in a browser:

```bash
xdg-open configmaps-and-secrets.html   # Linux
# or: open configmaps-and-secrets.html # macOS
```

## Running with Docker

The image is based on `nginx:1.27-alpine` and serves every `*.html` file as a
static site on port 80.

### Build

Run from the project root:

```bash
docker build -t k8s-slides:latest .
```

### Run locally

```bash
docker run --rm -p 8080:80 k8s-slides:latest
```

Then open: <http://localhost:8080/configmaps-and-secrets.html>

## Publishing to a private registry

```bash
# 1. Set your registry details (replace with your actual values)
export REGISTRY=registry.example.com        # your private registry host
export IMAGE=$REGISTRY/k8s-slides
export TAG=v1.0.0

# 2. Log in
docker login $REGISTRY

# 3. Build and tag
docker build -t $IMAGE:$TAG -t $IMAGE:latest .

# 4. Push
docker push $IMAGE:$TAG
docker push $IMAGE:latest
```

### Registry-specific login notes

- **AWS ECR**
  ```bash
  aws ecr create-repository --repository-name k8s-slides   # once
  aws ecr get-login-password --region <region> \
    | docker login --username AWS --password-stdin <acct>.dkr.ecr.<region>.amazonaws.com
  ```
- **Harbor / Nexus / GitLab / ACR** — the image name must include the
  project/namespace path, e.g. `registry.example.com/myproject/k8s-slides`.

### Multi-architecture build

If build and runtime CPU architectures differ (e.g. amd64 nodes, arm64 laptop):

```bash
docker buildx build --platform linux/amd64,linux/arm64 \
  -t $IMAGE:$TAG --push .
```

## Image details

- **Base image:** `nginx:1.27-alpine` (small footprint)
- **Web root:** `/usr/share/nginx/html/`
- **Exposed port:** `80`
- **Healthcheck:** requests `/configmaps-and-secrets.html` every 30s

## Project layout

```
.
├── configmaps-and-secrets.html
├── namespace.html
├── daemonset.html
├── stateful-vs-stateless.html
├── Dockerfile
├── .dockerignore
└── README.md
```

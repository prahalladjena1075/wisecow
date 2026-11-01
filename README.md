# Wisecow on Kubernetes

A small app that shows a cow with a random quote on port 4499.
It is containerized with Docker, deployed on Kubernetes, and built and deployed automatically with GitHub Actions.

## Files
- `Dockerfile`: builds the container image
- `k8s/deployment.yaml`: runs 2 copies of the app
- `k8s/service.yaml`: exposes the app inside the cluster
- `k8s/ingress.yaml`: HTTPS (TLS) entry point
- `.github/workflows/cicd.yml`: CI/CD pipeline

## Run with Docker
```bash
docker build -t wisecow .
docker run -p 4499:4499 wisecow
```
Open http://localhost:4499

## Deploy on Kubernetes
```bash
kubectl apply -f k8s/deployment.yaml -f k8s/service.yaml
```

## TLS
The Ingress uses a TLS Secret. Create a self-signed certificate:
```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=wisecow.local"
kubectl create secret tls wisecow-tls --cert=tls.crt --key=tls.key
kubectl apply -f k8s/ingress.yaml
```

## CI/CD
On every push to `main`, GitHub Actions:
1. Builds the image and pushes it to `ghcr.io/prahalladjena1075/wisecow`.
2. Deploys it to a temporary kind cluster, creates the TLS secret, and tests the Service.

For a permanent cluster, use a `KUBE_CONFIG` secret.

## Docker
docker build -t wisecow .
docker run -p 4499:4499 wisecow

## Kubernetes
kubectl apply -f k8s/

## TLS setup
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=wisecow.local"
kubectl create secret tls wisecow-tls --cert=tls.crt --key=tls.key

## CI/CD
GitHub Actions builds and pushes the image to ghcr.io on every push to main.

## Continuous Deployment
A deploy job can run kubectl apply -f k8s/ and kubectl set image using a KUBE_CONFIG secret. This needs a cloud cluster reachable from GitHub runners, so it is documented but not enabled.

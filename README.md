# Ingress-Demo-K8s
# Ingress Demo on Minikube

This repository contains a simple demo showing how to expose multiple services (`app1`, `app2`) using Kubernetes Ingress on Minikube.

## Prerequisites

- Minikube installed
- kubectl installed

## Steps to Run the Project

### 1. Start Minikube
```bash
minikube start --driver=docker
```

### 2. Enable Ingress Addon
```bash
minikube addons enable ingress
```

Wait until the Ingress controller pods are running:
```bash
kubectl get pods -n ingress-nginx
```

### 3. Deploy Applications
```bash
kubectl apply -f app1-deployment.yaml
kubectl apply -f app2-deployment.yaml
```

Deploy their services:
```bash
kubectl apply -f app1-service.yaml
kubectl apply -f app2-service.yaml
```

### 4. Create the Ingress Resource
```bash
kubectl apply -f ingress.yaml
```

Example `ingress.yaml`:
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: <minikube-ip>.nip.io
    http:
      paths:
      - path: /app1
        pathType: Prefix
        backend:
          service:
            name: app1
            port:
              number: 80
      - path: /app2
        pathType: Prefix
        backend:
          service:
            name: app2
            port:
              number: 80
```

> **Tip:** Replace `<minikube-ip>` with the IP you get from:
> ```bash
> minikube ip
> ```

### 5. Access the Applications

Add `nip.io` based domain to access:

- `http://<minikube-ip>.nip.io/app1`
- `http://<minikube-ip>.nip.io/app2`

Example:
```bash
curl http://192.168.49.2.nip.io/app1
curl http://192.168.49.2.nip.io/app2
```

### Troubleshooting

- If you get a 404 error from NGINX, check if the paths are configured correctly in Ingress and your application is expecting `/` instead of `/app1` or `/app2`.
- Always ensure `rewrite-target: /` is set if your apps expect root path `/`.
- Check Ingress controller logs if needed:
```bash
kubectl logs -n ingress-nginx deploy/ingress-nginx-controller
```

## References
- [Minikube Documentation](https://minikube.sigs.k8s.io/docs/)
- [Ingress NGINX Controller](https://kubernetes.github.io/ingress-nginx/)

---

Made with ❤️ for learning!


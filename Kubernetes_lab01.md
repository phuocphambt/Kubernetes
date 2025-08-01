- [Bước 1: Cài Docker](#bước-1-cài-docker)
- [Bước 2: Cài kubectl](#bước-2-cài-kubectl)
- [Bước 3: Cài Minikube](#bước-3-cài-minikube)
- [Bước 4: Khởi động Minikube](#bước-4-khởi-động-minikube)
- [Bước 5: Kiểm tra Cluster](#bước-5-kiểm-tra-cluster)
- [Bước 6: Tạo file nginx-deploy.yaml](#bước-6-tạo-file-nginx-deployyaml)
- [Bước 7: Tạo file gold-service.yaml](#bước-7-tạo-file-gold-serviceyaml)
- [Bước 8: Apply](#bước-8-apply)

## Bước 1: Cài Docker
``` bash
apt update && apt install -y docker.io
usermod -aG docker $USER
```
## Bước 2: Cài kubectl
``` bash
curl -LO "https://dl.k8s.io/release/$(curl -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl && mv kubectl /usr/local/bin/
kubectl version --client
```
## Bước 3: Cài Minikube
``` bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
chmod +x minikube-linux-amd64 && mv minikube-linux-amd64 /usr/local/bin/minikube
```
## Bước 4: Khởi động Minikube
``` bash
minikube start --driver=docker
```
``` bash
## Nếu lỗi CNI:
minikube start --driver=docker --cni=bridge
```
## Bước 5 : Kiểm tra Cluster
``` bash
kubectl get nodes
kubectl get pods -A
```
->  Trả về trạng thái Ready
## Bước 6 : Tạo file nginx-deploy.yaml
``` bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: gold-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```
## Bước 7: Tạo file gold-service.yaml
```bash
apiVersion: v1
kind: Service
metadata:
  name: gold-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 32080
```
## Bước 8:Apply
``` bash
kubectl apply -f nginx-deploy.yaml
kubectl apply -f gold-service.yaml
```

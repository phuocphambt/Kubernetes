# Mục lục

- [Kubernetes là gì ?](#kubernetes-là-gì-)
- [Thành phần chính trong Kubernetes là gì ?](#thành-phần-chính-trong-kubernetes-là-gì-)
- [Mô tả mô hình hoạt động](#mô-tả-mô-hình-hoạt-động-)
  - [Tình huống đặt ra](#tình-huống-đặt-ra)
  - [Các bước thực hiện](#các-bước-thực-hiện)
- [Ví dụ File YAML](#ví-dụ-file-yaml-)
  - [1. nginx-deploy.yaml](#1-nginx-deployyaml)
  - [2. nginx-service.yaml](#2-nginx-serviceyaml)

## Kubernetes là gì ?
``` bash
- Kubernetes là hệ thống quản lý containers (như Docker) theo cụm (cluster)
- Giúp chạy, scale, update và monitor ứng dụng.
```
## Thành phần chính trong Kubernetes là gì ?
``` bash
Thành phần chính trong Kubernetes bao gồm : Cluster, Pod, Devployment, Service, kubectl
```
``` bash
Giải thích thành phần :
- Cluster: Tập hợp các node hoạt động cùng nhau. Trong đó node chính gọi là master, các node còn lại gọi là worker.
- Pod: Là đơn vị nhỏ nhất trong K8s. Chứa 1 hoặc nhiều containers, tuy nhiên thường chỉ chạy 1 containers trên 1 pod.
- Deployment: Quản lý version, số lượng pod, và tự động scale.
## Dùng kubectl apply -f để tạo Deployment.
- Service: Giúp expose pod ra bên ngoài. Có 3 loại :
        **ClusterIP**: dùng nội bộ
        **NodePort**: mở cổng cho client bên ngoài truy cập
        **LoadBalancer**: dùng trên cloud
- kubectl: CLI quản lý K8s
```
## Mô tả mô hình hoạt động :
  1. Tình huốt đặt ra : 
``` bash
Có 1 ứng dụng web Nginx cần :
- 2 bản sao để đảm bảo không bị downtime
- Tự động khởi động lại nếu 1 bản lỗi
- Cổng để client bên ngoài truy cập
```
  2. Các bước thực hiện:
``` bash
Bước 1:
  Viết 1 file YAML mô tả:
    - Muốn chạy Nginx
    - Chạy 2 bản (replicas)
    - Mở cổng 80 ra ngoài
Bước 2:
  Dùng lệng kubectl apply -f nginx.yaml để gởi yêu cầu cho K8s.
Bước 3:
  K8s nhận yêu cầu và tiến hành điều phối :
    - Tạo 2 Pod chứa Nginx.
    - Chạy các Pod này trên các Node
    - Gán cổng bằng Service
Bước 4:
  Nếu có 1 Pod bị lỗi, K8s sễ tự tạo lại Pod mới giống hệt.
Bước 5:
```
```bash
Mô hình :
           nginx-deploy.yaml
                  ↓
          kubectl apply -f ...
                  ↓
          +---------------------+
          |     Kubernetes      |
          +---------------------+
             ↓      ↓      ↓
         Pod1   Pod2   Pod3 (tự tạo)
             ↓      ↓
         Node1   Node2 (máy ảo)
```
## Ví dụ File YAML :
       ## 1. nginx-deploy.yaml
```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
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
       ## 2. nginx-service.yaml
```bash
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30080

```

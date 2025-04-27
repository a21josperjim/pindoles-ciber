## Creació de un entorn de Kubernetes en una màquina Ubuntu 20.04

### Eines necessàries

Actualització del sistema
`sudo apt update && sudo apt upgrade -y`

Instal·lació de dependències
`sudo apt install -y apt-transport-https ca-certificates curl software-properties-common`

Instal·lació de docker 
```
sudo apt install -y docker.io
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
```
Instal·lació de kubectl
```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client --output=yaml
```

Instal·lació de minikube
```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
minikube version
```

### Creació d'un clúster insegur amb Apache
Iniciar el cluster
`minikube start --profile insecure-cluster`

Crear un arxiu **insecure-apache.yaml**
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: insecure-apache
spec:
  replicas: 1
  selector:
    matchLabels:
      app: insecure-apache
  template:
    metadata:
      labels:
        app: insecure-apache
    spec:
      containers:
      - name: apache
        image: httpd:latest
        securityContext:
          runAsUser: 0  # Ejecutar como root (¡MAL!)
        volumeMounts:
        - name: host-root
          mountPath: /host-root  # Montamos el filesystem del host
      volumes:
      - name: host-root
        hostPath:
          path: /  # ¡Montamos toda la raíz del host!
          type: Directory
---
apiVersion: v1
kind: Service
metadata:
  name: insecure-apache-service
spec:
  type: NodePort  # Expuesto en un puerto alto (30000-32767)
  selector:
    app: insecure-apache
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30080  # Puerto accesible desde fuera
```
Apliquem la configuració
`kubectl apply -f insecure-apache.yaml`

Verifiquem que funciona
`minikube service insecure-apache-service --url -p insecure-cluster`

### Creació d'un clúster segur amb Nginx
Iniciar el cluster
`minikube start --profile secure-cluster`

Crear un arxiu **secure-nginx.yaml**
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: secure-nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: secure-nginx
  template:
    metadata:
      labels:
        app: secure-nginx
    spec:
      securityContext:
        runAsNonRoot: true
        seccompProfile:
          type: RuntimeDefault
      containers:
      - name: nginx
        image: nginxinc/nginx-unprivileged:latest  # Imagen diseñada para ejecutarse como usuario no root
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: "500m"
            memory: "512Mi"
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop:
            - ALL
          readOnlyRootFilesystem: true
---
apiVersion: v1
kind: Service
metadata:
  name: secure-nginx-service
spec:
  type: nodePort
  selector:
    app: secure-nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30080
```
Apliquem la configuració
`kubectl apply -f secure-nginx.yaml`







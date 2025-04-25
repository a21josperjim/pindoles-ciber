## Creació de un entorn de Kubernetes en una màquina Ubuntu 20.04

### Eines necessàries

// Actualització del sistme
`sudo apt update && sudo apt upgrade -y`

// Instal·lació de docker 
```
sudo apt install -y docker.io
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
```

// Instal·lació de kubectl
```
sudo apt install -y apt-transport-https ca-certificates curl
curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-archive-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update
sudo apt install -y kubectl
```

// Instal·lació de minikube
```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

### Creació d'un clúster insegur amb Apache
// Iniciar el cluster
`minikube start --profile insecure-cluster`

// Configurar contexte
`kubectl config use-context insecure-cluster`

// Desactivar características de seguridad (esto hace el cluster inseguro)
```
kubectl apply -f - <<EOF
apiVersion: audit.k8s.io/v1
kind: Policy
rules:
- level: Metadata
EOF
```

// Crear namespace inseguro
`kubectl create namespace insecure-ns`

// Desactivar Network Policies
`kubectl label namespace insecure-ns network-policy-`

// Desplegar Apache con configuraciones inseguras
```
kubectl apply -f - <<EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: insecure-apache
  namespace: insecure-ns
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
      hostNetwork: true
      containers:
      - name: apache
        image: httpd:2.4
        ports:
        - containerPort: 80
        securityContext:
          privileged: true
          capabilities:
            add: ["NET_ADMIN", "SYS_ADMIN"]
        volumeMounts:
        - mountPath: /host
          name: host-root
      volumes:
      - name: host-root
        hostPath:
          path: /
EOF
```

//  Exponer el servicio de forma insegura
```
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: insecure-apache-svc
  namespace: insecure-ns
spec:
  type: NodePort
  ports:
  - port: 80
    nodePort: 30080
  selector:
    app: insecure-apache
EOF
```


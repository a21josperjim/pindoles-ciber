# Creación de Cluster Kubernetes

## Cluster Kubernetes  
Vamos a crear dos clusters de Kubernetes, uno con malas prácticas y otro con buenas prácticas.  
Veremos las diferencias de seguridad entre estos dos clusters.  

## Requisitos  
Para este tutorial hemos utilizado una máquina Ubuntu 20.04.  

### Instalación de AWS CLI  
```bash
sudo apt update && sudo apt upgrade -y
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip -y
unzip awscliv2.zip
sudo ./aws/install
```
:::warning
Este es un mensaje de advertencia importante.
:::
 
Verificación de la instalación:  
```bash
aws --version
```

Para el siguiente paso, necesitamos un usuario con una access key. Creamos un usuario (o utilizamos uno existente) y generamos una access key para acceder al CLI (el usuario necesita permisos adecuados).  

Configuración del CLI de AWS:  
```bash
aws configure
aws sts get-caller-identity
```

### Instalación de `eksctl`  
```bash
curl -sSL "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_Linux_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin/
eksctl version
```

### Instalación de `kubectl`  
```bash
curl -LO "https://dl.k8s.io/release/v1.29.0/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
kubectl version --client
```

---

## Cluster Inseguro  
Para este caso, desactivamos el `nodegroup` para evitar configuraciones seguras de AWS por defecto.  
```bash
eksctl create cluster --name=eks-inseguro --region=eu-west-1 --without-nodegroup
```
Verificamos en la consola de AWS que se haya creado correctamente.  

Ahora creamos manualmente un `nodegroup` con nuestras configuraciones de seguridad:  
```bash
eksctl create nodegroup --cluster=eks-inseguro \
  --name=ng-inseguro \
  --node-type=t3.medium \
  --nodes=2 \
  --nodes-min=1 \
  --nodes-max=3 \
  --asg-access \
  --full-ecr-access \
  --alb-ingress-access \
  --region=eu-west-1  
```

### Malas prácticas aquí:  
- Permisos IAM excesivos (`--full-ecr-access`, `--asg-access`, etc.).
- Nodos con acceso irrestricto a la API de AWS.
- No hay restricciones de red ni seguridad.  

Configuración de acceso público al API Server:  
```bash
aws eks update-cluster-config --region eu-west-1 --name eks-inseguro \
  --resources-vpc-config endpointPublicAccess=true,endpointPrivateAccess=false
```

### Malas prácticas aquí:  
- Cualquier persona en Internet puede intentar acceder al API Server.  

Otorgar permisos administrativos a todos los usuarios:  
```bash
kubectl create clusterrolebinding cluster-admin-binding \
  --clusterrole=cluster-admin --user=system:anonymous
```

### Malas prácticas aquí:  
- Esto permite que cualquiera tenga control total del cluster.  

---

## Cluster Seguro  
Creación del cluster con mejores prácticas:  
```bash
eksctl create cluster --name=eks-seguro --region=us-east-1 \
  --nodegroup-name=ng-seguro \
  --node-type=t3.medium \
  --nodes=2 \
  --nodes-min=1 \
  --nodes-max=3 \
  --region=eu-west-1 \
  --managed
```

Configuración segura del API Server:  
```bash
aws eks update-cluster-config --region eu-west-1 --name eks-seguro \
  --resources-vpc-config endpointPublicAccess=false,endpointPrivateAccess=true
```

---

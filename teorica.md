## NARRATIVA PILDORES
### Introducció
Kubernetes s'ha convertit en un estàndard en la computació en el núvol a causa de la seva capacitat per a orquestrar contenidors de manera eficient. Permet desplegar, escalar i administrar aplicacions de manera automatizada en entorns distribuïts.

Aquest projecte analitzarà les vulnerabilitats i riscos associats amb Kubernetes, explorant com els atacants poden comprometre la infraestructura i quines mesures de seguretat han d'implementar-se per a mitigar aquests riscos. 

### Què és Docker i per a què serveix?
Docker és una plataforma que permet crear, empaquetar i executar aplicacions dins de contenidors.

Contenidor: És un entorn aïllat que inclou tot el necessari per executar una aplicació (codi, biblioteques, dependències, etc.), sense necessitat de virtualitzar un sistema operatiu complet.

Per què s’utilitza?

  - Portabilitat: Els contenidors funcionen igual en qualsevol entorn (desenvolupament, producció, etc.).
  - Eficiència: Consumeixen menys recursos que màquines virtuals.
  - Aïllament: Cada aplicació funciona de manera independent.

### Què és Kubernetes?
Kubernetes (K8s) és un orquestrador de contenidors que automatitza el desplegament, escalat i gestió d’aplicacions en contenidors.

Per què s’utilitza?

  - Gestiona múltiples contenidors en diferents màquines (nodes).
  - Escala aplicacions automàticament segons la demanda.
  - Garanteix alta disponibilitat (si un contenidor falla, en llança un altre).



### Què és kubectl?
kubectl és l’eina de línia d’ordres (CLI) per interactuar amb un cluster de Kubernetes.

Funcions principals:

  - Desplegar aplicacions.
  - Veure l’estat dels pods.
  - Gestionar recursos del cluster.

### Què és Minikube?
Minikube és una eina que permet executar un cluster de Kubernetes localment en una única màquina (ideal per proves i desenvolupament).

Característiques:

  - Simula un entorn real de Kubernetes sense necessitat de múltiples servidors.
  - Funciona amb un sol node (no és per producció).

### Què és un clúster i per a què serveix?
Un cluster de Kubernetes és un conjunt de nodes (màquines físiques o virtuals) que treballen junts per executar aplicacions en contenidors.

Components principals:

  - Nodes Worker: On s’executen els contenidors (pods).
  - Master Node: Controla i gestiona el cluster (planificador, API, etc.).

Per què s’utilitza?

  - Distribueix càrrega entre múltiples servidors.
  - Garanteix tolerància a fallades (si un node cau, els contenidors es mouen a un altre).


### Què és un Pod?
Un Pod és la unitat més petita en Kubernetes i pot contenir un o més contenidors que comparteixen recursos.

Característiques:

  - Cada Pod té una única adreça IP
  - Els contenidors dins del Pod comparteixen xarxa i emmagatzematge.
  - Són efímers (es poden eliminar i recrear dinàmicament).


### Què és un Deployment?
Un Deployment és un objecte de Kubernetes que gestiona el cicle de vida dels Pods, garantint que sempre hi hagi un nombre desitjat en execució.

Funcions principals:

  - Desplega i actualitza aplicacions sense temps d’inactivitat.
  - Escala automàticament els Pods.
  - Torna a una versió anterior si hi ha errors.


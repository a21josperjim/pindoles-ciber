## NARRATIVA PILDORES

Introducció
En aquest anàlisi, explorarem els riscos de seguretat que poden sorgir en un cluster de Kubernetes quan no està configurat correctament. Una mala configuració pot tenir conseqüències greus, com:

Exposició de recursos crítics a usuaris o sistemes no autoritzats.

Accés no permès a dades sensibles o serveis interns.

Fuita d’informació a causa de controls d’accés insuficients.

Aquest escenari ens permetrà entendre la importància d’una configuració robusta en Kubernetes, així com les millors pràctiques per evitar vulnerabilitats. A través d’aquest estudi, veurem com petits errors de configuració poden obrir portes a atacs i com mitigar-los eficientment.

El nostre objectiu és conscienciar sobre la seguretat en entorns Kubernetes i destacar la necessitat d’auditar i reforçar les configuracions per protegir els sistemes davant amenaces potencials.


### Males pràctiques

**Introducció**
  - Presentació del tema: Males pràctiques de Kubernetes
  - La importància d'una bona configuració.

**Configuracions incorrectes**
  - Creació d’un clúster insegur 
  - Configuracions en el nodegroup insegures
  - Conseqüències d’aquesta mala configuració

**Configuracions correctes**
  - Creació d’un clúster segur
  - Com configurar un nodegroup segur.
  - Comprovació de les configuracions

**Demostració**
  - Atac al clúster insegur
  - Atac al clúster segur
  - Diferències entre els dos escenaris

**Conclusions**
  - Conclusions dels dos escenaris
  - Recomanacions

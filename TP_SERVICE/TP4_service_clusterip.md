# Exercice

Dans cet exercice, vous allez créer deux Deployments et deux service pour exposer à l'intérieur du cluster et à l'exterieur du cluster en utilisant des Service de type *ClusterIP* et *NodePort*

## Prepa 

> Creer un répertoire TP_SERVICE

## 1. BACKEND


### 1.1 Ressource Déployment

Créez une ressource définissant un Deployment ayant les propriétés suivantes:

- nom: *backend-deploy*
- label associé au Pod: *app: myphp-backend* (ce label est à spécifier dans les metadatas du Pod)
- replica : 2
- nom du container: *backend*
- labels
    - *app: myphp-backend*
    - *projet: myphp*
- image du container: *bilbloke/backend:1.0*


### 1.3. Ressource service de type ClusterIP

Créez une ressource définissant un service ayant les caractéristiques suivantes:
- nom: *backend*
- label: 
    - *app: myphp-backend*
    - *projet: myphp*
- type: *ClusterIP*
- un selector permettant le groupement des Pods ayant le label *app: myphp-backend*.
- exposition du port *80* dans le cluster
- forward des requètes vers le port *80* des Pods sous-jacents

### 1.4 Application des ressources

La commande suivante permet de créer le deployment

```
$ kubectl apply -f 
```

### 1.5. Accès au Service depuis le cluster

- Créer un fichier *client_pod.yaml* définissant le Pod dont la spécification est la suivante:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: debug
  labels: 
    projet: myphp
spec:
  containers:
  - name: debug
    image: alpine:3.9
    command:
    - "sleep"
    - "10000"
```

Nous allons utiliser ce Pod pour accèder au Service *backend* depuis l'intérieur du cluster. Ce Pod contient un seul container, basé sur alpine et qui est lancé avec la commande `sleep 10000`. Ce container sera donc en attente pendant 10000 secondes. Nous pourrons alors lancer un shell intéractif à l'intérieur de celui-ci et tester la communication avec le Service *backend*.

- Lancez le Pod avec *kubectl*.

```kubectl apply -f client_pod.yaml```

- Lancez un shell intéractif *sh* dans le container *debug* du Pod.

```kubectl exec -it debug -- sh```

- Installer l'utilitaire *curl*

le container *debug* du Pod du même nom est basé sur l'image *alpine:3.9* qui ne contient pas l'utilitaire *curl* par défaut. Il faut donc l'installer avec la commande suivante:

```
/ # apk update && apk add curl
```

- Utilisez *curl* pour envoyer une requête HTTP Get sur le port *80* du service *myphp_backend*.
Vous devriez obtenir le contenu, sous forme textuel, de la page *index.php* servie par défaut par *myphp_backend*.

```
curl http://backend/index.php
```

Ceci montre que depuis le cluster, si l'on accède au Service *backend* la requête est bien envoyée à l'un des Pods (nous en avons créé un seul ici) regroupé par le Service (via la clé *selector*).

### 1.6. Utilisation ClusterIP + port-forward => accès extérieur temporaire

Accèder au service backend depuis l'extérieur temporairement à l'aide de port-forward :

```kubectl port-forward svc/backend 8080:80```

### 1.7. Visualisation de la ressource de type service 

A l'aide de `kubectl get`, visualisez la *spécification* du service *backend*.

```
kubectl get service backend
kubectl get service backend -o yaml
```


### 1.8. Détails du service

A l'aide de *kubectl describe*, listez les détails du service *backend*

Notez l'existence d'une entrée dans *Endpoints*, celle-ci correspond à l'IP du Pod qui est utilisé par le Service.

Note: si plusieurs Pods avaient le label *app: backend*, il y aurait une entrée Endpoint pour chacun d'entre eux.

```kubectl describe service backend```


## FRONTEND

## 2. Création d'un fichier de spec backend

- Créer un fichier de spec : deploy-frontend.yaml


### 2.1 Ressource Déployment

Créez définissant un Deployment ayant les propriétés suivantes:

- nom: *frontend-deploy*
- label associé au Pod: *app: myphp-frontend* (ce label est à spécifier dans les metadatas du Pod)
- replica : 2
- nom du container: *frontend*
- labels
    - *app: myphp-frontend*
    - *projet: myphp*
- image du container: *bilbloke/frontend:1.0*


### 2.2 Ressource service de type NodePort

Créez une ressource définissant un service ayant les caractéristiques suivantes:
- nom: *myphp_frontend*
- label: 
    - *app: myphp-frontend*
    - *projet: myphp*
- type: *NodePort*
- un selector permettant le groupement des Pods ayant le label *app: myphp-frontend*.
- exposition du port *80* dans le cluster
- forward des requètes vers le port *80* des Pods sous-jacents
- NodePort: 31500

### 2.3 Application des ressources

La commande suivante permet de créer le deployment

```
$ kubectl apply -f 
```

### 2.4 Accès au Service depuis le cluster

> Accèder à l'appli depuis un navigateur : http://IP_INTERNAL:31500/index.php
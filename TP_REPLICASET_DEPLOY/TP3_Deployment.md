# Découverte des ressources replicaSets et Deployments


## Exercice

L'objectif du TP est de convertir la déclaration de spec du pod multipod en un deployment avec 2 replicats

### 1. Création de la spécification

Créer un fichier **multi-deployment.yml** définissant un déploiement avec les spec suivantes :

- nom du deploiement : *multi-deploy*
- label du deploiement : app: multi-deploy
- replicats : 2
- matchLabels : app: multi-deploy
- pods : labels: app: multi-deploy


### 2. Validation préalable

Réaliser une preview ou validation du fichier de spec

```bash
$ kubectl apply -f TP_REPLICASET_DEPLOY/multi-deployment.yml --validate=true --dry-run=true
```

### 3. Appliquer le déploiement

```bash
kubectl apply -f TP_REPLICASET_DEPLOY/multi-deployment.yml
```

### 4. Demander un port forward

- On peut demander un port forward sur les ressources : Pod, ReplicaSet, Deployment et Service !

```bash
$ kubectl port-forward deployment.apps/multi-deploy 8080:80
$ kubectl port-forward replicaset.apps/multi-deploy-548846f94f 8080:80
```


### 5. commandes de check et visu

> Filter avec label :

```bash
$ kubectl get all -l app=multi-deploy
```
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


### 3. Appliquer le déploiement


### 4. commandes de check et visu
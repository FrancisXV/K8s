Dans cet exercice vous aller persister des données dans un volume de type hostpath sur un node du cluster

> https://kubernetes.io/fr/docs/concepts/storage/volumes/#hostpath

> https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/

## 1. Ajout d'un label sur l'un des nodes du cluster

Sur l'un de vos node, posez le label type=database

        ```bash
        $ kubectl label nodes minikube-m03 type=database
        ```

## 2. Création d'un namespace dédié à l'appli mongodb

Créez le namespace mongodb

```bash
$ kubectl create namespace mongodb --dry-run=client -o yaml > ns-mongodb.yaml
$ kubectl apply -f ns-mongodb.yaml
```

## 3. Création d'un répertoire (FS) sur le même node

Sur le même node nouvellement labellisé type=database, créer un répertoire /node-mongodb

```
$ minikube ssh -n NODE-NAME "sudo mkdir /node-mongodb"
minikube ssh -n NODE-NAME "sudo chown docker:docker /node-mongodb"
```

## 4. Création d'un pod mongodb avec volume hostpath

Créer fichier de spécification définissant un Pod ayant les propriétés suivantes:

- nom: mongodb
- label associé : *app: mongodb*
- namespace associé : *mongodb*
- nodeSelector: *type: database*
- nom du container: *mongodb*
- image: *mongo:4.4*
- volumeMount : *mountPath: /data/db*
- volume: *hostPath*, *path: /node-mongodb*

## 5. Lancement du Pod

Lancer la création du Pod

## 6. Visualiser les data mongo dans le hostPath du node 

```$ minikube ssh -n NODE-NAME ls  /node-mongodb```

## 7. Persistence des données

> Si le pod est détruit, les données de la base sont toujours présentes dans le hostPath



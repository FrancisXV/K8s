# Créer un déploiement MARIADB avec demande PVC dynamique


## 1. Créer un namespace mariadb

```bash
$ kubectl create namespace mariadb
```


## 2. Créer une PVC 

- Lister les storages class dispo

```bash
$ kubectl get sc
```

- Créer une PVC appelant la storage class **standard**

- Penser au namespace

- Choix espace et accessmode


```bash
$ kubectl -n mariadb get pvc
$ kubectl get pv
```

## 3. Creation d'un secret pour le mot de la base

> https://kubernetes.io/fr/docs/concepts/configuration/secret/

```bash
$ kubectl apply -f sct-mariadb.yaml
$ kubectl get -n mariadb secret
$ kubectl describe -n mariadb secret mariadb-secret
```

> Visible via le dashboard

## 3. Deploiement mariadb

- Creer un deploiement
    - utiliser le secret pour le présenter en tant que variable d'environnement dans le conteneur
    - volume: faire réference à la pvc precedemment créée
    - dans le conteneur : mountPath => /var/lib/mysql

- Ex:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mariadb-deployment
  labels:
    app: mariadb
    namespace: mariadb
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mariadb
  template:
    metadata:
      labels:
        app: mariadb
    spec:
      containers:
        - name: mariadb
          image: mariadb:10.6
          env:
            - name: MARIADB_ROOT_PASSWORD
              valueFrom:
```


- Application

```bash
$ kubectl apply -f deploy-mariadb.yaml
$ kubectl -n mariadb describe pod mariadb-77dc7c8649-lxwhv
$ kubectl logs -n mariadb deployment.apps/mariadb
```

- Check pvc -> pv

```bash
$ kubectl get pvc -n mariadb
$ kubectl get pv
$ kubectl describe pv pvc-3839fc07-a702-4939-b0f7-bbabc6dda024
```
# Créer un déploiement MARIADB avec demande PVC dynamique


## 1. Créer un namespace mariadb


## 2. Créer une PVC 

- Lister les storages class dispo

```bash
$ kubectl get sc
```

- Créer une PVC appelant la storage class **standard**

- Choix espace et accessmode

## 3. Deploiement mariadb

- Creer un deploiement
    - volume: faire réference à la pvc precedemment créée
    - dans le conteneur : mountPath => /var/lib/mysql


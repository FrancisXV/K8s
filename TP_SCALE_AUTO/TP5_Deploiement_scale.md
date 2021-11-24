# TP Deploiement avec controles ressources et metrics

## Deploiement http (apache)

- Créer un fichier de spec pour réaliser un déploiement :

    - name: apache
    - replicas : 1
    - label
    - conteneur : apache
    - image: httpd:2.4-alpine
    - resources : requests -> cpu 10m

    > https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/


## Autoscale horizontal

> https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/

> https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/

- Générer le fichier de spec :

```bash
$ kubectl autoscale deployment apache --cpu-percent=11 --min=1 --max=10 --dry-run=client -o yaml > hpa-apache.yaml
```
# K8S_112021

## Installation maquette

> https://kubernetes.io/fr/docs/tasks/tools/install-kubectl/

> https://kubernetes.io/fr/docs/setup/learning-environment/minikube/

## Commandes de bases

- Vérification du cluster

    ```bash
    $ kubectl cluster-info
    $ kubectl get nodes
    $ kubectl get nodes -o wide
    ```

## Client kubectl

- Binaire qui envoie des specs (creation de ressources, suppressions, modification, interrogation) aux clusters K8S

- Notion de cluster et contextes dans un fichier de config : ~/.kube/config

- https://kubernetes.io/fr/docs/reference/kubectl/cheatsheet/

- Commandes de config et check 

    ```bash
    $ kubectl config --help
    $ kubectl config set-context .....
    ```

- Outils tiers :

    - https://github.com/ahmetb/kubectx (gérer les contextes et namespaces Kubernetes)
    - direnv (loader automatiquement un fichier d'environnement : .envrc


- Auto-completion à mettre en place


    > https://kubernetes.io/docs/tasks/tools/included/optional-kubectl-configs-zsh/
    > https://kubernetes.io/docs/tasks/tools/included/optional-kubectl-configs-bash-linux/

- Convention d'utilisation

    > https://kubernetes.io/fr/docs/reference/kubectl/conventions/


## Tableau de bord

- dashboard

    > https://kubernetes.io/fr/docs/tasks/access-application-cluster/web-ui-dashboard/

    - 1. Déploiement des ressources

        ```bash
        $ kubectl apply -f DASHBOARD/recommended.yaml
        ```

    - 2. Création accès utilisateur

           > https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md

           ```bash
           $ kubectl apply -f DASHBOARD/dashboard-adminuser.yaml
           ```

    - 3. Port-forward sur le service : (temporaire)

        ```bash
        $ kubectl -n kubernetes-dashboard port-forward service/kubernetes-dashboard 8080:443
        ```

    > Minikube : $ minikube dashboard
- portainer


## Outillage

- Kube-linter

    > https://docs.kubelinter.io/#/

```bash
$ ./kube-linter lint TP_REPLICASET_DEPLOY/deploy_demo_nginx.yaml
```

- Mise en place des hooks (pre-commit)

    > https://pre-commit.com/hooks.html

    > https://eoli3n.eu.org/2021/06/02/git-pre-commit.html



## Metrics server

- Installation des ressources metrics serveur (préquis pour scale auto)

> https://github.com/kubernetes-sigs/metrics-server

  - 1. Download du yaml
  - 2. Custom : /!\ : Sur un cluster Minikube, il manque une options dans le fichier yaml au niveau du container metrics-server -> --kubelet-insecure-tls (à ajouter après ligne 135)
  - 3. APPLY
  - 4. Contrôle déploiement :

    ```bash
    $ kubectl get all -n kube-system -l k8s-app=metrics-server
    $ kubectl top nodes
    ```

- MINIKUBE :

```bash
$ minikube addons enable metrics-server
```

## Les ressources du cluster

- Lister les ressources dans mon cluster 

```bash
$ kubectl api-resources
```

### POD

> https://kubernetes.io/fr/docs/concepts/workloads/pods/pod/

> https://kubernetes.io/fr/docs/tasks/configure-pod-container/quality-service-pod/

> https://kubernetes.io/fr/docs/concepts/workloads/pods/pod-lifecycle/

- Création du premier POD :

   ```$ kubectl run nginx --image nginx```

- Vérification de la ressource Pod : 

   ```$ kubectl get pods```

- Inspection de la resource pod nginx :

  ```$ kubectl describe pods nginx```

- Logs d'un POD :

   ```$ kubectl logs nginx```

- Exécution d'une commande dans un conteneur du pod :

   ```$ kubectl exec nginx -- curl http://localhost```

- Se connecter dans un conteneur du POD :

    ```bash
    $ kubectl exec -it nginx -- bash
    $ kubectl exec -it nginx -- sh
    ```

- Forwarder un port pour tester l'appli depuis la machine client kubernetes => solution temporaire

    ```
    $ kubectl port-forward nginx 8080:80
    ```

- Exposer un port du conteneur ver sun port node hôte : expose (=> création de ressource service)

    ```
    $ kubectl expose pods nginx --type=NodePort --port 80 --target-port 80
    $ kubectl get all
    $ kubectl get service
    ```

- Suppression des ressources :

    ```bash
    $ kubectl delete pod/nginx service/nginx
    ```

- Générer un fichier de spec yaml d'un POD :

    ```bash
    $ kubectl run nginx --image=nginx:1.19-alpine --dry-run=client -o yaml > ex_spec_pod_nginx.yaml
    ```

- Appliquer un fichier de spec : (créer la ressource, ou l'updater)

    ```bash
    $ kubectl apply -f TP_POD/ex_spec_pod_nginx.yaml
    ```

### ReplicaSet

> https://kubernetes.io/fr/docs/concepts/workloads/controllers/replicaset/

- Kubernetes préconise de directement créer un deployment qui prend en charge le replicaset

### Deployment

> https://kubernetes.io/fr/docs/concepts/workloads/controllers/deployment/#cr%C3%A9ation-dun-d%C3%A9ploiement

- Mise à l'échelle (scaling)

  - Manuel via dashboard

  - Manuel via client kubectl :
  ```bash
  $ kubectl scale -n default deployment backend-deploy --replicas=3
  ```


- Upgrade/rollback

    - Stratégie RollingUpdate
        - 1. Ajout de config dans le fichier de spec
        - 2 possibilités : 
            - modifier la version d'image dans le fichier de spec puis ```kubectl apply```
            - en ligne de commande : ```kubectl set image deployment.apps/frontend-deploy frontend=bilbloke/frontend:2.0```
                - /!\ repercuter la nouvelle d'image dans le fichier si c'est ok


- Historique update

    ```bash
    $ kubectl rollout history deployment.apps/frontend-deploy
    ```

- Rollback

    ```bash
    $ kubectl rollout undo deployment.apps/frontend-deploy
    ```

- Modifier le change cause de la révision actuelle :

   ```bash
   $ kubectl annotate deployment.apps/frontend-deploy kubernetes.io/change-cause="Image frontend:1.0"
   ```

## Statefulset

> https://kubernetes.io/fr/docs/concepts/workloads/controllers/statefulset/

- Ex: 
    - https://kubernetes.io/docs/tasks/run-application/run-replicated-stateful-application/

## Daemonset

> https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/

### Service 

> https://kubernetes.io/fr/docs/concepts/services-networking/service/


### Affinity

- Vous pouvez restreindre un pod afin qu'il ne puisse s'exécuter que sur un ensemble particulier de nœuds. 
- Il existe plusieurs façons de procéder et les approches recommandées utilisent toutes des **selector** et **labels** pour faciliter la sélection.

> https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/

```bash
$ kubectl get pods -o wide
$ kubectl get pod -o=custom-columns=NODE:.spec.nodeName,NAME:.metadata.name
```

## Namespaces

> https://kubernetes.io/fr/docs/concepts/overview/working-with-objects/namespaces/

- Isolation des ressources (pas réseau)
- Ajouter des quotas de ressource dans un namespace
- 

```bash
$ kubectl get namespaces
$ kubectl get all -n kubernetes-dashboard
$ kubectl get all --all-namespaces
```

- Changer le namespace dans son contexte client kubetcl 

   ```bash
   $ kubectl config set-context --current --namespace=default
   ```

- /!\ : suppression d'un namespace = suppression de toutes les ressources du namespace


## ConfigMap

> https://kubernetes.io/docs/concepts/configuration/configmap/

> https://kubernetes.io/fr/docs/tasks/configure-pod-container/configure-pod-configmap/

### Secrets

> https://kubernetes.io/fr/docs/concepts/configuration/secret/


## Volumes

> https://kubernetes.io/fr/docs/concepts/storage/volumes/

> https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/


## Probes

- Sondes applicatives

    - Liveness : redémarrage (K8S) en cas de sonde KO (destruction conteneur) => perte de donnée
    - Readiness : sortie du POD du service réseau : plus de traffic possible sur le POD

> https://kubernetes.io/fr/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/

- EX : Cf TP_SERVICE/deploy-backend.yaml

```yaml
livenessProbe:
  httpGet:
    path: /index.php
    port: 80
  initialDelaySeconds: 5
  periodSeconds: 10
readinessProbe:
  httpGet:
    path: /index.php
    port: 80
  initialDelaySeconds: 5
  periodSeconds: 2
```

## RBAC

> https://kubernetes.io/docs/reference/access-authn-authz/rbac/



## HELM

- gestionnaire de "paquets" : **charts** dédié à K8S
- simplifier la génération des manifests yaml
- versionning : updates/rollback
- charts : stack de fichiers manifests

> https://helm.sh/
> https://helm.sh/fr/docs/intro/using_helm/
> https://artifacthub.io/
> https://helm.sh/docs/helm/helm_completion/

- Binaire indépendant de K8S
    - Il faut disposer d'une configuration kubectl pour se connecter à un cluster
    - helm utilise kubectl et le fichier ~/.kube/config

- Version:

```bash
$ helm version
```

- Config :
    - AJout un repo qui contient des packages helm

    ```bash
    $ helm repo add grafana https://grafana.github.io/helm-charts
    ```

- Liste repo configure 

```bash
$ helm repo list
```

- Chercher un package (charts)

```bash
$ helm search repo grafana
```

- Installer un package

```bash
$ helm install my-grafana grafana/grafana
```

- Visu des déploiement helm

```bash
$ helm list
$ helm status my-grafana 
```


- Installation d'un chart avec customisation des variables :
    - On peut utiliser un fichier exterieur au chart :

    ```bash
    $ helm install -f helm-grafana-values.yaml my-grafana grafana/grafana
    ```

- Créer son propre chart

```bash
$ helm create tpchart
```

- Déploiement Portainer HELM

    ```bash
    $ helm repo add portainer https://portainer.github.io/k8s
    $ helm install --create-namespace -n portainer portainer portainer/portainer
    ```

    - Si besoin on peut faire un port-forward sur le deployment pour tester:

        ```bash
        $ kubectl port-forward -n portainer deployment.apps/portainer 8085:9000
        ```

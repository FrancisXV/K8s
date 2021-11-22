# K8S_112021

## Installation maquette

> https://kubernetes.io/fr/docs/tasks/tools/install-kubectl/

> https://kubernetes.io/fr/docs/setup/learning-environment/minikube/

## Commandes de bases

- Vérification du cluster

    ```bash
    $ kubectl cluster-info
    $ kubectl get nodes
    ```

## Client kubectl

- Binaire qui envoie des specs (creation de ressources, suppressions, modification, interrogation) aux clusters K8S

- Notion de cluster et contextes dans un fichier de config : ~/.kube/config

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


## Les ressources du cluster

### POD

- Création du premier POD :

   ```$ kubectl run nginx --image nginx```

- Vérification de la ressource Pod : 

   ```$ kubectl get pods```

- Inspection de la resource pod nginx :

  ```$ kubectl describe pods nginx```
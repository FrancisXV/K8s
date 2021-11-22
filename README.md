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
    $ kubectl set-context .....
    ```

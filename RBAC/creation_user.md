## Cas d’utilisation

L'objectif de ce mode opératoire est de générer une paire certificat/key pour un utilisateur et de l'enregistrer dans le cluster.
Une fois l'utilisateur connu du cluster, celui-ci peu alors demander des actions, seulement celles-ci risquent d'être refusées...

### 1. Création des crédentials *user*

- Création de la clé privée pour un user :

  ```$ openssl genrsa -out cert/formation.key 2048```

- Création d'une demande de signature certificat :

  > /!\ Le CN représente lenom de l'utilisateur qui sera utilisé par le client kubectl et associé aux roles dans le cluster

    ```$ openssl req -new -key cert/formation.key -out cert/formation.csr -subj "/CN=formation/O=kube2021"```

- Signature du certificat avec l'autorité de certification du cluster K8S : (ex Minikube)

  ```$ openssl x509 -req -in cert/formation.csr -CA ~/.minikube/ca.crt -CAkey ~/.minikube/ca.key -CAcreateserial -out cert/formation.crt -days 500```

### 2. Création du user **formation** dans *kubeconfig*

- Ajout d'un credential user avec ces informations les certificat et clé :

  ```$ kubectl config set-credentials formation --client-certificate=cert/formation.crt --client-key=cert/formation.key```

- Ajout d'un nouveau context **formation** dans *kubeconfig*

  ```$ kubectl config set-context formation-context --cluster=minikube --user=formation```

- Visualisation :

  ```
  $ kubectl config get-users
  $ kubectl config view
  ```


- Changement de *context* vers user formation-context :

  ```bash
  $ kubectl config use-context formation-context
  $ kubectl config current-context
  ```

- Test opérations sur le cluster ?:

  ```bash
  $ kubectl get nodes
  $ kubectl get pods
  $ kubectl auth can-i list pods
  ```

> Notre nouvel user est connu du cluster via certificat/key, cependant aucune assocation vers un rôle ne lui permet d'être autorisé à exécuter aucune action.
# Création d'un Pod multi conteneurs avec partage volume

## Exercice

Dans cet exercice, vous allez créer changer la spécification pour lancer un  Pod avec deux conteneurs de type différents et un volume partagé

### 1. Supprimer les ressources avec le fichier de spec

```bash
$ kubectl delete -f multipod.yaml
```


### 2. Modifier le fichier de spec multipod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multipod
spec:
  containers:
    - name: nginx
      image: nginx:1.18-alpine
      volumeMounts:
        - name: partage-multipod
          mountPath: /usr/share/nginx/html
  initContainers:
    - name: debian
      image: debian:buster-slim
      command: ["/bin/bash"]
      args: ["-c", "echo Message depuis debian satellite > /partage/index.html"]
      volumeMounts:
        - name: partage-multipod
          mountPath: /partage
  volumes:
    - name: partage-multipod
      emptyDir: {}
```

### 4. Appliquer le fichier de spec multipod.yaml

```bash
$ kubectl apply -f multipod.yaml
```


### 5. Observer les ressources et leur status

```bash
$ kubectl get pods
$ kubectl describe pod multipod
```

> On voit via la prise en compte initCOntainer par le POD, si l'exécution du conteneur init se termine correctement alors le POD instancie le conteneur appli

### 6. Via port-forward afficher la page web
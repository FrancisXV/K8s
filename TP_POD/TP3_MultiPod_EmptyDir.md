# Création d'un Pod multi conteneurs avec partage volume

## Exercice

Dans cet exercice, vous allez créer changer la spécification pour lancer un  Pod avec deux conteneurs et un volume partagé

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
$ kubectl apply -f multipod
```

### 5. Observer les ressources et leur status

```bash
$ kubectl get pods
```

> Le conteneur  debian est redémarré 3X, le status passe en CrashLoopBackOff

> Puis encore tentative

> UN POD fait tout maintenir ses conteneurs UP

### 6. Via port-forward afficher la page web

```bash
kubectl port-forward multipod 8080:80
```
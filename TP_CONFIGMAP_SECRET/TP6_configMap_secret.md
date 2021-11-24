# TP configMap secret


- Utilisation d'un ConfigMap pour la configuration d'un reverse proxy

## 1. Context

- Dans cette mise en pratique nous allons voir l'utilisation de l'objet ConfigMap pour fournir un fichier de configuration à un reverse proxy très simple que nous allons baser sur *nginx*.

Nous allons configurer ce proxy de façon à ce que les requètes reçues sur le endpoint  soient forwardées sur un service nommé *php-service*, tournant également dans le cluster. Ce service expose le pod php

## 2. Deploiement php

- 1. Deploiement php

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: php
  labels:
    app: php
    projet: npm
spec:
  replicas: 1
  selector:
    matchLabels:
      app: php
  template:
    volumes:
    - name: php-volume
      emptyDir: {}
    initContainers:
    - name: side-car
      image: busybox
      volumeMounts:
      - name: php-volume
        mountPath: /php_code
      command:
      - wget
      - "-O"
      - "/php_code/index.php"
      - https://raw.githubusercontent.com/psable/php/main/myphp.php
    containers:
    - name: php
      image: phpdockerio/php73-fpm
      volumeMounts:
      - name: php-volume
        mountPath: /srv/http
```

- 2. Deploiement service php-service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: php-service
  labels:
    projet: npm
    app: php
spec:
  type: ClusterIP
  selector:
    app: php
    projet: npm
  ports:
  - protocol: TCP
    port: 9000
    targetPort: 9000
```

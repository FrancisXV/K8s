Le but de cet exercice est de créer un role *RBAC* permettant à un utilisateur d'acceder à un namespace du cluster en mode *lecture* pour les ressources *pod*

> Source : https://kubernetes.io/docs/reference/access-authn-authz/rbac/

## 1. Créer un role

Créer un fichier role-formation.yml définissant un role  ayant les propriétés suivantes:

- namespace: default
- name: pod-watch
- rules
  - resources: ["pods"]
  - verbs: ["get","watch","list"]

## 2. Appliquer le role

Appliquer le role

## 3. Créer un roleBinding

Créer un fichier rolebinding-formation.yml définissant un roleBinding  ayant les propriétés suivantes:

- name: read-pods
- namespace: default
- subjects
  - kind: user
  - name: formation
- roleRef:
  - kind: Role
  - name: pod-reader

## 4. Appliquer le roleBinding

Appliquer le role

## 5. Tester les requêtes au cluster en tant que user **formation**

Modifier la config kubectl pour changer d'utilisateur.

> Context

Tester ensuite une requete de type get
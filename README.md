# ChatApp - Déploiement avec Docker, Kubernetes et Jenkins

Ce projet consiste en une application de chat construite avec **Node.js** pour le backend et **React** pour le frontend, déployée sur un **cluster Kubernetes** avec une base de données **MongoDB**. Nous utilisons **Docker** pour la containerisation des services, **Kubernetes** pour l'orchestration, et **Jenkins** pour automatiser le processus de build et de déploiement.

### Structure du projet

Le projet se compose de plusieurs services :

- **Frontend** : Une application React qui sert d'interface utilisateur.
- **Backend** : Un serveur Node.js qui gère la logique du chat.
- **Base de données MongoDB** : Utilisée pour stocker les messages et les utilisateurs.

### Étapes de déploiement

#### 1. **Dockerisation des services**

Nous avons créé des images Docker pour chaque partie du projet :

- **Backend (Node.js)** : Un `Dockerfile` est utilisé pour installer les dépendances et exécuter l'application. L'image est construite en utilisant `node:20` comme base.
- **Frontend (React)** : Nous utilisons un `Dockerfile multi-étapes` pour créer l'application React, puis nous la servons avec un serveur **Nginx** dans un second conteneur. 
- **MongoDB** : Nous utilisons l'image officielle de MongoDB.

Les fichiers Docker nécessaires pour la création des conteneurs sont les suivants :

- `Dockerfile` (Backend et Frontend)
- `docker-compose.yml` (Utilisé pour le développement local)
- `deployment-a.yml` (Déploiement Kubernetes pour le backend, frontend, et MongoDB)
- `configmap-a.yml` (Configuration de l'environnement)

#### 2. **Déploiement sur Kubernetes**

Le déploiement de l'application se fait sur un cluster **Kubernetes**. Nous avons créé des fichiers de manifeste Kubernetes pour gérer les déploiements, services, volumes persistants, etc. Les étapes suivantes sont prises en charge :

1. **Namespace** : Un namespace `chat` est créé pour isoler les ressources liées à cette application.
2. **Déploiement du Frontend** : Nous déployons l'application React sur un pod Kubernetes. Ce service expose le port 3000 pour accéder à l'interface utilisateur.
3. **Déploiement du Backend** : Le backend est déployé dans un autre pod. Ce service expose le port 5000 et dépend de MongoDB.
4. **Déploiement de MongoDB** : MongoDB est déployé avec un volume persistant pour garantir la conservation des données.
5. **Services Kubernetes** : Des services sont créés pour exposer les différents pods (frontend, backend, et mongo).

#### 3. **Pipeline Jenkins**

Un **pipeline Jenkins** est configuré pour automatiser le processus de build, push et déploiement de l'application.

Le pipeline se divise en plusieurs étapes :

1. **Checkout from SCM** : Récupère le code source depuis le dépôt GitHub.
2. **Build and Push to Registry** : 
    - Construit l'image Docker pour le backend à partir du `Dockerfile` situé dans le dossier `backend`.
    - Pousse l'image Docker vers **Docker Hub** avec un tag spécifique.
3. **Deploy to Kubernetes** : 
    - Configure le fichier `kubeconfig` pour interagir avec le cluster Kubernetes.
    - Applique les fichiers de déploiement Kubernetes (comme `configmap-a.yaml` et `deployment-a.yaml`) pour déployer les services.
    - Redémarre le déploiement Kubernetes si nécessaire pour appliquer les changements.
4. **Cleanup Artifacts** : Supprime les images Docker locales pour libérer de l'espace.

#### 4. **Commandes Kubernetes**

Lors du déploiement, les commandes suivantes sont utilisées :

```bash
kubectl apply -f backend/configmap-a.yaml --validate=false
kubectl apply -f backend/deployment-a.yaml
kubectl rollout restart deployment monimage

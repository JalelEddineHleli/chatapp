# Étape 1 : Utiliser une image de Node.js officielle comme base
FROM node:20 AS build

# Définir le répertoire de travail
WORKDIR /app

# Copier le package.json et package-lock.json pour installer les dépendances
COPY package*.json ./

# Installer les dépendances
RUN npm install

# Copier tous les fichiers du projet dans le conteneur
COPY . .

# Générer la build de l'application React (production)
RUN npm run build

# Étape 2 : Utiliser une image Nginx pour servir l'application React
FROM nginx:alpine

# Copier la build générée par React vers le répertoire de Nginx
COPY --from=build /app/build /usr/share/nginx/html

# Exposer le port 80 (le port utilisé par Nginx par défaut)
EXPOSE 80

# Démarrer Nginx pour servir l'application
CMD ["nginx", "-g", "daemon off;"]

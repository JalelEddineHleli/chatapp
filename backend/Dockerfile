# Étape 1 : Utiliser une image de Node.js officielle comme base
FROM node:20 AS build

# Définir le répertoire de travail
WORKDIR /app

# Copier les fichiers package.json et package-lock.json pour installer les dépendances
COPY package*.json ./

# Installer les dépendances
RUN npm install

# Copier tous les fichiers du projet dans le conteneur
COPY . .

# Exposer le port que le serveur écoute (par exemple, le port 5000)
EXPOSE 5000

# Démarrer l'application avec Nodemon pour le développement
CMD ["npm", "run", "dev"]

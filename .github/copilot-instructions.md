# Instructions IA pour L3MON

## Vue d'ensemble du projet

**L3MON** (Système avancé de gestion et de surveillance réseau de couche 3) est une plateforme de gestion et de surveillance réseau basée sur Node.js, offrant une interface web, la surveillance de la bande passante, le suivi de la santé des serveurs et des alertes personnalisables.

**Statut** : Développement précoce (pré-MVP). La structure du code existe mais la fonctionnalité principale n'est pas encore implémentée.

## Architecture

### Structure des répertoires
```
L3MON/
├── src/
│   ├── frontend/              # Composants UI web et routes
│   │   ├── index.js           # Point d'entrée/middleware frontend
│   │   ├── routes/            # Gestionnaires de routes
│   │   └── assets/            # Fichiers statiques (CSS, JS, images)
│   ├── monitors/              # Modules de surveillance (bande passante/serveurs)
│   └── index.js               # Logique application principale
├── index.js                   # Point d'entrée application (placeholder)
├── package.json               # Dépendances et scripts
└── .dockerfile                # Configuration déploiement Docker
```

### Services principaux
1. **Service Frontend** (`src/frontend/`) - Middleware Express/web servant l'UI sur le port 3000
2. **Système de surveillance** (`src/monitors/`) - Modules enfichables pour surveillance bande passante & serveurs
3. **Cœur backend** (`src/index.js`) - Coordonne les services et flux de données

### Flux de données
- Frontend → Backend : Requêtes HTTP vers `/routes/*`
- Backend → Monitors : Appels de polling/abonnement pour collecter les métriques
- Monitors → Backend : Retourne données brutes de surveillance
- Backend → Frontend : Réponses JSON avec métriques traitées

## Patterns et conventions de développement

### Conventions JavaScript
- **Pas de transpilation** - Écrire ES6+ directement (Node 20 supporté dans Docker)
- **Règle d'extension de fichier** : TOUS les fichiers modules SAUF `index.js` sont ignorés par git (voir `.gitignore`)
  - Seuls les fichiers `index.js` sont versionnés
  - Les fichiers non-index doivent toujours exister localement mais ne seront pas committés
  - Implication : Garder la logique principale dans les fichiers `index.js`, code supplémentaire dans des modules séparés si nécessaire

### Organisation des modules
- Utiliser des fichiers séparés pour chaque type de moniteur (ex: `BandwidthMonitor.js`, `ServerMonitor.js`) dans `src/monitors/`
- Les routes frontend doivent être des fichiers modulaires sous `src/frontend/routes/`
- Chaque module doit exporter une seule classe ou fonction factory

### Port et configuration
- L'application s'exécute sur **port 3000** (codé en dur dans Dockerfile)
- Pas de fichier config encore—les paramètres seront ajoutés
- Configuration basée sur l'environnement encouragée (NODE_ENV, chaînes de connexion BD)

## Dépendances critiques
- **Node.js** : v20+
- **Aucun package npm installé** - `npm install` réussit sans dépendances
- **Dépendances futures probables** : Express (framework web), client BD, bibliothèques de surveillance

## Build et déploiement

### Développement local
```sh
npm install        # Actuellement sans effet
node index.js      # Démarre l'application (actuellement se termine immédiatement)
```

### Déploiement Docker
- **Base** : Image Node 20 officielle
- **Build** : `docker build -t l3mon .`
- **Run** : `docker run -p 3000:3000 l3mon`
- Tous les fichiers application copiés dans le conteneur ; exécute `node index.js`

## Lacunes d'implémentation immédiates
- `index.js` est un placeholder ; initialisation serveur principale nécessaire
- `src/index.js` est vide ; nécessite logique application principale
- `src/frontend/` et `src/monitors/` n'ont pas d'implémentations
- Pas de couche BD/persistance
- Pas d'authentification/autorisation
- Pas d'implémentations de surveillance réelles

## Ce qu'il faut savoir avant de contribuer

1. **La structure placeholder est intentionnelle** - Les répertoires sont scaffoldisés pour développement futur
2. **Privilégier la modularité** - Chaque moniteur et route frontend doit être indépendamment testable
3. **Port 3000 est fixe** - Ne pas changer sans mettre à jour Dockerfile
4. **Pas de dépendances externes encore** - Garder minimal ; ajouter seulement si nécessaire
5. **Documenter les APIs moniteur** - Chaque module moniteur doit exporter une interface cohérente (start, stop, getMetrics)

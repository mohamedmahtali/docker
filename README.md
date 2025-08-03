# 🐳 Guide Complet des Commandes Docker

## 🧹 Nettoyage Complet de Docker

### Nettoyage Standard
```powershell
# Supprimer tous les conteneurs arrêtés, réseaux non utilisés, images et volumes
docker system prune -a --volumes -f

# Nettoyer le cache de construction
docker builder prune -a -f
```

### Types de Cache Docker

#### Cache de Construction (Build Cache)
```powershell
# Supprimer tout le cache de construction
docker builder prune -a -f

# Voir l'utilisation du cache de construction
docker builder du

# Supprimer le cache de construction avec confirmation
docker builder prune -a
```

#### Cache des Images
```powershell
# Supprimer les images non utilisées
docker image prune -f

# Supprimer toutes les images non utilisées (même celles avec des tags)
docker image prune -a -f

# Supprimer les images "dangling" (sans tag)
docker image prune -f
```

#### Cache des Conteneurs
```powershell
# Supprimer tous les conteneurs arrêtés
docker container prune -f

# Supprimer un conteneur spécifique
docker rm <container_id>

# Forcer la suppression d'un conteneur en cours
docker rm -f <container_id>
```

#### Cache des Volumes
```powershell
# Supprimer tous les volumes non utilisés
docker volume prune -f

# Supprimer tous les volumes (même ceux utilisés) - ATTENTION !
docker volume prune -a -f

# Supprimer un volume spécifique
docker volume rm <volume_name>
```

#### Cache des Réseaux
```powershell
# Supprimer tous les réseaux non utilisés
docker network prune -f

# Supprimer un réseau spécifique
docker network rm <network_name>
```

### Nettoyage Projet Spécifique (Onyx)
```powershell
# Arrêter et supprimer le projet Onyx avec ses volumes
docker compose -f "c:\Users\USER\Desktop\01_Projet_Perso\02-01-Himalayia\onyx\deployment\docker_compose\docker-compose.dev.yml" down -v

# Ou depuis le dossier du projet
cd "c:\Users\USER\Desktop\01_Projet_Perso\02-01-Himalayia\onyx\deployment\docker_compose"
docker compose down -v
```

### Nettoyage Forcé Complet
```powershell
# 1. Arrêter tous les conteneurs en cours
docker stop $(docker ps -q)

# 2. Supprimer tous les conteneurs
docker rm $(docker ps -aq)

# 3. Supprimer toutes les images
docker rmi $(docker images -q)

# 4. Supprimer tous les volumes
docker volume prune -a -f

# 5. Supprimer tous les réseaux
docker network prune -f

# 6. Nettoyage système final
docker system prune -a --volumes -f
```

### Nettoyage Sélectif
```powershell
# Supprimer les images de plus de 24h
docker image prune --filter "until=24h" -f

# Supprimer les conteneurs arrêtés depuis plus de 1h
docker container prune --filter "until=1h" -f

# Supprimer les volumes non utilisés (sans confirmation)
docker volume prune -f

# Supprimer seulement les images "dangling"
docker image prune -f

# Nettoyage par projet (basé sur les labels)
docker system prune --filter "label!=keep" -f
```

### Nettoyage Intelligent (Recommandé)
```powershell
# Étape 1: Diagnostic avant nettoyage
docker system df

# Étape 2: Nettoyage progressif des éléments les moins risqués
docker container prune -f
docker image prune -f
docker network prune -f

# Étape 3: Nettoyage du cache de construction si nécessaire
docker builder prune -f

# Étape 4: Nettoyage des volumes (ATTENTION - peut supprimer des données)
docker volume prune -f

# Étape 5: Vérification finale
docker system df
```

---

## 🚀 Commandes de Déploiement

### Démarrage du Projet Onyx
```powershell
# Depuis le dossier racine du projet
cd "c:\Users\USER\Desktop\01_Projet_Perso\02-01-Himalayia\onyx\deployment\docker_compose"

# Démarrage avec construction des images
docker compose -f docker-compose.dev.yml up --build

# Démarrage en arrière-plan
docker compose -f docker-compose.dev.yml up -d

# Démarrage avec logs en temps réel
docker compose -f docker-compose.dev.yml up --build --no-cache
```

### Redémarrage de Services Spécifiques
```powershell
# Redémarrer un service spécifique
docker compose -f docker-compose.dev.yml restart api_server

# Redémarrer la base de données
docker compose -f docker-compose.dev.yml restart relational_db

# Redémarrer tous les services
docker compose -f docker-compose.dev.yml restart
```

---

## 📊 Surveillance et Diagnostic

### État des Conteneurs
```powershell
# Voir tous les conteneurs en cours
docker ps

# Voir tous les conteneurs (y compris arrêtés)
docker ps -a

# Voir l'état du projet Onyx
docker compose -f docker-compose.dev.yml ps
```

### Logs et Debugging
```powershell
# Logs de tous les services
docker compose -f docker-compose.dev.yml logs

# Logs d'un service spécifique
docker compose -f docker-compose.dev.yml logs api_server

# Logs en temps réel
docker compose -f docker-compose.dev.yml logs -f

# Logs d'un conteneur spécifique
docker logs onyx-stack-api_server-1

# Logs avec horodatage
docker logs -t onyx-stack-api_server-1
```

### Inspection des Ressources
```powershell
# Utilisation de l'espace disque Docker
docker system df

# Utilisation détaillée de l'espace disque
docker system df -v

# Informations détaillées sur les volumes
docker volume ls

# Informations sur les réseaux
docker network ls

# Informations sur les images
docker images

# Statistiques en temps réel des conteneurs
docker stats
```

### Diagnostic du Cache Docker
```powershell
# Voir l'utilisation détaillée du cache de construction
docker builder du

# Historique des builds
docker builder ls

# Inspecter le cache de construction
docker buildx du

# Voir les couches d'une image
docker history <image_name>

# Analyser la taille des couches
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"

# Identifier les gros volumes
docker volume ls --format "table {{.Name}}\t{{.Driver}}" | head -20
```

---

## 🔧 Gestion des Services

### Accès aux Conteneurs
```powershell
# Accéder au shell d'un conteneur
docker exec -it onyx-stack-api_server-1 /bin/bash

# Exécuter une commande dans un conteneur
docker exec onyx-stack-api_server-1 ls -la

# Accéder à PostgreSQL
docker exec -it onyx-stack-relational_db-1 psql -U postgres -d onyx
```

### Gestion des Volumes
```powershell
# Lister tous les volumes
docker volume ls

# Inspecter un volume spécifique
docker volume inspect onyx-stack_db_volume

# Supprimer un volume spécifique
docker volume rm onyx-stack_db_volume

# Sauvegarder un volume
docker run --rm -v onyx-stack_db_volume:/data -v $(pwd):/backup alpine tar czf /backup/backup.tar.gz /data
```

---

## 🛠️ Maintenance et Optimisation

### Mise à Jour des Images
```powershell
# Télécharger les dernières versions des images
docker compose -f docker-compose.dev.yml pull

# Reconstruire les images
docker compose -f docker-compose.dev.yml build --no-cache

# Mise à jour complète
docker compose -f docker-compose.dev.yml down
docker compose -f docker-compose.dev.yml pull
docker compose -f docker-compose.dev.yml up -d
```

### Sauvegarde et Restauration
```powershell
# Sauvegarder la base de données
docker exec onyx-stack-relational_db-1 pg_dump -U postgres onyx > backup.sql

# Restaurer la base de données
docker exec -i onyx-stack-relational_db-1 psql -U postgres onyx < backup.sql

# Exporter un conteneur
docker export onyx-stack-api_server-1 > api_server_backup.tar

# Sauvegarder une image
docker save onyxdotapp/onyx-backend:latest > onyx_backend.tar
```

---

## 🚨 Dépannage

### Problèmes Courants

#### Conteneur qui ne démarre pas
```powershell
# Vérifier les logs d'erreur
docker logs onyx-stack-relational_db-1

# Vérifier la configuration
docker inspect onyx-stack-relational_db-1

# Redémarrer le conteneur
docker restart onyx-stack-relational_db-1
```

#### Problème de port occupé
```powershell
# Voir les ports utilisés
netstat -ano | findstr :3000
netstat -ano | findstr :5432
netstat -ano | findstr :8080

# Tuer le processus utilisant le port
taskkill /PID <PID> /F
```

#### Problème de Base de Données PostgreSQL
```powershell
# Vérifier si PostgreSQL est déjà installé localement
Get-Service -Name "*postgres*" -ErrorAction SilentlyContinue

# Arrêter PostgreSQL local s'il existe
Stop-Service -Name "postgresql*" -Force

# Vérifier les logs spécifiques de la DB
docker logs onyx-stack-relational_db-1 --tail 50

# Forcer la recréation du conteneur DB
docker compose -f docker-compose.dev.yml rm -f relational_db
docker compose -f docker-compose.dev.yml up -d relational_db

# Vérifier l'état de santé de la DB
docker exec onyx-stack-relational_db-1 pg_isready -U postgres

# Se connecter à la DB pour tester
docker exec -it onyx-stack-relational_db-1 psql -U postgres -c "\l"
```

#### Problème de permissions Windows sur les ports
```powershell
# Vérifier les ports exclus par Windows
netsh int ipv4 show excludedportrange protocol=tcp

# Redémarrer Docker Desktop (en tant qu'administrateur)
# Ou changer le port PostgreSQL dans docker-compose.dev.yml

# Solution temporaire : utiliser un autre port pour PostgreSQL
# Dans docker-compose.dev.yml, changer:
# ports:
#   - "5433:5432"  # au lieu de "5432:5432"
```

#### Problème d'espace disque
```powershell
# Vérifier l'utilisation
docker system df

# Nettoyage automatique
docker system prune -a --volumes -f

# Supprimer les images non taguées
docker rmi $(docker images -f "dangling=true" -q)
```

#### Problème de réseau
```powershell
# Lister les réseaux
docker network ls

# Inspecter le réseau du projet
docker network inspect onyx-stack_default

# Recréer le réseau
docker network rm onyx-stack_default
docker compose -f docker-compose.dev.yml up -d
```

---

## 📋 Commandes de Monitoring

### Surveillance Continue
```powershell
# Surveiller l'utilisation des ressources
docker stats --format "table {{.Container}}\t{{.CPUPerc}}\t{{.MemUsage}}\t{{.NetIO}}"

# Surveiller les logs en continu
docker compose -f docker-compose.dev.yml logs -f --tail=100

# Surveiller un service spécifique
docker compose -f docker-compose.dev.yml logs -f api_server
```

### Health Checks
```powershell
# Vérifier l'état de santé des conteneurs
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"

# Tester la connectivité
curl http://localhost:3000
curl http://localhost:8080/health

# Vérifier les dépendances
docker compose -f docker-compose.dev.yml config --services
```

---

## 🔑 Accès aux Services

### URLs d'Accès
- **Interface Web Onyx:** http://localhost:3000
- **API Backend:** http://localhost:8080
- **MinIO Console:** http://localhost:9001
- **Vespa:** http://localhost:8081

### Credentials par Défaut
- **PostgreSQL:** 
  - User: `postgres`
  - Password: (défini dans docker-compose.dev.yml)
  - Database: `onyx`
- **MinIO:**
  - Access Key: (défini dans docker-compose.dev.yml)
  - Secret Key: (défini dans docker-compose.dev.yml)

---

## 📝 Notes Importantes

1. **Toujours utiliser le chemin complet** vers `docker-compose.dev.yml` si vous n'êtes pas dans le bon dossier
2. **Sauvegarder vos données** avant un nettoyage complet avec `-v`
3. **Vérifier les ports** avant de démarrer (3000, 8080, 5432, etc.)
4. **Surveiller l'utilisation RAM** - Onyx nécessite au moins 10GB de RAM
5. **Attendre que tous les services** soient "healthy" avant d'utiliser l'application

---

*Généré le 3 août 2025 - Guide pour le projet Onyx*

# 🐳 Guide Complet des Commandes Docker

## 🧹 Nettoyage Complet de Docker

### Nettoyage Standard
```powershell
# Supprimer tous les conteneurs arrêtés, réseaux non utilisés, images et volumes
docker system prune -a --volumes -f

# Nettoyer le cache de construction
docker builder prune -a -f
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

# Informations détaillées sur les volumes
docker volume ls

# Informations sur les réseaux
docker network ls

# Informations sur les images
docker images

# Statistiques en temps réel des conteneurs
docker stats
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

# Tuer le processus utilisant le port
taskkill /PID <PID> /F
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

# Projet Zabbix avec Docker Compose

Ce projet configure un environnement complet de supervision Zabbix utilisant Docker Compose, incluant :
- Serveur Zabbix
- Interface Web Zabbix
- Base de données MySQL
- Agent Zabbix pour superviser l'hôte
- Grafana pour la visualisation avancée

## 📋 Prérequis

- Docker et Docker Compose installés
- Au moins 2 GB de RAM disponible
- Ports 80, 443, 3000, 3306, 10051 disponibles

## 🚀 Installation et Démarrage

### 1. Cloner ou télécharger le projet

```bash
git clone https://github.com/AbderrahimeEl/zabbix-vagrant
cd zabbix-vagrant
```

### 2. Configuration des variables d'environnement

Le fichier `.env` contient toutes les configurations nécessaires. Vous pouvez modifier les mots de passe :

```bash
# Éditer le fichier .env si nécessaire
nano .env
```

### 3. Démarrer les services

```bash
# Démarrer tous les services en arrière-plan
docker-compose up -d

# Vérifier que tous les conteneurs sont en cours d'exécution
docker-compose ps
```

### 4. Attendre l'initialisation

La première fois, l'initialisation peut prendre 2-3 minutes. Surveillez les logs :

```bash
# Suivre les logs de tous les services
docker-compose logs -f

# Ou suivre un service spécifique
docker-compose logs -f zabbix-server
```

## 🌐 Accès aux Interfaces

### Interface Web Zabbix
- **URL** : http://localhost:80 ou http://votre-ip:80
- **Utilisateur** : Admin
- **Mot de passe** : zabbix

### Grafana (optionnel)
- **URL** : http://localhost:3000
- **Utilisateur** : admin
- **Mot de passe** : admin123 (défini dans .env)

## ⚙️ Configuration Initiale de Zabbix

### 1. Première connexion à Zabbix

1. Ouvrez http://localhost:80
2. Connectez-vous avec Admin/zabbix
3. Changez le mot de passe par défaut

### 2. Vérifier l'agent Zabbix

1. Allez dans **Configuration** → **Hosts**
2. Vous devriez voir "Docker-Host" avec un statut vert
3. Si l'agent n'est pas visible, ajoutez-le manuellement :
   - Nom : Docker-Host
   - Groupes : Linux servers
   - Interfaces : Agent - 127.0.0.1:10050

### 3. Configurer la supervision de base

Le système inclut des templates par défaut pour :
- Supervision du système Linux
- Supervision Docker
- Métriques réseau et disque

## 📊 Métriques Supervisées

### Métriques Système
- CPU (utilisation, charge)
- Mémoire (RAM, swap)
- Disque (espace, I/O)
- Réseau (trafic, erreurs)
- Processus système

### Métriques Docker
- Conteneurs en cours d'exécution
- Utilisation des ressources par conteneur
- Statut des services

## 🔧 Commandes Utiles

### Gestion des services

```bash
# Arrêter tous les services
docker-compose down

# Redémarrer un service spécifique
docker-compose restart zabbix-server

# Voir les logs en temps réel
docker-compose logs -f

# Mettre à jour les images
docker-compose pull
docker-compose up -d
```

### Sauvegarde

```bash
# Sauvegarder la base de données
docker exec zabbix-mysql mysqldump -u root -p zabbix > backup_zabbix.sql

# Sauvegarder les volumes
docker run --rm -v zabbix-vagrant_mysql-data:/data -v $(pwd):/backup alpine tar czf /backup/mysql-backup.tar.gz /data
```

### Restauration

```bash
# Restaurer la base de données
docker exec -i zabbix-mysql mysql -u root -p zabbix < backup_zabbix.sql
```

## 🛠️ Dépannage

### Problèmes courants

1. **Les conteneurs ne démarrent pas**
   ```bash
   # Vérifier les logs
   docker-compose logs
   
   # Vérifier l'espace disque
   df -h
   ```

2. **L'interface web n'est pas accessible**
   ```bash
   # Vérifier que le port 80 n'est pas utilisé
   netstat -tlnp | grep :80
   
   # Redémarrer le service web
   docker-compose restart zabbix-web
   ```

3. **L'agent Zabbix n'apparaît pas**
   ```bash
   # Vérifier la connectivité
   docker exec zabbix-server zabbix_get -s zabbix-agent -k agent.ping
   ```

### Logs importants

```bash
# Logs du serveur Zabbix
docker-compose logs zabbix-server

# Logs de la base de données
docker-compose logs mysql-server

# Logs de l'interface web
docker-compose logs zabbix-web
```

## 📈 Intégration Grafana

### Configuration du plugin Zabbix dans Grafana

1. Connectez-vous à Grafana (http://localhost:3000)
2. Allez dans **Configuration** → **Plugins**
3. Activez le plugin "Zabbix"
4. Ajoutez une source de données Zabbix :
   - URL : http://zabbix-web:8080/api_jsonrpc.php
   - Utilisateur : Admin
   - Mot de passe : zabbix

## 🔒 Sécurité

### Recommandations de sécurité

1. **Changez tous les mots de passe par défaut**
2. **Configurez HTTPS** pour l'interface web
3. **Limitez l'accès réseau** aux ports nécessaires
4. **Activez l'authentification à deux facteurs** dans Zabbix
5. **Mettez à jour régulièrement** les images Docker

### Configuration HTTPS (optionnel)

Pour activer HTTPS, placez vos certificats dans le volume `zabbix-web-ssl` et modifiez la configuration Apache.

# Réponses aux questions des TP 
Arnaud VAN EEKCHOVEN - I2 : SE

## TP 1 : Docker

#### Question 1-1 : Pourquoi vaut-il mieux passer les variables d'environnement avec -e plutôt que dans le Dockerfile ?

Utiliser `-e` permet de :
- Gérer les secrets (mots de passe, etc.) de manière sécurisée
- Changer les paramètres sans reconstruire l'image
- Éviter d'exposer les identifiants dans le Dockerfile versionné

#### Question 1-2 : Pourquoi attacher un volume au container PostgreSQL ?

- Sans volume, les données sont supprimées à l'arrêt du container
- Le volume permet de stocker les données de façon persistante sur la machine hôte
- Essentiel pour une base de données réelle et durable

#### Question 1-3 : Dockerfile et commandes essentielles pour la base de données

**Dockerfile** :
```Dockerfile
FROM postgres:17.2-alpine

COPY init/ /docker-entrypoint-initdb.d/
```

**Commandes** :
```bash
docker build -t my-database ./database
docker network create app-network
docker run -d --name postgres-db --network app-network -v pgdata:/var/lib/postgresql/data my-database
```

#### Question 1-4 : Pourquoi faire un build multi-stage ?

- La première étape compile le code Java (JDK)
- La deuxième étape exécute uniquement l'application (JRE léger)
- L'image finale est plus légère, rapide et sécurisée

#### Question 1-5 :Pourquoi a-t-on besoin d’un reverse proxy ?

- Sert de point d’entrée unique vers plusieurs services
- Permet la terminaison SSL, le load balancing, etc.
- Sépare proprement les couches frontend et backend

#### Question 1-6 : Pourquoi `docker-compose` est utile ?

- Orchestration facile de plusieurs containers
- Une seule commande pour tout lancer ou arrêter
- Idéal pour le développement et les environnements de production

#### Question 1-7 : Commandes `docker-compose` importantes

```bash
docker-compose up
docker-compose down
docker-compose build
docker-compose logs -f
```

#### Question 1-8 : Structure du fichier `docker-compose.yml`

```yaml
services:
  backend:      # Ici le backend
    build: ./backend
    depends_on:
      - database
    networks:
      - frontend-net
      - backend-net

  database:     # Ici la base de donnée
    build: ./database
    volumes:
      - pgdata:/var/lib/postgresql/data
    networks:
      - backend-net

  httpd:       # Ici le front-end
    build: ./httpd
    ports:
      - "80:80"
    depends_on:
      - backend
    networks:
      - frontend-net

networks:
  frontend-net:
  backend-net:

volumes:
  pgdata:
```

#### Question 1-9 : Commandes de publication sur DockerHub

```bash
docker login
docker tag my-database NOMUTILISATEUR/my-database:1.0
docker push NOMUTILISATEUR/my-database:1.0
```


#### Question 1-10 : Pourquoi publier ses images Docker ?

- Pour partager avec d'autres développeurs
- Pour les utiliser en CI/CD ou en production
- Pour automatiser les déploiements


## TP 2 : GitHub Actions : Pipeline CI/CD

#### Question 2-1 : Que sont les Testcontainers ?

- Bibliothèques Java permettant de lancer des containers Docker pendant les tests
- Fournissent une base PostgreSQL temporaire pour les tests d’intégration
- Très pratique pour tester dans un environnement proche de la production


#### Question 2-2 : Pourquoi utiliser des variables sécurisées ?

- Pour ne pas exposer les identifiants et tokens dans le code
- GitHub chiffre automatiquement les secrets
- Recommandé dans tous les pipelines CI/CD


#### Question 2-3 : Pourquoi `needs: test-backend` ?

- Assure que le job Docker ne se lance que si les tests sont passés
- Empêche le déploiement de code cassé
- Rend la CI/CD fiable


#### Question 2-4 : Pourquoi pousser les images Docker ?

- Permet de déployer l'application n'importe où
- Nécessaire pour la livraison continue (CD)
- Base du déploiement en production

## TP 3 : 


#### Question 3-1 : Inventaire et Commandes de base

##### Fichier d’inventaire `inventories/setup.yml` :

```yaml
all:
  vars:
    ansible_user: admin
    ansible_ssh_private_key_file: ../../home/id_rsa #Clé privé
  children:
    prod:
      hosts:
        mon-serveur: arnaud.van-eeckhoven.takima.cloud 
```

##### Commandes de base :

```bash
ansible all -i inventories/setup.yml -m ping
ansible all -i inventories/setup.yml -m setup -a "filter=ansible_distribution*"
ansible all -i inventories/setup.yml -m apt -a "name=apache2 state=absent" --become
```

#### Question : 3-2 — Document your playbook

##### `ansible/playbook.yml`

```yaml
- hosts: all
  gather_facts: false
  become: true
  tasks:
    - name: Test de connexion
      ping:
```

##### Commandes associées :

```bash
ansible-playbook -i inventories/setup.yml playbook.yml
ansible-playbook --syntax-check -i inventories/setup.yml playbook.yml
```
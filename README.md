# Outils

## URL du projet
- [Application traefik](https://traefik-gr3.cefim-formation.org)
- [Application harbor](https://harbor-gr3.cefim-formation.org)
- [Application sonarqube](https://sonarqube-gr3.cefim-formation.org)
- [Application grafana](https://grafana-gr3.cefim-formation.org)
- [Application prometheus](https://prometheus-gr3.cefim-formation.org)
- [Application loki](https://loki-gr3.cefim-formation.org)

## Présentation :
Pour notre projet nous avons utilisé un serveur **Debian** installé sur une instance **AWS** sur lequel nous avons installé **Docker Compose** pour la contenerisarion. Puis nous avons installé **Traefik** comme reverse proxy et load balancer pour placer tous nos conteneurs derrière.

## Installation :

### **Serveur**

### Présentation :
Le cloud AWS propose une vaste gamme de services d'infrastructure, notamment des services d'optimisation
de la puissance de calcul, des options de stockage, de mise en réseau et des bases de données fournis en
tant que ressources utiles.

### Installation :
- Création du groupe de sécurité (SSH, HTTP, HTTPS).
- Création d'une clé SSH.
- Création d'une IP statique.
- Création d'une instance EC2 t2.medium 2 core et 4 GB de RAM et 25 GB de stockage avec Debian pour installer Docker.

### **Docker**

- [Documentation employée](https://docs.docker.com/engine/install/debian/)

### Présentation :
Docker est une plate-forme logicielle qui vous permet de concevoir, tester et déployer des applications rapidement. Docker intègre les logiciels dans des unités normalisées appelées conteneurs, qui rassemblent tous les éléments nécessaires à leur fonctionnement, dont les bibliothèques, les outils système, le code et l'environnement d'exécution. Avec Docker, vous pouvez facilement déployer et dimensionner des applications dans n'importe quel environnement, avec l'assurance que votre code s'exécutera correctement.

### Installation :
Les commandes à entrer en console :
```bash
sudo apt-get update
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

### **Traefik**
- [Repository traefik](https://gitlab-gr3.cefim-formation.org/devops/Outils/-/tree/main/traefik)
- [Application traefik](https://traefik-gr3.cefim-formation.org)
- [Documentation  employée](https://doc.traefik.io/traefik/providers/docker/)
### Présentation :
Traefik est un reverse proxy et un répartiteur de charge open source qui facilite le déploiement de micro services.  
Il a été spécialement développé pour fonctionner avec Docker.  
Il Génère et renouvelle des certificats Let’s Encrypt et a une interface web.

### Installation :
Nous nous sommes servis d'un ficher docker-compose.yml pour l'installation, d'un fichier traefik.yml pour la configuration, d'un fichier acme.json pour les certificats et d'un fichier passwd pour les authentifications.  

Les commandes à entrer en console :
```bash
touch acme.json
chmod 600 acme.json

sudo htpasswd -c .passwd ****
sudo htpasswd .passwd *******

sudo docker network create traefik

sudo docker compose up -d
```

### **Harbor**

### Présentation :
Harbor est un produit open source permettant de stocker des images Docker, comme le produit Docker Registry.  
Il permet de gérer des comptes, la réplication d'image, a une interface web et une API RESTful.

- [Repository harbor](https://gitlab-gr3.cefim-formation.org/devops/Outils/-/tree/main/harbor)
- [Application harbor](https://harbor-gr3.cefim-formation.org)
- [Documentation employée](https://github.com/bitnami/bitnami-docker-harbor-portal)

### Installation :
Nous avons utilisé un ficher docker-compose.yml pour l'installation, des fichiers de configuration bitnami du repository GitHub 2/debian10. Auxquels nous avons apporté les modifications propres à notre serveur.

Les commandes à entrer en console :
```bash
sudo docker compose up -d
```

### Difficultés rencontrées :
- [Documentation employée](https://www.claudiokuenzler.com/blog/958/running-harbor-registry-behind-reverse-proxy-solve-docker-push-errors)

Problème avec le header X-Forwarded-Proto répété par le reverse Nginx et Traefik. Il faut donc désactiver cette fonctionnalité sur Nginx.

### **Sonarqube**
- [Repository harbor](https://gitlab-gr3.cefim-formation.org/devops/Outils/-/tree/main/harbor)
- [Application sonarqube](https://sonarqube-gr3.cefim-formation.org)
- [Documentation employée](https://forge.univ-lyon1.fr/tiw-is/tiw-is-2020-sources/-/blob/efd602563b08421d76bf7c86ebe3081ed49118af/sonar/docker-compose.yaml)

### Présentation :
SonarQube (précédemment Sonar2) est un logiciel open source permettant de mesurer la qualité du code source en continu.

### Installation :
Nous avons recopié le fichier docker-compose.tml proposé sur ce site et ajouter les modifications nécessaires.  

Les commandes à entrer en console :
```bash
sudo docker compose up -d
```

### Difficultés rencontrées :
- [Documentation employée](https://www.claudiokuenzler.com/blog/958/running-harbor-registry-behind-reverse-proxy-solve-docker-push-errors)

Problème avec la mémoire.  

Message dans les logs :
```
ERROR: [2] bootstrap checks failed
[1]: max file descriptors [4096] for elasticsearch process is too low, increase to at least [65535]
[2]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
```

Les commandes à entrer en console :
```bash
sysctl -w vm.max_map_count=262144
```

### **Grafana**
- [Repository grafana](https://gitlab-gr3.cefim-formation.org/devops/Outils/-/tree/main/grafana)
- [Application grafana](https://grafana-gr3.cefim-formation.org)
- [Documentation employée](https://grafana.com/docs/grafana/latest/installation/docker/)

### Présentation :
Grafana est un logiciel open source qui permet la visualisation de données. (anciennement sous licence Apache 2.0 avant avril 2021) qui permet la visualisation de données.  
Il permet de réaliser des tableaux de bord et des graphiques depuis plusieurs sources dont des bases de données temporelles comme Graphite, InfluxDB et OpenTSDB3.

### Installation :
Réalisation du fichier docker-compose.yml et des fichiers loki_ds.yml et prometheus_ds.yml pour ajouter automatiquement les datasources.

Les commandes à entrer en console :
```bash
sudo docker compose up -d
```

### **Prometheus**
- [Repository prometheus](https://gitlab-gr3.cefim-formation.org/devops/Outils/-/tree/main/prometheus)
- [Application prometheus](https://prometheus-gr3.cefim-formation.org)
- [Documentation employée](https://dev.to/ablx/minimal-prometheus-setup-with-docker-compose-56mp)

### Présentation :
Prometheus est une application de surveillance open source. Il récupère les points de terminaison HTTP pour collecter les métriques exposées dans un format texte simple.

### Installation :
Mise en place du fichier docker-compose.yml et des fichiers associés pour la gestion des messages d'alerte.

Les commandes à entrer en console :
```bash
sudo docker compose up -d
```

### **Loki**
- [Repository loki](https://gitlab-gr3.cefim-formation.org/devops/Outils/-/tree/main/loki)
- [Application loki](https://loki-gr3.cefim-formation.org)
- [Documentation employée](https://grafana.com/docs/loki/latest/getting-started/)

### Présentation :
Loki est un système d'agrégation de journaux, hautement disponible, inspiré de Prometheus. Il est conçu pour être très économique et facile à utiliser. Il n'indexe pas le contenu des journaux, mais plutôt un ensemble d'étiquettes pour chaque flux de journaux.

### Installation :
Téléchargement des fichiers docker-compose.yml, loki-config.yml, promtail-local-config.yml.  

Les commandes à entrer en console :
```bash
sudo docker compose up -d
```
# Presentation du projet 
# Environnement de travail
Dans ce projet , nous allons travailler dans ces environnement:
- Une VM centos contenant Jenkins. Sur la meme Vm on deploie un "registry local" ,qui va nous servir à sauvegarder nos images docker.
- Une VM centos pour le déploiment en pré-prod.
- Un Cloud public Héruko por le déploiement en prod

# Creation d'un registry local 
Dans cette partie nous allons créer un registry local et une IHM pour pouvoir y accéder:

* Registry local:
   
- image de base registry version 2.
- Exposter sur le port 5000
- Le volume qu'il génére sura monter dans le dossier /volume 
  
* IHM: 
  
- image de base konradkleine/docker-registry-frontend:v2.
- Exposter sur le port 8081
- On Configure les variables d'nvironnement ENV_DOCKER_REGISTRY_HOST et ENV_DOCKER_REGISTRY_PORT


---
version: '3'

services:
    docker-registry:
        container_name: docker-registry
        image: registry:2
        ports:
            - 5000:5000
        restart: always
        volumes:
            - ./volume:/var/lib/registry
    docker-registry-ui:
        container_name: docker-registry-ui
        image: konradkleine/docker-registry-frontend:v2
        ports:
            - 8081:80
        environment:
            ENV_DOCKER_REGISTRY_HOST: docker-registry
            ENV_DOCKER_REGISTRY_PORT: 5000


* On lance le docker compose : docker-compose -f docker-compose.yml up -d

* On modifie le fichier etc/hosts pour pouvoir acceder à la machine avec le hostname
- On ajoute ${l'adresse de jenkins }: jenkins
  
* On modifie le fichier /etc/docker/daemon.json pour pouvoir utiliser le protocol HTTPS

- On ajoutant cette ligne :



{
     "insecure-registries":["jenkins:5000"]
}


- On redemare le service docker 
  service docker stop
  service docker start



docker pull alpine
docker tag alpine:latest jenkins:5000/jenkins/alpine-modifie:v1
docker push jenkins:5000/jenkins/alpine-modifie:v1

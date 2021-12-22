# student-list 
This repo is a simple application to list student with a webserver (PHP) and API (Flask)

![project](https://user-images.githubusercontent.com/18481009/84582395-ba230b00-adeb-11ea-9453-22ed1be7e268.jpg)


------------

## 1.1	Contexte

POZOS est une société informatique qui développe des logiciels pour des lycées. Le département de l'innovation veut améliorer l'infrastructure existante pour s'assurer qu’elle peut être évolutive, facilement déployable avec un maximum d'automatisation.

POZOS souhaite que vous construisiez un **POC** (Proof Of Concept) pour montrer comment docker peut vous aider et à quel point cette technologie est efficace. Pour ce POC, POZOS vous donnera une application et voudra que vous construisiez une infrastructure "découple" basée sur Docker. Actuellement, l'application s'exécute sur un seul serveur avec aucune répartition de charge et aucune haute disponibilité. Lorsque POZOS a besoin de déployer une nouvelle version, à chaque fois, une erreur se produit. En conclusion, POZOS a besoin d'agilité dans son parc logiciel.
L'application sur laquelle vous allez travailler pour cette première partie s'appelle " student_list ", cette application est très basique et permet à POZOS d'afficher la liste des étudiants avec leur âge.

Cette application a deux modules :
- Le premier module est une API REST (avec authentification basique nécessaire) qui envoie la liste d'étudiants souhaitée en format JSON
- Le deuxième module est une application web écrite en HTML + PHP qui permet à l'utilisateur final d'obtenir une liste d'étudiants
Votre travail consiste à créer un conteneur pour chaque module et à les faire interagir les uns avec les autres
Le code source de l'application peut être trouvé ici (https://github.com/takoukam/B3_Ynov_student-list.git )

Les fichiers que vous devez fournir (dans votre livraison) sont Dockerfile et rapport. Les fichers Dockerfile sont actuellement vides.  
Il est maintenant temps de vous expliquer le rôle de chaque fichier :
-	Dockerfile : le fichier qui sera utilisé pour construire l'image API et web (des détails seront donnés)
-	student_age.json : contient le nom des étudiants et l'âge au format JSON
-	student_age.py : contient le code source de l'API en python
-	index.php : page PHP où l'utilisateur final sera connecté pour interagir avec le service afin de lister les étudiants ainsi que leur l'âge. Vous devez mettre à jour la ligne suivante $url = 'http://<api_ip_or_name:port>/pozos/api/v1.0/get_student_ages'; avant d'exécuter le conteneur de site Web pour que api_ip_or_name et le port correspondent à votre déploiement. 

## 1.2	Construire et tester l’API
Ci-dessous des informations capitales nécessaires pour construire le conteneur API
-	Image de base

Pour créer une image API, vous devez utiliser "python:2.7-stretch"
-	Mainteneur

N'oubliez pas de préciser les informations du responsable. C’est une bonne pratique. 
-	Ajouter le code source

Vous devez copier le code source de l'API dans le conteneur dans le chemin racine "/" 
-	Prérequis

L'API utilise le moteur Flask, voici une liste du package que vous devez installer

````apt-get update -y && apt-get install python-dev python3-dev libsasl2-dev python-dev libldap2-dev libssl-dev -y````

````pip install flask==1.1.2 flask_httpauth==4.1.0 flask_simpleldap python-dotenv==0.14.0````

-	Données persistantes (volume)

Créez un dossier de données à la racine "`/data`" où les données seront stockées et déclarez-le en tant que volume
Vous utiliserez ce dossier pour monter la liste des étudiants
-	Port API

Pour interagir avec cette API, exposez le port 5000
-	CMD

Lorsque le conteneur démarre, il doit exécuter student_age.py (copié à l'étape 4), il devrait donc ressembler à quelque chose comme
`CMD [ "python", "./student_age.py" ]`

-	Faites le build de votre image 

Nom de l’image : api 
tag: 1.0

-	Réseau

Créez un réseau de type bridge appelé ``my-network``. C’est ce réseau qui sera utilisé afin de faire communiquer l’api et le web de l’application.  

-	Faites le run de votre image 

Vérifiez que le conteneur est à l’écoute et est prêt à répondre. Lors du run de votre image, pensez bien à préciser le réseau dans lequel votre api sera déployée.  Exécutez cette commande pour vous assurer que l'API répond correctement (prenez une capture d'écran à des fins de livraison)

``curl -u toto:python -X GET http://<host IP>:<API exposed port>/pozos/api/v1.0/get_student_ages``

Félicitations ! Vous venez de déployer l’API (le back-end de l’application). Vous êtes maintenant prêt pour l'étape suivante qui est de déployer le web (le front-end de l’application)


## 1.3	Construire et tester la page web
Après avoir testé votre image API, vous devez déployer le site web dans le même réseau créer précédemment ``my-network``. 

Ci-dessous des informations nécessaires pour construire le conteneur de la page web.

-	Image de base

Pour créer une image web, vous devez utiliser l’image " ``php:apache`` "

-	Mainteneur

N'oubliez pas de préciser les informations du responsable. C’est une bonne pratique. 

-	Variable d’environnement

Afin de permettre au conteneur web de se connecter à la base de données avec les identifiants corrects vous fournirez en variable d’environnement les informations ``USERNAME`` et ``PASSWORD``. 

-	Volume 

Afin d’éviter que l'image ``php:apache`` ne soit exécutée avec le site Web par défaut, le fichiers du site web doivent être déposés dans le répertoire ``/var/www/html``.
-	Port web

N'oubliez pas d'exposer le port d’écoute de votre serveur web " ``php:apache`` "

-	Faites le build de votre image 

Nom de l’image : web-student_list 

tag: 1.0

-	Réseau

N’oubliez pas de place le web dans le même réseau que l’API. Il s’agit en effet du réseau de type bridge appelé ``my-network`` crée précédemment. 

-	Faites le run de votre image 

Vérifiez que le conteneur est à l’écoute et est prêt à répondre. Lors du run de votre image, pensez bien à préciser le réseau dans lequel votre api sera déployée et aussi à mapper les ports.  

***Enfin, accédez à votre site Web et cliquez sur le bouton  "List Student"
Si la liste des élèves apparaît, vous avez réalisé avec succès l'application POZOS ! Félicitations (faites une capture d'écran)***



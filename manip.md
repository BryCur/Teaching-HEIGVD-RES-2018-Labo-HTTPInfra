# RES-Labo05 : Infrastructure HTTP

Auteurs : Burgener François, Curchod Bryan

##  Etape 1 - Serveur HTTP statique (php)

```
docker build -t res/apache_php
```

```
docker run -d -p 9090:80 res/apache_php
```

dans le navigateur nous pouvons désormais accéder à l'adresse **192.168.99.100:9090** pour admirer notre site et template bootstrap.

## Etape 2 - Serveur HTTP dynamique (js)

Dans le dossier express-image

```
docker build -t res/express_adresses .
```

```
docker run -p 9090:3000 res/express_adresses
```

sur le navigateur : **192.168.99.199:9090** pour accéder à l'application javasrcipt et voir que nous générons effectivement des "adresses" aléatoires.

## Etape 3 - Reverse proxy (statique)

```
docker build -t res/apache_php .
docker build -t res/express_adresses .
docker build -t res/apache_rp .
```

```
docker run --name apache_static -d res/apache_php
docker run --name express_dynamic -d res/express_adresses
docker run -p 8080:80 --name apache_rp -d res/apache_rp
```

dans le fichier host ajouter la ligne suivante (chemin du fichier host **C:\Windows\System32\drivers\etc**)

```
192.168.99.100 demo.res.ch
```

Dans le navigateur on peut maintenant utiliser les adresses **http://demo.res.ch:8080** pour accéder au serveur apache (avec le site bootstrap) et **http://demo.res.ch:8080/api/addresses/** pour l'application javascript générant des adresses aléatoires.

## Etape 4 - Requête AJAX (JQuery)

Dans le dossier apache_php_image

```
docker build -t res/apache_php .
```

Dans le dossier express-image

```
docker build -t res/dynamic_adresses .
```

Dans le dossier apache-reverse-proxy

```
docker build -t res/apache_rp .
```

S'il y a des problème avec les dépendance faire un `npm install --save`

Il faut run les containers dans cette ordre

```
docker run -d -name apache_static res/apache_php
```

```
docker run -d -name express_dynamic res/express_adresses
```

```
docker run -d -name apache_rp -p 8080:80 res/apache_rp
```

## Etape 5 - Reverse proxy (dynamique) 

``` bash
docker build -t res/apache_php .
docker build -t res/express_adresses .
docker build -t res/apache_rp .
```

NB : nous construisons une première fois nos images pour les initialiser

lancement des containers : 

```dockerfile
docker run -d --name apache_static res/apache_php
docker run -d --name epress_adresses res/express_adresses
```

Nous pourrions lancer plusieurs fois ces containers afin d'avoir l'embaras du choix des serveurs à utiliser. Une fois les containers lancés, il faut récupérer les adresses IP de ces derniers à l'aide de la commande 

```dockerfile
docker inspect CONTAINER_APACHE_STATIQUE | grep -i ipaddr
docker inspect CONTAINER_EXPRESS_DYNAMIQUE | grep -i ipaddr
```

une fois ces adresses récupérées lancer le serveur reverse-proxy en lui fournissant lesdites adresses pour définir les variables environnementales (instruction `-e VAR=VAL`).

````dockerfile
docker run -d -e STATIC_APP=IP_APACHE_STATIQUE:80 -e DYNAMIC_APP=IP_EXPRESS_DYNAMIQUE:3000 -p 8080:80 --name apache_rp res/apache_rp
````

## BONUS : gestion par interface graphique (UI)

Pour cette partie nous avons décidé (comme sûrement ~~toute la classe~~ beaucoup de nos collègues) d'utiliser l'outil [Portainer](https://portainer.io/index.html), qui permet de gérer simplement et rapidement nos conteneurs et images docker via une interface web. 

```
docker volume create portainer_data
docker run -d -p 9000:9000 -v portainer_data:/data portainer/portainer
```

Une fois ces commandes saisies, il suffit d'accéder à l'adresse de docker, sur le port 9000, pour commencer à gérer nos conteneurs.
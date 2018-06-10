# Etape 1

```
docker build -t res/apache_php
```

```
docker run -d -p 9090:80 res/apache_php
```

dans le navigateur **192.168.99.100:9090**

# Etape 2

Dans le dossier express-image

```
docker build -t res/express_adresses .
```

```
docker run res/express_adresses
```

sur le navigateur : **localhost:3000** pour voir que la génération aléatoire d'adresse s'effectue bien

Si on veut le faire via le containter il suffie de mapper les port

```
docker run -p 9090:3000 res/express_adresses
```

sur le navigateur : **192.168.99.199:9090** pour voir que la génération aléatoire d'adresse s'effectue bien

# Etape 3

Dans le dossier apache-reverse-proxy

```
docker build -t res/apache_rp .
```

```
docker run -p 8080:80 res/apache_rp
```

dans le fichier host ajouter la ligne suivante (chemin du fichier host**C:\Windows\System32\drivers\etc**)

```
192.168.99.100 demo.res.ch
```

Dans le navigateur on peut maintenant faire **http://demo.res.ch:8080** ou **http://demo.res.ch:8080/api/addresses/**



# Etape 4

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

Si il y a des problème avec les dépendance faire un npm install

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


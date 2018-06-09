# Migration des Projets

## 1. Github
Avoir 2 repositories Github : 
- un front
- un back

## 2. Heroku
Aller dans Heroku, et faire 2 nouvelles applications (votreprojet-back, votreprojet-front).  
Dans la création des applications il faut importer le code de Github et lier les comptes.  

## 3. Angular
Ensuite on va modifier son app Angular et pusher :  
Dans le terminal (ne pas oublier de `cd application`) :  
    `npm install @angular/cli@latest @angular/compiler-cli --save-dev`

Dans le package.json :  
- ajouter dans dependencies :  
```
"@angular/cli": "^6.0.8",
"@angular/compiler-cli": "^6.0.4",
"typescript": "~2.7.2",
```

- modifier dans `scripts` :  
```
"start": "node server.js",
```
- ajouter dans `scripts` :  
```
"postinstall": "ng build --aot -prod"
```
Dans le terminal :  
```
npm install enhanced-resolve@3.3.0 --save-dev
npm install express path --save
```

À la racine du dossier (au même niveau que le `node_modules` de votre appli) créer un fichier server.js et y mettre :  
```
const express = require('express');
const path = require('path');
const app = express();
app.use(express.static(__dirname + '/dist'));
app.get('/*', function(req,res) { 
res.sendFile(path.join(__dirname+'/dist/index.html'));
	});
app.listen(process.env.PORT || 8080);
```  


Dernière étape Angular :    
Changer l'url de spring.
Jusqu'à maintenant on avait [localhost:8080](localhost:8080)  
Maintenant il faut mettre L'URL où se trouve notre nouveau back :  
[https://votreprojet-back.herokuapp.com](https://votreprojet-back.herokuapp.com)  
Ou alors allez simplement chercher votre url sur le site Heroku.



## SPRING - BACK 

Ici il va falloir : 
- ajouter et configurer la BDD
- c'est tout lol 


On va dans le projet dans Heroku dans nos app,
Et on doit ajouter l'addon (Gratuit) [heroku-postgresql](https://elements.heroku.com/addons/heroku-postgresql)  
On l'attache au projet-back.

Là le souci c'est qu'Heroku ne propose que PostgreSQL.
En fait c'est une bonne nouvelle pour vous parce qu'il va falloir coder en PostgreSQL
CàD PL/SQL, la même surcouche que Oracle Database, et ça c'est CLASSE

Bon du coup, il faut installer PSQL, [c'est par ici](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads)   

On prend la version 10  
Logiquement l'installation se fait dans un des dossiers Program Files  
Vous devez ajouter le dossier PostgreSQL/10/bin au path.

Vous avez PgAdmin 4 qui doit s'installer en même temps.  
C'est une interface en HTML qui fait la même chose que Workbench.

Bon alors là il faut essayer de convertir la DB de votre projet de MySQL vers PL/SQL  
Pour le type `varchar(..)` en MySQL vous mettez `text`  
Pour l'ID je vous laisse checker en ligne

Puis aller dans Heroku -> votreprojet-back.  
là vous avez logiquement votre addon qui s'affiche, vous pouvez cliquer dessus.  
Vous allez dans paramètres > Database credentials  
et vous obtenez une url de malade de ce genre :  
`postgres://aghkjdfshsdjg:79d50450b871de780fdghfjdlshjghnvsjkfdc7a26d0ab04f8911167389@ec2-79-125-12-27.eu-west-1.compute.amazonaws.com:5432/skfhsghfiugh`  
vous pouvez donc taper :  
`psql url`  
remplacez `url` par l'url... ^^

Et ensuite faites les modifs sql à partir d'ici.

Une fois votre database en ligne à jour,
on va injecter les paramètres de la db à spring

Les params vont aller dans `application.properties`  
Attention, on ne modifie que la datasource, on laisse le reste : 
```
spring.datasource.driverClassName=org.postgresql.Driver
spring.datasource.maxActive=10
spring.datasource.maxIdle=5
spring.datasource.minIdle=2
spring.datasource.initialSize=5
spring.datasource.removeAbandoned=true
#${nomVariableEnvironnement:valeurSiPasDeNom}
spring.datasource.url=${JDBC_DATABASE_URL:jdbc:postgresql://localhost:5433/heroku}
spring.datasource.username=${JDBC_DATABASE_USERNAME:postgres}
spring.datasource.password=${JDBC_DATABASE_PASSWORD:mdpLOCAL}
```
On aura donc les variables d'env qui seront injectées dans Spring sur Heroku, et les variables par defaut Locales
qui seront injectées quand on run en local

Logiquement là ça doit marcher !

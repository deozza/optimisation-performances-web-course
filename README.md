# Optimisation des performances web <!-- omit in toc -->

## Table des matières <!-- omit in toc -->

- [Introduction](#introduction)
  - [Quelles optimisations de quelles performances ?](#quelles-optimisations-de-quelles-performances-)
  - [Pourquoi optimiser](#pourquoi-optimiser)
  - [Les prérequis avant d'optimiser](#les-prérequis-avant-doptimiser)
- [Optimisation du frontend](#optimisation-du-frontend)
  - [Objectif SEO](#objectif-seo)
  - [Chargement des assets](#chargement-des-assets)
    - [Compression et format adapté](#compression-et-format-adapté)
    - [Preload](#preload)
    - [CDN](#cdn)
  - [Exemples et mises en pratique](#exemples-et-mises-en-pratique)
    - [Le cas de McMASTER-CARR](#le-cas-de-mcmaster-carr)
- [Optimisation de l'architecture et des serveurs](#optimisation-de-larchitecture-et-des-serveurs)
  - [L'impact de l'architecture backend](#limpact-de-larchitecture-backend)
    - [Le monolithe](#le-monolithe)
    - [Le micro service](#le-micro-service)
    - [GraphQL ou REST](#graphql-ou-rest)
  - [Scaling](#scaling)
    - [Scaling vertical](#scaling-vertical)
    - [Scaling horizontal](#scaling-horizontal)
    - [Web scale, planet scale, ...](#web-scale-planet-scale-)
  - [L'impact de l'architecture frontend](#limpact-de-larchitecture-frontend)
    - [Le SSR](#le-ssr)
      - [Le SSG](#le-ssg)
    - [Le CSR](#le-csr)
    - [Le SPA](#le-spa)
  - [L'impact de l'infrastructure serveur](#limpact-de-linfrastructure-serveur)
    - [Le VPS](#le-vps)
    - [Le serverless](#le-serverless)
    - [Le edge](#le-edge)
- [Optimisation du backend](#optimisation-du-backend)
  - [Quel langage est le plus performant](#quel-langage-est-le-plus-performant)
  - [L'impact des algorithmes](#limpact-des-algorithmes)
    - [Quels algorithmes utiliser](#quels-algorithmes-utiliser)
    - [Quelles fonctions favoriser suivant les langages](#quelles-fonctions-favoriser-suivant-les-langages)
      - [PHP](#php)
      - [JS](#js)
    - [Comment monitorer les fonctions](#comment-monitorer-les-fonctions)
  - [L'impact de la BDD](#limpact-de-la-bdd)
    - [SGBD/NoSQL ou SGBDR/SQL ?](#sgbdnosql-ou-sgbdrsql-)
    - [Améliorer les requêtes](#améliorer-les-requêtes)
    - [Monitorer les requêtes](#monitorer-les-requêtes)
  - [Le cache](#le-cache)
    - [Page caching](#page-caching)
    - [Browser cache](#browser-cache)
    - [Serveur de cache](#serveur-de-cache)

## Introduction

### Quelles optimisations de quelles performances ?

Optimisation :

- collection d'outils et de techniques pour répondre à un besoin, avec une approche quantitative
- les metrics sont au centre de la procédure
- on part d'un état donné et on tend vers un objectif réalisable

Performances :

- rapidité d'exécution
- fluidité
- utilisation de ressources (CPU, RAM, électricité, budget)
- mise à jour du programme (developer experience)

### Pourquoi optimiser

Une application lente va frustrer puis repousser les utilisateurs, donc diminuer les revenus :

![bounce rate and conversion renault](assets/a-chart-showing-negative-4d67036b9e0c5_1920.png)
source : https://web.dev/case-studies/renault

![bounce rate and conversion vodafone](assets/an-illustration-reiterat-d99bc1eace701_1920.png)
source : https://web.dev/case-studies/vodafone

Une application couteuse donne des excuses aux entreprises pour fermer des postes ou diminuer les salaires :
https://ludic.mataroa.blog/blog/i-accidentally-saved-half-a-million-dollars/

### Les prérequis avant d'optimiser

- avoir un besoin d'optimiser
- cibler un aspect précis de l'application
- avoir une baseline sur laquelle comparer et retenir des metrics simples
- mettre en place des outils pour quantifier les changements qu'ont eu les optimisations sur ces metrics

## Optimisation du frontend

### Objectif SEO

- Search Engine Optimization
- améliorer la structure et le contenu des pages web pour les placer en tête des résultats d'un moteur de recherche
- meilleur positionnement = augmentation des visites (= reach) + meilleure réputation auprès des visiteurs = plus de conversions

Les metrics à surveiller :

- score sur google pagespeed / lighthouse, ou score sur GTMetrix
- nombre de visites avec google analytics ou un équivalent open source (plausible, matomo)
- positionnement sur des mots clefs précis ou une recherche précise (semrush, google console)

Comment améliorer :

- https
- balises meta (keyword et description)
- titre de l'onglet
- ordre respecté des balises header
- alt text dans les balises img
- ajouter des backlinks
- ajouter un fichier sitemap.xml pour lister les pages et les organiser entre elles
- ajouter un fichier robots.txt pour indiquer aux crawlers des moteurs de recherche quelles pages indexer
- schema markup pour donner du contexte aux données de la page

### Chargement des assets

- les fichiers medias, CSS et JS sont les fichiers les plus longs à charger pour le navigateur

Les metrics à surveiller :

- les Core Web Vitals :
  - First Input Delay
    - le temps entre le début du chargement de la page et le moment où l'utilisateur peut intéragir avec
  - Largest Contentful Paint
    - le temps entre le début du chargement de la page et l'affichage du plus gros bloc de texte / média visible dans la fenêtre
    - remplace le First Meaningful Paint et le Speed Index
  - Cumulative Layout Shift
    - le score du nombre de décalage des éléments d'une page depuis son chargement jusqu'à sa fermeture

#### Compression et format adapté

- minifier les fichiers CSS et JS
  - retire tous les caractères inutiles (espaces, saut de ligne, indentation) pour réduire la taille des fichiers

- code splitting
  - séparer le code JS et CSS en plusieurs fichiers
    - ne pas charger un fichier gigantesque pour utiliser uniquement une fonction

- compresser les fichiers textes (JS, HTML et CSS) avec GZIP ou Brotli
  - pour réduire la taille des fichiers
  - ne pas utiliser sur les fichiers binaires (comme les images)
    - sauf les .svg

- utiliser un format adapté pour les images
  - svg pour les logos, les icones et toutes les autres images vectorielles
    - permet de s'adapter à toutes les résolutions sans changer de taille de fichier
  - webp pour le reste
    - meilleure compression que jpg
    - améliore le SEO
  - gif est aussi un format avec une bonne compression pour une image fixe
    - à ne pas utiliser pour une vidéo, préférer utiliser mp4
  - si le format n'est pas supporté par un navigateur, utiliser une image fallback :

```html
<picture>
    <source srcset="fichier.webp" type="image/webp" />
    <img src="fichier.jpg" type="image/jpeg" alt="mon fichier" />
</picture>
```

- compresser les images
  - compresser un jpg à 70% est invisible à l'oeil nu
  - réduit la taille du fichier par 7
  - utiliser une API (kraken.io) ou des librairies (imagemagick, imagemin)
  
- réduire la dimension des images
  - pourquoi utiliser une image 4k alors que le container fait 500px ?

#### Preload

- `dns-prefetch`
  - lorsque le nom de domaine d'une ressource est connue, mais son URI peut varier
  - permet de faire une requête DNS en avance et de connaître l'adresse IP du serveur de la resource
  - permet de gagner entre 50 et 300ms
  - utile pour se connecter à un CDN ou à Google Fonts

- `preconnect`
  - comme `dns-prefetch` mais fait une connection conplète
  - fait gagner plus de temps mais demande plus de resources

- `prefetch`
  - permet de charger et mettre en cache une resource précise
  - s'active lorsque la balise qui l'utilise est visible sur la page
  - utile pour charger un bundle JS, une feuille CSS
  - la ressource doit être prioritaire pour l'affichage de la page

- `preload`
  - comme `prefetch`, mais pour des ressources non prioritaires pour l'affichage de la page

- `prerender`
  - charge en avance le contenu d'une page
  - permet de l'afficher instantanément une fois qu'on y navigue

#### CDN

- Content Delivery Network
- infrastructure qui met à disposition des serveurs dans le monde entier
- permet de stocker des fichiers statiques (images, fonts, css, js)
- lorsqu'une visite est faire sur un site, le serveur du CDN le plus proche lui fournit les fichiers demandés
- permet de réduire de 100ms environ une requête
- très efficace lorsque plusieurs requêtes sont faites d'affilées pour une page

Exemples de CDN :

- cloudflare
- fastly

### Exemples et mises en pratique

#### Le cas de McMASTER-CARR

- backend en .NET
- frontend en html et js maison

- pour les perfs
  - dns-prefetch pour les domaines des assets
  - preload des fonts, logos et autres images
  - utilisation d'un CDN pour stocker les assets...
  - ...et l'html du site
  - utilisation d'un service worker pour charger le site du cache (comme une PWA)
  - comportement :
    - la page est séparée en component
    - uniquement la partie centrale est mise à jour pendant la navigation
    - lorsqu'un lien est survolé, le contenu de la page et ses configs sont préchargés et mis en cache
    - lorsqu'il est cliqué, la partie centrale est mise à jour *puis* l'url
      - **SPA**

- pour le CLS
  - css critique en style avant l'html
  - taille fixe des images

## Optimisation de l'architecture et des serveurs

### L'impact de l'architecture backend

![monolith vs microservice](assets/monolith_vs_microservice.gif)

#### Le monolithe

Définition :

- toutes les fonctionnalités réunies dans une seule application, avec une seule base de données

Exemples d'utilisation :

- shopify (Ruby on Rails)
- whatsapp (Erlang)
- stackoverflow (C# et ASP.NET)

Avantages :

- tout est accessible à un seul endroit
  - facile à utiliser
  - facile à tester
  - facile à débugger
  - facile pour implémenter de nouvelles fonctionnalités
- scalable verticalement

Inconvénients :

- si un serveur est down, toutes les fonctionnalités sont downs
- mises à jour lentes
- déploit tout ou rien
- scale tout ou rien
- couplage fort des dépendances
  - au langage
  - au framework
  - aux designs patterns

#### Le micro service

Définition :

- ensemble de programmes répartis sur plusieurs noeuds pour atteindre un objectif commun
- 1 domaine applicatif = une application avec son API, sa logique métier et sa base de données
- dépendant d'un système pour faire communiquer les noeuds
  - par API HTTP, par RPC, par messaging

> Certains pourraient dire que le micro service est du rebranding des systèmes distribués.

Exemples d'implémentation :

- email
- internet
- systèmes d'aviation
- amazon, twitter, youtube, netflix

Avantages :

- permet de scaler horizontalement
- équipes de développement indépendantes

Inconvénients :

- définition floue
  - quand est-ce que c'est micro ?
  - c'est quoi un service ?
- complexité : [Problème des microservices](https://youtu.be/y8OnoxKotPQ)
- latence :

![latence](assets/latency.png)

- absence de tooling
- destruction du DRY
  - il y a forcément des fonctionnalités communes aux services
  - faire un bundle
    - comment gérer les mises à jour ?
    - comment gérer les versions compatibles ?
    - comment gérer les fonctionnalités utiles pour services A et B mais inutiles pour X et Y ?
- destruction de la DX
- tests
  - de charge
  - d'intégration
    - qui maîtrise toute la chaîne pour maîtriser les tests d'intégration ?
  - passage sur de l'observabilité ?
    - canary

![canary](assets/canary.png)

- coût énorme
  - en infrastructure
  - en personnes
- dette technique à moyen et long terme

> Certains pourraient faire un paralèlle avec la loi de Conway (la structure d'un système reflète la structure de l'organisation à son origine)

https://www.youtube.com/watch?v=LcJKxPXYudE&t=80s

#### GraphQL ou REST

![REST vs GRAPHQL](assets/REST-vs-GRAPHQL.png)
[logrocket](https://blog.logrocket.com/graphql-vs-rest-api-why-you-shouldnt-use-graphql)

À utiliser pour :

- réunir plusieurs requêtes API en une seule et éviter les allez-retours
- préciser quelle donnée est réellement utilisée, donc réduire la bande passante de la requête
- palier à un problème d'architecture, plus que de performances

En réalité, beaucoup des fonctionnalités de GraphQL peuvent être implémentées avec du REST. Elles ne sont pas disponibles par défaut.

### Scaling

#### Scaling vertical

- augmenter les performances d'une instance serveur
  - augmenter la puissance CPU , la RAM, l'espace disque, le type de stockage, l'interface réseau, ...
- difficilemnt rétrogradable

#### Scaling horizontal

- augmenter le nombre d'instances de serveurs pour répartir la charge
- facile à faire évoluer et adapter à la charge en temps réel
- coût adapté à la charge

#### Web scale, planet scale, ...

![planet scale](assets/planet-scale.png)

https://www.youtube.com/watch?v=b2F-DItXtZs

- architecture qui s'adapte automatiquement au nombre d'utiliseurs
  - les performances ne sont pas impactées
  - le nombre d'erreurs n'augmente pas
- utilisé par Facebook, Amazon, Google, ...

### L'impact de l'architecture frontend

#### Le SSR

Définition :

- server side rendering
  - le client fait une requête HTTP au serveur
  - le serveur construit la page et envoie l'HTML au client
  - le client télécharge des scripts JS et les exécute pour rendre la page dynamique
    - principe de l'hydration

> Certains pourraient dire que c'est exactement ce qu'on faisait en construisant des monolithes en PHP, Ruby on Rails, ...

Cas d'usages :

- blogs, e commerce

Avantages :

- le contenu est présent sur le serveur, donc crawlables et indexables
- temps de chargement rapide
  - chargement des données avant l'affichage
  - les serveur sont plus proches, moins d'aller-retours
  - plus facile pour mettre du cache
  - ne dépend pas de la capacité du client pour charger

Inconvénients :

- potentiels problèmes de shared state
- applications moins intéractives
- dépendant des performances du serveur
  - et de leur coût

##### Le SSG

Définition :

- static site generation
  - l'intégralité du site est généré sous forme de pages HTML prêtes à être utilisées

> Un site réalisé "à l'ancienne" avec des fichiers HTML, sans framework, est un SSG

Cas d'usages :

- blogs, site vitrine / landing page, portfolios

Avantages :

- tous ceux du SSR
- deployable sur CDN
  - donc coût d'hébergement réduits
  - chargement très rapide, peu importe la localisation des visiteur.ses
  - sécurité (pas de serveur)

Inconvénients :

- absence de logique côté serveur
  - pas de personnalisation de la page en fonction de l'utilisateur.ice
- chaque changement de contenu nécessite une nouvelle génération et un nouveau déploiement

#### Le CSR

Définition :

- client side rendering
  - le client télécharge des scripts JS
  - le JS exécuté construit la structure de la page depuis le client

Cas d'usages :

- dashboard, messagerie

Avantages :

- soulage le serveur
- peut tromper l'utilisateur.ice sur les performances en remplaçant des éléments au lieu de recharger la page
- grande intéractivité

Inconvénients :

- performances dépendantes de la bande passante du client et de son matériel
- difficultés pour implémenter du SEO efficacement
- difficultés pour mettre en place du cache
- doit constamment vérifier l'état des données (chargées ? nulles ?)

#### Le SPA

Définition :

- single page application
- une application qui ne recharge pas la page lors d'un changement de route
- traditionnellement une application CSR, mais peut être SSR

### L'impact de l'infrastructure serveur

#### Le VPS

Définition :

- virtual private server
- serveur sur lequel tout doit être configuré
- abonnement mensuel

Cas d'usages :

- tout ?

Avantages ... et inconvénients :

- prix fixe
- contrôle total sur la machine
  - gestion des ressources
  - gestion de la sécurité

#### Le serverless

Définition :

- serverless !== sans serveur
  - sans serveur *à gérer*
- déploiement chez un cloud provider (aws, azure, gcp, ...)
- souvent déploiement automatique OU automatisable facilement
- la machine est éteinte par défaut et s'allume lorsqu'il y a de la charge
  - scalable horizontalement
  - prix à l'utilisation, pas d'abonnement mensuel
    - souvent un free tier

Cas d'usages :

- scripts, cron
- prototypes, demo

Avantages :

- rapide à mettre en place
- couplé à un CDN pour les applications SSR

Inconvénients :

- doit être architecturé différemment
- management des coûts
- boîte noire
- walled garden
- cold start

#### Le edge

Définition :

- se base sur le serverless / cloud computing
- *CDN mais pour des exécutables*
- déploie le même script sur plusieurs serveurs dans le monde pour que le plus proche de l'utilisateur.ice soit utilisé

Avantages :

- pas de cold start

Inconvénients :

- on fait quoi de la DB ?

## Optimisation du backend

### Quel langage est le plus performant

https://xcancel.com/BenjDicken/status/1863977678690541570

> - sur le fond, le benchmark ne reflète pas un cas réel donc ne vaut rien
> - sur la forme, les barres bougent trop différemment alors que les chronos sont très similaires
> - concernant les programmes compilés, c'est davantage un benchmark sur les compilateurs qu'un benchmark sur le programme
>   - est-ce que la personne qui programme sait comment les abuser/optimiser
>   - est-ce que le compilateur est performant

https://www.youtube.com/shorts/oVKvfMYsVDw

> - express a été utilisé pour node, alors qu'il est reconnu comme le moins efficace des frameworks + l'overhead du moteur V8
> - un middleware écrit en C++ a été utilisé pour php pour booster les performances + tourne sans framework + le serveur de base de PHP a été utilisé (alors qu'il n'est pas fait pour la prod)
> - go ne devrait pas être aussi lent puisque compilé

### L'impact des algorithmes

![algorithm complexity chart](assets/complexity.webp)

- metric : complexité cyclomatique des algorithmes
- concept : le nombre d'instructions à exécuter pour la réaliser d'une fonction, par rapport à l'input de cette fonction.

Exemples :

```TS

// complexité O(1)
function returnFirstItemOfArray(input: array) {
  return input[0];
}


// complexité O(n)
function reverseString(input: string): string {
  let result: string = '';
  for(let i = input.length - 1; i >=0; i--) {
    result += input[i];
  }

  return result;
}

// complexité O(2^n)
function fibonacci(input: number): number {
  if(input < 2) {
    return input;
  }

  return fibonnaci(input - 1) + fibonnacci(input - 2);
}

```

#### Quels algorithmes utiliser

- dans une fonction
  - réduire les boucles imbriquées
  - réduire les embranchements `if else`

> Algorithmes avec un BigO faible === algorithmes écrits en clean code

- éviter les fonctions récursives
  - la fonction est interprétée à nouveau à chaque itération et prend de plus en plus de place mémoire

- utiliser des hash maps
  - prendre un tableau associatif de clefs-valeurs
  - hasher les clefs et les répartir dans des sous-tableaux (*bins* ou *buckets*)
  - sauvegarder les valeurs dans les bins correspondant
  - plus rapide de chercher une clef dans 100 bins puis dans le bin correspondant, que dans un tableau de 10 000 clefs

#### Quelles fonctions favoriser suivant les langages

##### PHP

- pour vérifier qu'une valeur existe dans un tableau, utiliser `isset()` plutôt que `in_array()`
  - même si `isset()` n'est pas clean (elle vérifie si la valeur existe et si elle est différente de null)
  - `isset()` utilise un algorithme de recherche basé sur une hash map alors que `in_array()` parcours tout le tableau
  - on peut aussi utiliser `array_flip()` pour inverser clefs et valeurs dans un tableau (et créer une hash map) puis utiliser `array_key_exists()`

##### JS

- pour parcourir un tableau, une boucle `for(let i = 1...)` est plus rapide que `array.forEach()`, `array.reduce()` et `for(... of ...)`
- pour rechercher une valeur dans un tableau, créer un `Set` (une hash map) et utiliser `set.has()` est plus rapide que `array.includes()`
- pour trier un tableau de chiffres, la fonction `array.sort()` est plus rapide qu'un algorithme de merge sort ou de bubble sort

#### Comment monitorer les fonctions

- en php :
  - avec la fonction `microtime()`
  - avec xdebug
  - avec [xhprof](https://github.com/phacility/xhprof)
- en js :
  - utiliser deno ?

### L'impact de la BDD

#### SGBD/NoSQL ou SGBDR/SQL ?

SQL :

- consistant
- efficace pour manipuler de la donnée

NoSQL :

- tolérance à la partition
- disponibilité
- efficace pour uniquement écrire et lire de la donnée
  - souvent en très grande quantité et/ou à grande fréquence
  - grâce au format document / denormalized

#### Améliorer les requêtes

![Order of exection of the clauses in a SQL request](assets/SQL-request-execution-order.png)

Le plus intensif pour une requête SQL est :

- le `FROM` / `JOIN`
- le `WHERE` / `ON`
- le `SELECT`

**Pour optimiser le `FROM`**

- utiliser des indexs
- optimiser les jointures
  - préférer les `INNER JOIN` pour limiter le nombre de lignes à traiter

**Pour optimiser le `WHERE`**

- utiliser des arguments SARGABLE
  - pour Search ARGument ABLE
  - éviter d'utiliser des fonctions
    - une fonction bypass les indexs
  - éviter les joker/wildcard en début de chaîne

Exemples :

```SQL
SELECT ... WHERE YEAR(date) = 2023; --bad
SELECT ... WHERE date >= '2023-01-01' AND date < '2024-01-01'; --good

SELECT ... WHERE SUBSTRING(name, 3) = 'foo'; --bad
SELECT ... WHERE name LIKE 'foo%'; --good
```

- ne pas faire de sous-requêtes
  - préférer faire une jointure plutôt que `WHERE property = (SELECT ...)`

**Pour optimiser le `SELECT`**

- select uniquement les propriétés qui seront utilisées
  - limiter les `SELECT *`

**Optimisations globales**

- paginer les résultats (utilisation de `LIMIT` et `OFFSET`)
- batch update/insert
- attention à ne pas lancer plusieurs requêtes en même temps sur les mêmes ressources
  - problèmes de concurrence d'accès aux données
- utiliser les bons types
  - `BIGINT`, `INT`, `TINYINT`, ...
- utiliser des tables dédiées / optimisées
  - partitionner une table pour en avoir plusieurs petites
    - sharding
  - créer une vue pour une requête lourde et récurrente

#### Monitorer les requêtes

- avec un outil en CLI : https://github.com/charles-001/dolphie
- avec un outil en GUI : https://www.mysql.com/fr/products/workbench/
- avec le mot clef `EXPLAIN` :

```SQL
EXPLAIN SELECT * FROM table;
```

### Le cache

#### Page caching

- mettre en cache le résultat d'une page html
- si la page mise en cache est considérée récente
  - on la renvoie directement
  - sinon on génère la page de nouveau, on la stocke en cache et on la renvoie
- avantage : on renvoie des fichiers statiques, avec très peu de traitements, donc très rapide
- inconvénient : si le contenu de la page évolue souvent, impossible à mettre en place

#### Browser cache

- utiliser le header `Cache-Control` avec les options :
  - `max-age` pour faire expirer la réponse du cache au bout d'un certain temps en ms
  - `expires` pour faire expirer la réponse du cache après une date
  - `private` pour stocker le cache uniquement sur le client (un autre serveur ne pourra pas mettre en cache)
  - `public` pour stocker le cache sur n'importe quel appareil

#### Serveur de cache

- exemple de redis
- base de données non relationnelle
- stocke dans la ram, pas sur le disque
- s'installe comme application dédiée ou en dépendance d'une application existante
- dispose d'une api simple pour écrire et lire depuis le cache
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
    - [Le cas de NextFaster](#le-cas-de-nextfaster)
- [Optimisation de l'architecture et des serveurs](#optimisation-de-larchitecture-et-des-serveurs)
  - [L'impact de l'architecture backend](#limpact-de-larchitecture-backend)
    - [Le monolithe](#le-monolithe)
    - [Le micro service](#le-micro-service)
  - [L'impact de l'architecture frontend](#limpact-de-larchitecture-frontend)
    - [Le SSR](#le-ssr)
    - [Le SSG](#le-ssg)
    - [Le SPA](#le-spa)
  - [L'impact de l'infrastructure serveur](#limpact-de-linfrastructure-serveur)
    - [Le VPS](#le-vps)
    - [Le serverless](#le-serverless)
    - [Le edge](#le-edge)
  - ["Web scale"](#web-scale)
    - [Scaling vertical](#scaling-vertical)
    - [Scaling horizontal](#scaling-horizontal)
- [Optimisation du backend](#optimisation-du-backend)
  - [L'impact des algorithmes](#limpact-des-algorithmes)
    - [Comment monitorer les fonctions](#comment-monitorer-les-fonctions)
  - [L'impact de la BDD](#limpact-de-la-bdd)
    - [SGBD ou SGBDR ?](#sgbd-ou-sgbdr-)
    - [GraphQL](#graphql)
    - [Améliorer les requêtes](#améliorer-les-requêtes)
  - [Le cache](#le-cache)

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

#### Le cas de NextFaster

## Optimisation de l'architecture et des serveurs

### L'impact de l'architecture backend

#### Le monolithe

#### Le micro service

### L'impact de l'architecture frontend

#### Le SSR

#### Le SSG

#### Le SPA

### L'impact de l'infrastructure serveur

#### Le VPS

#### Le serverless

#### Le edge

### "Web scale"

#### Scaling vertical

#### Scaling horizontal

## Optimisation du backend

### L'impact des algorithmes

#### Comment monitorer les fonctions

### L'impact de la BDD

#### SGBD ou SGBDR ?

#### GraphQL

#### Améliorer les requêtes

### Le cache

# Projet II (2023-24)
## Technologies Big Data. M1 MIDS/Informatique

### Vlady Ravelomanana et Stéphane Boucheron

**Date de remise : 29 mai 2024**

## Système de vélos partagés de New York

Ce devoir porte sur les systèmes de vélos partagés de New York (Citibikes). Le système de vélos partagés fonctionne depuis plus de 10 ans.

Citibikes a publié un ensemble de données historiques détaillées couvrant plus de 10 ans, de 2013 à aujourd'hui. Pris dans son ensemble, les données détaillées au niveau des trajets représentent plus qu'une simple liste de coordonnées de départ et d'arrivée : c'est l'histoire des trajets à vélo dans la Grande Pomme (NYC).

- Où roulent les utilisateurs de Citibikes ?
- Quand roulent-ils ?
- Quelle distance parcourent-ils ?
- Quelles stations sont les plus populaires ?
- Quels jours de la semaine enregistrent le plus de trajets ?
- Quelle est la saisonnalité du trafic à vélo ?
- Comment varie-t-il dans le temps et l'espace ?
- Où se rassemble la foule le dimanche après-midi ?
- Qui roule à vélo en hiver ?
- À quelle heure les banquiers d'affaires se rendent-ils au travail ?
- Comment l'assistance électrique a-t-elle modifié le paysage des systèmes de vélos partagés ?

L'ensemble de données répond à toutes ces questions et bien d'autres encore.

### Ressources

Les informations peuvent être collectées aux URL suivantes :
- [Bikeshare Research](https://bikeshare-research.org)
- [Citi Bike NYC](https://citibikenyc.com)

- Flux en temps réel
- Données de flux de vélos à NYC
- Flux de statut des stations
- Itinéraires des vélos

### Conseil

L'ensemble de données est disponible sur Amazon S3 (service de stockage en nuage d'Amazon).
- [System Data](https://citibikenyc.com/system-data)
- [Citi Bike NYC API](https://api.citybik.es/citi-bike-nyc.json)
- [Trip Data on S3](https://s3.amazonaws.com/tripdata/index.html)

### Important

Pour ce devoir, vous pouvez choisir les outils à utiliser (attendus : Spark/Dask) et trouver les informations par vous-même.

### Téléchargement des archives zip

Téléchargez (de manière programmée) les données des trajets pour les années 2014 à 2023. Votre processus de téléchargement doit être reproductible. Il y a plusieurs fichiers CSV pour chaque mois ou pour chaque année civile. Chaque fichier annuel est de taille modérément grande, allant de quelques centaines de mégaoctets à un gigaoctet. L'ensemble des données est modérément grand s'il doit être manipulé sur un ordinateur portable (les fichiers CSV annuels compressés font 8 gigaoctets).

Quel que soit le cadre que vous utilisez (Pandas, Dask ou Spark), les fichiers CSV sont difficiles à manipuler.

### Conversion des fichiers CSV en un format compatible avec Spark

Après avoir téléchargé les fichiers (cela prend du temps, mais c'est une routine), la première étape consistera à convertir les fichiers csv en un format plus compatible avec Spark, tel que parquet ou orc. La sauvegarde dans l'un de ces formats nécessite des décisions sur le découpage en compartiments, la partition, etc. Ces décisions influencent les performances. C'est votre décision.

### Sauvegarde des données dans un format de Big Data (parquet/ORC)

Le format Parquet (et ORC) permet une évolution limitée du schéma. Vous devez en tirer parti. Cela nécessite un travail substantiel :
- Renommer les colonnes pour obtenir un schéma de nommage cohérent
- Prendre en compte les incohérences dans le formatage des horodatages (pas toujours conforme à l'ISO)
- Prendre en compte les différentes façons d'identifier les stations (différents formats d'identifiant de station sont utilisés au cours des dix ans)
- Prendre en compte le fait que certaines colonnes disparaissent à certains moments tandis que d'autres apparaissent.

À la fin du processus, vous devriez avoir un fichier parquet ou ORC partitionné.

### Organisation des données en un schéma en étoile

À partir des données de trajets, vous devez construire une table de dimension rassemblant des informations concernant les stations (nom, id(s), latitude, longitude, etc.) et une table d'événements rassemblant des informations sur les trajets avec des références à la table de dimension (les identifiants des stations de départ/arrivée).

### Conseils

- N'oubliez pas de demander à Spark/Dask d'utiliser une part substantielle de la mémoire et des ressources de votre ordinateur.
- N'oubliez pas de spécifier une colonne de partitionnement et un nombre de partitions lors de la création des fichiers parquet.

### Questions à se poser

- Quel est le StorageLevel du dataframe après la lecture des fichiers csv ?
- Quel est le nombre de partitions du dataframe ?
- Est-il possible de régler ce nombre au moment du chargement ?
- Pourquoi voudrions-nous modifier le nombre de partitions lors de la création des fichiers parquet ?

### Analyse des données avant et après Covid

Analysez (au moins) une année de données avant Covid et une année après Covid.

Calculez au moins les éléments suivants et produisez des graphiques pertinents :
1. Répartition des distances des trajets pour chaque jour de la semaine
2. Compter le nombre de trajets pour chaque couple de lieux de départ/arrivée
3. Répartition des distances des trajets par genre (lorsque disponible)
4. Répartition des distances des trajets par tranches d'âge (15-24, 25-44, 45-54, 55-64, 65+ ans) (lorsque disponible)
5. Répartition des distances des trajets pour différents types de vélos (lorsque disponible)

### Conseils pour les graphiques

Si vous souhaitez créer des graphiques attrayants, vous pouvez utiliser matplotlib + seaborn, plotly ou altair pour créer des graphiques interactifs, mais vous pouvez utiliser ce que vous voulez.

### Évaluation des saisonnalités et analyse des séries temporelles

Calculez et tracez les séries temporelles suivantes indexées par jour de la semaine et heure de la journée :
1. Le nombre de départs/arrivées
2. La distance moyenne
3. La durée moyenne des trajets
4. Le nombre moyen de trajets en cours

### Suivi de l'exécution des travaux

1. Utilisez la méthode `explain` ou consultez l'interface utilisateur de Spark pour analyser le travail. Vous devriez pouvoir évaluer :
   - Plan logique analysé
   - Plan logique optimisé
   - Plan physique

2. Les plans logique analysé et optimisé diffèrent-ils ? Repérez les différences, le cas échéant. Comment une SGBDR procéderait-elle à une telle requête ?

3. Comment le plan physique diffère-t-il du plan logique optimisé ? Quels sont les mots-clés que vous n'attendez pas dans une SGBDR ? Quelle est leur signification ?

4. Inspectez les étapes sur l'interface utilisateur de Spark. Combien d'étapes sont nécessaires pour compléter le travail Spark ? Quels sont les rôles de `HashAggregate` et `Exchange hashpartitioning` ?

5. Le plan physique effectue-t-il des opérations de shuffle ? Si oui, combien ?
6. Qu'est-ce que les tâches par rapport aux étapes (en langage Spark) ? Combien de tâches composent vos étapes ?

### Plongée dans les problèmes d'information spatiale

**Informations géographiques**

**Choisissez une année**
Pour cela, vous devrez trouver des outils pour afficher des cartes et créer des cartes choroplèthes. Nous vous laissons rechercher et trouver les outils pertinents pour cela.

1. Créez une carte de chaleur indexée par couple de stations où la couleur est fonction du nombre de trajets d'une station à une autre.
2. Créez une carte de chaleur interactive avec un curseur permettant à l'utilisateur de sélectionner une heure de la journée et où la couleur est fonction du nombre de trajets d'une station à une autre.

Vous pourriez avoir besoin des éléments suivants pour travailler avec des coordonnées GPS et tracer facilement les choses.

```bash
!pip install geojson geopandas plotly geopy
!pip install ipyleaflet

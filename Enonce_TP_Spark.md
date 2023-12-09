**Sommaire**

[TOC]

Cet énoncé recueille le sujet à réaliser en séance (_Il est où le bel arbre ?_), ainsi que l'énoncé de l'exercice à réaliser pour le CR (ce dernier ne portera que sur cet exercice).

---
## Exercice 1 : Il est où le bel arbre ?

### Introduction

On considère ici le fichier de données [Opendata Paris](http://opendata.paris.fr), de type CSV, concernant des arbres remarquables à Paris. Ce fichier est disponible au [téléchargement](https://opendata.paris.fr/explore/dataset/arbresremarquablesparis/information/). Choisissez le menu `Export` et le format `csv`.

Copiez ce fichier dans le _Namenode_ (`hadoop-master`). La commande 
```bash
cat arbresremarquablesparis.csv
```
montre que chaque ligne décrit un arbre de la manière suivante : 

  - position GPS, arrondissement, 
  - genre, espèce, famille, 
  - année de plantation, hauteur, circonférence, etc. 

Le séparateur entre les colonnes est le caractère ';'. La première ligne du fichier contient les titres des colonnes ; pour la supprimer, il suffit d'écrire:
```bash
sed '1d' arbresremarquablesparis.csv > arbresremarquablesparis2.csv 
```
Copiez alors _arbresremarquablesparis2.csv_ sur _HDFS_ (dans le répertoires `input`).

*Remarque :* 

  - Vous ne pouvez pas rédiger vos algo sur le _Namenode_ (il n'y a pas d'éditeur de texte). Éditez le fichier sur votre système d'exploitation, et envoyez-le sur le _Namenode_ par la commande `docker cp ..`.


### Travail à réaliser

Écrivez des programmes _pyspark_ permettant 

  1. d'afficher les coordonnées GPS, la taille et l'adresse de l'arbre le plus grand.   
     - Certains arbres n'ont pas de taille renseignée : on devra donc filtrer le RDD en supprimant les lignes dont la hauteur est égale à `''`.       
     - La hauteur sera considérée comme une chaîne de caractères. Pour la transformer en nombre réel : `hauteur_decimal = float(hauteur_string)`.     
     - Pour vérifier le résultat, vous pouvez utiliser [ce site](http://www.coordonnees-gps.fr/). Entrez les coordonnées GPS trouvées et vérifiez que l'adresse est bien la bonne !    
  1. d'afficher les coordonnées GPS des arbres de plus grandes circonférences pour chaque arrondissement de la ville de Paris.
  1. d'afficher toutes les espèces d'arbre, triées par genre. 

---
## Exercice 2: _The MovieLens Database_ (à reporter dans votre CR)

Cette partie du TP est à rendre, en respectant les deadlines (_cf_ site edunao du cours), seul ou en binôme.

> - Sur le CR, rédigé en *markdown*, merci d'inclure les scripts permettant de répondre à la question posée, et d'ajouter un extrait des résultats obtenus sur le fichier proposé. Tout ajout, *p. ex.* une variante de l'algorithme, ou un test sur d'autres fichiers de vocabulaires..., sera TRES apprécié. Inscrivez sur le CR toutes les informations me permettant de rejouer ces ajouts.  
> - N'oubliez pas d'inscrire votre nom / vos noms sur les scripts, et de déposer un seul fichier compressé nommé _VosNoms_rendu3.zip_.
> - Cet algorithme sera évalué et comptera dans votre note finale. La note prendra en compte la qualité et la clarté du code (qui doit être légèrement commenté, avec des noms de variables qui donnent du sens à votre programme), ainsi que l'originalité et la complexité de la requête que vous aurez choisie !

### Téléchargement des données

Il faut d'abord télécharger la base de données
```bash
wget http://files.grouplens.org/datasets/movielens/ml-latest-small.zip
```
et la décompresser:
```bash
unzip ml-latest-small.zip
rm ml-latest-small.zip
```
Dans le répertoire _ml-latest-small_, créé lors de la décompression, vous constaterez que les fichiers correspondent à 100 000 notes et 3 600 tags donnés par 600 utilisateurs concernant 9 000 films.

### Travail à réaliser

Pour le CR, je vous demande de proposer une (1) requête, écrite en _PySPark_, relativement sophistiquée (et originale) qui interroge un ou plusieurs fichiers de cette base.

*Remarque* : Vous **NE devez PAS** utiliser `pyspark.sql` dans votre requête.

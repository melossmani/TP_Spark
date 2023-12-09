**Sommaire**

[TOC]


## Tests de _Spark_, avec la librairie _pyspark_

Nous allons ici faire fonctionner à nouveau l'algorithme de comptage de mots, mais cette fois-ci rédigé avec _pyspsark_, la librairie _Python_ qui permet de programmer avec _Spark_.

----
### Relancer le cluster

Il vous faut, dans un premier temps, relancer le cluster que nous avions installé pour le TP _Hadoop map-reduce_, avec son _Namenode_ et ses deux _Datanodes_. Tout d'abord, lancer le logiciel _Docker Desktop_ (pour lancer les _daemon Docker_). Puis, dans un premier _Terminal_, tapez :
```bash
docker start hadoop-master hadoop-slave1 hadoop-slave2
```
Puis entrez dans le _bash_ du _Namenode_ :
```bash
docker exec -it hadoop-master bash
```
Lancez alors le _daemon hadoop_ :
```bash
./start-hadoop.sh
```
Vérifiez alors que _HDFS_ est bien monté, avec la commande :
```bash
hadoop fs -ls
```

----
### _wordcount_ en Spark

Entrez dans le répertoire _wordcount_, listez les fichiers contenus dans ce répertoire :
```bash
cd wordcount
ls
```
Vous voyez apparaître les scripts python _Hadoop_ du [TP2](./TP2). Nous allons maintenant importer le même programme mais rédigé en _pyspark_. 

a. **Si vous avez conservé les fichiers sur votre DD** :

- Ouvrez un _Terminal_ et rendez-vous dans votre répertoire local (ce répertoire s'appelle : `TP_BigData_ECL`). 
- Mettez à jour ce dépôt avec la commande
```bash
git pull
```

b. **Si vous avez supprimé les fichiers de votre DD** :

- Depuis un second _Terminal_, dans un répertoire temporaire, exécutez 
```bash
git clone https://gitlab.ec-lyon.fr/sderrode/TP_BigData_ECL.git
```

- Copiez le fichier déposé dans le sous-répertoire _TP\_BigData\_ECL/TP\_Spark/wordcount_ sur le _Namenode_ à l'aide de la commande
```bash
cd TP_BigData_ECL/TP_Spark/wordcount
docker cp PySpark_wc.py hadoop-master:/root/wordcount
```

- Revenez au premier _Terminal_, et vérifiez que le fichier est là où il est attendu !


Avant de lancer le script, il convient de vérifier que le répertoire _sortie_ n'existe pas déjà sous _HDFS_. Pour faire cela, on tente de l'effacer (qu'il existe ou non, c'est plus sûr et plus rapide !) :
```bash
hadoop fs -rm -r -f sortie
```

Ensuite, lancez le programme de comptage de mots sur le livre _dracula_, en local, avec 2 cœurs de votre processeur : 
```bash
spark-submit --deploy-mode client --master local[2] PySpark_wc.py input/dracula
```
Le _Job_ est exécuté localement, sur le client, en exploitant 2 _threads_. Le répertoire de sortie est fixé dans le programme _Python_. Le mode `--deploy-mode cluster` permet d’exécuter le programme pilote sur le cluster _Hadoop_ (mode principalement utilisé en phase de production, et non en phase de mise au point). Dans ce cas, il vous faut aussi préciser l'option `--master yarn` (puisque notre cluster est géré par _Hadoop Yarn_, où _Yarn_ est le nom du moteur _Hadoop_). __Attention__ Je n'ai pas réussi de mon côté à faire fonctionner le programme dans ce mode _cluster_. Sans doute un pb de configuration du container _Docker_!

Pour vérifier le résultat, scruter le contenu du répertoire _sortie_ sous _HDFS_ :
```bash
hadoop fs -ls sortie
```
et le contenu des deux fichiers de sortie
```bash
hadoop fs -text sortie/part-00000
hadoop fs -text sortie/part-00001
```

**Travail à faire** Faites évoluer la version précédente de telle manière que l'on ne garde que les mots qui apparaissent dans le texte au moins _X_ fois, la valeur de _X_ étant fixée par un argument supplémentaire lors de l’appel à `spark-submit`. Par exemple :
```bash
spark-submit --deploy-mode client --master local[2] PySpark_wc.py input/dracula 1000
```

----
### Tester les scripts vus en cours

D'abord, créez un nouveau répertoire, à la racine de votre compte Linux, et déplacez-vous dedans :
```bash
cd ..
mkdir pyspark
cd pyspark
```

Dans le second _Terminal_, rapatriez l'ensemble des scripts _PySpark_ex*.py_ du répertoire _TP_BigData_ECL/TP_Spark/scripts_ dans le répertoire que nous venons de créer :
```bash
for f in PySpark_ex*.py; do docker cp $f hadoop-master:/root/pyspark; done
```
Cette boucle ne fonctionnera peut-être pas sous _Windows_ : faites alors le transfert fichier par fichier.

Rapatriez également le programme qui donne une approximation de _pi_ ainsi que le fichier _baby_names_2013.csv_ (utilisé par _PySpark_exemple5.py_) :
```bash
docker cp PySpark_Pi.py hadoop-master:/root/pyspark
docker cp baby_names_2013.csv hadoop-master:/root/pyspark
```
N'oubliez pas, si nécessaire, d'utiliser `dos2unix...`.

Vous pouvez ainsi tester les scripts vus en cours, par exemple :
```bash
spark-submit --deploy-mode client --master local[2] PySpark_Pi.py
```
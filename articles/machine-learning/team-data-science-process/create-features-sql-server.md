---
title: Créer des fonctionnalités dans SQL Server à l’aide de SQL et Python – Team Data Science Process
description: Générez des fonctionnalités pour les données stockées dans une machine virtuelle SQL Server sur Azure à l’aide de SQL et Python (étape incluse dans le processus Team Data Science Process).
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/21/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 2d01b74e7db275f4b2e3933415bbae40911b114b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60399287"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Créer des fonctionnalités pour les données dans SQL Server à l’aide de SQL et Python
Ce document montre comment générer des fonctionnalités pour des données stockées dans une machine virtuelle SQL Server sur Azure qui aident les algorithmes à apprendre efficacement à partir des données. Vous pouvez utiliser SQL ou un langage de programmation comme Python pour accomplir cette tâche. Les deux approches sont décrites ici.

Cette tâche est une étape du [processus TDSP (Team Data Science Process)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

> [!NOTE]
> Pour découvrir un exemple pratique, vous pouvez consulter le [jeu de données NYC Taxi](https://www.andresmh.com/nyctaxitrips/) et vous reporter au bloc-notes IPython intitulé [NYC Data wrangling using IPython Notebook and SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) (Retraitement des données de New-York City à l’aide de Notebook IPython et de SQL Server) pour connaître la procédure pas à pas.
> 
> 

## <a name="prerequisites"></a>Prérequis
Cet article suppose que vous avez :

* Créé un compte de stockage Azure. Si vous avez besoin d'aide, consultez [Créer un compte Azure Storage](../../storage/common/storage-quickstart-create-account.md)
* Stocké vos données dans SQL Server. Si ce n’est pas le cas, consultez [Déplacement de données vers une base de données SQL Azure pour Azure Machine Learning](move-sql-azure.md) pour obtenir des instructions sur la façon d’y déplacer des données.

## <a name="sql-featuregen"></a>Génération de fonctionnalités avec SQL
Dans cette section, nous décrivons plusieurs manières de générer des fonctionnalités via SQL :  

1. [Génération de fonctionnalités utilisant des décomptes](#sql-countfeature)
2. [Génération de caractéristiques de compartimentage](#sql-binningfeature)
3. [Déploiement des caractéristiques à partir d’une seule colonne](#sql-featurerollout)

> [!NOTE]
> Une fois que vous avez généré des fonctionnalités supplémentaires, vous pouvez soit les ajouter sous forme de colonnes à la table existante, soit créer une autre table avec les fonctionnalités supplémentaires et la clé primaire que vous pouvez joindre à la table d’origine.
> 
> 

### <a name="sql-countfeature"></a>Génération de fonctionnalités utilisant des décomptes
Ce document décrit deux manières de générer des fonctionnalités utilisant des décomptes. La première méthode a recours à une somme conditionnelle, tandis que la seconde méthode utilise la clause « where ». Vous pouvez ensuite associer ces dernières à la table d’origine (à l’aide des colonnes de clé primaire) pour disposer de fonctionnalités de décompte parallèlement aux données d’origine.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="sql-binningfeature"></a>Génération de caractéristiques de compartimentage
L’exemple ci-dessous illustre comment générer des fonctionnalités compartimentées en divisant (à l’aide de 5 emplacements) une colonne numérique qui peut être plutôt utilisée sous la forme d’une fonctionnalité :

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Déploiement des caractéristiques à partir d’une seule colonne
Dans cette section, nous décrivons comment déployer une seule colonne dans une table afin de générer des fonctionnalités supplémentaires. Cet exemple présuppose l’existence d’une colonne de latitude ou de longitude dans la table à partir de laquelle vous essayez de générer des fonctionnalités.

Voici une brève introduction relative aux données de latitude/longitude (reposant sur les informations du site stackoverflow `https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Voici quelques informations utiles à connaître sur les données de localisation avant de créer des fonctionnalités sur le terrain :

* Le signe indique si nous nous trouvons au nord, au sud, à l’est ou à l’ouest.
* Un chiffre non nul de centaines indique la longitude, pas la latitude.
* Le chiffre des dizaines équivaut à environ 1 000 kilomètres. Il fournit des informations utiles sur le continent ou l’océan dans lequel nous nous trouvons.
* Le chiffre des unités (un degré décimal) équivaut à 111 kilomètres maximum (60 milles marins, soit environ 69 milles terrestres). Il indique approximativement le département de grande superficie ou le pays dans lequel nous sommes.
* La première décimale équivaut à 11,1 km maximum : elle permet de distinguer la position d’une grande ville de celle d’une autre grande localité voisine.
* La deuxième décimale équivaut à 1,1 km maximum : elle permet de différencier un village du suivant.
* La troisième décimale équivaut à 110 m maximum : elle permet d’identifier un domaine agricole ou un campus universitaire de grande taille.
* La quatrième décimale correspond à 11 m maximum : elle permet d’identifier une parcelle de terre. Cette information est comparable à la précision classique d’un appareil GPS non corrigé sans aucune interférence.
* La cinquième décimale équivaut à 1,1 m maximum : elle permet de distinguer un arbre d’un autre. Un tel niveau de précision sur les appareils GPS commerciaux ne peut être atteint qu’au moyen d’une correction différentielle.
* La sixième décimale équivaut à 0,11 m maximum : vous pouvez notamment l’utiliser pour représenter des structures en détail, pour concevoir des plans d’aménagement paysager et pour construire des routes. Elle devrait se révéler amplement suffisante pour assurer le suivi des mouvements des glaciers et des rivières. L’obtention d’une telle précision sur un GPS nécessite l’emploi de mesures rigoureuses, telles qu’une correction différentielle.

Vous pouvez implémenter les informations de localisation en les répartissant par région, par emplacement et par ville. Notez qu’il est également possible d’appeler un point de terminaison REST, tel que l’API Bing Cartes disponible à l’adresse `https://msdn.microsoft.com/library/ff701710.aspx` , pour obtenir les informations de région/secteur.

    select
        <location_columnname>
        ,round(<location_columnname>,0) as l1        
        ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
        ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
        ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
        ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
        ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
        ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
    from <tablename>

Vous pouvez en outre exploiter ces fonctionnalités de localisation pour générer d’autres fonctionnalités utilisant des décomptes comme décrit précédemment.

> [!TIP]
> Vous pouvez insérer les enregistrements par programmation en utilisant le langage de votre choix. Vous devrez peut-être insérer les données en blocs pour améliorer l'efficacité de l'écriture. [Voici un exemple montrant comment faire à l’aide de la commande pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Une autre solution consiste à insérer les données dans la base de données à l’aide de l’ [utilitaire BCP](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="sql-aml"></a>Connexion à Azure Machine Learning
La fonctionnalité que vous venez de générer peut être ajoutée sous la forme d’une colonne à une table existante ou stockée dans une nouvelle table et associée à la table d’origine pour l’apprentissage automatique. Vous pouvez générer des fonctionnalités ou y accéder si elles sont déjà créées à l’aide du module [Importer des données](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) dans Azure Machine Learning comme expliqué ci-dessous :

![Lecteurs Azure ML](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="python"></a>Utilisation d’un langage de programmation tel que Python
L’utilisation de Python pour générer des fonctionnalités quand les données sont stockées dans SQL Server est comparable au traitement des données dans l’objet blob Azure à l’aide de Python. Pour obtenir une comparaison, voir [Traiter les données Azure Blob dans votre environnement de science des données](data-blob.md). Chargez les informations de la base de données dans une trame de données pandas pour poursuivre le traitement. Le processus de connexion à la base de données et le chargement des données dans la trame de données sont décrits dans cette section.

Le format de chaîne de connexion ci-après vous permet de vous connecter à une base de données SQL Server à partir de Python à l’aide de pyodbc (en remplaçant les variables servername, dbname, username et password par les valeurs qui vous correspondent) :

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

La [bibliothèque Pandas](https://pandas.pydata.org/) de Python offre un ensemble élaboré de structures de données et d’outils d’analyse des données pour la manipulation des données dans le cadre d’une programmation en Python. Le code suivant lit les résultats renvoyés par une base de données SQL Server dans une trame de données Pandas :

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Vous pouvez à présent utiliser la trame de données Pandas comme décrit dans la rubrique [Créer des fonctionnalités pour les données de stockage d’objets blob Azure à l’aide de Pandas](create-features-blob.md).


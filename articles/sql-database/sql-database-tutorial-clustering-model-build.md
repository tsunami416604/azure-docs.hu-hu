---
title: 'Oktatóanyag: Az R fürtözési modell létrehozása'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: A háromrészes oktatóanyag-sorozat második részében a K-közép modell végrehajtásához az Azure SQL Database, Machine Learning Services (előzetes verzió) az R fürtszolgáltatás fog létrehozni.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 05/17/2019
ms.openlocfilehash: 12738b63be92420c5f3afea6c133522cbd97f849
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66419761"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Oktatóanyag: Az Azure SQL Database, Machine Learning Services (előzetes verzió) az R fürtözési modell létrehozása

A háromrészes oktatóanyag-sorozat második részében a K-közép modell végrehajtásához az Azure SQL Database, Machine Learning Services (előzetes verzió) az R fürtszolgáltatás fog létrehozni.

Ebből a cikkből megtudhatja, hogyan lehet:

> [!div class="checklist"]
> * A K-közép-algoritmus a fürtök számának meghatározása
> * Hajtsa végre a fürtszolgáltatás
> * Az eredmények elemzésével

A [részében](sql-database-tutorial-clustering-model-prepare-data.md), megtanulta, hogyan készíti elő az adatokat a fürtszolgáltatás R. végrehajtásához egy Azure SQL database

A [harmadik részt](sql-database-tutorial-clustering-model-deploy.md), megtudhatja, hogyan hozzon létre egy tárolt eljárást az Azure SQL-adatbázis, amely a fürtszolgáltatás új adatok alapján hajthat végre.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Ez az oktatóanyag második része feltételezi, hogy befejezte [ **részében** ](sql-database-tutorial-clustering-model-prepare-data.md) és előfeltételei.

## <a name="define-the-number-of-clusters"></a>A fürtök számának meghatározása

A fürtben az ügyféladatokat, szeretné használni a **K-közép** fürtözési algoritmust, az egyik legegyszerűbb és jól ismert módszer csoportosítási adatok.
További információ a K-közép [K-közép-csoportosítási algoritmus egy teljes körű útmutatót](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

Az algoritmus fogadja el a két bemenet: Magukhoz az adatokhoz, és a egy előre meghatározott számú "*k*" fürtök létrehozásához számát jelöli.
A kimenet *k* -fürtöket a bemeneti adatok particionálása a fürtök között.

Az algoritmus a fürtök számának meghatározásához használja a rajzot a csoportok sum által kinyert fürtök száma a négyzetes belül. A fürtök használandó megfelelő számú ív vagy az ábrázolást "szögletes" van.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Töréspont graph](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

A gráf alapján, tűnik *k = 4* próbálja ki a megfelelő érték lesz. Hogy *k* érték csoportosítja négy csoportosít azokat az ügyfeleket.

## <a name="perform-clustering"></a>Hajtsa végre a fürtszolgáltatás

A következő R-parancsfájl, a függvény használatával **rxKmeans**, azaz a K-közép-függvény a RevoScaleR csomagban.

```r
# Output table to hold the customer group mappings.
# This is a table where the cluster mappings will be saved in the database.
return_cluster = RxSqlServerData(table = "return_cluster", connectionString = connStr);
# Set the seed for the random number generator for predictability
set.seed(10);
# Generate clusters using rxKmeans and output key / cluster to a table in SQL database
# called return_cluster
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters=4,
                   outFile=return_cluster,
                   outColName="cluster",
                   extraVarsToWrite=c("customer"),
                   overwrite=TRUE);

# Read the customer returns cluster table from the database
customer_cluster <- rxDataStep(return_cluster);
```

## <a name="analyze-the-results"></a>Az eredmények elemzésével

Most, hogy a Fürtszolgáltatás használatával a K-közép hajtotta végre, a következő lépéseként elemezheti az eredményt, és tekintse meg, ha bármely döntéstámogató információkat talál.

A **clust** objektum tartalmazza a K-közép-fürtszolgáltatás eredményét.

```r
#Look at the clustering details to analyze results
clust
```

```results
Call:
rxKmeans(formula = ~orderRatio + itemsRatio + monetaryRatio + 
    frequency, data = customer_returns, outFile = return_cluster, 
    outColName = "cluster", extraVarsToWrite = c("customer"), 
    overwrite = TRUE, numClusters = 4)
Data: customer_returns
Number of valid observations: 37336
Number of missing observations: 0 
Clustering algorithm:  

K-means clustering with 4 clusters of sizes 31675, 671, 2851, 2139
Cluster means:
   orderRatio   itemsRatio monetaryRatio frequency
1 0.000000000 0.0000000000    0.00000000  0.000000
2 0.007451565 0.0000000000    0.04449653  4.271237
3 1.008067345 0.2707821817    0.49515232  1.031568
4 0.000000000 0.0004675082    0.10858272  1.186068
Within cluster sum of squares by cluster:
         1          2          3          4
    0.0000  1329.0160 18561.3157   363.2188
```

A négy fürt azt jelenti, hogy a változók használata a megadott meghatározott [részében](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers):

* *orderRatio* visszatérési rendelés arány (megrendelések teljes számát és a visszaadott részlegesen vagy teljesen megrendelések teljes száma) =
* *itemsRatio* visszatérési elem arány (és a megvásárolt elemek száma a visszaadott elemek teljes száma) =
* *monetaryRatio* visszatérési összeg arány (díja vásárolt tárterülethez visszaadott elemek) =
* *gyakoriság* = visszatérési gyakorisága

Adatbányászat segítségével gyakran a K-közép igényel további elemzési eredmények és jobb megértése érdekében egyes fürtök további lépéseket, de azt is meg néhány jó.
Az alábbiakban néhány olyan módon, meg tudta értelmezni az eredményeket:

* 1 (a legnagyobb fürt) fürt úgy tűnik, hogy az ügyfelek, amelyek nem aktív csoport (összes értékek: 0).
* Fürt 3 úgy tűnik, hogy egy csoportot, amely vesz fel visszatérési viselkedése tekintetében.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

***Ha nem fog ez az oktatóanyag folytatásához***, törölje a tpcxbb_1gb adatbázist az Azure SQL Database-kiszolgáló.

Az Azure Portalról kövesse az alábbi lépéseket:

1. A bal oldali menüben az Azure Portalon, válassza ki a **összes erőforrás** vagy **SQL-adatbázisok**.
1. Az a **Szűrés név alapján...**  írja be a következőt **tpcxbb_1gb**, és válassza ki előfizetését.
1. Válassza ki a **tpcxbb_1gb** adatbázis.
1. Az **Áttekintés** oldalon válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések

Az oktatóanyag-sorozat második részében elvégezte ezeket a lépéseket:

* A K-közép-algoritmus a fürtök számának meghatározása
* Hajtsa végre a fürtszolgáltatás
* Az eredmények elemzésével

A létrehozott machine learning-modellek üzembe helyezéséhez kövesse az oktatóanyag-sorozat harmadik részében:

> [!div class="nextstepaction"]
> [Oktatóanyag: Modell üzembe helyezése fürtözéssel az R az Azure SQL Database, Machine Learning Services (előzetes verzió)](sql-database-tutorial-clustering-model-deploy.md)
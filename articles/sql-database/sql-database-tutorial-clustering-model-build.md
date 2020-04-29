---
title: 'Oktatóanyag: fürtszolgáltatási modell létrehozása az R-ben'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: A jelen háromrészes oktatóanyag-sorozat második részében egy K-Mean modellt fog kiépíteni az R-ben a Azure SQL Database Machine Learning Services (előzetes verzió) használatával történő fürtözés végrehajtásához.
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
ms.date: 07/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ebea6117420ee6de67025dfd4cfba71e905cb9ec
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81453097"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Oktatóanyag: fürtszolgáltatási modell létrehozása az R-ben Azure SQL Database Machine Learning Services (előzetes verzió)

A háromrészes oktatóanyag-sorozat második részében egy K-Mean modellt fog létrehozni az R-ben a fürtözés végrehajtásához. A sorozat következő részében ezt a modellt egy Azure SQL Database Machine Learning Servicest (előzetes verzió) tartalmazó SQL-adatbázisban fogja telepíteni.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

> [!div class="checklist"]
> * A K-means algoritmushoz tartozó fürtök számának meghatározása
> * Fürtözés végrehajtása
> * Az eredmények elemzése

Az első [részben](sql-database-tutorial-clustering-model-prepare-data.md)megtanulta, hogyan készítse elő az adatok előkészítését egy Azure SQL Database-adatbázisból a fürtözés végrehajtásához.

A [harmadik részből](sql-database-tutorial-clustering-model-deploy.md)megtudhatja, hogyan hozhat létre egy tárolt eljárást egy olyan Azure SQL Database-adatbázisban, amely új adatok alapján képes a fürtözésre az R-ben.

## <a name="prerequisites"></a>Előfeltételek

* Az oktatóanyag második része feltételezi, hogy elvégezte az [**első részt**](sql-database-tutorial-clustering-model-prepare-data.md) , és annak előfeltételeit.

## <a name="define-the-number-of-clusters"></a>A fürtök számának meghatározása

Az ügyféladatok fürtözéséhez a **K-means** fürtszolgáltatási algoritmust kell használnia, amely az adatcsoportosítás legegyszerűbb és legismertebb módja.
A K-vel kapcsolatos további információkért tekintse meg a " [k" teljes útmutatóját a fürtözési algoritmust](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

Az algoritmus két bemenetet fogad el: az adatokat, valamint egy előre definiált "*k*" számot, amely a létrehozott fürtök számát jelöli.
A kimenet olyan *k* -fürtök, amelyekben a bemeneti adatok particionálva vannak a fürtök között.

A használandó algoritmushoz tartozó fürtök számának meghatározásához használja a négyzetek összegét a kinyert fürtök száma alapján. A használandó fürtök megfelelő száma a mintaterület kanyarjában vagy a "könyök".

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Könyök gráf](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

A gráf alapján úgy tűnik, hogy a (z) *k = 4* jó érték lenne a kipróbáláshoz. Ez a *k* érték négy fürtbe csoportosítja az ügyfeleket.

## <a name="perform-clustering"></a>Fürtözés végrehajtása

A következő R-szkriptben használja a **rxKmeans**függvényt, amely a RevoScaleR csomag K-Mean függvénye.

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

## <a name="analyze-the-results"></a>Az eredmények elemzése

Most, hogy elvégezte a fürtözést a (K) használatával, a következő lépés az eredmény elemzése, és annak megállapítása, hogy megtalálható-e a gyakorlatban használható információ.

A **clust** objektum a K-means fürtözés eredményét tartalmazza.

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

A négy fürt a következő [részben](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers)meghatározott változók használatával van megadva:

* *orderRatio* = visszaadott sorrendi arány (a megrendelések teljes száma részben vagy teljes mértékben visszaadva a megrendelések teljes száma szerint)
* *itemsRatio* = visszaadott elem aránya (a visszaadott elemek teljes száma és a megvásárolt elemek száma)
* *monetaryRatio* = visszaadott összeg aránya (a visszaadott elemek teljes pénzügyi mennyisége és a megvásárolt mennyiség)
* *gyakoriság* = visszatérési gyakoriság

A K-t használó adatbányászat gyakran megköveteli az eredmények további elemzését, valamint az egyes fürtök jobb megértéséhez szükséges további lépéseket, de a megfelelő érdeklődőket is biztosít.
Az alábbi néhány módon értelmezheti ezeket az eredményeket:

* Az 1. fürt (a legnagyobb fürt) úgy tűnik, hogy olyan ügyfelek csoportja, amelyek nem aktívak (az összes érték nulla).
* A 3. fürt úgy tűnik, hogy egy olyan csoport, amely a visszatérési viselkedés szempontjából kiemelkedik.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

***Ha nem folytatja ezt az oktatóanyagot***, törölje a tpcxbb_1gb-adatbázist a Azure SQL Database-kiszolgálóról.

A Azure Portal hajtsa végre az alábbi lépéseket:

1. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** vagy **SQL-adatbázis**lehetőséget.
1. A **szűrés név szerint...** mezőbe írja be **tpcxbb_1gb**, majd válassza ki az előfizetését.
1. Válassza ki a **tpcxbb_1gb** -adatbázist.
1. Az **Áttekintés** oldalon válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések

Az oktatóanyag-sorozat második részében a következő lépéseket végezte el:

* A K-means algoritmushoz tartozó fürtök számának meghatározása
* Fürtözés végrehajtása
* Az eredmények elemzése

A létrehozott Machine learning-modell üzembe helyezéséhez kövesse az oktatóanyag-sorozat harmadik részét:

> [!div class="nextstepaction"]
> [Oktatóanyag: fürtszolgáltatási modell üzembe helyezése az R-ben Azure SQL Database Machine Learning Services (előzetes verzió)](sql-database-tutorial-clustering-model-deploy.md)
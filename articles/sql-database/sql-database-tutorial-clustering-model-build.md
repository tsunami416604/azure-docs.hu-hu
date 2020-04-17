---
title: 'Oktatóanyag: Fürtözési modell létrehozása r-ben'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: A háromrészes oktatóanyag-sorozat második részében egy K-Means modellt hozhat létre az R-ben az Azure SQL Database Machine Learning Services (előzetes verzió) fürtözésének elvégzéséhez.
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
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453097"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Oktatóanyag: Fürtözési modell létrehozása r-ben az Azure SQL Database Machine Learning Services szolgáltatással (előzetes verzió)

A három részes oktatóanyag-sorozat második részében egy K-Means modellt hozhat létre az R-ben a fürtözés végrehajtásához. A sorozat következő részében ezt a modellt egy SQL-adatbázisban telepíti az Azure SQL Database Machine Learning Services használatával (előzetes verzió).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Ebben a cikkben megtudhatja, hogyan:

> [!div class="checklist"]
> * A K-Means algoritmus fürtjaiszámának meghatározása
> * Fürtözés végrehajtása
> * Az eredmények elemzése

Az [első részben](sql-database-tutorial-clustering-model-prepare-data.md)megtanulta, hogyan készítse elő az adatokat egy Azure SQL-adatbázis fürtözés végrehajtásához.

[A harmadik részben](sql-database-tutorial-clustering-model-deploy.md)megtudhatja, hogyan hozhat létre egy tárolt eljárást egy Azure SQL-adatbázisban, amely az új adatok alapján r-ben fürtözést végezhet.

## <a name="prerequisites"></a>Előfeltételek

* Az oktatóanyag második része feltételezi, hogy befejezte az [**első részt**](sql-database-tutorial-clustering-model-prepare-data.md) és annak előfeltételeit.

## <a name="define-the-number-of-clusters"></a>A fürtök számának meghatározása

Az ügyféladatok fürtözéséhez a **K-Means** fürtözési algoritmust fogja használni, amely az adatok csoportosításának egyik legegyszerűbb és legismertebb módja.
A K-Means-ről a [K-means fürtözési algoritmus teljes útmutatójában](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html)olvashat bővebben.

Az algoritmus két bemenetet fogad el: magát az adatot, és egy előre definiált "*k*" számot, amely a létrehozandó fürtök számát jelöli.
A kimenet *k* fürtök a bemeneti adatok particionált a fürtök között.

Az algoritmus által használandó fürtök számának meghatározásához használja a négyzetek csoporton belüli összegének nyomtatását a kivont fürtök száma szerint. A megfelelő számú klaszterek használni a kanyarban vagy "könyök" a telek.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Könyök grafikon](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

A grafikon alapján úgy néz ki, hogy *k = 4* jó érték lenne kipróbálni. Ez a *k* érték négy fürtre csoportosítja az ügyfeleket.

## <a name="perform-clustering"></a>Fürtözés végrehajtása

A következő R-parancsfájlban az **rxKmeans**függvényt fogja használni, amely a RevoScaleR csomag K-Means függvénye.

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

Most, hogy a Fürtözést a K-Means használatával végezte el, a következő lépés az eredmény elemzése, és nézze meg, hogy talál-e végrehajtható információt.

A **clust** objektum tartalmazza a K-Means fürtözés eredményeit.

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

A négy halmazeszköz az [első részben](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers)meghatározott változók alapján adható meg:

* *orderRatio* = visszárurendelés-arány (a rendelések teljes száma részben vagy teljesen visszalett küldve a rendelések teljes számával szemben)
* *itemsRatio* = visszárucikk arány (a visszaküldött cikkek teljes száma a beszerzett cikkek számához képest)
* *monetárisRatio* = hozamösszeg hányadosa (a visszaküldött tételek teljes pénzbeli összege a megvásárolt összeggel szemben)
* *frekvencia* = visszatérési frekvencia

A K-Means használatával történő adatbányászat gyakran további elemzést igényel az eredményekről, és további lépéseket kell tenni az egyes fürtök jobb megértéséhez, de jó érdeklődőket nyújthat.
Íme néhány módszer, amelyekkel értelmezheti ezeket az eredményeket:

* Az 1-es fürt (a legnagyobb fürt) úgy tűnik, hogy az ügyfelek egy csoportja, amely nem aktív (minden érték nulla).
* Cluster 3 úgy tűnik, hogy egy csoport, amely kiemelkedik a visszatérési viselkedés.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

***Ha nem folytatja ezt az oktatóanyagot,*** törölje a tpcxbb_1gb adatbázist az Azure SQL Database-kiszolgálóról.

Az Azure Portalon kövesse az alábbi lépéseket:

1. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** vagy **SQL-adatbázis lehetőséget.**
1. A **Szűrés név szerint...** mezőbe írja be **tpcxbb_1gb,** és válassza ki az előfizetést.
1. Válassza ki a **tpcxbb_1gb** adatbázist.
1. Az **Áttekintés** oldalon válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések

Az oktatóanyag-sorozat második részében az alábbi lépéseket hajtotta végre:

* A K-Means algoritmus fürtjaiszámának meghatározása
* Fürtözés végrehajtása
* Az eredmények elemzése

A létrehozott gépi tanulási modell üzembe helyezéséhez kövesse az oktatóanyag-sorozat harmadik részét:

> [!div class="nextstepaction"]
> [Oktatóanyag: Fürthálózati modell üzembe helyezése az R-ben az Azure SQL Database Machine Learning Services szolgáltatással (előzetes verzió)](sql-database-tutorial-clustering-model-deploy.md)
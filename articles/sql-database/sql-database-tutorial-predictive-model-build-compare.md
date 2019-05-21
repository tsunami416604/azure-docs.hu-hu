---
title: 'Oktatóanyag: Betanítása és prediktív modelleket a R összehasonlítása'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: A háromrészes oktatóanyag-sorozat második részében fog két prediktív modelleket hozhat létre az Azure SQL Database, Machine Learning Services (előzetes verzió) az R, és válassza ki a legnagyobb pontosságú modellt.
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
ms.date: 05/02/2019
ms.openlocfilehash: 3d336d6a53b6d234048c56d8492d278bef6fed64
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957609"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Oktatóanyag: Egy prediktív modell létrehozásához az Azure SQL Database, Machine Learning Services (előzetes verzió) az R

A háromrészes oktatóanyag-sorozat második részében fog két prediktív modelleket hozhat létre az Azure SQL Database, Machine Learning Services (előzetes verzió) az R, és válassza ki a legnagyobb pontosságú modellt.

Ebből a cikkből megtudhatja, hogyan lehet:

> [!div class="checklist"]
> * Két gépi tanulási modellek tanítása
> * Mindkét modellből előrejelzéseket
> * Hasonlítsa össze az eredményeket a legpontosabb modell kiválasztása

A [részében](sql-database-tutorial-predictive-model-prepare-data.md), útmutatóból megtudhatta, hogyan importálhatja a mintaadatbázis egy Azure SQL database-be, és ezután előkészíti az adatokat az r segítségével prediktív modell betanításához használandó

A [harmadik részt](sql-database-tutorial-predictive-model-deploy.md), megtudhatja, hogyan tárolja a model adatbázisban, és majd hozzon létre egy tárolt eljárást, amely megkönnyíti az új adatok alapján előrejelzéseket.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Ez az oktatóanyag második része feltételezi, hogy befejezte [ **részében** ](sql-database-tutorial-predictive-model-prepare-data.md) és előfeltételei.

## <a name="train-two-models"></a>Két modelleket taníthat be

A legjobb modellt a télisport bérleti adatok megkereséséhez (lineáris regresszió és döntési fa) két különböző modelleket hozhat létre, és tekintse meg, melyik pontosabb előrejelzésére. Az adathalmaz használni kívánt `rentaldata` , hogy ez a sorozat első részében létrehozott.

```r
#First, split the dataset into two different sets:
# one for training the model and the other for validating it
train_data = rentaldata[rentaldata$Year < 2015,];
test_data  = rentaldata[rentaldata$Year == 2015,];

#Use the RentalCount column to check the quality of the prediction against actual values
actual_counts <- test_data$RentalCount;

#Model 1: Use rxLinMod to create a linear regression model, trained with the training data set
model_linmod <- rxLinMod(RentalCount ~  Month + Day + WeekDay + Snow + Holiday, data = train_data);

#Model 2: Use rxDTree to create a decision tree model, trained with the training data set
model_dtree  <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = train_data);
```

## <a name="make-predictions-from-both-models"></a>Mindkét modellből előrejelzéseket

A predict a bérleti száma minden egyes betanított modell használatával a predict függvény használható.

```r
#Use both models to make predictions using the test data set.
predict_linmod <- rxPredict(model_linmod, test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

predict_dtree  <- rxPredict(model_dtree,  test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

#To verify it worked, look at the top rows of the two prediction data sets.
head(predict_linmod);
head(predict_dtree);
```

```results
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1         27.45858          42       2     11     4      0       0
2        387.29344         360       3     29     1      0       0
3         16.37349          20       4     22     4      0       0
4         31.07058          42       3      6     6      0       0
5        463.97263         405       2     28     7      1       0
6        102.21695          38       1     12     2      1       0
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1          40.0000          42       2     11     4      0       0
2         332.5714         360       3     29     1      0       0
3          27.7500          20       4     22     4      0       0
4          34.2500          42       3      6     6      0       0
5         645.7059         405       2     28     7      1       0
6          40.0000          38       1     12     2      1       0
```

## <a name="compare-the-results"></a>Az eredmények összehasonlítása

Most meg szeretné tekinteni a modellek révén a legjobb előrejelzéseket. Ehhez egy gyorsan és egyszerűen elvégezhető az alapszintű ábrázolási függvényt használni a betanítási adatok a tényleges értékek és az előre jelzett értékek közötti különbség megtekintéséhez.

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![A két modell összehasonlítása](./media/sql-database-tutorial-predictive-model-build-compare/compare-models.png)

Úgy tűnik, a döntési fa modell, a két modell pontosabb.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem ez az oktatóanyag folytatásához, törölje a oktatóanyagaiban tutorialdb adatbázist adatbázist az Azure SQL Database-kiszolgáló.

Az Azure Portalról kövesse az alábbi lépéseket:

1. A bal oldali menüben az Azure Portalon, válassza ki a **összes erőforrás** vagy **SQL-adatbázisok**.
1. Az a **Szűrés név alapján...**  írja be a következőt **oktatóanyagaiban tutorialdb adatbázist**, és válassza ki előfizetését.
1. Válassza ki a oktatóanyagaiban tutorialdb adatbázist adatbázist.
1. Az **Áttekintés** oldalon válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések

Az oktatóanyag-sorozat második részében elvégezte ezeket a lépéseket:

* Két gépi tanulási modellek tanítása
* Mindkét modellből előrejelzéseket
* Hasonlítsa össze az eredményeket a legpontosabb modell kiválasztása

A létrehozott machine learning-modellek üzembe helyezéséhez kövesse az oktatóanyag-sorozat harmadik részében:

> [!div class="nextstepaction"]
> [Oktatóanyag: Telepített egy prediktív modellt az R az Azure SQL Database, Machine Learning Services (előzetes verzió)](sql-database-tutorial-predictive-model-deploy.md)

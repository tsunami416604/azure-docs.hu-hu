---
title: 'Bemutató: Vonat és hasonlítsa össze a prediktív modellek R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: A háromrészes oktatóanyag-sorozat második részében két prediktív modellt hozhat létre az R-ben az Azure SQL Database Machine Learning Services (előzetes verzió) használatával, majd kiválaszthatja a legpontosabb modellt.
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
ms.date: 07/26/2019
ms.openlocfilehash: 52f4347fa545df88f11878709b4061662d5faffe
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345755"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Oktatóanyag: Prediktív modell létrehozása az R-ben az Azure SQL Database Machine Learning Services szolgáltatással (előzetes verzió)

A háromrészes oktatóanyag-sorozat második részében két prediktív modellt hozhat létre az R-ben, és kiválaszthatja a legpontosabb modellt. A sorozat következő részében ezt a modellt egy SQL-adatbázisban telepíti az Azure SQL Database Machine Learning Services használatával (előzetes verzió).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Ebben a cikkben megtudhatja, hogyan:

> [!div class="checklist"]
> * Két gépi tanulási modell betanítása
> * Előrejelzések et készíthet mindkét modellből
> * Hasonlítsa össze az eredményeket, hogy kiválassza a legpontosabb modellt

Az [első részben](sql-database-tutorial-predictive-model-prepare-data.md)megtanulta, hogyan importálhat egy mintaadatbázist, majd előkészíti az adatokat az R prediktív modell betanításához.

A [harmadik részben](sql-database-tutorial-predictive-model-deploy.md)megtudhatja, hogyan tárolhatja a modellt egy adatbázisban, majd hozzon létre tárolt eljárásokat az első és a második részben kifejlesztett R-parancsfájlokból. A tárolt eljárások egy SQL-adatbázisban futnak, hogy új adatok alapján előrejelzéseket készítsenek.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Az oktatóanyag második része feltételezi, hogy befejezte az [**első részt**](sql-database-tutorial-predictive-model-prepare-data.md) és annak előfeltételeit.

## <a name="train-two-models"></a>Két modell betanítása

A síbérleti adatok legjobb modelljének megtalálásához hozzon létre két különböző modellt (lineáris regressziós és döntési fa), és nézze meg, melyiket jósolja meg pontosabban. A sorozat első részében létrehozott adatkeretet `rentaldata` fogja használni.

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

## <a name="make-predictions-from-both-models"></a>Előrejelzések et készíthet mindkét modellből

A predict funkció segítségével előre jelezheti a bérleti díjak száma az egyes betanított modellek használatával.

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

Most szeretné látni, hogy melyik modell adja a legjobb előrejelzéseket. Ennek gyors és egyszerű módja, ha egy alapszintű nyomtatási függvényt használ a betanítási adatok ban szereplő tényleges értékek és az előre jelzett értékek közötti különbség megtekintéséhez.

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![A két modell összehasonlítása](./media/sql-database-tutorial-predictive-model-build-compare/compare-models.png)

Úgy néz ki, mint a döntési fa modell a pontosabb a két modell.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja ezt az oktatóanyagot, törölje a TutorialDB adatbázist az Azure SQL Database-kiszolgálóról.

Az Azure Portalon kövesse az alábbi lépéseket:

1. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** vagy **SQL-adatbázis lehetőséget.**
1. A **Szűrés név szerint...** mezőbe írja be a **TutorialDB**mezőt, és válassza ki az előfizetést.
1. Válassza ki a TutorialDB adatbázist.
1. Az **Áttekintés** oldalon válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések

Az oktatóanyag-sorozat második részében az alábbi lépéseket hajtotta végre:

* Két gépi tanulási modell betanítása
* Előrejelzések et készíthet mindkét modellből
* Hasonlítsa össze az eredményeket, hogy kiválassza a legpontosabb modellt

A létrehozott gépi tanulási modell üzembe helyezéséhez kövesse az oktatóanyag-sorozat harmadik részét:

> [!div class="nextstepaction"]
> [Oktatóanyag: Prediktív modell üzembe helyezése az R-ben az Azure SQL Database Machine Learning Services szolgáltatással (előzetes verzió)](sql-database-tutorial-predictive-model-deploy.md)

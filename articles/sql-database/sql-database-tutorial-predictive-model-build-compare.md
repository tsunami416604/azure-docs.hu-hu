---
title: 'Oktatóanyag: prediktív modellek betanítása és összehasonlítása az R-ben'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: A jelen háromrészes oktatóanyag-sorozat második részében két prediktív modellt hoz létre az R-ben Azure SQL Database Machine Learning Services (előzetes verzió), majd kiválasztja a legpontosabb modellt.
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
ROBOTS: NOINDEX
ms.openlocfilehash: 0985b37280e3cd363ba1728a5ec33b0012611ab2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81452927"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Oktatóanyag: prediktív modell létrehozása az R-ben Azure SQL Database Machine Learning Services (előzetes verzió)

A háromrészes oktatóanyag-sorozat második részében két prediktív modellt hoz létre az R-ben, és kiválasztja a legpontosabb modellt. A sorozat következő részében ezt a modellt egy Azure SQL Database Machine Learning Servicest (előzetes verzió) tartalmazó SQL-adatbázisban fogja telepíteni.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

> [!div class="checklist"]
> * Két gépi tanulási modell betanítása
> * Előrejelzések készítése mindkét modellből
> * Hasonlítsa össze az eredményeket a legpontosabb modell kiválasztásához

Az első [részben](sql-database-tutorial-predictive-model-prepare-data.md)megtanulta, hogyan importálhat egy minta-adatbázist, majd hogyan készítheti elő a prediktív modell az R-ben való betanításához használni kívánt adatfeldolgozást.

A [harmadik részből](sql-database-tutorial-predictive-model-deploy.md)megtudhatja, hogyan tárolhatja a modellt egy adatbázisban, majd hogyan hozhat létre tárolt eljárásokat az 1. és a 2. részekben kifejlesztett R-parancsfájlok alapján. A tárolt eljárások egy SQL-adatbázisban futnak, így az előrejelzések az új adatértékek alapján lesznek elérhetők.

## <a name="prerequisites"></a>Előfeltételek

* Az oktatóanyag második része feltételezi, hogy elvégezte az [**első részt**](sql-database-tutorial-predictive-model-prepare-data.md) , és annak előfeltételeit.

## <a name="train-two-models"></a>Két modell betanítása

A Ski Rental-információk legjobb modelljének megkereséséhez hozzon létre két különböző modellt (lineáris regressziós és döntési fát), és nézze meg, melyik a pontosabb előrejelzés. A sorozat első részében létrehozott `rentaldata` adatkeretet fogja használni.

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

## <a name="make-predictions-from-both-models"></a>Előrejelzések készítése mindkét modellből

Az egyes betanított modellek használatával előre jelezheti a bérletek számát.

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

Most szeretné megtekinteni, hogy melyik modell a legjobb előrejelzéseket kínálja. Ennek gyors és egyszerű módja egy alapszintű ábrázolási függvény használata a betanítási adatok és az előre jelzett értékek tényleges értékei közötti különbség megtekintéséhez.

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![A két modell összehasonlítása](./media/sql-database-tutorial-predictive-model-build-compare/compare-models.png)

Úgy tűnik, a döntési famodell a két modell pontosabb példája.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja ezt az oktatóanyagot, törölje a oktatóanyagaiban-adatbázist a Azure SQL Database-kiszolgálóról.

A Azure Portal hajtsa végre az alábbi lépéseket:

1. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** vagy **SQL-adatbázis**lehetőséget.
1. A **szűrés név szerint...** mezőbe írja be a **oktatóanyagaiban**nevet, és válassza ki az előfizetését.
1. Válassza ki a oktatóanyagaiban-adatbázist.
1. Az **Áttekintés** oldalon válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések

Az oktatóanyag-sorozat második részében a következő lépéseket végezte el:

* Két gépi tanulási modell betanítása
* Előrejelzések készítése mindkét modellből
* Hasonlítsa össze az eredményeket a legpontosabb modell kiválasztásához

A létrehozott Machine learning-modell üzembe helyezéséhez kövesse az oktatóanyag-sorozat harmadik részét:

> [!div class="nextstepaction"]
> [Oktatóanyag: prediktív modell üzembe helyezése az R-ben Azure SQL Database Machine Learning Services (előzetes verzió)](sql-database-tutorial-predictive-model-deploy.md)

---
title: 'Oktatóanyag: Prediktív modell üzembe helyezése r-ben'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: A három részes oktatóanyag harmadik részében egy prediktív modellt telepít az R-ben az Azure SQL Database Machine Learning Services segítségével (előzetes verzió).
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
ms.openlocfilehash: 7779db053344f99238d38d5d49762730efbc5fc4
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346316"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Oktatóanyag: Prediktív modell üzembe helyezése az R-ben az Azure SQL Database Machine Learning Services szolgáltatással (előzetes verzió)

A három részes oktatóanyag harmadik részében egy R-ben kifejlesztett prediktív modellt telepít egy SQL-adatbázisba az Azure SQL Database Machine Learning Services (előzetes verzió) használatával.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Hozzon létre egy tárolt eljárást egy beágyazott R-parancsfájllal, amely előrejelzéseket készít a modell használatával. Mivel a modell végrehajtása az Azure SQL-adatbázisban, könnyen betanítható az adatbázisban tárolt adatok ellen.

Ebben a cikkben az első és második részben kifejlesztett R-parancsfájlok használatával megtudhatja, hogyan:

> [!div class="checklist"]
> * A gépi tanulási modellt létrehozó tárolt eljárás létrehozása
> * A modell tárolása adatbázistáblában
> * Olyan tárolt eljárás létrehozása, amely előrejelzéseket készít a modell használatával
> * A modell végrehajtása új adatokkal

Az [első részben](sql-database-tutorial-predictive-model-prepare-data.md)megtanulta, hogyan importálhat egy mintaadatbázist, majd előkészíti az adatokat az R prediktív modell betanításához.

A [második részben](sql-database-tutorial-predictive-model-build-compare.md)megtanulta, hogyan hozhat létre és tanítson be több gépi tanulási modellt az R-ben, majd válassza ki a legpontosabbat.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Az oktatóanyag-sorozat harmadik része feltételezi, hogy befejezte [**az első**](sql-database-tutorial-predictive-model-prepare-data.md) és a [**második részt.**](sql-database-tutorial-predictive-model-build-compare.md)

## <a name="create-a-stored-procedure-that-generates-the-model"></a>A modellt létrehozó tárolt eljárás létrehozása

Az oktatóanyag-sorozat második részében úgy döntött, hogy a döntési fa (dtree) modell a legpontosabb. Most a kifejlesztett R-parancsfájlok használatával hozzon`generate_rental_rx_model`létre egy tárolt eljárást ( ), amely beindítja és létrehozza a dtree modellt a RevoScaleR csomag rxDTree használatával.

Futtassa a következő parancsokat az Azure Data Stu-ban vagy az SSMS-ben.

```sql
-- Stored procedure that trains and generates an R model using the rental_data and a decision tree algorithm
DROP PROCEDURE IF EXISTS generate_rental_rx_model;
GO
CREATE PROCEDURE generate_rental_rx_model (@trained_model VARBINARY(max) OUTPUT)
AS
BEGIN
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

rental_train_data$Holiday <- factor(rental_train_data$Holiday);
rental_train_data$Snow    <- factor(rental_train_data$Snow);
rental_train_data$WeekDay <- factor(rental_train_data$WeekDay);

#Create a dtree model and train it using the training data set
model_dtree <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = rental_train_data);
#Serialize the model before saving it to the database table
trained_model <- as.raw(serialize(model_dtree, connection=NULL));
'
        , @input_data_1 = N'
            SELECT RentalCount
                 , Year
                 , Month
                 , Day
                 , WeekDay
                 , Snow
                 , Holiday
            FROM dbo.rental_data
            WHERE Year < 2015
            '
        , @input_data_1_name = N'rental_train_data'
        , @params = N'@trained_model varbinary(max) OUTPUT'
        , @trained_model = @trained_model OUTPUT;
END;
GO
```

## <a name="store-the-model-in-a-database-table"></a>A modell tárolása adatbázistáblában

Hozzon létre egy táblát a TutorialDB adatbázisban, majd mentse a modellt a táblába.

1. Hozzon létre`rental_rx_models`egy táblázatot ( ) a modell tárolására.

    ```sql
    USE TutorialDB;
    DROP TABLE IF EXISTS rental_rx_models;
    GO
    CREATE TABLE rental_rx_models (
          model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(MAX) NOT NULL
        );
    GO
    ```

1. Mentse a modellt a táblába bináris objektumként, az "rxDTree" modellnévvel.

    ```sql
    -- Save model to table
    TRUNCATE TABLE rental_rx_models;
    
    DECLARE @model VARBINARY(MAX);
    
    EXECUTE generate_rental_rx_model @model OUTPUT;
    
    INSERT INTO rental_rx_models (
          model_name
        , model
        )
    VALUES (
         'rxDTree'
        , @model
        );
    
    SELECT *
    FROM rental_rx_models;
    ```

## <a name="create-a-stored-procedure-that-makes-predictions"></a>Előrejelzést tartalmazó tárolt eljárás létrehozása

Hozzon létre egy`predict_rentalcount_new`tárolt eljárás ( ) , amely előrejelzéseket készít a betanított modell és egy sor új adatokat.

```sql
-- Stored procedure that takes model name and new data as input parameters and predicts the rental count for the new data
DROP PROCEDURE IF EXISTS predict_rentalcount_new;
GO
CREATE PROCEDURE predict_rentalcount_new (
      @model_name VARCHAR(100)
    , @input_query NVARCHAR(MAX)
    )
AS
BEGIN
    DECLARE @rx_model VARBINARY(MAX) = (
            SELECT model
            FROM rental_rx_models
            WHERE model_name = @model_name
            );

    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

#Convert types to factors
rentals$Holiday <- factor(rentals$Holiday);
rentals$Snow    <- factor(rentals$Snow);
rentals$WeekDay <- factor(rentals$WeekDay);

#Before using the model to predict, we need to unserialize it
rental_model <- unserialize(rx_model);

#Call prediction function
rental_predictions <- rxPredict(rental_model, rentals);
'
        , @input_data_1 = @input_query
        , @input_data_1_name = N'rentals'
        , @output_data_1_name = N'rental_predictions'
        , @params = N'@rx_model varbinary(max)'
        , @rx_model = @rx_model
    WITH RESULT SETS(("RentalCount_Predicted" FLOAT));
END;
GO
```

## <a name="execute-the-model-with-new-data"></a>A modell végrehajtása új adatokkal

Most már használhatja a `predict_rentalcount_new` tárolt eljárást, hogy előre jelezhesse a bérleti díjak számát az új adatokból.

```sql
-- Use the predict_rentalcount_new stored procedure with the model name and a set of features to predict the rental count
EXECUTE dbo.predict_rentalcount_new @model_name = 'rxDTree'
    , @input_query = '
        SELECT CONVERT(INT,  3) AS Month
             , CONVERT(INT, 24) AS Day
             , CONVERT(INT,  4) AS WeekDay
             , CONVERT(INT,  1) AS Snow
             , CONVERT(INT,  1) AS Holiday
        ';
GO
```

A következőhöz hasonló eredményt kell látnia.

```results
RentalCount_Predicted
332.571428571429
```

Sikeresen létrehozott, betanított és üzembe helyezett egy modellt egy Azure SQL-adatbázisban. Ezután ezt a modellt egy tárolt eljárásban használta az új adatokon alapuló értékek előrejelzéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte a TutorialDB adatbázis használatát, törölje azt az Azure SQL Database-kiszolgálóról.

Az Azure Portalon kövesse az alábbi lépéseket:

1. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** vagy **SQL-adatbázis lehetőséget.**
1. A **Szűrés név szerint...** mezőbe írja be a **TutorialDB**mezőt, és válassza ki az előfizetést.
1. Válassza ki a TutorialDB adatbázist.
1. Az **Áttekintés** oldalon válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések

Az oktatóanyag-sorozat harmadik részében az alábbi lépéseket hajtotta végre:

* A gépi tanulási modellt létrehozó tárolt eljárás létrehozása
* A modell tárolása adatbázistáblában
* Olyan tárolt eljárás létrehozása, amely előrejelzéseket készít a modell használatával
* A modell végrehajtása új adatokkal

Ha többet szeretne tudni az R azure-beli SQL Database Machine Learning Services használatáról (előzetes verzió), olvassa el a következő témakört:

* [Speciális R-függvények írása az Azure SQL Database-ben a Machine Learning Services használatával (előzetes verzió)](sql-database-machine-learning-services-functions.md)
* [R- és SQL-adatok kal való kapcsolat az Azure SQL Database Machine Learning Services szolgáltatásban (előzetes verzió)](sql-database-machine-learning-services-data-issues.md)
* [R-csomag hozzáadása az Azure SQL Database Machine Learning Services szolgáltatáshoz (előzetes verzió)](sql-database-machine-learning-services-add-r-packages.md)

---
title: 'Oktatóanyag: prediktív modell üzembe helyezése az R-ben'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: A három részből álló oktatóanyag harmadik részében egy prediktív modellt helyez üzembe az R-ben Azure SQL Database Machine Learning Services (előzetes verzió).
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
ms.openlocfilehash: 84c0f6564fc9ab8abc43205b58d9445cda82a2da
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85253817"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Oktatóanyag: prediktív modell üzembe helyezése az R-ben Azure SQL Database Machine Learning Services (előzetes verzió)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A három részből álló oktatóanyag harmadik részében egy, az R-ben kifejlesztett prediktív modellt helyez üzembe egy Azure SQL Database-adatbázisban Azure SQL Database Machine Learning Services (előzetes verzió) használatával.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

Létrehoz egy tárolt eljárást egy olyan beágyazott R-parancsfájllal, amely az előrejelzéseket a modell használatával teszi elérhetővé. Mivel a modell végrehajtása az adatbázisban történik, könnyen betanítható az adatbázisban tárolt adataival.

Ebben a cikkben az 1. és a 2. részekben kifejlesztett R-szkripteket használva megismerheti a következőket:

> [!div class="checklist"]
>
> * A Machine learning-modellt létrehozó tárolt eljárás létrehozása
> * A modell tárolása egy adatbázis-táblában
> * Olyan tárolt eljárás létrehozása, amely az előrejelzéseket a modell használatával teszi lehetővé
> * A modell végrehajtása új adattal

Az első [részben](predictive-model-prepare-data-tutorial.md)megtanulta, hogyan importálhat egy minta-adatbázist, majd hogyan készítheti elő a prediktív modell az R-ben való betanításához használni kívánt adatfeldolgozást.

A [második részben](predictive-model-build-compare-tutorial.md)megtanulta, hogyan hozhat létre és taníthat több gépi tanulási modellt az R-ben, majd kiválaszthatja a legpontosabbat.

## <a name="prerequisites"></a>Előfeltételek

* Az oktatóanyag-sorozat harmadik része feltételezi, hogy elvégezte az első [**részt**](predictive-model-prepare-data-tutorial.md) , és a [**második részt.**](predictive-model-build-compare-tutorial.md)

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Modellt létrehozó tárolt eljárás létrehozása

Az oktatóanyag-sorozat második részében úgy döntött, hogy a döntési fa (dtree) modell a legpontosabb. Most, hogy a fejlesztett R-szkripteket használja, hozzon létre egy tárolt eljárást ( `generate_rental_rx_model` ), amely a dtree modellt a rxDTree használatával a RevoScaleR-csomagból állítja be és hozza létre.

Futtassa az alábbi parancsokat Azure Data Studio vagy SSMS.

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

## <a name="store-the-model-in-a-database-table"></a>A modell tárolása egy adatbázis-táblában

Hozzon létre egy táblát a oktatóanyagaiban-adatbázisban, majd mentse a modellt a táblába.

1. Hozzon létre egy táblát ( `rental_rx_models` ) a modell tárolásához.

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

1. Mentse a modellt a táblázatba bináris objektumként a "rxDTree" nevű modellel.

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

## <a name="create-a-stored-procedure-that-makes-predictions"></a>Előrejelzéseket tartalmazó tárolt eljárás létrehozása

Hozzon létre egy tárolt eljárást ( `predict_rentalcount_new` ), amely az előrejelzéseket a betanított modell és az új adatkészletek használatával teszi elérhetővé.

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

## <a name="execute-the-model-with-new-data"></a>A modell végrehajtása új adattal

Mostantól a tárolt eljárással `predict_rentalcount_new` előre megjósolhatja az új adatok bérletének darabszámát.

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

Sikeresen létrehozott, betanított és üzembe helyezett egy modellt a Azure SQL Database adatbázisban. Ezt a modellt egy tárolt eljárásban használta fel az értékek előrejelzésére az új adatok alapján.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha befejezte a oktatóanyagaiban-adatbázis használatát, törölje a kiszolgálóról.

A Azure Portal hajtsa végre az alábbi lépéseket:

1. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** vagy **SQL-adatbázis**lehetőséget.
1. A **szűrés név szerint...** mezőbe írja be a **oktatóanyagaiban**nevet, és válassza ki az előfizetését.
1. Válassza ki a oktatóanyagaiban-adatbázist.
1. Az **Áttekintés** oldalon válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések

Az oktatóanyag-sorozat harmadik része a következő lépéseket végezte el:

* A Machine learning-modellt létrehozó tárolt eljárás létrehozása
* A modell tárolása egy adatbázis-táblában
* Olyan tárolt eljárás létrehozása, amely az előrejelzéseket a modell használatával teszi lehetővé
* A modell végrehajtása új adattal

Ha többet szeretne megtudni az R Azure SQL Database Machine Learning Services (előzetes verzió) használatáról, tekintse meg a következőt:

* [Speciális R függvények írása a Azure SQL Database Machine Learning Services használatával (előzetes verzió)](machine-learning-services-functions.md)
* [R-és SQL-adatmennyiség használata Azure SQL Database Machine Learning Servicesban (előzetes verzió)](machine-learning-services-data-issues.md)
* [R-csomag hozzáadása Azure SQL Database Machine Learning Services (előzetes verzió)](machine-learning-services-add-r-packages.md)

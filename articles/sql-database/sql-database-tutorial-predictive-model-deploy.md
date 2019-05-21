---
title: 'Oktatóanyag: Egy prediktív modellt az R telepítése'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: A háromrészes oktatóanyag harmadik részében fogja központilag telepíteni az Azure SQL Database, Machine Learning Services (előzetes verzió) az R egy prediktív modellt.
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
ms.openlocfilehash: 17b68f71f4034e5eb637d40b975cc22d94438fb7
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978703"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Oktatóanyag: Telepített egy prediktív modellt az R az Azure SQL Database, Machine Learning Services (előzetes verzió)

A háromrészes oktatóanyag harmadik részében fogja központilag telepíteni az Azure SQL Database, Machine Learning Services (előzetes verzió) az R egy prediktív modellt.

A beágyazott R-szkriptet, amely a modell használatával előrejelzéseket tesz létre fog hozni egy tárolt eljárást. A modell az Azure SQL Database hajt végre, mert azt is könnyen vélik az adatbázisban tárolt adatokon.

Ebből a cikkből megtudhatja, hogyan lehet:

> [!div class="checklist"]
> * A prediktív modell egy adatbázistábla soraihoz Store
> * Hozzon létre egy tárolt eljárást, amely létrehozza a modell
> * Hozzon létre egy tárolt eljárást, amely a modell használatával előrejelzéseket tesz
> * Hajtsa végre a modell új adatokkal

A [részében](sql-database-tutorial-predictive-model-prepare-data.md), útmutatóból megtudhatta, hogyan importálhatja a mintaadatbázis egy Azure SQL database-be, és ezután előkészíti az adatokat az r segítségével prediktív modell betanításához használandó

A [második](sql-database-tutorial-predictive-model-build-compare.md), útmutatóból megtudhatta, hogyan hozhat létre, és több modelleket taníthat be és majd a legpontosabb közül választhat.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Az oktatóanyag-sorozat harmadik része feltételezi, hogy befejezte [ **részében** ](sql-database-tutorial-predictive-model-prepare-data.md) és [ **második**](sql-database-tutorial-predictive-model-build-compare.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Hozzon létre egy tárolt eljárást, amely létrehozza a modell

Az oktatóanyag-sorozat második részében, úgy döntött, hogy a döntési fa (dtree) modell volt a legpontosabb. Most hozzon létre egy tárolt eljárást (`generate_rental_rx_model`), amely betanítja, és a használatával a RevoScaleR csomagból rxDTree dtree modellt hoz létre.

Futtassa az alábbi parancsokat a Azure Data Studio vagy az ssms-ben.

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

## <a name="store-the-model-in-a-database-table"></a>A modell egy adatbázistábla soraihoz Store

Hozzon létre egy táblát az adatbázisban oktatóanyagaiban tutorialdb adatbázist, és mentse a modell a táblába.

1. Hozzon létre egy táblát (`rental_rx_models`) modell tárolására.

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

1. Mentse a modell egy bináris objektumot a modell neve "rxDTree" táblához.

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

## <a name="create-a-stored-procedure-that-makes-predictions"></a>Hozzon létre egy tárolt eljárást, amely előrejelzéseket tesz

Hozzon létre egy tárolt eljárást (`predict_rentalcount_new`), amely lehetővé teszi a betanított modell és a egy új adatkészletet előrejelzéseket.

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

## <a name="execute-the-model-with-new-data"></a>Hajtsa végre a modell új adatokkal

Most már használhatja a tárolt eljárás `predict_rentalcount_new` előre jelezni az új adatok bérleti száma.

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

A következőhöz hasonló eredményt kell megjelennie.

```results
RentalCount_Predicted
332.571428571429
```

Sikeresen létrehozott, betanított, és üzembe helyezett egy modellt az Azure SQL database-ben. Ezután használja a modellt a tárolt értékek új adatok alapján előre.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a oktatóanyagaiban tutorialdb adatbázist adatbázist használ, törölje azt az Azure SQL Database-kiszolgáló.

Az Azure Portalról kövesse az alábbi lépéseket:

1. A bal oldali menüben az Azure Portalon, válassza ki a **összes erőforrás** vagy **SQL-adatbázisok**.
1. Az a **Szűrés név alapján...**  írja be a következőt **oktatóanyagaiban tutorialdb adatbázist**, és válassza ki előfizetését.
1. Válassza ki a oktatóanyagaiban tutorialdb adatbázist adatbázist.
1. Az **Áttekintés** oldalon válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések

Az oktatóanyag-sorozat harmadik részében elvégezte ezeket a lépéseket:

* A prediktív modell egy adatbázistábla soraihoz Store
* Hozzon létre egy tárolt eljárást, amely létrehozza a modell
* Hozzon létre egy tárolt eljárást, amely a modell használatával előrejelzéseket tesz
* Hajtsa végre a modell új adatokkal

Az r nyelv használatát az Azure SQL Database, Machine Learning Services (előzetes verzió) kapcsolatos további információkért lásd:

* [Az Azure SQL Database, Machine Learning-Szolgáltatásokoz (előzetes verzió) használatával speciális R-függvények írása](sql-database-machine-learning-services-functions.md)
* [R- és SQL adatok használata Azure SQL Database, Machine Learning Services (előzetes verzió)](sql-database-machine-learning-services-data-issues.md)
* [Egy R-csomag hozzáadása az Azure SQL Database, Machine Learning Services (előzetes verzió)](sql-database-machine-learning-services-add-r-packages.md)

---
title: 'Oktatóanyag: Az R-fürtözési modell üzembe helyezése'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: A háromrészes oktatóanyag-sorozat harmadik részében fogja központilag telepíteni az Azure SQL Database, Machine Learning Services (előzetes verzió) az R csoportosító modell.
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
ms.openlocfilehash: 1fe9df6378884ba55cb1017da87522ae66edaff0
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66419754"
---
# <a name="tutorial-deploy-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Oktatóanyag: Modell üzembe helyezése fürtözéssel az R az Azure SQL Database, Machine Learning Services (előzetes verzió)

A háromrészes oktatóanyag-sorozat harmadik részében fogja központilag telepíteni az Azure SQL Database, Machine Learning Services (előzetes verzió) az R csoportosító modell.

A beágyazott R-szkriptet, amely végrehajtja a fürtszolgáltatás létre fog hozni egy tárolt eljárást. A modell az Azure SQL Database hajt végre, mert azt is könnyen vélik az adatbázisban tárolt adatokon.

Ebből a cikkből megtudhatja, hogyan lehet:

> [!div class="checklist"]
> * Hozzon létre egy tárolt eljárást, amely létrehozza a modell
> * Hajtsa végre a fürtszolgáltatás SQL Database-ben
> * Fürtözési információk

A [részében](sql-database-tutorial-clustering-model-prepare-data.md), megtanulta, hogyan készíti elő az adatokat a fürtszolgáltatás R. végrehajtásához egy Azure SQL database

A [második](sql-database-tutorial-clustering-model-build.md), megtanulta, hogyan hozhat létre a végrehajtására a fürtszolgáltatás K-közép modell.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Az oktatóanyag-sorozat harmadik része feltételezi, hogy befejezte [ **részében** ](sql-database-tutorial-clustering-model-prepare-data.md) és [ **második**](sql-database-tutorial-clustering-model-build.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Hozzon létre egy tárolt eljárást, amely létrehozza a modell

Futtassa a következő T-SQL parancsfájlt a tárolt eljárás létrehozása. Az eljárás létrehozza a lépéseket, az oktatóanyag-sorozat első és második részein fejlesztett:

* a vásárlás ügyfeleket besorolását, és adja vissza az előzmények
* a K-közép-algoritmust használó ügyfelei négy fürtök létrehozása

Az eljárás az eredményül kapott ügyfél fürt leképezések tárolja az adatbázistáblában **customer_return_clusters**.

```sql
USE [tpcxbb_1gb]
DROP PROC IF EXISTS generate_customer_return_clusters;
GO
CREATE procedure [dbo].[generate_customer_return_clusters]
AS
/*
  This procedure uses R to classify customers into different groups
  based on their purchase & return history.
*/
BEGIN
    DECLARE @duration FLOAT
    , @instance_name NVARCHAR(100) = @@SERVERNAME
    , @database_name NVARCHAR(128) = db_name()
-- Input query to generate the purchase history & return metrics
    , @input_query NVARCHAR(MAX) = N'
SELECT ss_customer_sk AS customer,
    round(CASE 
            WHEN (
                    (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio,
    round(CASE 
            WHEN (
                    (orders_items = 0)
                    OR (returns_items IS NULL)
                    OR (orders_items IS NULL)
                    OR ((returns_items / orders_items) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio,
    round(CASE 
            WHEN (
                    (orders_money = 0)
                    OR (returns_money IS NULL)
                    OR (orders_money IS NULL)
                    OR ((returns_money / orders_money) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio,
    round(CASE 
            WHEN (returns_count IS NULL)
                THEN 0.0
            ELSE returns_count
            END, 0) AS frequency
FROM (
    SELECT ss_customer_sk,
        -- return order ratio
        COUNT(DISTINCT (ss_ticket_number)) AS orders_count,
        -- return ss_item_sk ratio
        COUNT(ss_item_sk) AS orders_items,
        -- return monetary amount ratio
        SUM(ss_net_paid) AS orders_money
    FROM store_sales s
    GROUP BY ss_customer_sk
    ) orders
LEFT OUTER JOIN (
    SELECT sr_customer_sk,
        -- return order ratio
        count(DISTINCT (sr_ticket_number)) AS returns_count,
        -- return ss_item_sk ratio
        COUNT(sr_item_sk) AS returns_items,
        -- return monetary amount ratio
        SUM(sr_return_amt) AS returns_money
    FROM store_returns
    GROUP BY sr_customer_sk
    ) returned ON ss_customer_sk = sr_customer_sk
 '
EXECUTE sp_execute_external_script
      @language = N'R'
    , @script = N'
# Define the connection string
connStr <- paste("Driver=SQL Server; Server=", instance_name,
               "; Database=", database_name,
               "; Trusted_Connection=true; ",
                  sep="" );

# Input customer data that needs to be classified.
# This is the result we get from the query.
customer_returns <- RxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);
# Output table to hold the customer cluster mappings
return_cluster = RxSqlServerData(table = "customer_return_clusters",
                                 connectionString = connStr);

# Set seed for random number generator for predictability
set.seed(10);

# Generate clusters using rxKmeans and output clusters to a table
# called "customer_return_clusters"
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters = 4,
                   outFile = return_cluster,
                   outColName = "cluster",
                   writeModelVars = TRUE ,
                   extraVarsToWrite = c("customer"),
                   overwrite = TRUE);
'
    , @input_data_1 = N''
    , @params = N'@instance_name nvarchar(100), @database_name nvarchar(128), @input_query nvarchar(max), @duration float OUTPUT'
    , @instance_name = @instance_name
    , @database_name = @database_name
    , @input_query = @input_query
    , @duration = @duration OUTPUT;
END;

GO
```

## <a name="perform-clustering-in-sql-database"></a>Hajtsa végre a fürtszolgáltatás SQL Database-ben

Most, hogy létrehozta a tárolt eljárás, hajtsa végre a következő parancsfájl végrehajtására a fürtszolgáltatás.

```sql
--Empty table of the results before running the stored procedure
TRUNCATE TABLE customer_return_clusters;

--Execute the clustering
--This will load the table customer_return_clusters with cluster mappings
EXECUTE [dbo].[generate_customer_return_clusters];
```

Ellenőrizze, működik-e, és, hogy vannak olyan ügyfelek és a hozzájuk tartozó fürt leképezések listáját.

```sql
--Select data from table customer_return_clusters
--to verify that the clustering data was loaded
SELECT TOP (5) *
FROM customer_return_clusters;
```

```result
cluster  customer  orderRatio  itemsRatio  monetaryRatio  frequency
1        29727     0           0           0              0
4        26429     0           0           0.041979       1
2        60053     0           0           0.065762       3
2        97643     0           0           0.037034       3
2        32549     0           0           0.031281       4
```

## <a name="use-the-clustering-information"></a>Fürtözési információk

A csoportosítási eljárás tárolódnak az adatbázisban, mert hajthat végre fürtszolgáltatás hatékonyan ugyanabban az adatbázisban tárolt ügyféladatok ellen. Az eljárást hajthat végre, amikor az ügyféladatok frissül, és a frissített fürtözési adatokat használja.

Tegyük fel, hogy szeretne promóciós e-maileket küldeni a fürt 3, a csoport több aktív visszatérési viselkedés rendelkező ügyfelek számára (láthatja, hogyan lettek ismertetett a négy fürtök [második](sql-database-tutorial-clustering-model-build.md#analyze-the-results)). A következő kódot az e-mail-címeket az ügyfelek 3 fürtben választja ki.

```sql
USE [tpcxbb_1gb]

SELECT customer.[c_email_address],
    customer.c_customer_sk
FROM dbo.customer
JOIN [dbo].[customer_return_clusters] AS r ON r.customer = customer.c_customer_sk
WHERE r.cluster = 3
```

Módosíthatja a **r.cluster** értéket adja vissza az e-mail címet egyéb fürtök esetén.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült, ebben az oktatóanyagban, törölheti a tpcxbb_1gb adatbázis az Azure SQL Database-kiszolgáló.

Az Azure Portalról kövesse az alábbi lépéseket:

1. A bal oldali menüben az Azure Portalon, válassza ki a **összes erőforrás** vagy **SQL-adatbázisok**.
1. Az a **Szűrés név alapján...**  írja be a következőt **tpcxbb_1gb**, és válassza ki előfizetését.
1. Válassza ki a **tpcxbb_1gb** adatbázis.
1. Az **Áttekintés** oldalon válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések

Az oktatóanyag-sorozat harmadik részében elvégezte ezeket a lépéseket:

* Hozzon létre egy tárolt eljárást, amely létrehozza a modell
* Hajtsa végre a fürtszolgáltatás SQL Database-ben
* Fürtözési információk

Az r nyelv használatát az Azure SQL Database, Machine Learning Services (előzetes verzió) kapcsolatos további információkért lásd:

* [Oktatóanyag: Betanítunk egy prediktív modellt az Azure SQL Database, Machine Learning Services (előzetes verzió) az R az adatok előkészítése](sql-database-tutorial-predictive-model-prepare-data.md)
* [Az Azure SQL Database, Machine Learning-Szolgáltatásokoz (előzetes verzió) használatával speciális R-függvények írása](sql-database-machine-learning-services-functions.md)
* [R- és SQL adatok használata Azure SQL Database, Machine Learning Services (előzetes verzió)](sql-database-machine-learning-services-data-issues.md)
* [Egy R-csomag hozzáadása az Azure SQL Database, Machine Learning Services (előzetes verzió)](sql-database-machine-learning-services-add-r-packages.md)

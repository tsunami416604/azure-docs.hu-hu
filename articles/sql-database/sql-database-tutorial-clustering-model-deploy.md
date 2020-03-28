---
title: 'Oktatóanyag: Fürtmodell telepítése r-ben'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: A három részes oktatóanyag-sorozat harmadik részében egy fürtözési modellt telepít az R-ben az Azure SQL Database Machine Learning Services segítségével (előzetes verzió).
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
ms.openlocfilehash: d67f007ac91d4830557a2cae646698b130b02314
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345791"
---
# <a name="tutorial-deploy-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Oktatóanyag: Fürthálózati modell üzembe helyezése az R-ben az Azure SQL Database Machine Learning Services szolgáltatással (előzetes verzió)

A három részes oktatóanyag-sorozat harmadik részében egy R-ben kifejlesztett fürtözési modellt telepít egy SQL-adatbázisba az Azure SQL Database Machine Learning Services (előzetes verzió) használatával.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Hozzon létre egy tárolt eljárást egy beágyazott R-parancsfájllal, amely fürtözést hajt végre. Mivel a modell végrehajtása az Azure SQL-adatbázisban, könnyen betanítható az adatbázisban tárolt adatok ellen.

Ebben a cikkben megtudhatja, hogyan:

> [!div class="checklist"]
> * A modellt létrehozó tárolt eljárás létrehozása
> * Fürtözés végrehajtása az SQL Database-ben
> * A fürtözési adatok használata

Az [első részben](sql-database-tutorial-clustering-model-prepare-data.md)megtanulta, hogyan készítse elő az adatokat egy Azure SQL-adatbázis fürtözés végrehajtásához.

A [második részben](sql-database-tutorial-clustering-model-build.md)megtanulta, hogyan kell létrehozni és beképezni a K-Means fürtözési modellt az R-ben.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Az oktatóanyag-sorozat harmadik része feltételezi, hogy befejezte [**az első**](sql-database-tutorial-clustering-model-prepare-data.md) és a [**második részt.**](sql-database-tutorial-clustering-model-build.md)

## <a name="create-a-stored-procedure-that-generates-the-model"></a>A modellt létrehozó tárolt eljárás létrehozása

A tárolt eljárás létrehozásához futtassa a következő T-SQL-parancsfájlt. Az eljárás újralétrehozza az oktatóanyag-sorozat első és második részében kifejlesztett lépéseket:

* a vevők besorolása a vásárlási és visszaküldési előzményeik alapján
* négy ügyfélcsoportot hoz létre egy K-Means algoritmus segítségével

Az eljárás az eredményül kapott vevőfürt-hozzárendeléseket az **adatbázistáblában tárolja customer_return_clusters**.

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

## <a name="perform-clustering-in-sql-database"></a>Fürtözés végrehajtása az SQL Database-ben

Most, hogy létrehozta a tárolt eljárást, hajtsa végre a következő parancsfájlt a fürtözés végrehajtásához.

```sql
--Empty table of the results before running the stored procedure
TRUNCATE TABLE customer_return_clusters;

--Execute the clustering
--This will load the table customer_return_clusters with cluster mappings
EXECUTE [dbo].[generate_customer_return_clusters];
```

Ellenőrizze, hogy működik-e, és hogy valóban rendelkezünk-e az ügyfelek és fürtleképezéseik listájával.

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

## <a name="use-the-clustering-information"></a>A fürtözési adatok használata

Mivel a fürtözési eljárást az adatbázisban tárolta, hatékonyan képes fürtözést végrehajtani az ugyanabban az adatbázisban tárolt ügyféladatokkal szemben. Az eljárást az ügyféladatok frissítésekor végrehajthatja, és használhatja a frissített fürtözési adatokat.

Tegyük fel, hogy promóciós e-mailt szeretne küldeni a 3-as fürtben lévő ügyfeleknek, az a csoport, amely aktívabb visszatérési viselkedéssel rendelkezik (láthatja, hogy a négy fürtet hogyan írták le a [második részben).](sql-database-tutorial-clustering-model-build.md#analyze-the-results) A következő kód kiválasztja a 3-as fürtben lévő ügyfelek e-mail címét.

```sql
USE [tpcxbb_1gb]

SELECT customer.[c_email_address],
    customer.c_customer_sk
FROM dbo.customer
JOIN [dbo].[customer_return_clusters] AS r ON r.customer = customer.c_customer_sk
WHERE r.cluster = 3
```

Módosíthatja az **r.cluster** értékét, hogy más fürtökben lévő ügyfelek e-mail címét adja vissza.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte az oktatóanyag, törölheti a tpcxbb_1gb adatbázist az Azure SQL Database-kiszolgálóról.

Az Azure Portalon kövesse az alábbi lépéseket:

1. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** vagy **SQL-adatbázis lehetőséget.**
1. A **Szűrés név szerint...** mezőbe írja be **tpcxbb_1gb,** és válassza ki az előfizetést.
1. Válassza ki a **tpcxbb_1gb** adatbázist.
1. Az **Áttekintés** oldalon válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések

Az oktatóanyag-sorozat harmadik részében az alábbi lépéseket hajtotta végre:

* A modellt létrehozó tárolt eljárás létrehozása
* Fürtözés végrehajtása az SQL Database-ben
* A fürtözési adatok használata

Ha többet szeretne tudni az R azure-beli SQL Database Machine Learning Services használatáról (előzetes verzió), olvassa el a következő témakört:

* [Oktatóanyag: Adatok előkészítése az R-ben egy prediktív modell betanításához az Azure SQL Database Machine Learning Services szolgáltatással (előzetes verzió)](sql-database-tutorial-predictive-model-prepare-data.md)
* [Speciális R-függvények írása az Azure SQL Database-ben a Machine Learning Services használatával (előzetes verzió)](sql-database-machine-learning-services-functions.md)
* [R- és SQL-adatok kal való kapcsolat az Azure SQL Database Machine Learning Services szolgáltatásban (előzetes verzió)](sql-database-machine-learning-services-data-issues.md)
* [R-csomag hozzáadása az Azure SQL Database Machine Learning Services szolgáltatáshoz (előzetes verzió)](sql-database-machine-learning-services-add-r-packages.md)

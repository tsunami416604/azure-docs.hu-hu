---
title: 'Oktatóanyag: fürtszolgáltatási modell üzembe helyezése az R-ben'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: A jelen háromrészes oktatóanyag-sorozat harmadik részében egy Azure SQL Database Machine Learning Services (előzetes verzió) üzembe helyez egy fürtszolgáltatási modellt az R-ben.
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
ms.openlocfilehash: d5227fc89d99257f3390820d4930c3d57b63f03d
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053398"
---
# <a name="tutorial-deploy-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Oktatóanyag: fürtszolgáltatási modell üzembe helyezése az R-ben Azure SQL Database Machine Learning Services (előzetes verzió)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A jelen háromrészes oktatóanyag-sorozat harmadik részében az R-ben kifejlesztett, Azure SQL Database Machine Learning Services (előzetes verzió) használatával egy SQL-adatbázisba helyez üzembe egy fürtözött modellt.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

Hozzon létre egy tárolt eljárást egy olyan beágyazott R-parancsfájllal, amely fürtözést végez. Mivel a modell végrehajtása az Azure SQL Database-ben történik, könnyen kitanítható az adatbázisban tárolt adataival.

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

> [!div class="checklist"]
>
> * Modellt létrehozó tárolt eljárás létrehozása
> * Fürtözés végrehajtása a SQL Databaseban
> * A fürtözési adatok használata

Az első [részben](clustering-model-prepare-data-tutorial.md)megtanulta, hogyan készítse elő az adatok előkészítését egy Azure SQL Database-adatbázisból a fürtözés végrehajtásához.

A [második részben](clustering-model-build-tutorial.md)megtanulta, hogyan hozhat létre és taníthat egy k-csoportosítási modellt az R-ben.

## <a name="prerequisites"></a>Előfeltételek

* Az oktatóanyag-sorozat harmadik része feltételezi, hogy elvégezte az első [**részt**](clustering-model-prepare-data-tutorial.md) , és a [**második részt.**](clustering-model-build-tutorial.md)

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Modellt létrehozó tárolt eljárás létrehozása

Futtassa az alábbi T-SQL-szkriptet a tárolt eljárás létrehozásához. Az eljárás újból létrehozza az oktatóanyag-sorozat első és második részében létrehozott lépéseket:

* ügyfelek osztályozása a vásárlási és visszatérési előzmények alapján
* négy fürt létrehozása a K-means algoritmust használó ügyfelek számára

Az eljárás az eredményül kapott ügyfél-fürt leképezéseit tárolja **customer_return_clusters**adatbázis-táblájában.

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

## <a name="perform-clustering-in-sql-database"></a>Fürtözés végrehajtása a SQL Databaseban

Most, hogy létrehozta a tárolt eljárást, hajtsa végre a következő parancsfájlt a fürtözés végrehajtásához.

```sql
--Empty table of the results before running the stored procedure
TRUNCATE TABLE customer_return_clusters;

--Execute the clustering
--This will load the table customer_return_clusters with cluster mappings
EXECUTE [dbo].[generate_customer_return_clusters];
```

Ellenőrizze, hogy működik-e, és hogy valóban van-e az ügyfelek és a fürthöz tartozó hozzárendelések listája.

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

Mivel az adatbázisban tárolta a fürtszolgáltatási eljárást, az képes a fürtözést hatékonyan végrehajtani az ugyanabban az adatbázisban tárolt ügyféladatokat illetően. Az eljárást akkor hajthatja végre, amikor frissíti az ügyféladatokat, és a frissített fürtszolgáltatási információkat használja.

Tegyük fel, hogy egy promóciós e-mailt szeretne küldeni a 3. fürtben lévő ügyfeleknek, az aktívabb visszatérési viselkedésű csoportnál (láthatja, hogyan írták le a négy fürtöt a [második részben](clustering-model-build-tutorial.md#analyze-the-results)). A következő kód kiválasztja a 3. fürtben lévő ügyfelek e-mail-címeit.

```sql
USE [tpcxbb_1gb]

SELECT customer.[c_email_address],
    customer.c_customer_sk
FROM dbo.customer
JOIN [dbo].[customer_return_clusters] AS r ON r.customer = customer.c_customer_sk
WHERE r.cluster = 3
```

Az **r. cluster** érték módosításával visszatérhet a más fürtökben lévő ügyfelek e-mail-címeire.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha elkészült ezzel az Oktatóanyaggal, törölheti a tpcxbb_1gb adatbázist a kiszolgálóról.

A Azure Portal hajtsa végre az alábbi lépéseket:

1. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** vagy **SQL-adatbázis**lehetőséget.
1. A **szűrés név szerint...** mezőbe írja be **tpcxbb_1gb**, majd válassza ki az előfizetését.
1. Válassza ki a **tpcxbb_1gb** -adatbázist.
1. Az **Áttekintés** oldalon válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések

Az oktatóanyag-sorozat harmadik része a következő lépéseket végezte el:

* Modellt létrehozó tárolt eljárás létrehozása
* Fürtözés végrehajtása a SQL Databaseban
* A fürtözési adatok használata

Ha többet szeretne megtudni az R Azure SQL Database Machine Learning Services (előzetes verzió) használatáról, tekintse meg a következőt:

* [Oktatóanyag: az Azure SQL Database Machine Learning Services (előzetes verzió) segítségével előkészítheti a prediktív modelleket az R-ben.](predictive-model-prepare-data-tutorial.md)
* [Speciális R függvények írása a Azure SQL Database Machine Learning Services használatával (előzetes verzió)](machine-learning-services-functions.md)
* [R-és SQL-adatmennyiség használata Azure SQL Database Machine Learning Servicesban (előzetes verzió)](machine-learning-services-data-issues.md)
* [R-csomag hozzáadása Azure SQL Database Machine Learning Services (előzetes verzió)](machine-learning-services-add-r-packages.md)

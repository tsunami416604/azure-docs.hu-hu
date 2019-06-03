---
title: 'Oktatóanyag: Az R fürtszolgáltatás végrehajtásához adatok előkészítése'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: A háromrészes oktatóanyag-sorozat egyik részben fogja készítse elő az adatokat egy Azure SQL database, Azure SQL Database, Machine Learning Services (előzetes verzió) az R a fürtszolgáltatás végrehajtásához.
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
ms.openlocfilehash: 83ef25f04012933c2665e63e4617d480eb336f7b
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66419460"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Oktatóanyag: Az Azure SQL Database, Machine Learning Services (előzetes verzió) az R fürtszolgáltatás végrehajtásához adatok előkészítése

A háromrészes oktatóanyag-sorozat egyik részben fogja készítse elő az adatokat egy Azure SQL database, Azure SQL Database, Machine Learning Services (előzetes verzió) az R a fürtszolgáltatás végrehajtásához.

*Fürtszolgáltatás* bemutatható, ahol egy csoport tagjai hasonló módon csoportokba adatok rendezéséhez.
Szeretné használni a **K-közép** algoritmus a fürtszolgáltatás egy adatkészlet termék ügyfelek végrehajtásához vásárol, és adja vissza. Ügyfelek fürtözésével összpontosíthat a marketingtevékenységek hatékonyabban által meghatározott csoportokat.
K-közép-fürtözés egy *tanítást* feltárt Hasonlóságok alapján keres. mintázatok az adatok algoritmus.

Ebből a cikkből megtudhatja, hogyan lehet:

> [!div class="checklist"]
> * Minta importálása egy Azure SQL database-be
> * Másik dimenzió mentén külön ügyfelek
> * Az adatok betöltése az Azure SQL database-ből az R használata adatok keretbe

A [második](sql-database-tutorial-clustering-model-build.md), megtudhatja, hogyan hozhat létre, és a K-közép-fürtözési modell betanításához.

A [harmadik részt](sql-database-tutorial-clustering-model-deploy.md), megtudhatja, hogyan hozzon létre egy tárolt eljárást az Azure SQL-adatbázis, amely a fürtszolgáltatás új adatok alapján hajthat végre.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Az Azure-előfizetés – Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/) megkezdése előtt.

* Azure SQL Database-kiszolgáló a Machine Learning-szolgáltatások engedélyezve – felvétele a Microsoft lesz a nyilvános előzetes verzióban, és a meglévő vagy új adatbázisok engedélyezése gépi tanulás. Kövesse a [regisztrálni az előzetes verzióra](sql-database-machine-learning-services-overview.md#signup).

* RevoScaleR package - lásd [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) kell telepítenie a csomagot a helyi beállításokat.

* R IDE - ebben az oktatóanyagban [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* SQL-lekérdezési eszköz – Ez az oktatóanyag feltételezi használ [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) vagy [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>A mintaadatbázis importálása

Ebben az oktatóanyagban használt minta adatkészlet mentése megtörtént a **.bacpac** , töltse le és használja az adatbázis biztonsági másolatát. Ez az adatkészlet származik a [tpcx-bb](http://www.tpc.org/tpcx-bb/default.asp) által biztosított adatkészlet a [tranzakciós feldolgozási teljesítmény Tanács (TPC)](http://www.tpc.org/default.asp).

1. Töltse le a fájlt [tpcxbb_1gb.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac).

1. Kövesse az utasításokat [hozhat létre Azure SQL-adatbázis BACPAC-fájl importálása](https://docs.microsoft.com/azure/sql-database/sql-database-import), használja ezeket az adatokat:

   * Importálhat a **tpcxbb_1gb.bacpac** letöltött fájl
   * A nyilvános előzetes verzió ideje alatt válassza ki a **Gen5/virtuális mag** az új adatbázis konfigurálása
   * Nevezze el az új adatbázis "tpcxbb_1gb"

## <a name="separate-customers"></a>Külön ügyfelek

Az RStudio hozzon létre egy új RScript fájlt, és futtassa a következő szkriptet.
Az SQL-lekérdezést használ megadhat, az alábbi dimenziók mentén ügyfelek:

* **orderRatio** visszatérési rendelés arány (megrendelések teljes számát és a visszaadott részlegesen vagy teljesen megrendelések teljes száma) =
* **itemsRatio** visszatérési elem arány (és a megvásárolt elemek száma a visszaadott elemek teljes száma) =
* **monetaryRatio** visszatérési összeg arány (díja vásárolt tárterülethez visszaadott elemek) =
* **gyakoriság** = visszatérési gyakorisága

Az a **illessze be** működik, cserélje le **kiszolgáló**, **UID**, és **PWD** a saját kapcsolatadatokkal.

```r
# Define the connection string to connect to the tpcxbb_1gb database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
               "; Database=tpcxbb_1gb",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");


#Define the query to select data from SQL Server
input_query <- "
SELECT ss_customer_sk AS customer
    ,round(CASE 
            WHEN (
                       (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio
    ,round(CASE 
            WHEN (
                     (orders_items = 0)
                  OR (returns_items IS NULL)
                  OR (orders_items IS NULL)
                  OR ((returns_items / orders_items) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio
    ,round(CASE 
            WHEN (
                     (orders_money = 0)
                  OR (returns_money IS NULL)
                  OR (orders_money IS NULL)
                  OR ((returns_money / orders_money) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio
    ,round(CASE 
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
"
```

## <a name="load-the-data-into-a-data-frame"></a>Az adatok betöltése az adatkeretek

Most már használja a következő szkriptet, térjen vissza az eredményeket a lekérdezésből származó használatával R adatok keret a **rxSqlServerData** függvény.
A folyamat részeként típusának (colClasses használatával), győződjön meg arról, hogy a típusok r megfelelően átkerülnek a kijelölt oszlopokban fogja definiálni

```r
# Query SQL Server using input_query and get the results back
# to data frame customer_returns
# Define the types for selected columns (using colClasses),
# to make sure that the types are correctly transferred to R
customer_returns <- rxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);

# Transform the data from an input dataset to an output dataset
customer_data <- rxDataStep(customer_returns);

# Take a look at the data just loaded from SQL Server
head(customer_data, n = 5);
```

A következőhöz hasonló eredményt kell megjelennie.

```results
  customer orderRatio itemsRatio monetaryRatio frequency
1    29727          0          0      0.000000         0
2    26429          0          0      0.041979         1
3    60053          0          0      0.065762         3
4    97643          0          0      0.037034         3
5    32549          0          0      0.031281         4
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

***Ha nem fog ez az oktatóanyag folytatásához***, törölje a tpcxbb_1gb adatbázist az Azure SQL Database-kiszolgáló.

Az Azure Portalról kövesse az alábbi lépéseket:

1. A bal oldali menüben az Azure Portalon, válassza ki a **összes erőforrás** vagy **SQL-adatbázisok**.
1. Az a **Szűrés név alapján...**  írja be a következőt **tpcxbb_1gb**, és válassza ki előfizetését.
1. Válassza ki a **tpcxbb_1gb** adatbázis.
1. Az **Áttekintés** oldalon válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések

Az első rész a jelen oktatóanyag-sorozatban elvégezte ezeket a lépéseket:

* Minta importálása egy Azure SQL database-be
* Másik dimenzió mentén külön ügyfelek
* Az adatok betöltése az Azure SQL database-ből az R használata adatok keretbe

Hozzon létre egy gépi tanulási modellt, amely a vásárlói adatokat használ, kövesse az oktatóanyag-sorozat második részére:

> [!div class="nextstepaction"]
> [Oktatóanyag: Egy prediktív modell létrehozásához az Azure SQL Database, Machine Learning Services (előzetes verzió) az R](sql-database-tutorial-clustering-model-build.md)

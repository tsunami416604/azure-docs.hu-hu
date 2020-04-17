---
title: 'Oktatóanyag: Adatok előkészítése az R fürtözésének végrehajtásához'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: A három részes oktatóanyag-sorozat első részében előkészíti az adatokat egy Azure SQL-adatbázisból az Azure SQL Database Machine Learning Services (előzetes verzió) R-alapú fürtözésének elvégzéséhez.
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
ms.openlocfilehash: abe7d5ed1d4ba1308abde04aee32a3ea222456b8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452876"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Oktatóanyag: Adatok előkészítése az R-ben történő fürtözéshez az Azure SQL Database Machine Learning Services szolgáltatással (előzetes verzió)

A három részes oktatóanyag-sorozat első részében importálja és előkészíti az adatokat egy Azure SQL-adatbázisból az R használatával. Ebben a sorozatban később ezeket az adatokat fogja használni egy fürtözési modell betanításához és üzembe helyezéséhez az R-ben az Azure SQL Database Machine Learning Services használatával (előzetes verzió).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

*A fürtözés* úgy magyarázható, hogy az adatokat csoportokba rendszerezi, ahol a csoport tagjai valamilyen módon hasonlóak.
A **K-Means** algoritmust fogja használni az ügyfelek fürtözésének elvégzésére a termékvásárlások és visszaküldések adatkészletében. Az ügyfelek fürtözésével hatékonyabban összpontosíthatja marketingerőfeszítéseit adott csoportok megcélzásával.
K-Means fürtözés egy *felügyelet nélküli tanulási* algoritmus, amely keresi a minták adatok hasonlóságok alapján.

A sorozat első és második részében az RStudio néhány R-parancsfájlt fejleszt az adatok előkészítéséhez és a gépi tanulási modell betanításához. Ezután a harmadik részben az okat az R-parancsfájlokat egy SQL-adatbázisban futtatja tárolt eljárások használatával.

Ebben a cikkben megtudhatja, hogyan:

> [!div class="checklist"]
> * Mintaadatbázis importálása Azure SQL-adatbázisba
> * Az R használatával különböző dimenziók mentén különügyfelek
> * Adatok betöltése az Azure SQL-adatbázisból egy R adatkeretbe

A [második részben](sql-database-tutorial-clustering-model-build.md)megtudhatja, hogyan kell létrehozni és beképezni a K-Means fürtözési modell R.

[A harmadik részben](sql-database-tutorial-clustering-model-deploy.md)megtudhatja, hogyan hozhat létre egy tárolt eljárást egy Azure SQL-adatbázisban, amely az új adatok alapján r-ben fürtözést végezhet.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

* [Azure SQL Database Machine Learning Services (R)](sql-database-machine-learning-services-overview.md) engedélyezve van.

* RevoScaleR-csomag – A csomag helyi telepítésének lehetőségeiről a [RevoScaleR című](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) témakörben található.

* R IDE - Ez a bemutató használ [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* SQL-lekérdezési eszköz – Ez az oktatóanyag feltételezi, hogy [az Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) vagy az SQL Server Management [Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) használatával.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Mintaadatbázis importálása

Az oktatóanyagban használt mintaadatkészletet egy **.bacpac** adatbázis biztonsági másolatfájlba mentette a letöltéshez és a használathoz. Ez az adatkészlet a [Transaction Processing Performance Council (TPC)](http://www.tpc.org/default.asp)által biztosított [tpcx-bb](http://www.tpc.org/tpcx-bb/default.asp) adatkészletből származik.

1. Töltse le a [tpcxbb_1gb.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac).

1. Kövesse a [BACPAC-fájl importálása](https://docs.microsoft.com/azure/sql-database/sql-database-import)című részben található utasításokat az Azure SQL-adatbázis létrehozásához az alábbi adatok használatával:

   * Importálás a letöltött **tpcxbb_1gb.bacpac** fájlból
   * A nyilvános előzetes verzió során válassza ki az új adatbázis **Gen5/virtuálismag-konfigurációját**
   * Az új adatbázis elnevezése "tpcxbb_1gb"

## <a name="separate-customers"></a>Külön vevők

Hozzon létre egy új RScript-fájlt az RStudio-ban, és futtassa a következő parancsfájlt.
Az SQL-lekérdezésben a következő dimenziók mentén választja el az ügyfeleket:

* **orderRatio** = visszárurendelés-arány (a rendelések teljes száma részben vagy teljesen visszalett küldve a rendelések teljes számával szemben)
* **itemsRatio** = visszárucikk arány (a visszaküldött cikkek teljes száma a beszerzett cikkek számához képest)
* **monetárisRatio** = hozamösszeg hányadosa (a visszaküldött tételek teljes pénzbeli összege a megvásárolt összeggel szemben)
* **frekvencia** = visszatérési frekvencia

A **beillesztés** i függvényben cserélje le a **Kiszolgáló**, **UID**és **PWD** kiszolgálót a saját kapcsolati adataira.

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

## <a name="load-the-data-into-a-data-frame"></a>Az adatok betöltése adatkeretbe

Most használja a következő parancsfájlt, hogy visszaadja a lekérdezés eredményeit egy R adatkeretbe az **rxSqlServerData** függvény használatával.
A folyamat részeként meg kell határoznia a kijelölt oszlopok típusát (a colClasses használatával), hogy megbizonyosodjon arról, hogy a típusok megfelelően átkerültek az R-be.

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

Az eredményeknek az alábbiakhoz hasonlónak kell lenniük.

```results
  customer orderRatio itemsRatio monetaryRatio frequency
1    29727          0          0      0.000000         0
2    26429          0          0      0.041979         1
3    60053          0          0      0.065762         3
4    97643          0          0      0.037034         3
5    32549          0          0      0.031281         4
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

***Ha nem folytatja ezt az oktatóanyagot,*** törölje a tpcxbb_1gb adatbázist az Azure SQL Database-kiszolgálóról.

Az Azure Portalon kövesse az alábbi lépéseket:

1. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** vagy **SQL-adatbázis lehetőséget.**
1. A **Szűrés név szerint...** mezőbe írja be **tpcxbb_1gb,** és válassza ki az előfizetést.
1. Válassza ki a **tpcxbb_1gb** adatbázist.
1. Az **Áttekintés** oldalon válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések

Az oktatóanyag-sorozat első részében az alábbi lépéseket hajtotta végre:

* Mintaadatbázis importálása Azure SQL-adatbázisba
* Az R használatával különböző dimenziók mentén különügyfelek
* Adatok betöltése az Azure SQL-adatbázisból egy R adatkeretbe

Ha olyan gépi tanulási modellt szeretne létrehozni, amely ezt az ügyféladatokat használja, kövesse az oktatóanyag-sorozat második részét:

> [!div class="nextstepaction"]
> [Oktatóanyag: Prediktív modell létrehozása az R-ben az Azure SQL Database Machine Learning Services szolgáltatással (előzetes verzió)](sql-database-tutorial-clustering-model-build.md)

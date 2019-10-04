---
title: 'Oktatóanyag: Adatok előkészítése a fürtözés végrehajtásához az R-ben'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: A jelen háromrészes oktatóanyag-sorozat első részében előkészíti az Azure SQL Database-ből származó adatokból a fürtözést az R-ben Azure SQL Database Machine Learning Services (előzetes verzió) használatával.
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
ms.openlocfilehash: 800dbfc05c47a949bf024e9a5c671979b49ad201
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639970"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Oktatóanyag: Adatok előkészítése az R-ben történő fürtözés végrehajtásához Azure SQL Database Machine Learning Services (előzetes verzió)

A háromrészes oktatóanyag-sorozat első részében az R használatával importálhatja és készítheti elő az Azure SQL Database-adatbázisok adatait. A sorozat későbbi részében ezeket az adatfeldolgozási modelleket fogja használni az R-ben, Azure SQL Database Machine Learning Services (előzetes verzió).

A fürtözéssel olyan csoportokba rendezheti az adatrendezést, ahol a csoport tagjai valamilyen módon hasonlóak.
A **K-means** algoritmus használatával végezheti el az ügyfelek fürtözését a termékek beszerzése és visszaadása során. Az ügyfelek fürtözésével hatékonyabban összpontosíthat a marketingre, ha meghatározott csoportokat céloz meg.
K – azt jelenti, hogy a fürtözés egy nem *felügyelt tanulási* algoritmus, amely hasonlóságok alapján keresi az adatmintákat.

A sorozat első és két részén egy R-szkriptet fejleszt ki a RStudio-ben az adatai előkészítéséhez és a gépi tanulási modellek betanításához. Ezt követően a harmadik részen az R-szkripteket az SQL Database-ben tárolt eljárásokkal futtathatja.

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

> [!div class="checklist"]
> * Mintaadatbázis importálása egy Azure SQL Database-adatbázisba
> * Ügyfelek elkülönítése különböző dimenziókban az R használatával
> * Az adatok betöltése az Azure SQL Database-ből egy R-adatkeretbe

A [második részből](sql-database-tutorial-clustering-model-build.md)megtudhatja, hogyan hozhat létre és taníthat egy k-alapú fürtözési modellt az R-ben.

A [harmadik részből](sql-database-tutorial-clustering-model-deploy.md)megtudhatja, hogyan hozhat létre egy tárolt eljárást egy olyan Azure SQL Database-adatbázisban, amely új adatok alapján képes a fürtözésre az R-ben.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/) a Kezdés előtt.

* Azure SQL Database-kiszolgáló Machine Learning Services engedélyezve – a nyilvános előzetes verzióban a Microsoft bevezeti Önt, és lehetővé teszi a gépi tanulást a meglévő vagy új adatbázisokhoz. Kövesse a regisztráció az [előzetes](sql-database-machine-learning-services-overview.md#signup)verzióra című témakör lépéseit.

* RevoScaleR csomag – a csomag helyi telepítéséhez szükséges [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) lásd:.

* R IDE – ez az oktatóanyag a [RStudio Desktopot](https://www.rstudio.com/products/rstudio/download/)használja.

* SQL-lekérdezési eszköz – ez az oktatóanyag feltételezi, hogy [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) vagy [SQL Server Management Studiot](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) használ.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>A mintaadatbázis importálása

Az oktatóanyagban használt minta adatkészletet egy **. bacpac** -adatbázis biztonsági másolati fájljába mentette a letöltéshez és a használatához. Ez az adatkészlet a [tranzakció-feldolgozási teljesítmény Tanácsa (TPC)](http://www.tpc.org/default.asp)által biztosított [tpcx-bb](http://www.tpc.org/tpcx-bb/default.asp) adatkészletből származik.

1. Töltse le a [tpcxbb_1gb. bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac)fájlt.

1. Kövesse az BACPAC- [fájl importálása egy Azure SQL Database-adatbázis létrehozásához](https://docs.microsoft.com/azure/sql-database/sql-database-import)című témakör utasításait a következő információk használatával:

   * Importálás a letöltött **tpcxbb_1gb. bacpac** fájlból
   * A nyilvános előzetes verzióban válassza ki az új adatbázis **Gen5/virtuális mag-** konfigurációját
   * Nevezze el az új "tpcxbb_1gb" adatbázist

## <a name="separate-customers"></a>Különálló ügyfelek

Hozzon létre egy új RScript-fájlt a RStudio-ben, és futtassa az alábbi szkriptet.
Az SQL-lekérdezésben az ügyfeleket a következő dimenziók mentén választjuk el:

* **orderRatio** = visszaadott sorrendi arány (a megrendelések teljes száma részben vagy teljes mértékben visszaadva a megrendelések teljes száma szerint)
* **itemsRatio** = visszaadott elem aránya (a visszaadott elemek teljes száma és a megvásárolt elemek száma)
* **monetaryRatio** = visszaadott összeg aránya (a visszaadott elemek teljes pénzügyi mennyisége és a megvásárolt mennyiség)
* **gyakoriság** = visszatérési gyakoriság

A **beillesztési** függvényben cserélje le a **kiszolgálót**, az **UID**-t és a **pwd** -t a saját kapcsolatok adataira.

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

## <a name="load-the-data-into-a-data-frame"></a>Az adatgyűjtés egy adatkeretbe

A következő szkripttel adja vissza az eredményeket a lekérdezésből egy R-adatkeretre a **rxSqlServerData** függvénnyel.
A folyamat részeként meg kell határozni a kiválasztott oszlopok típusát (a colClasses használatával), hogy a rendszer helyesen továbbítsa a típusokat az R-nek.

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

A következőhöz hasonló eredményeknek kell megjelennie.

```results
  customer orderRatio itemsRatio monetaryRatio frequency
1    29727          0          0      0.000000         0
2    26429          0          0      0.041979         1
3    60053          0          0      0.065762         3
4    97643          0          0      0.037034         3
5    32549          0          0      0.031281         4
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

***Ha nem folytatja ezt***az oktatóanyagot, törölje a tpcxbb_1gb-adatbázist a Azure SQL Database-kiszolgálóról.

A Azure Portal hajtsa végre az alábbi lépéseket:

1. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** vagy **SQL-adatbázis**lehetőséget.
1. A **szűrés név szerint...** mezőbe írja be a **tpcxbb_1gb**nevet, és válassza ki az előfizetését.
1. Válassza ki a **tpcxbb_1gb** -adatbázist.
1. Az **Áttekintés** oldalon válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések

Az oktatóanyag-sorozat első részében a következő lépéseket végezte el:

* Mintaadatbázis importálása egy Azure SQL Database-adatbázisba
* Ügyfelek elkülönítése különböző dimenziókban az R használatával
* Az adatok betöltése az Azure SQL Database-ből egy R-adatkeretbe

Az ügyféladatokat használó Machine learning-modell létrehozásához kövesse az oktatóanyag-sorozat második részét:

> [!div class="nextstepaction"]
> [Oktatóanyag: Prediktív modell létrehozása az R-ben Azure SQL Database Machine Learning Services (előzetes verzió)](sql-database-tutorial-clustering-model-build.md)

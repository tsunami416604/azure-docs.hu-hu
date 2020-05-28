---
title: 'Oktatóanyag: az adatelőkészítés prediktív modell tanítása az R-ben'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: A jelen háromrészes oktatóanyag-sorozat első részében egy Azure SQL Database-adatbázisból készít elő egy prediktív modellt az R-ben a Azure SQL Database Machine Learning Services (előzetes verzió) használatával.
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
ms.openlocfilehash: a82467a097c50314e8f26f4a5cc4507f867ad504
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053770"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Oktatóanyag: az Azure SQL Database Machine Learning Services (előzetes verzió) segítségével előkészítheti a prediktív modelleket az R-ben.
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A háromrészes oktatóanyag-sorozat első részében az R használatával importálhatja és készítheti elő az Azure SQL Database-adatbázisok adatait. A sorozat későbbi részében ezeket az adattípusokat a prediktív gépi tanulási modellek betanítására és üzembe helyezésére fogja használni az R Azure SQL Database Machine Learning Services (előzetes verzió) használatával.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

Ebben az oktatóanyag-sorozatban Képzelje el, hogy Ön a Ski Rental Business, és szeretné megjósolni, hogy a későbbiekben hány bérletet fog használni. Ez az információ segítséget nyújt a készlet, a személyzet és a létesítmények előkészítéséhez.

A sorozat első és két részén egy R-szkriptet fejleszt ki a RStudio-ben az adatai előkészítéséhez és a gépi tanulási modellek betanításához. Ezt követően a harmadik részen az R-szkripteket az SQL Database-ben tárolt eljárásokkal futtathatja.

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

> [!div class="checklist"]
>
> * Mintaadatbázis importálása Azure SQL Database-adatbázisba R használatával
> * Az adatok betöltése az Azure SQL Database-ből egy R-adatkeretbe
> * Készítse elő az R-t az egyes oszlopok kategorikusként való azonosításával

A [második részből](predictive-model-build-compare-tutorial.md)megtudhatja, hogyan hozhat létre és taníthat több gépi tanulási modellt az R-ben, majd kiválaszthatja a legpontosabbat.

A [harmadik részből](predictive-model-deploy-tutorial.md)megtudhatja, hogyan tárolhatja a modellt egy adatbázisban, majd hogyan hozhat létre tárolt eljárásokat az 1. és a 2. részekben kifejlesztett R-parancsfájlok alapján. A tárolt eljárások egy SQL-adatbázisban futnak, így az előrejelzések az új adatértékek alapján lesznek elérhetők.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/) a Kezdés előtt.

* [Azure SQL Database, ha engedélyezve van a Machine learning Services (R)](machine-learning-services-overview.md) .

* RevoScaleR csomag – a csomag helyi telepítéséhez szükséges [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) lásd:.

* R IDE – ez az oktatóanyag a [RStudio Desktopot](https://www.rstudio.com/products/rstudio/download/)használja.

* SQL-lekérdezési eszköz – ez az oktatóanyag feltételezi, hogy [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) vagy [SQL Server Management Studiot](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) használ.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>A mintaadatbázis importálása

Az oktatóanyagban használt minta adatkészletet egy **. bacpac** -adatbázis biztonsági másolati fájljába mentette a letöltéshez és a használatához.

1. Töltse le a [oktatóanyagaiban. bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac)fájlt.

1. Kövesse az BACPAC- [fájl importálása egy Azure SQL Database-adatbázis létrehozásához](https://docs.microsoft.com/azure/sql-database/sql-database-import)című témakör utasításait a következő információk használatával:

   * Importálás a letöltött **oktatóanyagaiban. bacpac** fájlból
   * A nyilvános előzetes verzióban válassza ki az új adatbázis **Gen5/virtuális mag-** konfigurációját
   * Nevezze el az új "oktatóanyagaiban" adatbázist

## <a name="load-the-data-into-a-data-frame"></a>Az adatgyűjtés egy adatkeretbe

Az R-ben tárolt adatok használatához az Azure SQL Database-ből származó adatok egy adatkeretbe () lesznek betöltve `rentaldata` .

Hozzon létre egy új RScript-fájlt a RStudio-ben, és futtassa az alábbi szkriptet. Cserélje le a **kiszolgálót**, az **UID**-t és a **pwd** a saját kapcsolatok adataira.

```r
#Define the connection string to connect to the TutorialDB database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Logical SQL server>",
               "; Database=TutorialDB",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");

#Get the data from the table
SQL_rentaldata <- RxSqlServerData(table = "dbo.rental_data", connectionString = connStr, returnDataFrame = TRUE);

#Import the data into a data frame
rentaldata <- rxImport(SQL_rentaldata);

#Take a look at the structure of the data and the top rows
head(rentaldata);
str(rentaldata);
```

A következőhöz hasonló eredményeknek kell megjelennie.

```results
   Year  Month  Day  RentalCount  WeekDay  Holiday  Snow
1  2014    1     20      445         2        1      0
2  2014    2     13       40         5        0      0
3  2013    3     10      456         1        0      0
4  2014    3     31       38         2        0      0
5  2014    4     24       23         5        0      0
6  2015    2     11       42         4        0      0
'data.frame':       453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : num  2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : int  1 0 0 0 0 0 0 0 0 0 ...
$ Snow       : num  0 0 0 0 0 0 0 0 0 0 ...
```

## <a name="prepare-the-data"></a>Az adatok előkészítése

Ebben a mintaadatbázisban már megtörtént az előkészítés nagy része, de még egy előkészületet is végrehajthat.
A következő R-szkripttel azonosíthatja a három oszlopot *kategóriánként* , ha az adattípusokat a *faktor*értékre módosítja.

```r
#Changing the three factor columns to factor types
rentaldata$Holiday <- factor(rentaldata$Holiday);
rentaldata$Snow    <- factor(rentaldata$Snow);
rentaldata$WeekDay <- factor(rentaldata$WeekDay);

#Visualize the dataset after the change
str(rentaldata);
```

A következőhöz hasonló eredményeknek kell megjelennie.

```results
data.frame':      453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : Factor w/ 7 levels "1","2","3","4",..: 2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : Factor w/ 2 levels "0","1": 2 1 1 1 1 1 1 1 1 1 ...
$ Snow       : Factor w/ 2 levels "0","1": 1 1 1 1 1 1 1 1 1 1 ...
```

Az adatgyűjtés most már készen áll a képzésre.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha nem folytatja ezt az oktatóanyagot, törölje a oktatóanyagaiban-adatbázist a kiszolgálóról.

A Azure Portal hajtsa végre az alábbi lépéseket:

1. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** vagy **SQL-adatbázis**lehetőséget.
1. A **szűrés név szerint...** mezőbe írja be a **oktatóanyagaiban**nevet, és válassza ki az előfizetését.
1. Válassza ki a oktatóanyagaiban-adatbázist.
1. Az **Áttekintés** oldalon válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések

Az oktatóanyag-sorozat első részében a következő lépéseket végezte el:

* Mintaadatbázis importálása Azure SQL Database-adatbázisba R használatával
* Az adatok betöltése az Azure SQL Database-ből egy R-adatkeretbe
* Készítse elő az R-t az egyes oszlopok kategorikusként való azonosításával

A oktatóanyagaiban-adatbázisból származó adatokkal rendelkező Machine learning-modell létrehozásához kövesse az oktatóanyag-sorozat második részét:

> [!div class="nextstepaction"]
> [Oktatóanyag: prediktív modell létrehozása az R-ben Azure SQL Database Machine Learning Services (előzetes verzió)](predictive-model-build-compare-tutorial.md)

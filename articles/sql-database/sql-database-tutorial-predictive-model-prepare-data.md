---
title: 'Oktatóanyag: Betanítunk egy prediktív modellt az R az adatok előkészítése'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: A háromrészes oktatóanyag-sorozat egyik részben fogja készítse elő az adatokat egy Azure SQL database betanítunk egy prediktív modellt az Azure SQL Database, Machine Learning Services (előzetes verzió) az R.
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
ms.openlocfilehash: aa9c41ee34a50ab9b1409357bfe7d123166601bf
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978734"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Oktatóanyag: Betanítunk egy prediktív modellt az Azure SQL Database, Machine Learning Services (előzetes verzió) az R az adatok előkészítése

A háromrészes oktatóanyag-sorozat egyik részben fogja készítse elő az adatokat egy Azure SQL database betanítunk egy prediktív modellt az Azure SQL Database, Machine Learning Services (előzetes verzió) az R.

Az oktatóanyag-sorozat, az imagine télisport bérleti üzleti a saját és a számát, amely rendelkezni fog egy későbbi dátumon bérlését előre jelezni kívánt. Ez az információ segít Felkészülés a készlet, a személyzet és a létesítményekben.

Ebből a cikkből megtudhatja, hogyan lehet:

> [!div class="checklist"]
> * Minta importálása egy Azure SQL database-be
> * Az adatok betöltése az Azure SQL database-ből az R használata adatok keretbe
> * Néhány kategorikus oszlopok azonosításával az adatok előkészítése

A [második](sql-database-tutorial-predictive-model-build-compare.md), megtudhatja, hogyan hozhat létre, és több modelleket taníthat be és majd a legpontosabb közül választhat.

A [harmadik részt](sql-database-tutorial-predictive-model-deploy.md), megtudhatja, hogyan tárolja a model adatbázisban, és majd hozzon létre egy tárolt eljárást, amely megkönnyíti az új adatok alapján előrejelzéseket.

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

Ebben az oktatóanyagban használt minta adatkészlet mentése megtörtént a **.bacpac** , töltse le és használja az adatbázis biztonsági másolatát.

1. Töltse le a fájlt [TutorialDB.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac).

1. Kövesse az utasításokat [hozhat létre Azure SQL-adatbázis BACPAC-fájl importálása](https://docs.microsoft.com/azure/sql-database/sql-database-import), használja ezeket az adatokat:

   * Importálhat a **TutorialDB.bacpac** letöltött fájl
   * A nyilvános előzetes verzió ideje alatt válassza ki a **Gen5/virtuális mag** az új adatbázis konfigurálása
   * Nevezze el az új adatbázis "Oktatóanyagaiban tutorialdb adatbázist"

## <a name="load-the-data-into-a-data-frame"></a>Az adatok betöltése az adatkeretek

Szeretne használni az adatok R, meg fogjuk az adatok betöltése az az Azure SQL database az adatkeretek (`rentaldata`).

Az RStudio hozzon létre egy új RScript fájlt, és futtassa a következő szkriptet. Cserélje le **kiszolgáló**, **UID**, és **PWD** a saját kapcsolatadatokkal.

```r
#Define the connection string to connect to the TutorialDB database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
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

A következőhöz hasonló eredményt kell megjelennie.

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

Ez a mintaadatbázis az előkészítés többsége már megtörtént, de egy több előkészítést itt teheti.
A következő R-parancsfájl segítségével azonosíthatók a három oszlopok *kategóriák* az adattípusokat módosításával *tényező*.

```r
#Changing the three factor columns to factor types
rentaldata$Holiday <- factor(rentaldata$Holiday);
rentaldata$Snow    <- factor(rentaldata$Snow);
rentaldata$WeekDay <- factor(rentaldata$WeekDay);

#Visualize the dataset after the change
str(rentaldata);
```

A következőhöz hasonló eredményt kell megjelennie.

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

Az adatok elő van készítve a betanításhoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem ez az oktatóanyag folytatásához, törölje a oktatóanyagaiban tutorialdb adatbázist adatbázist az Azure SQL Database-kiszolgáló.

Az Azure Portalról kövesse az alábbi lépéseket:

1. A bal oldali menüben az Azure Portalon, válassza ki a **összes erőforrás** vagy **SQL-adatbázisok**.
1. Az a **Szűrés név alapján...**  írja be a következőt **oktatóanyagaiban tutorialdb adatbázist**, és válassza ki előfizetését.
1. Válassza ki a oktatóanyagaiban tutorialdb adatbázist adatbázist.
1. Az **Áttekintés** oldalon válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések

Az első rész a jelen oktatóanyag-sorozatban elvégezte ezeket a lépéseket:

* Egy adatbázis biztonságimásolat-fájl importálása az Azure SQL database-be
* Az adatok betöltése az Azure SQL database-ből az R használata adatok keretbe
* Az adatok előkészítése az egyes kategóriák oszlopok azonosításával

Hozzon létre egy gépi tanulási modellt, amely a oktatóanyagaiban tutorialdb adatbázist adatbázisban található adatokat használ, kövesse az oktatóanyag-sorozat második részére:

> [!div class="nextstepaction"]
> [Oktatóanyag: Egy prediktív modell létrehozásához az Azure SQL Database, Machine Learning Services (előzetes verzió) az R](sql-database-tutorial-predictive-model-build-compare.md)

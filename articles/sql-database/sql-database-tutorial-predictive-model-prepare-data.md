---
title: 'Oktatóanyag: Adatok előkészítése prediktív modell betanításához az R-ben'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: A három részes oktatóanyag-sorozat első részében előkészíti az adatokat egy Azure SQL-adatbázisból, hogy betanítsa a prediktív modellt az R-ben az Azure SQL Database Machine Learning Services használatával (előzetes verzió).
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
ms.openlocfilehash: 505f58f13a7186948a228fefe872d74fb98eba33
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345777"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Oktatóanyag: Adatok előkészítése az R-ben egy prediktív modell betanításához az Azure SQL Database Machine Learning Services szolgáltatással (előzetes verzió)

A három részes oktatóanyag-sorozat első részében az R használatával importálja és előkészíti az adatokat egy Azure SQL-adatbázisból. Ebben a sorozatban később ezeket az adatokat fogja használni egy prediktív gépi tanulási modell betanításához és üzembe helyezéséhez az R-ben az Azure SQL Database Machine Learning Services használatával (előzetes verzió).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Ebben a bemutató sorozat, képzeljük el, hogy a saját síkölcsönző vállalkozás, és szeretné megjósolni, hogy hány bérleti, hogy lesz egy későbbi időpontban. Ez az információ segít abban, hogy a készlet, a személyzet és a létesítmények készen álljanak.

A sorozat első és második részében az RStudio néhány R-parancsfájlt fejleszt az adatok előkészítéséhez és a gépi tanulási modell betanításához. Ezután a harmadik részben az okat az R-parancsfájlokat egy SQL-adatbázisban futtatja tárolt eljárások használatával.

Ebben a cikkben megtudhatja, hogyan:

> [!div class="checklist"]
> * Mintaadatbázis importálása Azure SQL-adatbázisba az R használatával
> * Adatok betöltése az Azure SQL-adatbázisból egy R adatkeretbe
> * Az r-ben lévő adatok előkészítése néhány oszlop kategorikusként való azonosításával

A [második részben](sql-database-tutorial-predictive-model-build-compare.md)megtudhatja, hogyan hozhat létre és taníthat be több gépi tanulási modellt az R-ben, majd kiválaszthatja a legpontosabbat.

A [harmadik részben](sql-database-tutorial-predictive-model-deploy.md)megtudhatja, hogyan tárolhatja a modellt egy adatbázisban, majd hozzon létre tárolt eljárásokat az első és a második részben kifejlesztett R-parancsfájlokból. A tárolt eljárások egy SQL-adatbázisban futnak, hogy új adatok alapján előrejelzéseket készítsenek.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

* Azure SQL Database Server a Machine Learning Services engedélyezve van – A nyilvános előzetes verzió során a Microsoft befogja a fedélzetre, és engedélyezi a gépi tanulást a meglévő vagy az új adatbázisokhoz. Kövesse a [Regisztráció az előnézetre](sql-database-machine-learning-services-overview.md#signup)című részben található lépéseket.

* RevoScaleR-csomag – A csomag helyi telepítésének lehetőségeiről a [RevoScaleR című](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) témakörben található.

* R IDE - Ez a bemutató használ [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* SQL-lekérdezési eszköz – Ez az oktatóanyag feltételezi, hogy [az Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) vagy az SQL Server Management [Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) használatával.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

## <a name="import-the-sample-database"></a>Mintaadatbázis importálása

Az oktatóanyagban használt mintaadatkészletet egy **.bacpac** adatbázis biztonsági másolatfájlba mentette a letöltéshez és a használathoz.

1. Töltse le a fájlt [TutorialDB.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac).

1. Kövesse a [BACPAC-fájl importálása](https://docs.microsoft.com/azure/sql-database/sql-database-import)című részben található utasításokat az Azure SQL-adatbázis létrehozásához az alábbi adatok használatával:

   * Importálás a letöltött **TutorialDB.bacpac** fájlból
   * A nyilvános előzetes verzió során válassza ki az új adatbázis **Gen5/virtuálismag-konfigurációját**
   * Nevezze el az új adatbázist "TutorialDB"

## <a name="load-the-data-into-a-data-frame"></a>Az adatok betöltése adatkeretbe

Az R-ben lévő adatok használatához az Azure SQL-adatbázisból származó`rentaldata`adatokat egy adatkeretbe ( tölti be.

Hozzon létre egy új RScript-fájlt az RStudio-ban, és futtassa a következő parancsfájlt. Cserélje le **a Kiszolgálót**, **az UID-t**és **a PWD-t** a saját kapcsolati adataira.

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

Az eredményeknek az alábbiakhoz hasonlónak kell lenniük.

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

Ebben a mintaadatbázisban a készítmény nagy része már megtörtént, de itt még egy előkészületet fog végezni.
A következő R parancsfájl segítségével három oszlopot *azonosíthat kategóriaként* az adattípusok *tényezőre*történő módosításával.

```r
#Changing the three factor columns to factor types
rentaldata$Holiday <- factor(rentaldata$Holiday);
rentaldata$Snow    <- factor(rentaldata$Snow);
rentaldata$WeekDay <- factor(rentaldata$WeekDay);

#Visualize the dataset after the change
str(rentaldata);
```

Az eredményeknek az alábbiakhoz hasonlónak kell lenniük.

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

Az adatok most már előkészítve betanítás.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja ezt az oktatóanyagot, törölje a TutorialDB adatbázist az Azure SQL Database-kiszolgálóról.

Az Azure Portalon kövesse az alábbi lépéseket:

1. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** vagy **SQL-adatbázis lehetőséget.**
1. A **Szűrés név szerint...** mezőbe írja be a **TutorialDB**mezőt, és válassza ki az előfizetést.
1. Válassza ki a TutorialDB adatbázist.
1. Az **Áttekintés** oldalon válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések

Az oktatóanyag-sorozat első részében az alábbi lépéseket hajtotta végre:

* Mintaadatbázis importálása Azure SQL-adatbázisba az R használatával
* Adatok betöltése az Azure SQL-adatbázisból egy R adatkeretbe
* Az r-ben lévő adatok előkészítése néhány oszlop kategorikusként való azonosításával

Ha olyan gépi tanulási modellt szeretne létrehozni, amely a TutorialDB adatbázis adatait használja, kövesse az oktatóanyag-sorozat második részét:

> [!div class="nextstepaction"]
> [Oktatóanyag: Prediktív modell létrehozása az R-ben az Azure SQL Database Machine Learning Services szolgáltatással (előzetes verzió)](sql-database-tutorial-predictive-model-build-compare.md)

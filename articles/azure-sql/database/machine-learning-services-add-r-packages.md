---
title: R-csomag hozzáadása a Machine Learning Serviceshoz (előzetes verzió)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Ez a cikk azt ismerteti, hogyan telepíthet olyan R-csomagot, amely még nincs telepítve Azure SQL Database Machine Learning Servicesban (előzetes verzió).
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
ms.date: 04/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 764d0131b0b8074a210bd9eb7f806f5d1a32fa1f
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84324232"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>R-csomag hozzáadása Azure SQL Database Machine Learning Services (előzetes verzió)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a cikk azt ismerteti, hogyan adhat hozzá R-csomagokat Azure SQL Database Machine Learning Services (előzetes verzió).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Telepítse az [R](https://www.r-project.org) és a [RStudio Desktopot](https://www.rstudio.com/products/rstudio/download/) a helyi számítógépre. Az R Windows, macOS és Linux rendszerekhez érhető el. Ez a cikk feltételezi, hogy a Windowst használja.

- Ez a cikk például [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) vagy [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) használatával futtatja az R-szkriptet Azure SQL Databaseban. Az R-szkripteket más adatbázis-kezelő vagy lekérdezési eszközökkel is futtathatja, de ez a példa Azure Data Studio vagy SSMS feltételezi.
   
> [!NOTE]
> A csomagok nem telepíthetők olyan R-szkript futtatásával, Azure Data Studio vagy SSMS **sp_execute_external_script** használatával. A csomagokat csak az R parancssor és a RStudio használatával telepítheti és távolíthatja el, a jelen cikkben leírtak szerint. A csomag telepítése után egy R-szkriptben elérheti a Package functions funkciót **sp_execute_external_script**használatával.

## <a name="list-r-packages"></a>R-csomagok listázása

A Microsoft számos, előre telepített R-csomagot biztosít az Azure SQL Database Machine Learning Services.
A telepített R-csomagok listáját a következő parancs futtatásával Azure Data Studio vagy SSMS is megtekintheti.

1. Nyissa meg Azure Data Studio vagy SSMS, és kapcsolódjon a Azure SQL Databasehoz.

1. Futtassa az alábbi parancsot:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
    OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License")]);'
    WITH RESULT SETS((
                Package NVARCHAR(255)
                , Version NVARCHAR(100)
                , Depends NVARCHAR(4000)
                , License NVARCHAR(1000)
                ));
    ```

A kimenetnek az alábbihoz hasonlóan kell kinéznie.

**Eredmények**

![Telepített csomagok az R-ben](./media/machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>Csomag hozzáadása a sqlmlutils

Ha olyan csomagot kell használnia, amely még nincs telepítve a Azure SQL Databaseban, akkor a [sqlmlutils](https://github.com/Microsoft/sqlmlutils)használatával telepítheti. a **sqlmlutils** egy olyan csomag, amely segítséget nyújt a felhasználóknak a Microsoft Azure SQL-és SQL Server-adatbázisokhoz, valamint r vagy Python-kód végrehajtásához az SQL-ben r-vagy Python-ügyfélről. Jelenleg csak az **Sqlmlutils** R verziója támogatott a Azure SQL Databaseban.

A következő példában a karakterláncok formázására és interpolációra képes **[ragasztó](https://cran.r-project.org/web/packages/glue/)** csomagot kell telepíteni. Ezekkel a lépésekkel telepítheti a **sqlmlutils** és a **RODBCext** (a **sqlmlutils**előfeltételeit), és hozzáadhatja a **ragasztó** csomagot.

### <a name="install-sqlmlutils"></a>A **sqlmlutils** telepítése

1. Töltse le a legújabb **sqlmlutils** zip-fájlt a számítógépről a https://github.com/Microsoft/sqlmlutils/tree/master/R/dist helyi számítógépre. Nem kell kibontani a fájlt.

1. Nyisson meg egy **parancssort** , és futtassa a következő parancsokat a **RODBCext** és a **sqlmlutils** telepítéséhez a helyi számítógépen. Helyettesítse be a letöltött **sqlmlutils** zip-fájl teljes elérési útját (a példa feltételezi, hogy a fájl a dokumentumok mappában található).
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    A megjelenített kimenetnek a következőhöz hasonlónak kell lennie.

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > Ha a következő hibaüzenet jelenik meg: "R" nem ismerhető fel belső vagy külső parancsként, futtatható programként vagy batch-fájlként ", akkor valószínű, hogy az R. exe elérési útja nem szerepel a Windows rendszerben a **path** környezeti változóban. Adja hozzá az elérési utat a környezeti változóhoz, vagy keresse meg a parancssorban a mappát (például `cd C:\Program Files\R\R-3.5.3\bin` ), majd próbálja megismételni a parancsot.

### <a name="add-the-package"></a>A csomag hozzáadása

1. Indítsa el az RStudio alkalmazást, majd hozzon létre egy új **R-szkript** fájlt. 

1. A következő R-kód használatával telepítse a **ragasztó** csomagot a **sqlmlutils**használatával. Helyettesítse be a saját Azure SQL Database a kapcsolatok adatait.

    ```R
    library(sqlmlutils)
    connection <- connectionInfo(
    server= "yourserver.database.windows.net",
    database = "yourdatabase",
    uid = "yoursqluser",
    pwd = "yoursqlpassword")
    
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!TIP]
    > A **hatókör** lehet **nyilvános** vagy **privát**. A nyilvános hatókörrel az adatbázis rendszergazdája minden felhasználó által használható csomagokat tölthet fel. A privát hatókör csak azon felhasználó számára teszi elérhetővé a csomagot, aki telepíti. Ha nem adja meg a hatókört, a rendszer alapértelmezés szerint a **PRIVATE** értéket fogja használni.

### <a name="verify-the-package"></a>A csomag ellenőrzése

A következő R-szkript futtatásával ellenőrizze, hogy a RStudio telepítve van-e a **kapcsolási** csomag. Használja ugyanazt a **kapcsolatokat** , amelyet az előző lépésben adott meg.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**Eredmények**

![Az RTestData tábla tartalma](./media/machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>A csomag használata

A csomag telepítése után a **sp_execute_external_script**használatával R-szkriptben is használhatja.

1. Nyissa meg Azure Data Studio vagy SSMS, és kapcsolódjon a Azure SQL Databasehoz.

1. Futtassa az alábbi parancsot:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    library(glue)
    
    name <- "Fred"
    age <- 50
    anniversary <- as.Date("2020-06-14")
    text <- glue(''My name is {name}, '',
    ''my age next year is {age + 1}, '',
    ''my anniversary is {format(anniversary, "%A, %B %d, %Y")}.'')
    
    print(text)
    ';
    ```

    A következő eredmény jelenik meg az **üzenetek** lapon.

    **Eredmények**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>A csomag eltávolítása

Ha el szeretné távolítani a csomagot, futtassa a következő R-szkriptet a RStudio-ben. Használja ugyanazt a **kapcsolatokat** , amelyet korábban adott meg.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Az R-csomagok az Azure SQL Database-be való telepítésének egy másik módja, ha az R-csomagot egy byte streamből tölti fel a **külső függvénytár létrehozása** T-SQL-utasítás használatával. Lásd: [könyvtár létrehozása bájtos adatfolyamból](/sql/t-sql/statements/create-external-library-transact-sql#create-a-library-from-a-byte-stream) a [külső könyvtár létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) dokumentációjában.

## <a name="next-steps"></a>Következő lépések

Az R (előzetes verzió) Azure SQL Database Machine Learning Servicesról a következő cikkekben talál további információt.

- [Azure SQL Database Machine Learning Services R-vel (előzetes verzió)](machine-learning-services-overview.md)
- [Speciális R függvények írása a Azure SQL Database Machine Learning Services használatával (előzetes verzió)](machine-learning-services-functions.md)
- [R-és SQL-adatmennyiség használata Azure SQL Database Machine Learning Servicesban (előzetes verzió)](machine-learning-services-data-issues.md)

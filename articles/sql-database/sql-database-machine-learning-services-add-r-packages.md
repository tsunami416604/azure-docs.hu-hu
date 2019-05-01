---
title: Egy R-csomag hozzáadása az Azure SQL Database, Machine Learning Services (előzetes verzió)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Ez a cikk bemutatja, hogyan telepítheti egy R-csomagot, amely még nincs telepítve az Azure SQL Database, Machine Learning Services (előzetes verzió).
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
ms.openlocfilehash: 4e7145570cbc906ea540c9d8f95f6c3cbde1c610
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927099"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>Egy R-csomag hozzáadása az Azure SQL Database, Machine Learning Services (előzetes verzió)

Ez a cikk ismerteti az Azure SQL Database, Machine Learning Services (előzetes verzió) egy R-csomag hozzáadása.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Telepítés [R](https://www.r-project.org) és [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) a helyi számítógépen. Az R Windows, macOS és a Linux rendszerekhez érhető el. Ez a cikk feltételezi, hogy Windows használata esetén.

- Ez a cikk egy példát tartalmaz [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) vagy [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) futtatásához egy R parancsfájlt az Azure SQL Database-ben. Más adatbázis-kezelő vagy a lekérdezési eszközökkel R-szkriptek futtatása, de ez a példa feltételezi, hogy az Azure Data Studio vagy az SSMS.
   
> [!NOTE]
> A csomag nem telepíthető egy R parancsfájlt a futtatásával **sp_execute_external_script** Azure Data Studio vagy az ssms-ben. Csak telepítse és használja az R-parancssor és az RStudio ebben a cikkben leírtak szerint csomagok eltávolítása. A csomag telepítése után hozzáférhet-e a csomag funkciókat egy R parancsfájlt a **sp_execute_external_script**.

## <a name="list-r-packages"></a>R-csomagok listázása

A Microsoft számos olyan R-csomagok előre telepítve a Machine Learning-szolgáltatások az Azure SQL database-ben.
A telepített R-csomagok listáját láthatja az Azure Data Studio vagy az SSMS az alábbi parancs futtatásával.

1. Nyissa meg az Azure Data Studio vagy az ssms-ben, és csatlakozzon az Azure SQL Database.

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

A kimenet az alábbihoz hasonlóan kell kinéznie.

**Results**

![Telepített csomagok az R-ben](./media/sql-database-machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>A sqlmlutils csomag hozzáadása

Ha, amely még nincs telepítve az Azure SQL Database a csomag használatára van szüksége, telepítheti a [sqlmlutils](https://github.com/Microsoft/sqlmlutils). **sqlmlutils** egy olyan csomag, amelyek segítségével a felhasználók SQL-adatbázisok (SQL Server és Azure SQL Database) használhatnak, és hajtsa végre az R vagy Python nyelvű programkódjával SQL R vagy Python-ügyfélről. Jelenleg csak az R verzióját **sqlmlutils** Azure SQL Database-ben támogatott.

Telepítse a következő példában a **[kapcsolás](https://cran.r-project.org/web/packages/glue/)** csomag, amely formázza és karakterláncok interpolálja. Ezeket a lépéseket telepítése **sqlmlutils** és **RODBCext** (előfeltétel **sqlmlutils**), és adja hozzá a **kapcsolás** csomag.

### <a name="install-sqlmlutils"></a>Telepítés **sqlmlutils**

1. Töltse le a legújabb **sqlmlutils** zip-fájlt a https://github.com/Microsoft/sqlmlutils/tree/master/R/dist a helyi számítógépen. Nem kell bontsa ki a fájlt.

1. Nyissa meg a **parancssor** , és futtassa az alábbi parancsokat **RODBCext** és **sqlmlutils** a helyi számítógépen. Helyettesítse be a teljes elérési útja a **sqlmlutils** zip fájl letöltött (a példa feltételezi, hogy a fájl a dokumentumok mappában van).
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    A kimenetben megjelenik az alábbihoz hasonló lehet.

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > Ha a hiba, "A"R"nem ismerhető fel belső vagy külső parancs, működtethető program vagy parancsfájl", azt valószínűleg azt jelenti, hogy R.exe elérési útja nem szerepel a **elérési út** Windows környezeti változóba. Adja hozzá az elérési utat a környezeti változó vagy lépjen abba a mappába, a parancssori ablakban (például `cd C:\Program Files\R\R-3.5.3\bin`), és próbálkozzon újra a parancsot.

### <a name="add-the-package"></a>Adja hozzá a csomagot

1. Nyissa meg az RStudio, és hozzon létre egy új **R-szkript** fájlt. 

1. A következő R-kód segítségével telepítse a **kapcsolás** csomag használatával **sqlmlutils**. Helyettesítse be a saját Azure SQL Database kapcsolati adatait.

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
    > A **hatókör** lehet **nyilvános** vagy **PRIVÁT**. A nyilvános hatókörrel az adatbázis rendszergazdája minden felhasználó által használható csomagokat tölthet fel. Privát hatókör elérhetővé teszi a csomag csak a felhasználó, aki telepíti azt. Ha nem adja meg a hatókört, a rendszer alapértelmezés szerint a **PRIVATE** értéket fogja használni.

### <a name="verify-the-package"></a>A csomag ellenőrzése

Ellenőrizze, hogy a **kapcsolás** csomag telepítve van az RStudio a következő R-szkript futtatásával. Ugyanaz, mint **kapcsolat** az előző lépésben meghatározott.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**Results**

![Az RTestData tábla tartalma](./media/sql-database-machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>A csomag használatára

Miután telepítette a csomagot, használhatja azt az R-szkriptek segítségével **sp_execute_external_script**.

1. Nyissa meg az Azure Data Studio vagy az ssms-ben, és csatlakozzon az Azure SQL Database.

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

    Az alábbi eredményt láthatja a **üzenetek** fülre.

    **Results**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>Távolítsa el a csomagot

Ha szeretné eltávolítani a csomagot, futtassa a következő R-szkriptet az RStudio. Ugyanaz, mint **kapcsolat** korábban megadott.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Egy másik módja egy R-csomag telepítése az Azure SQL Database, hogy az R-csomagot bájt stream használatával töltse fel a **CREATE EXTERNAL LIBRARY** T-SQL-utasítással. Lásd: [hozzon létre egy könyvtárat bájt streamből](/sql/t-sql/statements/create-external-library-transact-sql#c-create-a-library-from-a-byte-stream) a a [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) referenciák dokumentációiba.

## <a name="next-steps"></a>További lépések

Az Azure SQL Database Machine Learning Services (előzetes verzió) R-rel kapcsolatos további információkért tekintse meg a következő cikkeket.

- [Az Azure SQL Database Machine Learning-szolgáltatások (előzetes verzió) R-rel](sql-database-machine-learning-services-overview.md)
- [Az Azure SQL Database, Machine Learning-Szolgáltatásokoz (előzetes verzió) használatával speciális R-függvények írása](sql-database-machine-learning-services-functions.md)
- [R- és SQL adatok használata Azure SQL Database, Machine Learning Services (előzetes verzió)](sql-database-machine-learning-services-data-issues.md)
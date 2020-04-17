---
title: R-csomag hozzáadása a Machine Learning Services szolgáltatáshoz (előzetes verzió)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Ez a cikk bemutatja, hogyan telepíthet olyan R-csomagot, amely még nincs telepítve az Azure SQL Database Machine Learning Services szolgáltatásban (előzetes verzió).
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
ms.openlocfilehash: ab066609bff773ceacb06be604e386eed5cdf7ec
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453336"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>R-csomag hozzáadása az Azure SQL Database Machine Learning Services szolgáltatáshoz (előzetes verzió)

Ez a cikk bemutatja, hogyan adhat hozzá egy R-csomagot az Azure SQL Database Machine Learning Services (előzetes verzióhoz).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Telepítse [az R](https://www.r-project.org) és [rstudio asztalt](https://www.rstudio.com/products/rstudio/download/) a helyi számítógépre. Az R Windows, macOS és a Linux rendszerekhez érhető el. Ez a cikk feltételezi, hogy Windows rendszert használ.

- Ez a cikk egy példát tartalmaz az [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) vagy az SQL Server Management [Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) használatával egy R-parancsfájl futtatásához az Azure SQL Database-ben. R-parancsfájlok futtathatók más adatbázis-kezelő vagy lekérdezési eszközök használatával, de ez a példa feltételezi az Azure Data Studio vagy az SSMS.
   
> [!NOTE]
> Nem telepíthet egy csomagot r-parancsfájl futtatásával **sp_execute_external_script** használatával az Azure Data Stu-ban vagy az SSMS-ben. A csomagokat csak az R parancssorés az RStudio használatával telepítheti és távolíthatja el a cikkben leírtak szerint. A csomag telepítése után a csomag funkcióit r parancsfájlban sp_execute_external_script **használatával**érheti el.

## <a name="list-r-packages"></a>R-csomagok listázása

A Microsoft számos, a Machine Learning Services szolgáltatással előre telepített R-csomagot biztosít az Azure SQL-adatbázisában.
A telepített R-csomagok listáját az Azure Data Studio vagy az SSMS következő parancsának futtatásával láthatja.

1. Nyissa meg az Azure Data Studio vagy az SSMS alkalmazást, és csatlakozzon az Azure SQL-adatbázisához.

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

A kimenetnek a következőhöz hasonlóan kell kinéznie.

**Results (Eredmények)**

![Telepített csomagok az R-ben](./media/sql-database-machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>Csomag hozzáadása sqlmlutils-szel

Ha olyan csomagot kell használnia, amely még nincs telepítve az Azure SQL Database-ben, telepítheti azt [az sqlmlutils](https://github.com/Microsoft/sqlmlutils)használatával. **Az sqlmlutils** egy olyan csomag, amely nek segít a felhasználóknak az SQL-adatbázisok (SQL Server és Az Azure SQL Database) használatával való interakcióban, és R- vagy Python-kód végrehajtásában az SQL-ben egy R vagy Python-ügyfélről. Jelenleg csak az **sqlmlutils** R-verziója támogatott az Azure SQL Database.

A következő példában telepíti a karakterláncok formázását és interpolációját tartalmazó **[kapcsolási](https://cran.r-project.org/web/packages/glue/)** csomagot. Ezek a lépések **telepítik az sqlmlutils** és **a RODBCext** (az **sqlmlutils**előfeltétele), és hozzáadjuk a **kapcsolási** csomagot.

### <a name="install-sqlmlutils"></a>**Sqlmlutils** telepítése

1. Töltse le a legújabb **sqlmlutils** zip fájlt https://github.com/Microsoft/sqlmlutils/tree/master/R/dist a helyi számítógépre. Nem kell kicsomagolnia a fájlt.

1. Nyisson meg egy **parancssort,** és futtassa a következő parancsokat az **ÍRÁSvédett írásvédett tartományvezérlő** és **az sqlmlutils** helyi számítógépre való telepítéséhez. Helyettesítse a letöltött **sqlmlutils** zip fájl teljes elérési útját (a példa feltételezi, hogy a fájl a Dokumentumok mappában van).
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    A látható kimenetnek a következőhöz hasonlónak kell lennie.

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > Ha a hibaüzenet az "R" nem ismeri fel belső vagy külső parancsként, működtethető programként vagy kötegfájlként, akkor valószínűleg azt jelenti, hogy az R.exe elérési útja nem szerepel a Windows **PATH** környezeti változójában. Hozzáadhatja az elérési utat a környezeti változóhoz, vagy megnyithatja `cd C:\Program Files\R\R-3.5.3\bin`a mappát a parancssorban (például ), majd próbálkozzon újra a paranccsal.

### <a name="add-the-package"></a>A csomag hozzáadása

1. Indítsa el az RStudio alkalmazást, majd hozzon létre egy új **R-szkript** fájlt. 

1. A következő R-kód dal telepítheti a **ragasztócsomagot** **az sqlmlutils**segítségével. Helyettesítse saját Azure SQL Database-kapcsolati adatait.

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
    > A **hatókör** lehet **nyilvános** vagy **privát.** A nyilvános hatókörrel az adatbázis rendszergazdája minden felhasználó által használható csomagokat tölthet fel. A privát hatókör csak a telepítő felhasználó számára teszi elérhetővé a csomagot. Ha nem adja meg a hatókört, a rendszer alapértelmezés szerint a **PRIVATE** értéket fogja használni.

### <a name="verify-the-package"></a>A csomag ellenőrzése

Ellenőrizze, hogy a **kapcsolási** csomag telepítve van-e, ha az RStudio következő R parancsfájlját futtatja. Használja ugyanazt a **kapcsolatot,** amelyet az előző lépésben megadott.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**Results (Eredmények)**

![Az RTestData tábla tartalma](./media/sql-database-machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>Használja a csomagot

A csomag telepítése után r-parancsfájlban használhatja **a sp_execute_external_script**.

1. Nyissa meg az Azure Data Studio vagy az SSMS alkalmazást, és csatlakozzon az Azure SQL-adatbázisához.

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

    Az **Üzenetek** lapon a következő eredmény jelenik meg.

    **Results (Eredmények)**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>A csomag eltávolítása

Ha el szeretné távolítani a csomagot, futtassa a következő R parancsfájlt az RStudio-ban. Használja ugyanazt a **kapcsolatot,** amelyet korábban megadott.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Egy másik módja annak, hogy telepítse az R-csomagot az Azure SQL-adatbázisba, hogy töltse fel az R-csomagot egy bájtból a **CREATE EXTERNAL LIBRARY** T-SQL utasítás használatával. Lásd: [Tár létrehozása bájtfolyamból](/sql/t-sql/statements/create-external-library-transact-sql#create-a-library-from-a-byte-stream) a [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) referenciadokumentációjában.

## <a name="next-steps"></a>További lépések

Az Azure SQL Database Machine Learning Services r-rel (előzetes verzió) című témakörről az alábbi cikkekben talál további információt.

- [Azure SQL Database Machine Learning Services R-rel (előzetes verzió)](sql-database-machine-learning-services-overview.md)
- [Speciális R-függvények írása az Azure SQL Database-ben a Machine Learning Services használatával (előzetes verzió)](sql-database-machine-learning-services-functions.md)
- [R- és SQL-adatok kal való kapcsolat az Azure SQL Database Machine Learning Services szolgáltatásban (előzetes verzió)](sql-database-machine-learning-services-data-issues.md)
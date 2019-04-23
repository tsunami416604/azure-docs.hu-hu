---
title: Hozzon létre, és egyszerű R-szkriptek futtatása
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Egyszerű R-szkriptek futtatása az Azure SQL Database, Machine Learning Services (előzetes verzió).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: ada09959391c551a9eff4d96b186be29c1e3b7a8
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013257"
---
# <a name="create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Hozzon létre, és egyszerű R-szkriptek futtatása az Azure SQL Database, Machine Learning Services (előzetes verzió)

Ebben a rövid útmutatóban fog létrehozása és futtatása az egyszerű R-szkriptek használata a nyilvános előzetes verziója [Machine Learning-szolgáltatások (az r nyelv) az Azure SQL Database](sql-database-machine-learning-services-overview.md). Megtudhatja, hogyan burkolhatja egy megfelelően formázott R-szkript a tárolt eljárás az, hogy [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) , és hajtsa végre a parancsfájl egy SQL database-ben.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/) megkezdése előtt.

- Példakód futtatható a témaköröket, először szüksége van egy Azure SQL database, a Machine Learning-szolgáltatások (az r nyelv) engedélyezve van. A nyilvános előzetes verzió ideje alatt a Microsoft is megjelenik majd, és engedélyezze a gépi tanulás a meglévő vagy új adatbázis számára. Kövesse a [regisztrálni az előzetes verzióra](sql-database-machine-learning-services-overview.md#signup).

- Győződjön meg arról, hogy telepítette a legújabb [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Más adatbázis-kezelő vagy a lekérdezési eszközökkel R-szkriptek futtatása, de ez a rövid útmutató az ssms-t fogja használni.

- Ebben a rövid útmutatóban egy kiszolgálószintű tűzfalszabály konfigurálását igényli. Ennek módjáról további információkért lásd: [kiszolgálószintű tűzfalszabály létrehozása](sql-database-server-level-firewall-rule.md).

## <a name="run-a-simple-script"></a>Egy egyszerű szkript futtatása

Egy R-szkript futtatásához fog át kell adnia azt az argumentumként a rendszerszintű tárolt eljárással [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

A következő lépésekben fog futtassa az SQL database-ben Ez a példa R-szkript:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Nyissa meg az **SQL Server Management Studiót**, és csatlakozzon az SQL-adatbázishoz.

   Ha a kapcsolódás segítségre van szüksége, tekintse meg [a rövid útmutató: SQL Server Management Studio használatával csatlakozhat, és az Azure SQL Database-adatbázis lekérdezéséhez](sql-database-connect-query-ssms.md).

1. Adja át a teljes R-szkriptet a [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) tárolt eljárást.

   A parancsfájl keresztülmegy a `@script` argumentum. Minden belül a `@script` argumentum érvényes R-kódot kell lennie.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c(c, d))
    '
    ```

   Ha hibába ütközik, előfordulhat, hogy a Machine Learning Services (with R) nyilvános előzetes verziója nincs engedélyezve az SQL-adatbázishoz. Lásd: [Előfeltételek](#prerequisites) felett.

   > [!NOTE]
   > Ha Ön rendszergazda, a külső kód automatikusan is futtathatja. A engedélyt más felhasználóknak a parancsot:
   <br>**ENGEDÉLYEZÉS végrehajtása bármely külső parancsfájl a**  *\<felhasználónév\>*.

2. A megfelelő eredmény kiszámítása és az R `print` függvény az eredményt adja vissza a **üzenetek** ablak.

   valahogy így kell kinéznie.

    **Results**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Egy "Hello World" parancsfájl futtatása

Egy tipikus példaszkript egyike, amely csak a "Hello World" karakterlánc. Futtassa a következő parancsot.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

Ez a tárolt eljárás bemeneti adatokat a következők:

| | |
|-|-|
|*@language* | Meghatározza a szeretne hívásokat indítani, ebben az esetben az R nyelv bővítmény |
|*@script* | határozza meg a parancsok átadva az R futtatókörnyezettel. A teljes R-szkript közé kell tenni ehhez az argumentumhoz Unicode formátumú. Is hozzáadhatja a szöveg típusú változó **nvarchar** majd hívja a változó |
|*@input_data_1* | az R futtatókörnyezettel, amely visszaadja az adatokat az SQL Server adatkeretek átadott a lekérdezés által visszaadott adatok |
|A RESULT SETS | záradék meghatározása a visszaadott adatok tábla sémája az SQL Server, az oszlop neve "Hello World" hozzáadása **int** az adattípus |

A parancs kimenete a következő szöveget:

| Hello World |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>Használat bemenetek és kimenetek

Alapértelmezés szerint [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) egyetlen adatkészletet fogad bemenetként, amely általában adjon meg egy érvényes SQL-lekérdezést formájában. Ezután visszaadja egy egyetlen R-adatkeretbe kimenetként.

Csak egy bemeneti adathalmaz továbbítható paraméterként, és csak egy adathalmaz adható vissza. Azonban az R-kódból más adathalmazokat is meghívhat, és az adathalmaz mellett más típusú kimeneteket is visszaadhat. Az OUTPUT kulcsszó hozzáadásával az eredmények között bármely paramétert visszaadhatja.

Egyelőre tekintsük használja az alapértelmezett bemeneti és kimeneti változókat [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql): **InputDataSet** és **OutputDataSet**.

1. Tesztadatok kis tábla létrehozása.

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    
    INSERT INTO RTestData
    VALUES (1);
    
    INSERT INTO RTestData
    VALUES (10);
    
    INSERT INTO RTestData
    VALUES (100);
    GO
    ```

1. Használja a `SELECT` utasítással lekérdezése a tábla.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Results**

    ![Az RTestData tábla tartalma](./media/sql-database-connect-query-r/select-rtestdata.png)

1. A következő R-szkript futtatásához. Lekéri az adatokat a tábla használatával a `SELECT` utasítással, azt áthalad az R futtatókörnyezettel, és visszaadja az adatokat, adatkeretek. A `WITH RESULT SETS` záradékban az SQL Database, az oszlop nevét határozza meg a visszaadott adatok tábla sémája *NewColName*.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Results**

    ![Egy R-szkript kimenete, amely adatokat ad vissza egy táblából](./media/sql-database-connect-query-r/r-output-rtestdata.png)

1. Most módosítsuk a a bemeneti és kimeneti változók nevében. Az alapértelmezett bemeneti és kimeneti változók nevében a rendszer **InputDataSet** és **OutputDataSet**, ez a szkript módosítja a nevek **SQL_in** és **SQL_out**:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    Vegye figyelembe, hogy az R a kis-és nagybetűket. A bemeneti és kimeneti változókat használja az R-szkript (**SQL_out**, **SQL_in**) meg kell egyeznie a megadott érték beolvasása `@input_data_1_name` és `@output_data_1_name`, beleértve az esetet.

   > [!TIP]
   > Csak egy bemeneti adathalmaz továbbítható paraméterként, és csak egy adathalmaz adható vissza. Azonban az R-kódból más adathalmazokat is meghívhat, és az adathalmaz mellett más típusú kimeneteket is visszaadhat. Az OUTPUT kulcsszó hozzáadásával az eredmények között bármely paramétert visszaadhatja.

1. Csak az R-szkript használata nincs a bemeneti adatok értékeket is generálható (`@input_data_1` értéke üres).

   A következő parancsfájl jelenít meg a "hello" szöveggel és a "world".

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    mytextvariable <- c("hello", " ", "world");
    OutputDataSet <- as.data.frame(mytextvariable);
    '
        , @input_data_1 = N''
    WITH RESULT SETS(([Col1] CHAR(20) NOT NULL));
    ```

    **Results**

    ![Eredmények lekérdezése az @script bemenetként való használatával](./media/sql-database-connect-query-r/r-data-generated-output.png)

## <a name="check-r-version"></a>Az R verziószámának ellenőrzése

Ha szeretné, megtekintheti, melyik R verziója telepítve van az SQL database-ben, futtassa a következő szkriptet.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

Az R `print` funkciója a **Messages** (Üzenetek) ablakban adja vissza a verziót. Az alábbi példa kimenetében látható, hogy az SQL Database ebben az esetben rendelkezik R 3.4.4 telepített verziója.

**Results**

```text
STDOUT message(s) from external script:
                   _
platform       x86_64-w64-mingw32
arch           x86_64
os             mingw32
system         x86_64, mingw32
status
major          3
minor          4.4
year           2018
month          03
day            15
svn rev        74408
language       R
version.string R version 3.4.4 (2018-03-15)
nickname       Someone to Lean On
```

## <a name="list-r-packages"></a>R-csomagok listázása

A Microsoft számos előre telepített R-csomagot biztosít a Machine Learning Services-hez az SQL-adatbázisban.

Milyen r csomagok telepítése, verzió, a függőségeket, a licenc és a könyvtár elérési útja információk, beleértve listájának megtekintéséhez futtassa az alábbi parancsfájlt.

```SQL
EXEC sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
WITH result sets((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            , LibPath NVARCHAR(2000)
            ));
```

A kimenet származik `installed.packages()` az R és eredményeként visszaadott beállítása.

**Results**

![Telepített csomagok az R-ben](./media/sql-database-connect-query-r/r-installed-packages.png)

## <a name="next-steps"></a>További lépések

Hozzon létre egy gépi tanulási modellt az R használata SQL Database-ben, kövesse az ebben a rövid útmutatóban:

> [!div class="nextstepaction"]
> [Hozzon létre és betanítunk egy prediktív modellt az R az Azure SQL Database, Machine Learning Services (előzetes verzió)](sql-database-quickstart-r-train-score-model.md)

A Machine Learning-szolgáltatások további információkért lásd az alábbi cikkeket. Néhány, az alábbi cikkek, amelyek közül az SQL Server legtöbb információ akkor is vonatkozik a Machine Learning-szolgáltatások (az r nyelv) Azure SQL Database-ben.

- [Az Azure SQL Database Machine Learning-szolgáltatások (az R)](sql-database-machine-learning-services-overview.md)
- [SQL Server Machine Learning-szolgáltatások](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)
- [Oktatóanyag: Ismerje meg, az r nyelv használatát az SQL Server adatbázis-elemzés](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)
- [Teljes körű adatelemzési útmutató az R-hez és az SQL Serverhez](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)
- [Oktatóanyag: RevoScaleR R-függvények használata az SQL Server-adatok](https://docs.microsoft.com/sql/advanced-analytics/tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages)

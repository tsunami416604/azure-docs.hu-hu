---
title: Egyszerű R-parancsfájlok létrehozása és futtatása
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Egyszerű R-parancsfájlokat futtatva az Azure SQL Database Machine Learning Services-ben (előzetes verzió).
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
ms.openlocfilehash: 5b2f8231952d25f5858f8e06a957f1056ecc3651
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768495"
---
# <a name="quickstart-create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Rövid útmutató: Egyszerű R-parancsfájlok létrehozása és futtatása az Azure SQL Database Machine Learning Services szolgáltatásban (előzetes verzió)

Ebben a rövid útmutatóban hozzon létre és futtasson r-parancsfájlokat a Machine Learning Services (R) használatával az Azure SQL Database-ben.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure SQL-adatbázis](sql-database-single-database-get-started.md) [kiszolgálószintű tűzfalszabállyal](sql-database-server-level-firewall-rule.md)
- [Machine Learning Services](sql-database-machine-learning-services-overview.md) engedélyezve van az R szolgáltatással. [Regisztráljon az előzetes verzióra](sql-database-machine-learning-services-overview.md#signup).
- [SQL Server Felügyeleti stúdió](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!NOTE]
> A nyilvános előzetes verzió során a Microsoft befogja önt, és engedélyezi a gépi tanulást a meglévő vagy az új adatbázishoz.

Ez a példa a tárolt eljárás [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) egy jól formázott R-parancsfájl burkolására használja.

## <a name="run-a-simple-script"></a>Egyszerű parancsfájl futtatása

Az R-parancsfájl futtatásához argumentumként adja át a rendszer tárolt eljárásának, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

A következő lépésekben ezt az R példa parancsfájlt fogja futtatni az SQL-adatbázisban:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Nyissa meg **az SQL Server Management Studio alkalmazást,** és csatlakozzon az SQL-adatbázishoz.

   Ha segítségre van szüksége a csatlakozáshoz, [olvassa el a rövid útmutató: Az SQL Server Management Studio használata Azure SQL-adatbázis csatlakoztatásához és lekérdezéséhez című témakört.](sql-database-connect-query-ssms.md)

1. Adja át a teljes R-parancsfájlt a [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) tárolt eljárásnak.

   A parancsfájl áthalad `@script` az argumentumon. Az argumentumon belül minden `@script` érvényes R-kódnak kell lennie.

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

   Ha hibába ütközik, előfordulhat, hogy a Machine Learning Services (with R) nyilvános előzetes verziója nincs engedélyezve az SQL-adatbázishoz. Lásd: Fenti [előfeltételek.](#prerequisites)

   > [!NOTE]
   > Ha Ön rendszergazda, automatikusan futtathatja a külső kódot. A következő paranccsal adhat engedélyt más felhasználóknak:
   <br>**GRANT HAJTSON VÉGRE EGY KÜLSŐ PARANCSFÁJLT A** * \<FELHASZNÁLÓNÉVHEZ.\>*

2. A program kiszámítja a `print` helyes eredményt, és az R függvény az eredményt az Üzenetek ablakba **adja** vissza.

   Valahogy így kell kinéznie.

    **Results (Eredmények)**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Hello World parancsfájl futtatása

Egy tipikus példa script az egyik, hogy csak kimenetek a string "Hello World". Futtassa a következő parancsot.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

A tárolt eljárás bemenetei a következők:

| | |
|-|-|
| @language | meghatározza a híváshoz használt nyelvi kiterjesztést, ebben az esetben az R |
| @script | meghatározza az R futásórának átadott parancsokat. A teljes R-parancsfájlt unicode szövegként kell bezárni ebbe az argumentumba. A szöveget hozzáadhatja egy **nvarchar** típusú változóhoz, majd meghívhatja a |
| @input_data_1 | a lekérdezés által visszaadott adatok, átadva az R futásórának, amely adatkeretként adja vissza az adatokat az SQL Server nek |
|EREDMÉNYKÉSZLETEKKEL | a záradék az SQL Server visszaadott adattáblájának sémáját határozza meg, és a "Hello World" értéket adja hozzá oszlopnévként, **int** az adattípushoz. |

A parancs a következő szöveget adja ki:

| Hello World |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>Bemenetek és kimenetek használata

Alapértelmezés szerint [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) egyetlen adatkészletet fogad el bemenetként, amelyet általában érvényes SQL-lekérdezés formájában ad meg. Ezután egyetlen R adatkeretet ad vissza kimenetként.

Most használjuk a [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)alapértelmezett bemeneti és kimeneti változóit: **InputDataSet** és **OutputDataSet**.

1. Hozzon létre egy kis táblázatot a vizsgálati adatokból.

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

1. Az `SELECT` utasítás segítségével kérdezze le a táblát.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Results (Eredmények)**

    ![Az RTestData tábla tartalma](./media/sql-database-quickstart-r-create-script/select-rtestdata.png)

1. Futtassa a következő R-parancsfájlt. Beolvassa az adatokat a `SELECT` táblából az utasítás használatával, továbbítja azt az R futásidőn keresztül, és adatkeretként adja vissza az adatokat. A `WITH RESULT SETS` záradék az SQL Database visszaadott adattáblájának sémáját határozza meg, és hozzáadja a *NewColName*oszlopnevet.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Results (Eredmények)**

    ![Egy R-szkript kimenete, amely adatokat ad vissza egy táblából](./media/sql-database-quickstart-r-create-script/r-output-rtestdata.png)

1. Most változtassuk meg a bemeneti és kimeneti változók nevét. Az alapértelmezett bemeneti és kimeneti **változónevek: InputDataSet** és **OutputDataSet**, ez a parancsfájl a neveket **SQL_in** és **SQL_out**változtat:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    Ne feledje, hogy az R a kis- és nagybetűket nem veszi figyelembe. Az R parancsfájlban használt bemeneti és kimeneti változóknak (**SQL_out** `@output_data_1_name`, **SQL_in**) meg kell egyezniük a programmal és a lehetőséggel meghatározott értékekkel. `@input_data_1_name`

   > [!TIP]
   > Csak egy bemeneti adathalmaz továbbítható paraméterként, és csak egy adathalmaz adható vissza. Azonban az R-kódból más adathalmazokat is meghívhat, és az adathalmaz mellett más típusú kimeneteket is visszaadhat. Az OUTPUT kulcsszó hozzáadásával az eredmények között bármely paramétert visszaadhatja.

1. Azt is létrehozhat értékeket csak használ az`@input_data_1` R script nem bemeneti adatok (üresre van állítva).

   A következő szkript a "hello" és a "world" szöveget adja ki.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    mytextvariable <- c("hello", " ", "world");
    OutputDataSet <- as.data.frame(mytextvariable);
    '
        , @input_data_1 = N''
    WITH RESULT SETS(([Col1] CHAR(20) NOT NULL));
    ```

    **Results (Eredmények)**

    ![Eredmények lekérdezése az @script bemenetként való használatával](./media/sql-database-quickstart-r-create-script/r-data-generated-output.png)

## <a name="check-r-version"></a>Az R verziószámának ellenőrzése

Ha szeretné látni, hogy az R melyik verziója van telepítve az SQL-adatbázisban, futtassa a következő parancsfájlt.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

Az R `print` funkciója a **Messages** (Üzenetek) ablakban adja vissza a verziót. Az alábbi példakimeneti, láthatja, hogy az SQL Database ebben az esetben az R 3.4.4-es verziója telepítve van.

**Results (Eredmények)**

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

Ha meg szeretné tekinteni, hogy mely R-csomagok vannak telepítve, beleértve a verziót, a függőségeket, a licenceket és a könyvtár elérési útját, futtassa a következő parancsfájlt.

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

A kimenet `installed.packages()` az R-ből származik, és eredményhalmazként adja vissza.

**Results (Eredmények)**

![Telepített csomagok az R-ben](./media/sql-database-quickstart-r-create-script/r-installed-packages.png)

## <a name="next-steps"></a>További lépések

Ha az SQL Database-ben r használatával szeretne gépi tanulási modellt létrehozni, kövesse az alábbi rövid útmutatót:

> [!div class="nextstepaction"]
> [Prediktív modell létrehozása és betanítása az R-ben az Azure SQL Database Machine Learning Services szolgáltatással (előzetes verzió)](sql-database-quickstart-r-train-score-model.md)

Az Azure SQL Database Machine Learning Services r-rel (előzetes verzió) című témakörről az alábbi cikkekben talál további információt.

- [Azure SQL Database Machine Learning Services R-rel (előzetes verzió)](sql-database-machine-learning-services-overview.md)
- [Speciális R-függvények írása az Azure SQL Database-ben a Machine Learning Services használatával (előzetes verzió)](sql-database-machine-learning-services-functions.md)
- [R- és SQL-adatok kal való kapcsolat az Azure SQL Database Machine Learning Services szolgáltatásban (előzetes verzió)](sql-database-machine-learning-services-data-issues.md)

---
title: Egyszerű R-parancsfájlok létrehozása és futtatása
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Futtasson egyszerű R-parancsfájlokat Azure SQL Database Machine Learning Servicesban (előzetes verzió).
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
ms.openlocfilehash: a47e7a81ba486056841bdc0fe65cfd10f1b2c412
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123195"
---
# <a name="create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Egyszerű R-parancsfájlok létrehozása és futtatása Azure SQL Database Machine Learning Servicesban (előzetes verzió)

Ebben a rövid útmutatóban egyszerű R-szkripteket hozhat létre és futtathat a [Azure SQL Database Machine learning Services (R)](sql-database-machine-learning-services-overview.md)nyilvános előzetes verziójával. Megtudhatja, hogyan csomagolhat jól formázott R-szkriptet a tárolt eljárás [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) , és hogyan hajthatja végre a szkriptet egy SQL-adatbázisban.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/free/) .

- A fenti gyakorlatokban szereplő mintakód futtatásához először rendelkeznie kell egy Azure SQL Database-adatbázissal, amelynek Machine Learning Services (R) engedélyezve van. A nyilvános előzetes verzióban a Microsoft bevezeti Önt, és lehetővé teszi a gépi tanulást a meglévő vagy az új adatbázishoz. Kövesse a regisztráció az [előzetes](sql-database-machine-learning-services-overview.md#signup)verzióra című témakör lépéseit.

- Győződjön meg arról, hogy telepítette a legújabb [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Az R-szkripteket más adatbázis-kezelő vagy lekérdezési eszközök használatával is futtathatja, de ebben a rövid útmutatóban a SSMS-t fogja használni.

- Ehhez a rövid útmutatóhoz kiszolgálói szintű tűzfalszabály konfigurálására van szükség. Ennek módjáról további információt a [kiszolgálói szintű tűzfalszabály létrehozása](sql-database-server-level-firewall-rule.md)című témakörben talál.

## <a name="run-a-simple-script"></a>Egyszerű parancsfájl futtatása

R-szkript futtatásához adja át argumentumként a rendszer tárolt eljárásának [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

A következő lépésekben az alábbi R-szkriptet fogja futtatni az SQL-adatbázisban:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Nyissa meg az **SQL Server Management Studiót**, és csatlakozzon az SQL-adatbázishoz.

   Ha segítségre van szüksége a csatlakozáshoz, tekintse [meg a gyors útmutató: Az Azure SQL Database-adatbázisok](sql-database-connect-query-ssms.md)összekapcsolásához és lekérdezéséhez használja a SQL Server Management Studio.

1. Adja át a teljes R-parancsfájlt a [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) tárolt eljárásnak.

   A szkript áthalad az `@script` argumentumon. Az `@script` argumentumon belül minden elemnek érvényes R-kódnak kell lennie.

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

   Ha hibába ütközik, előfordulhat, hogy a Machine Learning Services (with R) nyilvános előzetes verziója nincs engedélyezve az SQL-adatbázishoz. Lásd a fenti [előfeltételeket](#prerequisites) .

   > [!NOTE]
   > Ha Ön rendszergazda, a külső kódokat automatikusan is futtathatja. A paranccsal engedélyeket adhat más felhasználóknak a következő parancs használatával:
   <br>**külső parancsfájl végrehajtásának engedélyezése** Felhasználónév.  *\<\>*

2. A rendszer kiszámítja a megfelelő eredményt `print` , és az R függvény visszaadja az eredményt az **üzenetek** ablakba.

   Ehhez hasonlóan kell kinéznie.

    **Results**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>"Helló világ!" alkalmazás szkript futtatása

Egy tipikus példa a parancsfájl az egyik, hogy csak a ""Helló világ!"alkalmazás" karakterláncot adja eredményül. Futtassa a következő parancsot.

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
| @language | meghatározza, hogy milyen nyelvi bővítményt kell hívni, ebben az esetben R |
| @script | az R Runtime számára átadott parancsokat határozza meg. Ebben az argumentumban a teljes R-szkriptet Unicode-szövegként kell megadni. Azt is megteheti, hogy hozzáadta a szöveget egy **nvarchar** típusú változóhoz, majd meghívja a változót. |
| @input_data_1 | a lekérdezés által visszaadott adatok, amelyek az R futtatókörnyezetnek lettek átadva, amely visszaadja az adatok SQL Server adatkeretként |
|EREDMÉNYHALMAZ | a záradék meghatározza a visszaadott adattábla sémáját a SQL Serverhoz, hozzáadva a ""Helló világ!"alkalmazás" nevet az oszlop neveként, az **int** adattípushoz. |

A parancs kimenete a következő szöveg:

| Hello World |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>Bemenetek és kimenetek használata

Alapértelmezés szerint a [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) egyetlen adatkészletet fogad el bemenetként, amely általában érvényes SQL-lekérdezés formájában van megadva. Ezután egy adott R-adatkeretet ad vissza kimenetként.

Egyelőre a [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)alapértelmezett bemeneti és kimeneti változóit fogjuk használni: **InputDataSet** és **OutputDataSet**.

1. Hozzon létre egy kis táblát a tesztelési adathoz.

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

1. A tábla `SELECT` lekérdezéséhez használja az utasítást.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Results**

    ![Az RTestData tábla tartalma](./media/sql-database-quickstart-r-create-script/select-rtestdata.png)

1. Futtassa a következő R-szkriptet. Lekéri a táblázat adatait az `SELECT` utasítás használatával, átadja azt az R Runtime-on keresztül, és adatkeretként adja vissza az adatokból. A `WITH RESULT SETS` záradék meghatározza a visszaadott adattábla sémáját a SQL Databasehoz, és hozzáadja a *NewColName*oszlop nevét.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Results**

    ![Egy R-szkript kimenete, amely adatokat ad vissza egy táblából](./media/sql-database-quickstart-r-create-script/r-output-rtestdata.png)

1. Most változtassa meg a bemeneti és a kimeneti változók nevét. A bemeneti és kimeneti változók alapértelmezett nevei a **InputDataSet** és a **OutputDataSet**, ez a szkript a neveket a **SQL_in** és a **SQL_out**értékre módosítja:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    Vegye figyelembe, hogy az R a kis-és nagybetűk megkülönböztetése. Az R-szkriptben (**SQL_out**, **SQL_in**) használt bemeneti és kimeneti változóknak meg kell egyezniük a `@input_data_1_name` és `@output_data_1_name`a által definiált értékekkel, beleértve az esetet is.

   > [!TIP]
   > Csak egy bemeneti adathalmaz továbbítható paraméterként, és csak egy adathalmaz adható vissza. Azonban az R-kódból más adathalmazokat is meghívhat, és az adathalmaz mellett más típusú kimeneteket is visszaadhat. Az OUTPUT kulcsszó hozzáadásával az eredmények között bármely paramétert visszaadhatja.

1. A bemeneti adatok nélkül is létrehozhat értékeket az R-parancsfájl használatával (`@input_data_1` üresre van állítva).

   A következő szkript a "Hello" és a "World" szöveget adja eredményül.

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

    ![Eredmények lekérdezése az @script bemenetként való használatával](./media/sql-database-quickstart-r-create-script/r-data-generated-output.png)

## <a name="check-r-version"></a>Az R verziószámának ellenőrzése

Ha szeretné megtekinteni, hogy az R melyik verziója van telepítve az SQL-adatbázisban, futtassa az alábbi szkriptet.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

Az R `print` funkciója a **Messages** (Üzenetek) ablakban adja vissza a verziót. Az alábbi példában látható kimenetben láthatja, hogy ebben az esetben a SQL Database R Version 3.4.4 van telepítve.

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

Ha meg szeretné tekinteni, hogy mely R-csomagok vannak telepítve, beleértve a verziószámot, a függőségeket, a licenceket és a könyvtár elérési útját, futtassa az alábbi szkriptet.

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

A kimenet az R `installed.packages()` -ből származik, és eredményként adja vissza.

**Results**

![Telepített csomagok az R-ben](./media/sql-database-quickstart-r-create-script/r-installed-packages.png)

## <a name="next-steps"></a>További lépések

Ha a gépi tanulási modellt a SQL Database R használatával szeretné létrehozni, kövesse az alábbi rövid útmutatót:

> [!div class="nextstepaction"]
> [Prediktív modell létrehozása és betanítása az R-ben Azure SQL Database Machine Learning Services (előzetes verzió)](sql-database-quickstart-r-train-score-model.md)

Az R (előzetes verzió) Azure SQL Database Machine Learning Servicesról a következő cikkekben talál további információt.

- [Azure SQL Database Machine Learning Services R-vel (előzetes verzió)](sql-database-machine-learning-services-overview.md)
- [Speciális R függvények írása a Azure SQL Database Machine Learning Services használatával (előzetes verzió)](sql-database-machine-learning-services-functions.md)
- [R-és SQL-adatmennyiség használata Azure SQL Database Machine Learning Servicesban (előzetes verzió)](sql-database-machine-learning-services-data-issues.md)

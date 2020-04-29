---
title: R-és SQL-adattípusok és-objektumok használata
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Megtudhatja, hogyan dolgozhat az R-ben található adattípusokkal és-objektumokkal Azure SQL Database Machine Learning Services (előzetes verzió) használatával, beleértve az esetlegesen felmerülő gyakori problémákat.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: e81cca3e20d5b6c050489e80b91d013d5e934cce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81453199"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>R-és SQL-adatmennyiség használata Azure SQL Database Machine Learning Servicesban (előzetes verzió)

Ez a cikk néhány olyan gyakori problémát ismertet, amely akkor fordulhat elő, amikor az R és a SQL Database közötti adatátvitelt végez az Azure SQL Database-ben [Machine learning Services (r)](sql-database-machine-learning-services-overview.md)értékkel. Az ebben a gyakorlatban szerzett tapasztalatok alapvető hátteret biztosítanak a saját parancsfájlban található adatkezeléshez.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Az esetlegesen felmerülő gyakori problémák a következők:

- Az adattípusok néha nem egyeznek
- Implicit konverziók is megtörténhetnek
- Időnként szükség van a leadott és az átalakítási műveletekre
- Az R és az SQL különböző adatobjektumokat használ

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/free/) .

- A példában szereplő programkód futtatásához először Azure SQL Databasenek kell lennie, [Machine learning Services (R)](sql-database-machine-learning-services-overview.md) engedélyezve van.

- Győződjön meg arról, hogy telepítette a legújabb [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Az R-szkripteket más adatbázis-kezelő vagy lekérdezési eszközök használatával is futtathatja, de ebben a rövid útmutatóban a SSMS-t fogja használni.

## <a name="working-with-a-data-frame"></a>Adatkeret használata

Ha a parancsfájl az R és az SQL eredményét adja vissza, az adatoknak **adatok. keretként**kell visszaadnia. Bármely más, a parancsfájlban létrehozott objektum – legyen szó lista, faktor, vektor vagy bináris adattípushoz – adatkeretbe kell alakítani, ha a tárolt eljárás eredményeinek részeként szeretné kiadni. Szerencsére több R függvény van, amely támogatja a más objektumok adatkeretre való módosítását. Akár egy bináris modellt is szerializálhat, és visszaküldheti azt egy adatkeretbe, amelyet később ebben a cikkben fog elvégezni.

Először is kísérletezzen néhány alapszintű R-objektummal – vektorokkal, mátrixokkal és listával –, és tekintse meg, hogy az adatkeretre való átalakítás hogyan módosítja az SQL-nek átadott kimenetet.

Hasonlítsa össze ezt a két ""Helló világ!"alkalmazás" szkriptet az R-ben. A szkriptek majdnem azonosak, de az első egy három értékből álló egyoszlopos értéket ad vissza, míg a második három oszlopot ad vissza, amelyek mindegyike egyetlen értékkel rendelkezik.

**1. példa**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
'
    , @input_data_1 = N'';
```

**2. példa**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' OutputDataSet<- data.frame(c("hello"), " ", c("world"));'
    , @input_data_1 = N'';
```

Miért más az eredmények?

A válasz általában az R `str()` parancs használatával érhető el. Adja hozzá a `str(object_name)` függvényt az r-szkriptben bárhol, hogy a megadott R objektum adatsémája tájékoztató üzenetként legyen visszaadva. Az üzenetek a SSMS **üzenetek** lapján tekinthetők meg.

Annak megállapításához, hogy az 1. és a `str(OutputDataSet)` `@script` 2. példa milyen eredményeket tartalmaz, szúrja be a sort a változó definíciójának végén minden utasításban, a következőhöz hasonlóan:

**1. példa az Str függvény hozzáadásával**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**2. példa az Str függvény hozzáadásával**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

Most tekintse át az **üzenetek** szövegét, és nézze meg, miért különbözik a kimenet.

**Eredmények – 1. példa**

```text
STDOUT message(s) from external script:
'data.frame':    3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**Eredmények – 2. példa**

```text
STDOUT message(s) from external script:
'data.frame':    1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

Amint láthatja, az R-szintaxis enyhe módosítása nagy hatással volt az eredmények sémájára. Az összes adat esetében az R-adattípusok különbségeit a ["speciális R"](http://adv-r.had.co.nz) *adatszerkezetek* szakaszában, a Hadley Wickham című témakör ismerteti.

Egyelőre ne feledje, hogy a várt eredményeket ellenőriznie kell, amikor az R-objektumokat az adatkeretbe kényszeríti.

> [!TIP]
> Az R Identity functions `is.matrix` `is.vector`(például) is használható a belső adatszerkezettel kapcsolatos információk visszaküldéséhez.

## <a name="implicit-conversion-of-data-objects"></a>Az adatobjektumok implicit átalakítása

Mindegyik R-adatobjektum rendelkezik saját szabályokkal, amelyekkel az értékek kezelése más adatobjektumokkal együtt történik, ha a két adatobjektum azonos számú dimenzióval rendelkezik, vagy ha bármilyen adatobjektum heterogén adattípusokat tartalmaz.

Tegyük fel például, hogy az R használatával szeretné elvégezni a mátrixok szorzását. A három értékkel rendelkező egyoszlopos mátrixot egy négy értékkel rendelkező tömbben szeretné megszorozni, és ennek eredményeképpen egy 4x3 mátrixot vár.

Először hozzon létre egy kisméretű táblázatot a tesztelési adatról.

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

Most futtassa az alábbi szkriptet.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:15);
OutputDataSet <- as.data.frame(x %*% y);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col1] INT
            , [Col2] INT
            , [Col3] INT
            , Col4 INT
            ));
```

A borítók alatt a három érték oszlopát egy egyoszlopos mátrixba alakítja át a rendszer. Mivel a mátrix az R-tömbben csak egy speciális eset, a tömb `y` implicit módon egy egyoszlopos mátrixra van kényszerítve, hogy a két argumentum megfeleljen.

**Results (Eredmények)**

|Col1|Col2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

Vegye figyelembe azonban, hogy mi történik a tömb `y`méretének módosításakor.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:14);
OutputDataSet <- as.data.frame(y %*% x);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS(([Col1] INT));
```

Az R most egyetlen értéket ad vissza eredményként.

**Results (Eredmények)**
    
|Col1|
|---|
|1542|

Hogy miért? Ebben az esetben, mivel a két argumentum ugyanolyan hosszúságú vektorként kezelhető, az R a belső terméket mátrixként adja vissza.  A lineáris algebra szabályainak megfelelően ez a várt viselkedés. Azonban problémákat okozhat, ha az alsóbb rétegbeli alkalmazás a kimeneti sémát soha nem változtatja meg!

## <a name="merge-or-multiply-columns-of-different-length"></a>Eltérő hosszúságú oszlopok egyesítése vagy szorzása

Az R nagy rugalmasságot biztosít a különböző méretű vektorok használatához, valamint az oszlopok hasonló struktúráinak adatkeretbe való összekapcsolásához. A vektorok listája úgy néz ki, mint egy tábla, de nem követik az adatbázis-táblákat szabályozó összes szabályt.

Az alábbi szkript például egy 6. hosszúságú numerikus tömböt határoz meg, és az R változóban `df1`tárolja. A numerikus tömböt ezután a RTestData tábla (fent létrehozott) egész számával kombináljuk, amely három (3) értéket tartalmaz, hogy új adatkeretet hozzon `df2`létre.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
df1 <- as.data.frame( array(1:6) );
df2 <- as.data.frame( c( InputDataSet , df1 ));
OutputDataSet <- df2;
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col2] INT NOT NULL
            , [Col3] INT NOT NULL
            ));
```

Az adatkeret kitöltéséhez az R a tömbben `df1`lévő elemek számának megfelelően többször megismétli a RTestData lekért elemeket.

**Results (Eredmények)**
    
|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

Ne feledje, hogy az adatkeretek csak a táblákra hasonlítanak, de valójában a vektorok listája.

## <a name="cast-or-convert-sql-data"></a>SQL-adatfeldolgozás vagy-átalakítás

Az r és az SQL nem használja ugyanazt az adattípust, így amikor SQL-lekérdezést futtat az adatkéréshez, majd továbbítja azt az R futtatókörnyezetnek, bizonyos típusú implicit konverziók általában megtörténik. A konverziók másik készlete akkor kerül sor, amikor az R-ből az SQL-be küldi az adatforrást.

- Az SQL leküldi az adatait a lekérdezésből az R-folyamatba, és egy belső ábrázolásra konvertálja a nagyobb hatékonyság érdekében.
- Az R Runtime betölti az adatok egy adat. frame változóba, és elvégzi a saját műveleteit az adatokon.
- Az adatbázismotor a biztonságos belső kapcsolatok használatával visszaadja az SQL-adatoknak az SQL-t, és az SQL-adattípusok alapján jeleníti meg az adatok mennyiségét.
- Az adatokat úgy érheti el, ha olyan ügyfél vagy hálózati könyvtár használatával csatlakozik az SQL-hez, amely SQL-lekérdezéseket tud kiadni és táblázatos adatkészleteket kezel. Ez az ügyfélalkalmazás más módon is befolyásolhatja az adatvesztést.

A működésének megtekintéséhez futtasson egy lekérdezést, például a [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) -adattárházban. Ez a nézet az előrejelzések létrehozásakor használt értékesítési adatok visszaadása.

```sql
USE AdventureWorksDW
GO

SELECT ReportingDate
    , CAST(ModelRegion AS VARCHAR(50)) AS ProductSeries
    , Amount
FROM [AdventureWorksDW].[dbo].[vTimeSeries]
WHERE [ModelRegion] = 'M200 Europe'
ORDER BY ReportingDate ASC
```

> [!NOTE]
> Használhatja a AdventureWorks bármely verzióját, vagy létrehozhat egy másik lekérdezést egy saját adatbázis használatával. A pont a szöveg, a DateTime és a numerikus értékeket tartalmazó adatok kezelésének kísérlete.

Most próbálkozzon a lekérdezéssel a tárolt eljárás bemenetének használatával.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
str(InputDataSet);
OutputDataSet <- InputDataSet;
'
    , @input_data_1 = N'
           SELECT ReportingDate
         , CAST(ModelRegion as varchar(50)) as ProductSeries
         , Amount
           FROM [AdventureWorksDW].[dbo].[vTimeSeries]
           WHERE [ModelRegion] = ''M200 Europe''
           ORDER BY ReportingDate ASC ;'
WITH RESULT SETS undefined;
```

Ha hibaüzenetet kap, valószínűleg módosítania kell a lekérdezés szövegét. A WHERE záradékban szereplő karakterlánc-predikátumot például két szimpla idézőjelből álló készletbe kell foglalni.

A lekérdezés működésének megkezdése után tekintse át a `str` függvény eredményét, és nézze meg, hogyan kezeli az R a bemeneti adatokat.

**Results (Eredmények)**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- A DateTime oszlop az R adattípussal, a **POSIXct**lett feldolgozva.
- A "ProductSeries" szöveges oszlop a következő **tényezőként**lett azonosítva: kategorikus változó. A karakterlánc-értékek alapértelmezés szerint tényezőként vannak kezelve. Ha az R karakterláncot adja át, a rendszer a belső használatra egész számra konvertálja, majd visszaképezi a kimenetre a karakterláncot.

## <a name="summary"></a>Összefoglalás

Akár ezekből a rövid példákból is láthatja, hogy az SQL-lekérdezések bemenetként való átadásakor szükség van az adatkonverzió hatásának vizsgálatára. Mivel az R egyes SQL-adattípusokat nem támogat, vegye figyelembe a hibák elkerülésének módját:

- Előzetesen tesztelje az adatait, és ellenőrizze, hogy a séma oszlopai vagy értékei okozhatnak-e problémát az R-kód átadásakor.
- Adja meg a bemeneti adatforrás oszlopait a használata `SELECT *`helyett, és Ismerje meg, hogy az egyes oszlopok hogyan lesznek kezelve.
- A bevitt adatok előkészítésekor szükség esetén explicit módon végezze el a szükséges műveleteket a meglepetések elkerülése érdekében.
- Kerülje az adatoszlopok (például GUID-ok vagy ROWGUIDS) átadását, ami hibákat okoz, és nem használható modellezéshez.

További információ a támogatott és nem támogatott R-adattípusokról: [r-tárak és adattípusok](/sql/advanced-analytics/r/r-libraries-and-data-types).

---
title: R- és SQL-adattípusok és -objektumok használata
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Ismerje meg, hogyan dolgozhat az Adattípusokkal és adatobjektumokkal az R-ben az Azure SQL Database használatával a Machine Learning Services használatával (előzetes verzió), beleértve az esetleg felmerülő gyakori problémákat is.
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
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453199"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>R- és SQL-adatok kal való kapcsolat az Azure SQL Database Machine Learning Services szolgáltatásban (előzetes verzió)

Ez a cikk néhány olyan gyakori problémát ismerteti, amelyekkel az Adatok áthelyezése során az R és az SQL Database a [Machine Learning Services (R)](sql-database-machine-learning-services-overview.md)az Azure SQL Database. A tapasztalat, amit kap ez a gyakorlat biztosítja az alapvető háttér, ha dolgozik az adatokat a saját script.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

A gyakori problémák a következők:

- Az adattípusok néha nem egyeznek
- Implicit konverziók történhetnek
- Néha szükség van leadott és konvertáló műveletekre
- Az R és az SQL különböző adatobjektumokat használ

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

- A példakód futtatásához ezekben a gyakorlatokban először az [Azure SQL Database machine learning-szolgáltatásokkal (R)](sql-database-machine-learning-services-overview.md) engedélyezve kell lennie.

- Ellenőrizze, hogy telepítette-e a legújabb [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) rendszert. Az R-parancsfájlokat más adatbázis-kezelő vagy lekérdezési eszközökkel is futtathatja, de ebben a rövid útmutatóban az SSMS-t fogja használni.

## <a name="working-with-a-data-frame"></a>Adatkeret munka

Amikor a parancsfájl eredményeket ad vissza az R sql, vissza kell adnia az adatokat, mint a **data.frame**. A parancsfájlban létrehozott bármely más típusú objektumot – legyen az lista, tényező, vektor vagy bináris adat – adatkeretté kell konvertálni, ha azt a tárolt eljárás eredményének részeként szeretné kiadni. Szerencsére több R függvény is támogatja a többi objektum adatkeretre való módosítását. Akár egy bináris modellt is szerializálhat, és adatkeretben adhat vissza, amelyet a cikk későbbi részében fog megtenni.

Először kísérletezzünk néhány alapvető R-objektummal – vektorokkal, mátrixokkal és listákkal –, és nézzük meg, hogyan változtatja meg az adatkeretté konvertálás az SQL-nek átadott kimenetet.

Hasonlítsa össze ezt a két "Hello World" szkriptek R. A parancsfájlok majdnem azonosak, de az első három értékű egyetlen oszlopot ad vissza, míg a második három oszlopot ad vissza egyetlen értékkel.

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

Miért olyan különbözőek az eredmények?

A válasz általában az R `str()` paranccsal érhető el. Adja hozzá `str(object_name)` a függvényt bárhol az R-parancsfájlban, hogy a megadott R-objektum adatsémáját tájékoztató üzenetként adja vissza. Az üzeneteket az SSMS **Üzenetek** lapján tekintheti meg.

Annak kiderítéséhez, `str(OutputDataSet)` `@script` hogy az 1.

**1. példa hozzáadtával**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**2. példa hozzáadtával**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

Most tekintse át a szöveget az **Üzenetekben,** hogy lássa, miért más a kimenet.

**Eredmények - 1. példa**

```text
STDOUT message(s) from external script:
'data.frame':    3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**Eredmények - 2. példa**

```text
STDOUT message(s) from external script:
'data.frame':    1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

Mint látható, egy kis változás az R szintaxis volt nagy hatással a séma az eredmények. Az R adattípusok közötti különbségeket [hadley Wickham "Advanced R"](http://adv-r.had.co.nz) *című adatszerkezete* című részében magyarázzák részletesen.

Egyelőre csak vegye figyelembe, hogy ellenőriznie kell a várt eredményeket, amikor az R-objektumokat adatkeretekbe kényszeríti.

> [!TIP]
> Az R identitásfüggvények , `is.matrix`például `is.vector`a , használatával információt adhat vissza a belső adatszerkezetről.

## <a name="implicit-conversion-of-data-objects"></a>Adatobjektumok implicit átalakítása

Minden R adatobjektumnak saját szabályai vannak arra vonatkozóan, hogy az értékekhogyan legyenek kezelve más adatobjektumokkal kombinálva, ha a két adatobjektum mérete megegyezik a méretekkel, vagy ha bármely adatobjektum heterogén adattípusokat tartalmaz.

Tegyük fel például, hogy mátrixszorzást szeretne végrehajtani az R használatával. Meg szeretne szorozni egy egyoszlopos mátrixot a három értékkel egy négy értéket tartalmazó tömbbel, és ennek eredményeképpen 4x3-as mátrixra számít.

Először hozzon létre egy kis táblázatot a vizsgálati adatokból.

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

Most futtassa a következő parancsfájlt.

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

A borítók alatt a három érték oszlopa egyoszlopos mátrixmá alakul át. Mivel a mátrix csak egy színhely különleges esete `y` az R-ben, a tömb implicit módon egy egyoszlopos mátrixra kényszeríti a két argumentum megfelelővé teszi.

**Results (Eredmények)**

|Col1|Col2|Oszlop3|4. oszlop|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

Azonban vegye figyelembe, hogy mi történik, `y`ha módosítja a tömb méretét.

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

Most R egyetlen értéket ad eredményül.

**Results (Eredmények)**
    
|Col1|
|---|
|1542|

Hogy miért? Ebben az esetben, mivel a két argumentum azonos hosszúságú vektorként kezelhető, az R a belső terméket adja vissza mátrixként.  Ez a várt viselkedés a lineáris algebra szabályai szerint. Azonban problémákat okozhat, ha az alsóbb rétegbeli alkalmazás azt várja, hogy a kimeneti séma soha nem változik!

## <a name="merge-or-multiply-columns-of-different-length"></a>Különböző hosszúságú oszlopok egyesítése vagy szorzása

Az R nagy rugalmasságot biztosít a különböző méretű vektorok számára, valamint ezen oszlopszerű szerkezetek adatkeretekké történő kombinálásához. A vektorok listája úgy nézhet ki, mint egy tábla, de nem követik az adatbázistáblákat szabályozó összes szabályt.

A következő parancsfájl például egy 6-os hosszúságú numerikus tömböt határoz meg, és az R változóban `df1`tárolja. A numerikus tömbezután kombinálva az RTestData tábla (fent létrehozott) egész számai, amely három `df2`(3) értéket tartalmaz, új adatkeret létrehozásához .

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

Az adatkeret kitöltéséhez R annyiszor ismétli meg az RTestData-ból beolvasott elemeket, `df1`ahányszor csak szükséges, hogy megfeleljen a tömb elemeinek számának.

**Results (Eredmények)**
    
|*Col2*|*Oszlop3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

Ne feledje, hogy az adatkeret csak úgy néz ki, mint egy táblázat, de valójában a vektorok listája.

## <a name="cast-or-convert-sql-data"></a>SQL-adatok leadása vagy konvertálása

Az R és az SQL nem ugyanazokat az adattípusokat használja, így amikor sql-ben futtat egy lekérdezést az adatok lehívásához, majd átadja az R-futásidejűnek, általában valamilyen implicit konverzió történik. Egy másik konverziókészlet akkor történik, amikor adatokat ad vissza az R-ről az SQL-re.

- Az SQL lekérdezi az adatokat a lekérdezésből az R-folyamatba, és belső ábrázolásba konvertálja a nagyobb hatékonyság érdekében.
- Az R futásidejű betölti az adatokat egy data.frame változó, és végrehajtja a saját műveleteket az adatokon.
- Az adatbázis-motor biztonságos belső kapcsolathasználatával adja vissza az adatokat az SQL-nek, és az SQL-adattípusok tekintetében mutatja be az adatokat.
- Az adatokat úgy szerezheti be, hogy olyan ügyfél- vagy hálózati kódtár használatával csatlakozik az SQL-hez, amely SQL-lekérdezéseket adhat ki, és képes kezelni a táblázatos adatkészleteket. Ez az ügyfélalkalmazás más módon is befolyásolhatja az adatokat.

Ha meg szeretné tekinteni, hogyan működik ez, futtasson egy lekérdezést, például ezt az [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) adatraktárban. Ez a nézet az előrejelzések létrehozásához használt értékesítési adatokat adja vissza.

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
> Az AdventureWorks bármely verzióját használhatja, vagy létrehozhat egy másik lekérdezést saját adatbázishasználatával. A lényeg, hogy próbáljon kezelni néhány adatot, amely szöveget, datetime és numerikus értékeket tartalmaz.

Most próbálja meg ezt a lekérdezést a tárolt eljárás bemeneteként használni.

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

Ha hibaüzenetet kap, valószínűleg módosításokat kell eszközolnie a lekérdezés szövegén. A WHERE záradékban szereplő karakterlánc-predikátumot például két aposztróf-készlettel kell elfoglalni.

Miután a lekérdezés működik, tekintse `str` át a függvény eredményeit, és tekintse meg, hogyan kezeli r a bemeneti adatokat.

**Results (Eredmények)**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- A datetime oszlop feldolgozása a POSIXct R adattípussal lett **feldolgozva.**
- A "ProductSeries" **szövegoszlopot tényezőként**azonosították, ami kategorikus változót jelent. A karakterlánc-értékek alapértelmezés szerint tényezőkként vannak kezelve. Ha átad egy karakterláncot az R-nek, a program belső használatra egész számmá alakítja, majd visszarendeli a kimeneti karakterlánchoz.

## <a name="summary"></a>Összefoglalás

Még ezekből a rövid példákból is láthatja, hogy ellenőrizni kell az adatkonverzió hatásait, amikor SQL-lekérdezéseket ad át bemenetként. Mivel az R nem támogat bizonyos SQL-adattípusokat, fontolja meg a hibák elkerülésének módjait:

- Tesztelje előre az adatokat, és ellenőrizze a séma azon oszlopait vagy értékeit, amelyek problémát jelenthetnek az R-kódnak való átadáskor.
- A `SELECT *`használata helyett egyenként adja meg a bemeneti adatforrás oszlopait, és ismerje meg, hogyan lesznek kezelve az egyes oszlopok.
- Végezze el explicit öntet szükség szerint előkészítése során a bemeneti adatok, hogy elkerüljék a meglepetéseket.
- Kerülje a hibákat okozó és modellezéshez nem hasznos adatoszlopok (például GUIDS vagy rowguids) átadását.

A támogatott és nem támogatott R-adattípusokról az [R-tárak és adattípusok](/sql/advanced-analytics/r/r-libraries-and-data-types)című témakörben talál további információt.

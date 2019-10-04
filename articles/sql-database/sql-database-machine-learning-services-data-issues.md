---
title: R- és SQL-adattípusok és objektumok használata
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Ismerje meg, az adattípusok és az Azure SQL Database használata a Machine Learning Services (előzetes verzió), beleértve az esetlegesen felmerülő gyakori problémák R adatobjektumok használatának módját.
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
ms.openlocfilehash: 01d3af14963e92393d34a952bddc8097b7b08f18
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65232610"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>R- és SQL adatok használata Azure SQL Database, Machine Learning Services (előzetes verzió)

Ez a cikk ismerteti a gyakori problémák jelentkezhetnek, ha az adatok áthelyezése másik R- és SQL Database-be [Machine Learning-szolgáltatások (az r nyelv) az Azure SQL Database](sql-database-machine-learning-services-overview.md). Az ebben a gyakorlatban keresztül révén a felület alapvető háttér biztosít, amikor a szkriptben a saját adatok használata.

Esetlegesen felmerülő gyakori hibák a következők:

- Az adattípusok néha nem egyezik
- Implicit konverzió helyen is igénybe vehet
- A CAST és convert műveletekre néha szükség
- R- és SQL más objektumok használata

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/) megkezdése előtt.

- Példakód futtatható a témaköröket, először szüksége van egy Azure SQL database, a Machine Learning-szolgáltatások (az r nyelv) engedélyezve van. A nyilvános előzetes verzió ideje alatt a Microsoft is megjelenik majd, és engedélyezze a gépi tanulás a meglévő vagy új adatbázis számára. Kövesse a [regisztrálni az előzetes verzióra](sql-database-machine-learning-services-overview.md#signup).

- Győződjön meg arról, hogy telepítette a legújabb [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Más adatbázis-kezelő vagy a lekérdezési eszközökkel R-szkriptek futtatása, de ez a rövid útmutató az ssms-t fogja használni.

## <a name="working-with-a-data-frame"></a>Adatkeretek használata

Ha a parancsfájl adja vissza, az SQL-eredményeket az R, az adatokat kell visszaadnia egy **data.frame**. Bármilyen más típusú objektumot hoz létre a parancsfájl - e, amely lehet, hogy egy lista, tényező, vektor vagy bináris adatok - adatkeretek kell alakítható, ha azt szeretné, a kimenetben, a tárolt eljárás eredményeiben részeként. Szerencsére a nincsenek más objektumok módosítása a következőre adatkeretek támogatásához több R függvényekkel. Még egy bináris modell szerializálni, és küldje vissza egy adatkeretben, amely a cikk későbbi részében teheti.

Először is hozzunk néhány alapvető R objektum - vektorok, mátrixokban és listák - kísérletezhet, és meg, hogy az adatkeretek átalakítása hogyan változik a kimenet átadott SQL.

Hasonlítsa össze a két "Hello World" parancsfájlokat az R. A parancsfájlok hely majdnem teljesen megegyezik, de az első három értékeit egyetlen oszlopban ad vissza, míg a második adja vissza egyetlen három oszlop minden értékét.

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

Miért van így a különböző az eredményeket?

A válasz általában az R használatával található `str()` parancsot. Adja hozzá a függvényt `str(object_name)` bárhol az R-szkript az adatokat a megadott R objektum séma értéket küldi vissza egy tájékoztató üzenet. Megtekintheti az üzeneteket a **üzenetek** fülre az ssms-ben.

Döntse el, miért van az példa 1. és 2. példa az ilyen eltérő eredményeket, hogy a sor beszúrása `str(OutputDataSet)` végén a `@script` változódefinícióra egyes utasításban, ehhez hasonló:

**1. példa az str függvény hozzáadása**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**2. példa az str függvény hozzáadása**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

Most, tekintse át a szöveget **üzenetek** , miért különböző-e a kimenet.

**Eredmények – 1. példa**

```text
STDOUT message(s) from external script:
'data.frame':   3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**Eredmények – 2. példa**

```text
STDOUT message(s) from external script:
'data.frame':   1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

Ahogy láthatjuk, R szintaxis enyhe változás kellett az eredmények a séma big Data jellegű hatással. A részletekről az R-adattípusok közötti különbségek mutatjuk be a részletek a *adatstruktúrák* szakasz ["R speciális" szerint Hadley Wickham](http://adv-r.had.co.nz).

Egyelőre csak vegye figyelembe, hogy ellenőrizze a várt eredményt, amikor R objektumok coercing adatkeretek be kell.

> [!TIP]
> Is használhatja az R-identitás funkciók, például `is.matrix`, `is.vector`, a belső adatstruktúra információt ad vissza.

## <a name="implicit-conversion-of-data-objects"></a>Az adatok objektumok implicit konverzió

Minden R-adatobjektum értékek kezelésének módja együtt más objektumok, ha a két objektum dimenzióinak azonos számú rendelkezik, vagy ha minden objektum tartalmazza a különféle adattípusokat saját szabályokkal rendelkezik.

Tegyük fel, hogy szeretne végezni a mátrix szorzási r használatával Egy egyoszlopos mátrix három értékekkel szorzása egy tömb négy értékekkel, és ennek eredményeképpen várt 4 x 3 méretű össze szeretné.

Először hozzon létre egy kis táblák Tesztadatok.

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

Most futtassa a következő szkriptet.

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

Valójában a oszlopban található értékek egyoszlopos mátrix alakítja át. Mivel a mátrix egy tömb, az R, a tömb csak egy különleges esetben `y` van implicit módon telepítésére kényszerített rendszerhez, hogy a két argumentumot meg egyoszlopos mátrix.

**Results**

|Col1|Col2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

Vegye azonban figyelembe mi történik, ha módosítja a tömb mérete `y`.

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

Most már R eredményezi egy olyan értéket ad vissza.

**Results**
    
|Col1|
|---|
|1542|

Hogy miért? Ebben az esetben a két argumentumot, ugyanolyan hosszúságú vektorok is lehet kezelni, mivel az R értéket ad vissza a belső termék mátrix.  Ez az a várt viselkedés lineáris algebra szabályainak megfelelően. Azonban ez sikerült problémákat okozhat, ha az alsóbb rétegbeli alkalmazás soha nem módosíthatja a kimeneti sémával vár!

## <a name="merge-or-multiply-columns-of-different-length"></a>Egyesítse vagy szorozza meg a különböző hosszúságú oszlopok

Az R használata a különböző méretű vektorok, valamint ezen oszlop-szerű szerkezetek összefűzhet adatkeretek nagyfokú rugalmasságot biztosít. Listák vektorok is kinéznie egy táblát, de nem követik a adatbázistáblák meghatározó szabályok.

Például a következő parancsfájl meghatározása egy numerikus tömbje hosszának 6 és az R-változóban tárolja azokat `df1`. A numerikus tömb majd együtt (a fent létrehozott) RTestData tábla egész számokat tartalmazó három (3) értékeket, győződjön meg arról, egy új adatkeretbe `df2`.

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

Töltse ki az adathalmaz, az R megismétli az elemeket, ahányszor szükséges egyezik meg a tömb elemei lekért RTestData `df1`.

**Results**
    
|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

Ne feledje, hogy adatkeretek csak néz ki egy táblát, de ténylegesen vektorok listája.

## <a name="cast-or-convert-sql-data"></a>CAST vagy convert SQL-adatok

R- és SQL ne használja az azonos típusú adatokat, így az SQL-adatokat, és ezután kell küldenie az R futtatókörnyezettel lekérdezés futtatásakor a bizonyos típusú implicit konverzió általában kerül sor. Az R vissza adatokat SQL egy másik hárompéldányos készletet átalakítások kerül sor.

- Az SQL a lekérdezésből származó adatokat leküldi az R-folyamat, és konvertálja nagyobb hatékonyságot biztosít egy belső ábrázolását.
- Az R futtatókörnyezettel az adatokat tölt be egy data.frame változót, és az adatokat a saját műveleteket hajt végre.
- Az adatbázismotor visszaadja az adatokat SQL belső biztonságos kapcsolatot használ, és adatokat SQL-adattípusok tekintetében.
- Kap az adatok SQL csatlakozik egy ügyfél vagy a hálózati könyvtár, amely adja ki az SQL-lekérdezések és kezelni a táblázatos adatkészletek használatával. Az ügyfélalkalmazás hatással lehet az adatokat, egyéb módon.

Ha szeretné látni, hogyan is működik mindez, például a-lekérdezést futtathat a [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) data warehouse-bA. Ez a nézet létrehozásakor előrejelzések használt értékesítési adatait adja vissza.

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
> – Az AdventureWorks bármely verzióját használja, vagy egy másik lekérdezést, a saját adatbázis létrehozása. A pont, hogy néhány adat, amely tartalmazza a szöveg, dátum és idő és numerikus értékek kezelhesse.

Most próbáljon a lekérdezés a tárolt eljárás bemeneti.

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

Ha hibaüzenetet kap, valószínűleg szüksége, hogy bizonyos szerkesztések, a lekérdezés szövege. Ha például a karakterlánc predikátum a WHERE záradékban két készletnyi szimpla idézőjelek közé kell tenni.

Után kap, a lekérdezés működik, tekintse át az eredményeit a `str` hogy R hogyan kezeli a bemeneti adatok megtekintéséhez.

**Results**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- A dátum/idő oszlop feldolgozása megtörtént az R adattípust **POSIXct**.
- A szöveges oszlop, "ProductSeries" néven azonosított egy **tényező**, ami azt jelenti, egy kategorikus változó. Karakterlánc-értékek alapértelmezés szerint tényezőként kezeli. Ha az R egy karakterlánc, belső használatra egész számra konvertálni, és majd leképezve a karakterlánc a kimenetet.

## <a name="summary"></a>Összefoglalás

Még ezeket rövid példákból láthatja szükség az adatok átalakítása a hatásait amikor átadása az SQL-lekérdezések bemenetként. Mivel egyes SQL-adattípusok nem támogatottak az R, vegye figyelembe a következő módokon hibák elkerülése érdekében:

- Az adatok előre tesztelje, és ellenőrizze a oszlopból vagy értékből a sémában, amely a jelenthet problémát, ha a R-kód számára.
- Külön-külön, adja meg az adatforrás bemeneti oszlopok használata helyett `SELECT *`, és tudja, hogyan fogja kezelni minden oszlophoz.
- Hajtható végre explicit típuskonverziók szükség szerint, ha a bemeneti adatok elkerülheti a meglepetéseket.
- Kerülje a megadásának oszlopait, amelyek hibákat okozhatnak, és nem hasznos, ha modellezési adatok (például a GUID-AZONOSÍTÓI vagy frissíthetők).

A támogatott és nem támogatott R-adattípusok további információkért lásd: [R-kódtárak és adattípusok](/sql/advanced-analytics/r/r-libraries-and-data-types).

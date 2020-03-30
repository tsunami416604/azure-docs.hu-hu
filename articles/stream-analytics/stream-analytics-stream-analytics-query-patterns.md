---
title: Gyakori lekérdezési minták az Azure Stream Analytics szolgáltatásban
description: Ez a cikk számos gyakori lekérdezési minták és tervek, amelyek hasznosak az Azure Stream Analytics-feladatok.
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: aa8bd6e89dd47c4e972a860691d1bc3779ba5bc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982321"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Gyakori lekérdezési minták az Azure Stream Analytics szolgáltatásban

Az Azure Stream Analytics lekérdezései SQL-szerű lekérdezési nyelven vannak kifejezve. A nyelvi konstrukciók a [Stream Analytics lekérdezési nyelvi](/stream-analytics-query/stream-analytics-query-language-reference) referencia-útmutatójában vannak dokumentálva. 

A lekérdezésterv egyszerű áteresztő logikát fejezhet ki az eseményadatok áthelyezése egy bemeneti adatfolyamból egy kimeneti adattárba, vagy gazdag mintaegyeztetést és időbeli elemzést tehet a különböző időablakok összesítéseinek kiszámításához, mint az [IoT-megoldás létrehozása a Stream Analytics-útmutató használatával.](stream-analytics-build-an-iot-solution-using-stream-analytics.md) Több bemenetadatait összekapcsolhatja a streamelési események kombinálásához, és a statikus referenciaadatokkal való keresgéléseket az eseményértékek gazdagításához. Több kimenetre is írhat adatokat.

Ez a cikk a valós forgatókönyvek alapján számos gyakori lekérdezési minta megoldásait ismerteti.

## <a name="supported-data-formats"></a>Támogatott adatformátumok

Az Azure Stream Analytics támogatja a CSV,JSON és Avro adatformátumokban történő feldolgozási eseményeket.

A JSON és az Avro összetett típusokat is tartalmazhat, például beágyazott objektumokat (rekordokat) vagy tömböket. Az összetett adattípusok használatáról a [JSON- és AVRO-adatok elemzéséről](stream-analytics-parsing-json.md) szóló cikkben olvashat bővebben.

## <a name="simple-pass-through-query"></a>Egyszerű átadó lekérdezés

Egy egyszerű átadó lekérdezés segítségével másolhatja a bemeneti adatfolyam adatait a kimenetbe. Ha például egy valós idejű járműadatokat tartalmazó adatfolyamot egy SQL-adatbázisba kell menteni a levélelemzéshez, egy egyszerű átadó lekérdezés végzi el a feladatot.

**Bemenet**:

| Gyártmány | Time | Tömeg |
| --- | --- | --- |
| Gyártmény1 |2015-01-01T00:00:01.00000000Z |"1000" |
| Gyártmény1 |2015-01-01T00:00:02.02.0000000Z |"2000" |

**Kimenet**:

| Gyártmány | Time | Tömeg |
| --- | --- | --- |
| Gyártmény1 |2015-01-01T00:00:01.00000000Z |"1000" |
| Gyártmény1 |2015-01-01T00:00:02.02.0000000Z |"2000" |

**Lekérdezés**:

```SQL
SELECT
    *
INTO Output
FROM Input
```

A **SELECT** * lekérdezés kivetíti a bejövő események összes mezőjét, és elküldi azokat a kimenetre. Ugyanígy a **SELECT** csak a szükséges mezők et vetíti ki a bemenetből. Ebben a példában, ha a jármű *make* és *az idő* az egyetlen szükséges mezőket kell menteni, ezek a mezők megadhatók a **SELECT** utasításban.

**Bemenet**:

| Gyártmány | Time | Tömeg |
| --- | --- | --- |
| Gyártmény1 |2015-01-01T00:00:01.00000000Z |1000 |
| Gyártmény1 |2015-01-01T00:00:02.02.0000000Z |2000 |
| 2. |2015-01-01T00:00:04.04.0000000Z |1500 |

**Kimenet**:

| Gyártmány | Time |
| --- | --- |
| Gyártmény1 |2015-01-01T00:00:01.00000000Z |
| Gyártmény1 |2015-01-01T00:00:02.02.0000000Z |
| 2. |2015-01-01T00:00:04.04.0000000Z |

**Lekérdezés**:

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```
## <a name="data-aggregation-over-time"></a>Adatok összesítése az idő múlásával

Az adatok időablakon keresztüli kiszámításához az adatok összesíthetők. Ebben a példában a számlálás az utolsó 10 perc alatt számítja ki az egyes autógyártmányok számát.

**Bemenet**:

| Gyártmány | Time | Tömeg |
| --- | --- | --- |
| Gyártmény1 |2015-01-01T00:00:01.00000000Z |1000 |
| Gyártmény1 |2015-01-01T00:00:02.02.0000000Z |2000 |
| 2. |2015-01-01T00:00:04.04.0000000Z |1500 |

**Kimenet**:

| Gyártmány | Darabszám |
| --- | --- |
| Gyártmény1 | 2 |
| 2. | 1 |

**Lekérdezés**:

```SQL
SELECT
    Make,
    COUNT(*) AS Count
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Ez az összesítés csoportosítja az autókat *make* és számít őket 10 másodpercenként. A kimenet a *Make* és *az autók száma,* hogy ment keresztül az autópályadíj.

A TumblingWindow egy ablakos függvény, amely az események csoportosítására szolgál. Összesítés alkalmazható az összes csoportosított eseményre. További információt az [ablakos funkciók című témakörben talál.](stream-analytics-window-functions.md)

Az összesítéssel kapcsolatos további információkért tekintse meg az [összesítő függvényeket.](/stream-analytics-query/aggregate-functions-azure-stream-analytics)

## <a name="data-conversion"></a>Adatátalakítás

Az adatok valós időben, cast módszerrel **adhatók le.** Az autó súlya például **a nvarchar(max)** típusból **bigint** típusba konvertálható, és numerikus számításhoz használható.

**Bemenet**:

| Gyártmány | Time | Tömeg |
| --- | --- | --- |
| Gyártmény1 |2015-01-01T00:00:01.00000000Z |"1000" |
| Gyártmény1 |2015-01-01T00:00:02.02.0000000Z |"2000" |

**Kimenet**:

| Gyártmány | Tömeg |
| --- | --- |
| Gyártmény1 |3000 |

**Lekérdezés**:

```SQL
SELECT
    Make,
    SUM(CAST(Weight AS BIGINT)) AS Weight
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Az adattípus megadásához **használjon CAST** utasítást. Tekintse meg az adattípusok támogatott adattípusainak listáját [(Azure Stream Analytics).](/stream-analytics-query/data-types-azure-stream-analytics)

Az [adatkonverziós funkciókkal](/stream-analytics-query/conversion-functions-azure-stream-analytics)kapcsolatos további információkért.

## <a name="string-matching-with-like-and-not-like"></a>String megfelelő like és nem tetszik

**Like** és **NEM LIKE** lehet használni, hogy ellenőrizze, ha egy mező megfelel egy bizonyos mintát. Létrehozható például egy szűrő, amely csak az "A" betűvel kezdődő és a 9-es számmal végződő azonosítótáblákat adja vissza.

**Bemenet**:

| Gyártmány | License_plate | Time |
| --- | --- | --- |
| Gyártmény1 |ABC-123 |2015-01-01T00:00:01.00000000Z |
| 2. |AAA-999 között |2015-01-01T00:00:02.02.0000000Z |
| Make3 (Gyártmány3 |ABC-369 |2015-01-01T00:00:03.00000000Z |

**Kimenet**:

| Gyártmány | License_plate | Time |
| --- | --- | --- |
| 2. |AAA-999 között |2015-01-01T00:00:02.02.0000000Z |
| Make3 (Gyártmány3 |ABC-369 |2015-01-01T00:00:03.00000000Z |

**Lekérdezés**:

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

A **LIKE** utasítás segítségével ellenőrizze a **License_plate** mezőértékét. Az "A" betűvel kell kezdődnie, majd nulla vagy több karakterből álló karakterláncot kell, amely a 9-es számmal végződik.

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>Logika megadása különböző esetekhez/értékekhez (CASE utasítások)

**Az ESETutasítások** különböző számításokat nyújthatnak a különböző mezőkhöz, adott kritérium alapján. Például rendelje hozzá az "A" sávot a *Make1* és a "B" sávot bármely más gyártmányhoz.

**Bemenet**:

| Gyártmány | Time |
| --- | --- |
| Gyártmény1 |2015-01-01T00:00:01.00000000Z |
| 2. |2015-01-01T00:00:02.02.0000000Z |
| 2. |2015-01-01T00:00:03.00000000Z |

**Kimenet**:

| Gyártmány |Dispatch_to_lane | Time |
| --- | --- | --- |
| Gyártmény1 |"A" |2015-01-01T00:00:01.00000000Z |
| 2. |"B" |2015-01-01T00:00:02.02.0000000Z |

**Megoldás**:

```SQL
SELECT
    Make
    CASE
        WHEN Make = "Make1" THEN "A"
        ELSE "B"
    END AS Dispatch_to_lane,
    System.TimeStamp() AS Time
FROM
    Input TIMESTAMP BY Time
```

A **CASE** kifejezés egy kifejezést egyszerű kifejezések készletével hasonlít össze az eredmény meghatározásához. Ebben a példában a *Make1* járműveit az "A" sávba küldik, míg bármely más gyártmányú járművet "B" sávba rendelnek.

További információt a [kis- és nagybetűk kifejezésben](/stream-analytics-query/case-azure-stream-analytics)talál.

## <a name="send-data-to-multiple-outputs"></a>Adatok küldése több kimenetre

Több **SELECT** utasítás használható adatok különböző kimeneti fogadókba történő kimenetére. Például egy **SELECT** egy küszöbérték-alapú riasztást, míg egy másik eseményeket a blob storage kimeneti.

**Bemenet**:

| Gyártmány | Time |
| --- | --- |
| Gyártmény1 |2015-01-01T00:00:01.00000000Z |
| Gyártmény1 |2015-01-01T00:00:02.02.0000000Z |
| 2. |2015-01-01T00:00:01.00000000Z |
| 2. |2015-01-01T00:00:02.02.0000000Z |
| 2. |2015-01-01T00:00:03.00000000Z |

**Kimenet ArchiveOutput**:

| Gyártmány | Time |
| --- | --- |
| Gyártmény1 |2015-01-01T00:00:01.00000000Z |
| Gyártmény1 |2015-01-01T00:00:02.02.0000000Z |
| 2. |2015-01-01T00:00:01.00000000Z |
| 2. |2015-01-01T00:00:02.02.0000000Z |
| 2. |2015-01-01T00:00:03.00000000Z |

**Kimeneti riasztáskimenet**:

| Gyártmány | Time | Darabszám |
| --- | --- | --- |
| 2. |2015-01-01T00:00:10.00000000Z |3 |

**Lekérdezés**:

```SQL
SELECT
    *
INTO
    ArchiveOutput
FROM
    Input TIMESTAMP BY Time

SELECT
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count]
INTO
    AlertOutput
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING
    [Count] >= 3
```

Az **INTO** záradék közli a Stream Analytics-tel, hogy melyik kimenetbe kell írni az adatokat. Az első **SELECT** egy átadó lekérdezést határoz meg, amely adatokat fogad a bemenetből, és elküldi azt az **ArchiveOutput**nevű kimenetre. A második lekérdezés néhány egyszerű összesítést és szűrést végez, mielőtt az eredményeket egy AlertOutput nevű alsóbb rétegbeli riasztási rendszer kimenetére **küldené.**

Ne feledje, hogy a **WITH** záradék több allekérdezési blokk definiálására is használható. Ezzel a beállítással kevesebb olvasót nyit meg a bemeneti forráshoz.

**Lekérdezés**:

```SQL
WITH ReaderQuery AS (
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
)

SELECT * INTO ArchiveOutput FROM ReaderQuery

SELECT 
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count] 
INTO AlertOutput 
FROM ReaderQuery
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING [Count] >= 3
```

További információ: [ **WITH** záradék](/stream-analytics-query/with-azure-stream-analytics).

## <a name="count-unique-values"></a>Egyedi értékek megszámlálása

**A DARAB** és **a DISTINCT** segítségével megszámolhatja az adatfolyamban egy időablakon belül megjelenő egyedi mezőértékek számát. A lekérdezés lehet létrehozni, hogy kiszámítja, hogy hány egyedi *teszi* az autók áthaladt a fizetős fülkében egy 2 másodperces ablakban.

**Bemenet**:

| Gyártmány | Time |
| --- | --- |
| Gyártmény1 |2015-01-01T00:00:01.00000000Z |
| Gyártmény1 |2015-01-01T00:00:02.02.0000000Z |
| 2. |2015-01-01T00:00:01.00000000Z |
| 2. |2015-01-01T00:00:02.02.0000000Z |
| 2. |2015-01-01T00:00:03.00000000Z |

**Kimeneti:**

| Count_make | Time |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**Lekérdezés:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

**A COUNT(DISTINCT Make)** függvény a **Make** oszlopban lévő különböző értékek számát adja eredményül egy időablakon belül.
További információt a [ **COUNT** összesítő függvény című dokumentumban talál.](/stream-analytics-query/count-azure-stream-analytics)

## <a name="calculation-over-past-events"></a>Számítás a múltbeli eseményekhez

A **HACS** funkció segítségével egy időablakon belül megtekintheti a múltbeli eseményeket, és összehasonlíthatja azokat az aktuális eseménnyel. Például, a jelenlegi autó teszi lehet kimeneti, ha eltér az utolsó autó, hogy ment keresztül az autópályadíj.

**Bemenet**:

| Gyártmány | Time |
| --- | --- |
| Gyártmény1 |2015-01-01T00:00:01.00000000Z |
| 2. |2015-01-01T00:00:02.02.0000000Z |

**Kimenet**:

| Gyártmány | Time |
| --- | --- |
| 2. |2015-01-01T00:00:02.02.0000000Z |

**Lekérdezés**:

```SQL
SELECT
    Make,
    Time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

A **HELYI AKCIÓCSOPORT** segítségével egy eseményvisszapillanthat a bemeneti adatfolyamba, lekéri a *Make* értéket, és összehasonlítja az aktuális esemény *Make* értékével, és adja ki az eseményt.

További információkért lásd a [**HACS**](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="retrieve-the-first-event-in-a-window"></a>Ablak első eseményének beolvasása

**IsFirst** lehet használni, hogy beolvassa az első esemény egy időablakban. Például az első autóadatok 10 percenkénti kiterenz.

**Bemenet**:

| License_plate | Gyártmány | Time |
| --- | --- | --- |
| DXE 5291 |Gyártmény1 |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Make3 (Gyártmány3 |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Gyártmény1 |2015-07-27T00:05:01.00000000Z |
| YHN 6970 |2. |2015-07-27T00:06:00.00000000Z |
| VFE 1616 |2. |2015-07-27T00:09:31.0000000Z |
| Qyf 9358 |Gyártmény1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |4. |2015-07-27T00:13:45.0000000Z |

**Kimenet**:

| License_plate | Gyártmány | Time |
| --- | --- | --- |
| DXE 5291 |Gyártmény1 |2015-07-27T00:00:05.0000000Z |
| Qyf 9358 |Gyártmény1 |2015-07-27T00:12:02.0000000Z |

**Lekérdezés**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) = 1
```

**IsFirst** is partíció az adatokat, és kiszámítja az első esemény minden egyes autó *Make* található minden 10 perces időközönként.

**Kimenet**:

| License_plate | Gyártmány | Time |
| --- | --- | --- |
| DXE 5291 |Gyártmény1 |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Make3 (Gyártmány3 |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |2. |2015-07-27T00:06:00.00000000Z |
| Qyf 9358 |Gyártmény1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |4. |2015-07-27T00:13:45.0000000Z |

**Lekérdezés**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

További információt az [**IsFirst**](/stream-analytics-query/isfirst-azure-stream-analytics)című dokumentumban talál.

## <a name="return-the-last-event-in-a-window"></a>Az utolsó esemény visszaküldése egy ablakban

Mivel a rendszer valós időben használja fel az eseményeket, nincs olyan függvény, amely meghatározná, hogy egy esemény lesz-e az utolsó, amely az adott időablakra érkezik. Ennek elérése érdekében a bemeneti adatfolyamot egy másikhoz kell csatlakoztatni, ahol az esemény ideje az adott ablakösszes eseményének maximális ideje.

**Bemenet**:

| License_plate | Gyártmány | Time |
| --- | --- | --- |
| DXE 5291 |Gyártmény1 |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Make3 (Gyártmány3 |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Gyártmény1 |2015-07-27T00:05:01.00000000Z |
| YHN 6970 |2. |2015-07-27T00:06:00.00000000Z |
| VFE 1616 |2. |2015-07-27T00:09:31.0000000Z |
| Qyf 9358 |Gyártmény1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |4. |2015-07-27T00:13:45.0000000Z |

**Kimenet**:

| License_plate | Gyártmány | Time |
| --- | --- | --- |
| VFE 1616 |2. |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |4. |2015-07-27T00:13:45.0000000Z |

**Lekérdezés**:

```SQL
WITH LastInWindow AS
(
    SELECT 
        MAX(Time) AS LastEventTime
    FROM 
        Input TIMESTAMP BY Time
    GROUP BY 
        TumblingWindow(minute, 10)
)

SELECT 
    Input.License_plate,
    Input.Make,
    Input.Time
FROM
    Input TIMESTAMP BY Time 
    INNER JOIN LastInWindow
    ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
    AND Input.Time = LastInWindow.LastEventTime
```

A lekérdezés első lépése megkeresi a maximális időbélyegzőt a 10 perces ablakokban, azaz az ablak utolsó eseményének időbélyegzőjét. A második lépés az első lekérdezés eredményeit az eredeti adatfolyammal egyesíti, és megkeresheti az egyes ablakok utolsó időbélyegzőinek megfelelő eseményt. 

**DATEDIFF** egy dátum-specifikus függvény, amely összehasonlítja és adja vissza az időkülönbség két DateTime mezők, további információkért lásd a [dátum függvények](https://docs.microsoft.com/stream-analytics-query/date-and-time-functions-azure-stream-analytics).

Az adatfolyamok összekapcsolására vonatkozó további információkért tekintse meg a JOIN című [**részt.**](/stream-analytics-query/join-azure-stream-analytics)


## <a name="correlate-events-in-a-stream"></a>Események korrelációja egy adatfolyamban

Az ugyanabban az adatfolyamban lévő események korrelációja a **hacs** funkció használatával a múltbeli események et vizsgálhatja. Például, egy kimenet lehet generálni minden alkalommal, amikor két egymást követő autók ugyanabból a *Make* megy keresztül az autópályadíj az elmúlt 90 másodpercben.

**Bemenet**:

| Gyártmány | License_plate | Time |
| --- | --- | --- |
| Gyártmény1 |ABC-123 |2015-01-01T00:00:01.00000000Z |
| Gyártmény1 |AAA-999 között |2015-01-01T00:00:02.02.0000000Z |
| 2. |DEF-987 |2015-01-01T00:00:03.00000000Z |
| Gyártmény1 |GHI-345 |2015-01-01T00:00:04.04.0000000Z |

**Kimenet**:

| Gyártmány | Time | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Gyártmény1 |2015-01-01T00:00:02.02.0000000Z |AAA-999 között |ABC-123 |2015-01-01T00:00:01.00000000Z |

**Lekérdezés**:

```SQL
SELECT
    Make,
    Time,
    License_plate AS Current_car_license_plate,
    LAG(License_plate, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_license_plate,
    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

A **HACS** funkció egy esemény után megvizsgálhatja a bemeneti adatfolyamot, és lekérheti a *Make* értéket, összehasonlítva az aktuális esemény *Make* értékével.  A feltétel teljesülése után az előző esemény adatai a **HACS** használatával vetíthetők ki a **SELECT** utasításban.

További információkért lásd a [HACS](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="detect-the-duration-between-events"></a>Az események közötti időtartam észlelése

Egy esemény időtartama kiszámítható az utolsó Indítási eseményre, amint egy End esemény megérkezik. Ez a lekérdezés hasznos lehet annak meghatározásához, hogy a felhasználó milyen időt tölt egy oldalon vagy egy szolgáltatásban.

**Bemenet**:  

| Felhasználó | Szolgáltatás | Esemény | Time |
| --- | --- | --- | --- |
| user@location.com |Jobb menü |Indítás |2015-01-01T00:00:01.00000000Z |
| user@location.com |Jobb menü |Befejezés |2015-01-01T00:00:08.00000000Z |

**Kimenet**:  

| Felhasználó | Szolgáltatás | Időtartam |
| --- | --- | --- |
| user@location.com |Jobb menü |7 |

**Lekérdezés**:

```SQL
SELECT
    [user],
    feature,
    DATEDIFF(
        second,
        LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'),
        Time) as duration
FROM input TIMESTAMP BY Time
WHERE
    Event = 'end'
```

Az **UTOLSÓ** függvény egy adott feltételen belüli utolsó esemény beolvasására használható. Ebben a példában a feltétel egy Start típusú esemény, amely particionálja a **keresést partition by** felhasználó és szolgáltatás szerint. Így minden felhasználó és funkció egymástól függetlenül kezeli, amikor a Start esemény keresésekor. **A KORLÁT IDŐTARTAM** A keresés idővel 1 órára korlátozza a keresést a Befejezés és a Kezdő események között.

## <a name="detect-the-duration-of-a-condition"></a>Egy feltétel időtartamának észlelése

A több eseményre kiterjedő körülmények esetén a **HACS** funkció az adott feltétel időtartamának azonosítására használható. Tegyük fel például, hogy egy hiba miatt minden autó nak helytelen súlya volt (20 000 font felett), és ki kell számítani a hiba időtartamát.

**Bemenet**:

| Gyártmány | Time | Tömeg |
| --- | --- | --- |
| Gyártmény1 |2015-01-01T00:00:01.00000000Z |2000 |
| 2. |2015-01-01T00:00:02.02.0000000Z |25000 |
| Gyártmény1 |2015-01-01T00:00:03.00000000Z |26000 |
| 2. |2015-01-01T00:00:04.04.0000000Z |25000 |
| Gyártmény1 |2015-01-01T00:00:05.0000000Z |26000 |
| 2. |2015-01-01T00:00:06.00000000Z |25000 |
| Gyártmény1 |2015-01-01T00:00:07.07.0000000Z |26000 |
| 2. |2015-01-01T00:00:08.00000000Z |2000 |

**Kimenet**:

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**Lekérdezés**:

```SQL
WITH SelectPreviousEvent AS
(
SELECT
    *,
    LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previous_time,
    LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previous_weight
FROM input TIMESTAMP BY [time]
)

SELECT 
    LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previous_weight < 20000 ) [Start_fault],
    previous_time [End_fault]
FROM SelectPreviousEvent
WHERE
    [weight] < 20000
    AND previous_weight > 20000
```
Az első **SELECT** utasítás korrelálja az aktuális súlymérést az előző méréssel, és az aktuális méréssel együtt vetíti ki. A második **SELECT** visszatekint az utolsó eseményre, ahol a *previous_weight* kevesebb, mint 20000, ahol a jelenlegi súly kisebb, mint 20000, és a jelenlegi esemény *previous_weight* nagyobb volt, mint 20000.

A End_fault az aktuális nem hibás esemény, ahol az előző esemény hibás volt, és a Start_fault az utolsó nem hibás esemény előtt.

## <a name="periodically-output-values"></a>Rendszeres en kimeneti értékek

Szabálytalan vagy hiányzó események esetén rendszeres intervallumkimenet generálható egy gyéren kisebb adatbevitelből. Például hozzon létre egy eseményt 5 másodpercenként, amely a legutóbb látott adatpontot jelenti.

**Bemenet**:

| Time | Érték |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Kimenet (első 10 sor):**

| Window_end | Last_event. Idő | Last_event. Érték |
| --- | --- | --- |
| 2014-01-01T14:01:00.000Z |2014-01-01T14:01:00.000Z |1 |
| 2014-01-01T14:01:05.000Z |2014-01-01T14:01:05.000Z |2 |
| 2014-01-01T14:01:10.000Z |2014-01-01T14:01:10.000Z |3 |
| 2014-01-01T14:01:15.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:20.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:25.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:30.000Z |2014-01-01T14:01:30.000Z |5 |
| 2014-01-01T14:01:35.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:40.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:45.000Z |2014-01-01T14:01:35.000Z |6 |

**Lekérdezés**:

```SQL
SELECT
    System.Timestamp() AS Window_end,
    TopOne() OVER (ORDER BY Time DESC) AS Last_event
FROM
    Input TIMESTAMP BY Time
GROUP BY
    HOPPINGWINDOW(second, 300, 5)
```

Ez a lekérdezés 5 másodpercenként hoz létre eseményeket, és a korábban fogadott utolsó eseményt adja ki. Az **HOPPINGWINDOW** időtartama határozza meg, hogy a lekérdezés milyen messze van a legutóbbi esemény megtalálásához.

További információt az [Ugrás ablakban](/stream-analytics-query/hopping-window-azure-stream-analytics)talál.

## <a name="process-events-with-independent-time-substreams"></a>Események feldolgozása független idővel (Részfolyamok)

Az események az eseményelők közötti óraeltérés, a partíciók közötti óraeltérés vagy a hálózati késés miatt érkezhetnek későn vagy nem megfelelő en.
A *TollID* 2 eszközórája például öt másodperccel van lemaradva az *1-es díjazonosítótól,* a *TollID* 3 eszközórája pedig tíz másodperccel van lemaradva az *1-es díjjeltől.* A számítás történhet egymástól függetlenül minden útdíj, figyelembe véve csak a saját óraadatait időbélyegként.

**Bemenet**:

| Azonosító tábla | Gyártmány | Time | Tollazonosító |
| --- | --- | --- | --- |
| DXE 5291 |Gyártmény1 |2015-07-27T00:00:01.00000000Z | 1 |
| YHN 6970 |2. |2015-07-27T00:00:05.0000000Z | 1 |
| Qyf 9358 |Gyártmény1 |2015-07-27T00:00:01.00000000Z | 2 |
| GXF 9462 |Make3 (Gyártmány3 |2015-07-27T00:00:04.04.0000000Z | 2 |
| VFE 1616 |2. |2015-07-27T00:00:10.00000000Z | 1 |
| RMV 8282 |Gyártmény1 |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |Make3 (Gyártmány3 |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |4. |2015-07-27T00:00:07.07.0000000Z | 3 |

**Kimenet**:

| Tollazonosító | Darabszám |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Lekérdezés**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

A **TIMESTAMP OVER BY** záradék az egyes eszközök idővonalát egymástól függetlenül vizsgálja az alfolyamok használatával. Az egyes *TollID-k* kimeneti eseménye a számítás során jön létre, ami azt jelenti, hogy az események sorrendben vannak az egyes *TollID-khoz* képest, ahelyett, hogy úgy rendeznék át, mintha az összes eszköz ugyanazon az órán lenne.

További információ: [TIMESTAMP BY OVER](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering).

## <a name="remove-duplicate-events-in-a-window"></a>Ismétlődő események eltávolítása az ablakban

Egy művelet végrehajtásakor, például egy adott időablak ban az események átlagainak kiszámításakor szűrni kell az ismétlődő eseményeket. A következő példában a második esemény az első másolata.

**Bemenet**:  

| DeviceId | Time | Attribútum | Érték |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.00000000Z |Hőmérséklet |50 |
| 1 |2018-07-27T00:00:01.00000000Z |Hőmérséklet |50 |
| 2 |2018-07-27T00:00:01.00000000Z |Hőmérséklet |40 |
| 1 |2018-07-27T00:00:05.0000000Z |Hőmérséklet |60 |
| 2 |2018-07-27T00:00:05.0000000Z |Hőmérséklet |50 |
| 1 |2018-07-27T00:00:10.0000000Z |Hőmérséklet |100 |

**Kimenet**:  

| Átlagérték | DeviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Lekérdezés**:

```SQL
With Temp AS (
SELECT
    COUNT(DISTINCT Time) AS CountTime,
    Value,
    DeviceId
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Value,
    DeviceId,
    SYSTEM.TIMESTAMP()
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**A COUNT(DISTINCT Time)** függvény az Idő oszlopban szereplő különböző értékek számát adja eredményül egy időablakon belül. Az első lépés kimenete ezután az eszközönkénti átlag kiszámításához használható az ismétlődések elvetésével.

További információt a [COUNT(DISTINCT Time)](/stream-analytics-query/count-azure-stream-analytics)című dokumentumban talál.

## <a name="session-windows"></a>Munkamenet Windows

A munkamenetablak olyan ablak, amely folyamatosan bővül az események bekövetkezésekor, és számítás céljából bezárul, ha egy adott idő elteltével nem érkezik esemény, vagy ha az ablak eléri a maximális időtartamát.
Ez az ablak különösen akkor hasznos, ha a felhasználói kapcsolati adatokat számítja ki. Az ablak akkor kezdődik, amikor a felhasználó elkezdi a rendszerrel való interakciót, és akkor zárul be, amikor nem észlel több eseményt, ami azt jelenti, hogy a felhasználó leállította az interakciót.
Például, a felhasználó interakcióba lép egy weboldalt, ahol a kattintások száma van bejelentkezve, a Munkamenet ablak segítségével megtudhatja, hogy mennyi ideig a felhasználó interakcióba lépett a webhelyen.

**Bemenet**:

| User_id | Time | URL-cím |
| --- | --- | --- |
| 0 | 2017-01-26T00:00:00.00000000Z | "www.example.com/a.html" |
| 0 | 2017-01-26T00:00:20.00000000Z | "www.example.com/b.html" |
| 1 | 2017-01-26T00:00:55.0000000Z | "www.example.com/c.html" |
| 0 | 2017-01-26T00:01:10.0000000Z | "www.example.com/d.html" |
| 1 | 2017-01-26T00:01:15.0000000Z | "www.example.com/e.html" |

**Kimenet**:

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00:00:00.00000000Z | 2017-01-26T00:01:10.0000000Z | 70 |
| 1 | 2017-01-26T00:00:55.0000000Z | 2017-01-26T00:01:15.0000000Z | 20 |

**Lekérdezés**:

``` SQL
SELECT
    user_id,
    MIN(time) as StartTime,
    MAX(time) as EndTime,
    DATEDIFF(second, MIN(time), MAX(time)) AS duration_in_seconds
FROM input TIMESTAMP BY time
GROUP BY
    user_id,
    SessionWindow(minute, 1, 60) OVER (PARTITION BY user_id)
```

A **SELECT kivetíti** a felhasználói interakcióhoz kapcsolódó adatokat, valamint a kapcsolati tevékenység időtartamát. Az adatok csoportosítása felhasználó és egy **SessionWindow** szerint, amely akkor záródik be, ha 1 percen belül nem történik interakció, legfeljebb 60 perces ablakmérettel.

A **SessionWindow ablakról**további információt a [Munkamenet ablak című](/stream-analytics-query/session-window-azure-stream-analytics) szakaszban talál.

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>Nyelvi bővíthetőség a Felhasználó által definiált funkcióval JavaScript és C nyelven #

Az Azure Stream Analytics lekérdezési nyelve kiterjeszthető a JavaScript vagy C# nyelven írt egyéni függvényekkel. A felhasználó által definiált függvények (UDF) olyan egyéni/összetett számítások, amelyek nem fejezhetők ki könnyen az **SQL** nyelv használatával. Ezek az UDF-ek egyszer definiálhatók, és többször is használhatók egy lekérdezésen belül. Egy UDF például hexadecimális *nvarchar(max)* érték *bigint* értékké konvertálására használható.

**Bemenet**:

| Device_id | HexValue |
| --- | --- |
| 1 | "B4" |
| 2 | "11B" |
| 3 | "121" |

**Kimenet**:

| Device_id | Decimal |
| --- | --- |
| 1 | 180 |
| 2 | 283 |
| 3 | 289 |

```JavaScript
function hex2Int(hexValue){
    return parseInt(hexValue, 16);
}
```

```C#
public static class MyUdfClass {
    public static long Hex2Int(string hexValue){
        return int.Parse(hexValue, System.Globalization.NumberStyles.HexNumber);
    }
}
```

```SQL
SELECT
    Device_id,
    udf.Hex2Int(HexValue) AS Decimal
From
    Input
```

A felhasználó által definiált függvény kiszámítja a *bigint* értéket a HexValue minden felhasznált esemény.

További információ: [JavaScript](/azure/stream-analytics/stream-analytics-javascript-user-defined-functions) és [C#](/azure/stream-analytics/stream-analytics-edge-csharp-udf).

## <a name="advanced-pattern-matching-with-match_recognize"></a>Speciális mintaegyezés MATCH_RECOGNIZE

**MATCH_RECOGNIZE** egy speciális mintaegyeztetési mechanizmus, amely egy eseménysorozat és egy jól definiált reguláris kifejezésminta egyeztetésére használható.
Például egy ATM-et valós időben figyel a hibák, az ATM működése során, ha két egymást követő figyelmeztető üzenetet a rendszergazdának értesítenie kell.

**Bemenet**:

| ATM_id | Operation_id | Return_Code | Time |
| --- | --- | --- | --- |
| 1 | "Pin beírása" | "Siker" | 2017-01-26T00:10:00.00000000Z |
| 2 | "Nyitó pénznyerő hely" | "Siker" | 2017-01-26T00:10:07.00000000Z |
| 2 | "A pénznyerő hely lezárása" | "Siker" | 2017-01-26T00:10:11.0000000Z |
| 1 | "A mennyiség visszavonása" | "Siker" | 2017-01-26T00:10:08.00000000Z |
| 1 | "Nyitó pénznyerő hely" | "Figyelmeztetés" | 2017-01-26T00:10:14.0000000Z |
| 1 | "Banki egyenleg nyomtatása" | "Figyelmeztetés" | 2017-01-26T00:10:19.0000000Z |

**Kimenet**:

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | "Nyitó pénznyerő hely" | 2017-01-26T00:10:14.0000000Z |

```SQL
SELECT *
FROM intput TIMESTAMP BY time OVER ATM_id
MATCH_RECOGNIZE (
    PARTITON BY ATM_id
    LIMIT DURATION(minute, 1)
    MEASURES
        First(Warning.ATM_id) AS ATM_id,
        First(Warning.Operation_Id) AS First_Warning_Operation_id,
        First(Warning.Time) AS Warning_Time
    AFTER MATCH SKIP TO NEXT ROW
    PATTERN (Success* Warning{2,})
    DEFINE
        Success AS Succes.Return_Code = 'Success',
        Failure AS Warning.Return_Code <> 'Success'
) AS patternMatch
```

Ez a lekérdezés legalább két egymást követő hibaeseménynek felel meg, és riasztást hoz létre, ha a feltételek teljesülnek.
**A MINTA** határozza meg az egyeztetéshez használandó reguláris kifejezést, ebben az esetben tetszőleges számú sikeres műveletet, amelyet legalább két egymást követő hiba követ.
A sikeres ség és a sikertelenség Return_Code érték használatával van meghatározva, és a feltétel teljesülése után az **MTE-k** a *ATM_id*, az első figyelmeztető művelettel és az első figyelmeztetési idővel lesznek kivetítve.

További információt a MATCH_RECOGNIZE című dokumentumban [talál.](/stream-analytics-query/match-recognize-stream-analytics)

## <a name="geofencing-and-geospatial-queries"></a>Geokerítés és térinformatikai lekérdezések
Az Azure Stream Analytics beépített térinformatikai funkciókat biztosít, amelyek olyan forgatókönyvek megvalósításához használhatók, mint a flottakezelés, a fuvarmegosztás, az összekapcsolt autók és az eszközök nyomon követése.
A térinformatikai adatok GeoJSON vagy WKT formátumban is beszámíthatók az eseményfolyam vagy a referenciaadatok részeként.
Például egy olyan vállalat, amely az útlevelek nyomtatására szolgáló gépek gyártására specializálódott, bérbe adja a gépeket a kormányoknak és konzulátusoknak. Ezeknek a gépeknek a helyét erősen ellenőrzik, hogy elkerüljék az útlevelek helytelen elhelyezését és esetleges felhasználását. Minden gép rendelkezik egy GPS tracker, amely az adatok továbbítása egy Azure Stream Analytics-feladat.
A gyártás nyomon szeretné követni a gépek helyét, és figyelmeztetni kell, ha egyikük elhagyja az engedélyezett területet, így távolról letilthatják, riaszthatják a hatóságokat és visszaszerezhetik a berendezést.

**Bemenet**:

| Equipment_id | Equipment_current_location | Time |
| --- | --- | --- |
| 1 | "PONT(-122.13288797982818 47.64082002051315)" | 2017-01-26T00:10:00.00000000Z |
| 1 | "PONT(-122.13307252987875 47.64081350934929)" | 2017-01-26T00:11:00.00000000Z |
| 1 | "PONT(-122.13308862313283 47.6406508603241)" | 2017-01-26T00:12:00.00000000Z |
| 1 | "PONT(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.00000000Z |

**Referencia adatok bevitele**:

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "SOKSZÖG((-122.13326028450979 47.640983386794,-122.132616554344624 1 47.64061471471602751,-122.13326028450979 47.64061471471602751,-122.13326028450979 47.6409833866794))")" |

**Kimenet**:

| Equipment_id | Equipment_alert_location | Time |
| --- | --- | --- |
| 1 | "PONT(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.00000000Z |

```SQL
SELECT
    input.Equipment_id AS Equipment_id,
    input.Equipment_current_location AS Equipment_current_location,
    input.Time AS Time
FROM input TIMESTAMP BY time
JOIN
    referenceInput 
    ON input.Equipment_id = referenceInput.Equipment_id
    WHERE 
        ST_WITHIN(input.Equipment_currenct_location, referenceInput.Equipment_lease_location) = 1
```

A lekérdezés lehetővé teszi a gyártó számára, hogy automatikusan figyelje a gépek helyét, és riasztásokat kapjon, amikor egy gép elhagyja az engedélyezett geokerítést. A beépített térinformatikai funkció lehetővé teszi a felhasználók számára, hogy a lekérdezésben harmadik féltől származó könyvtárak nélkül gps-adatokat használjanak.

További információkért tekintse meg a [Geofencing és a térinformatikai összesítés forgatókönyvek az Azure Stream Analytics cikket.](geospatial-scenarios.md)

## <a name="get-help"></a>Segítségkérés

További segítségért próbálja ki [az Azure Stream Analytics fórumunkat.](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
* [Bevezetés az Azure Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

---
title: Gyakori lekérdezési minták a Azure Stream Analytics
description: Ez a cikk számos gyakori lekérdezési mintát és kialakítást ismertet, amelyek hasznosak Azure Stream Analytics feladatokban.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/16/2019
ms.openlocfilehash: 729385a2ce9feb6e69f9be29c2175b403093be3f
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68413363"
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Példák a gyakori Stream Analytics használati mintákra

Az Azure Stream Analyticsban lévő lekérdezések SQL-szerű lekérdezési nyelven vannak kifejezve. A nyelvi összeállítások leírását a [stream Analytics lekérdezési nyelvi](/stream-analytics-query/stream-analytics-query-language-reference) útmutatója tartalmazza. 

A lekérdezési terv egyszerű áteresztő logikát biztosít az események egyik bemeneti streamből egy kimeneti adattárba való áthelyezéséhez, vagy a megfelelő minta-egyeztetést és időbeli elemzést az összesített adatok kiszámításához a különböző időablakok között, mint a [IoT-megoldás létrehozása Stream Analytics útmutató használata](stream-analytics-build-an-iot-solution-using-stream-analytics.md) . Több bemenetből is csatlakoztathat adatokat a folyamatos átviteli események egyesítéséhez, és a statikus referenciák használatával végezheti el a keresést. Több kimenetre is írhat adatokat.

Ez a cikk a különböző gyakori lekérdezési mintákkal kapcsolatos megoldásokat ismerteti valós forgatókönyvek alapján.

## <a name="work-with-complex-data-types-in-json-and-avro"></a>Összetett adattípusok kezelése JSON és AVRO formátumban.

A Azure Stream Analytics támogatja az események CSV-, JSON-és Avro-adatformátumokban való feldolgozását.

A JSON és a Avro olyan összetett típusokat is tartalmazhat, mint például a beágyazott objektumok (rekordok) vagy a tömbök. Ezen összetett adattípusokkal kapcsolatos további információkért tekintse meg a [JSON-és AVRO-adatok elemzése](stream-analytics-parsing-json.md) című cikket.

## <a name="query-example-convert-data-types"></a>Példa lekérdezésre: Adattípusok konvertálása

**Leírás**: A bemeneti adatfolyamban definiálja a tulajdonságok típusait. Például a rendszer a bemeneti adatfolyamot karakterláncként továbbítja, és a **Sum**( **int** ) értékre kell alakítani.

**Bemenet**:

| Fordítás | Time | Tömeg |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**Kimenet**:

| Fordítás | Tömeg |
| --- | --- |
| Honda |3000 |

**Megoldás**:

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

**Magyarázat**: Az adattípusának megadásához használjon egy **Cast** utasítást a **Weight (súly** ) mezőben. Tekintse meg a támogatott adattípusok listáját az adattípusokban [(Azure stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics).

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Példa lekérdezésre: Használjon hasonló/nem a mintázat megfeleltetését

**Leírás**: Győződjön meg arról, hogy az eseményben megadott mezőérték megfelel egy bizonyos mintának.
Győződjön meg például arról, hogy az eredmény az A és a 9 értékkel kezdődő licencek lemezeit adja vissza.

**Bemenet**:

| Fordítás | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Kimenet**:

| Fordítás | LicensePlate | Time |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Megoldás**:

```SQL
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'
```

**Magyarázat**: A **Like** utasítás használatával keresse meg a **LicensePlate** mező értékét. Az A betűvel kell kezdődnie, majd a nulla vagy több karakterből álló karakterláncot kell tartalmaznia, majd a 9-es számmal kell végződnie. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Példa lekérdezésre: Logika megadása különböző esetekhez/értékekhez (CASE utasítások)

**Leírás**: Adjon meg egy másik számítást egy mezőhöz egy adott feltétel alapján. Például adjon meg egy karakterlánc-leírást, amely szerint a rendszer hány autót adott át, és ez egy speciális eset 1.

**Bemenet**:

| Fordítás | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Kimenet**:

| CarsPassed | Time |
| --- | --- |
| 1 Honda |2015-01-01T00:00:10.0000000Z |
| 2 Toyotas |2015-01-01T00:00:10.0000000Z |

**Megoldás**:

```SQL
    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp() AS AsaTime
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
```

**Magyarázat**: A **Case** kifejezés összehasonlítja egy kifejezést egy egyszerű kifejezésekkel az eredmény meghatározásához. Ebben a példában a jármű 1 számmal tért vissza, és egy másik karakterlánc-Leírás lett visszaadva, mint a jármű az 1-től eltérő számmal.

## <a name="query-example-send-data-to-multiple-outputs"></a>Példa lekérdezésre: Adatokat küldhet több kimenetre

**Leírás**: Adatok küldése több kimeneti célhelyre egyetlen feladatsorból. Például elemezheti a küszöbérték-alapú riasztások és az összes esemény a blob Storage-ba történő archiválását.

**Bemenet**:

| Fordítás | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output1**:

| Fordítás | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output2**:

| Fordítás | Time | Count |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000Z |3 |

**Megoldás**:

```SQL
    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp() AS AsaTime,
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

**Magyarázat**: A **into** záradékból megtudhatja, hogy stream Analytics mely kimenetek írhatnak adatokat ebből az utasításból. Az első lekérdezés a **ArchiveOutput**nevű kimenethez kapott adatokat továbbítja. A második lekérdezés néhány egyszerű összesítést és szűrést végez, és elküldi az eredményeket egy alsóbb rétegbeli riasztási rendszernek, a **AlertOutput**.

Vegye figyelembe, hogy a közös tábla kifejezések (például **a with** utasítások) eredményeit több kimeneti utasításban is felhasználhatja a közös táblakifejezés. Ez a lehetőség a kevesebb olvasónak a bemeneti forráshoz való megnyitásának előnyeit adja.

Példa: 

```SQL
    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'
```

## <a name="query-example-count-unique-values"></a>Példa lekérdezésre: Egyedi értékek megszámlálása

**Leírás**: Megszámolja az adatfolyamban megjelenő egyedi mezőértékek számát az adott időtartományon belül. Például hány egyedi módon haladtak át az autók az autópályadíj-kezelőn keresztül egy 2 másodperces ablakban?

**Bemenet**:

| Fordítás | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**A kimenetre:**

| CountMake | Time |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**Megoldás**

```SQL
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP() AS AsaTIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```


**Magyarázat:** 
a**Count (DISTINCT make)** függvény a **make** oszlopban lévő különböző értékek számát adja vissza egy időablakon belül.

## <a name="query-example-determine-if-a-value-has-changed"></a>Példa lekérdezésre: Annak megállapítása, hogy egy érték módosult-e

**Leírás**: Egy korábbi érték megkeresésével megállapíthatja, hogy az a jelenlegi értéktől eltérő-e. Például az előző autó az útdíjas úton ugyanaz, mint az aktuális autó?

**Bemenet**:

| Fordítás | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Kimenet**:

| Fordítás | Time |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Megoldás**:

```SQL
    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

**Magyarázat**: A **lag** használatával betekintést nyerhet a bemeneti streambe egy eseményre, és lekérheti a **make** értéket. Ezután hasonlítsa össze a **make** értékkel az aktuális eseményen, és adja meg az eseményt, ha azok eltérnek.

## <a name="query-example-find-the-first-event-in-a-window"></a>Példa lekérdezésre: Az első esemény megkeresése egy ablakban

**Leírás**: Az első autó megkeresése 10 percenként.

**Bemenet**:

| LicensePlate | Fordítás | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Alátámaszt |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Kimenet**:

| LicensePlate | Fordítás | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |

**Megoldás**:

```SQL
    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1
```

Most változtassa meg a problémát, és keresse meg az első autóját 10 percenként.

| LicensePlate | Fordítás | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Alátámaszt |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Megoldás**:

```SQL
    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

## <a name="query-example-find-the-last-event-in-a-window"></a>Példa lekérdezésre: Az utolsó esemény megkeresése egy ablakban

**Leírás**: Az utolsó autó megkeresése 10 percenként.

**Bemenet**:

| LicensePlate | Fordítás | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Alátámaszt |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Kimenet**:

| LicensePlate | Fordítás | Time |
| --- | --- | --- |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Megoldás**:

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
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime
```

**Magyarázat**: A lekérdezés két lépésből áll. Az első megkeresi a legutóbbi időbélyeget 10 perces Windows rendszerben. A második lépés összekapcsolja az első lekérdezés eredményét az eredeti streamtel, hogy megkeresse az egyes ablakokban az utolsó időbélyegeknek megfelelő eseményeket. 

## <a name="query-example-locate-correlated-events-in-a-stream"></a>Példa lekérdezésre: Korrelált események keresése egy adatfolyamban

**Leírás**: Korrelált események keresése egy adatfolyamban. Lehet például, hogy két egymást követő, azonos gyártmányú autót adtak meg az utolsó 90 másodpercen belül?

**Bemenet**:

| Fordítás | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Kimenet**:

| Fordítás | Time | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**Megoldás**:

```SQL
    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

**Magyarázat**: A **lag** használatával betekintést nyerhet a bemeneti streambe egy eseményre, és lekérheti a **make** értéket. Hasonlítsa össze a **make** értékkel az aktuális eseményben, majd adja ki az eseményt, ha azok megegyeznek. A **lag** használatával is lekérheti az előző autóval kapcsolatos információkat.

## <a name="query-example-detect-the-duration-between-events"></a>Példa lekérdezésre: Az események közötti időtartam észlelése

**Leírás**: Egy adott esemény időtartamának megkeresése. Ha például egy webes kattintássorozat adott meg, határozza meg a szolgáltatásra fordított időt.

**Bemenet**:  

| Felhasználó | Funkció | Esemény | Time |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Start |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |vége |2015-01-01T00:00:08.0000000Z |

**Kimenet**:  

| Felhasználó | Funkció | Duration |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Megoldás**:

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

**Magyarázat**: Az **utolsó** függvény használatával kérheti le az utolsó **időértéket** az esemény típusának elindulásakor. Az **utolsó** függvény a **[user] partíciót** használja annak jelzésére, hogy a rendszer egyedi felhasználó alapján számítja ki az eredményt. A lekérdezés az **indítási** és a leállítási események közötti időeltéréshez 1 órás  maximális küszöbértéket tartalmaz, de igény szerint konfigurálható **(korlát időtartama (óra, 1)** .

## <a name="query-example-detect-the-duration-of-a-condition"></a>Példa lekérdezésre: Feltétel időtartamának észlelése
**Leírás**: Megtudhatja, hogy mennyi ideig történt a feltétel.
Tegyük fel például, hogy egy hiba az összes olyan autót eredményezett, amely nem megfelelő súlyozású (20 000 kilós), a hiba időtartamát pedig számításba kell venni.

**Bemenet**:

| Fordítás | Time | Tömeg |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |2000 |
| Toyota |2015-01-01T00:00:02.0000000Z |25000 |
| Honda |2015-01-01T00:00:03.0000000Z |26000 |
| Toyota |2015-01-01T00:00:04.0000000Z |25000 |
| Honda |2015-01-01T00:00:05.0000000Z |26000 |
| Toyota |2015-01-01T00:00:06.0000000Z |25000 |
| Honda |2015-01-01T00:00:07.0000000Z |26000 |
| Toyota |2015-01-01T00:00:08.0000000Z |2000 |

**Kimenet**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**Megoldás**:

```SQL
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
```

**Magyarázat**: A **késés** használatával 24 órán át tekintheti meg a bemeneti streamet, és megkeresheti azokat a példányokat, amelyeken a **StartFault** és a **StopFault** súlyozása < 20000.

## <a name="query-example-fill-missing-values"></a>Példa lekérdezésre: Hiányzó értékek kitöltése

**Leírás**: A hiányzó értékekkel rendelkező események adatfolyama rendszeres időközönként hozza létre az események streamjét. Például állítson elő 5 másodpercenként egy eseményt, amely a legutóbb látott adatpontot jelenti.

**Bemenet**:

| t | value |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Kimenet (első 10 sor)** :

| windowend | lastevent. t | lastevent. Value |
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

**Megoldás**:

```SQL
    SELECT
        System.Timestamp() AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)
```

**Magyarázat**: A lekérdezés 5 másodpercenként hoz létre eseményeket, és a korábban kapott utolsó eseményt adja eredményül. A [kiugró ablak](/stream-analytics-query/hopping-window-azure-stream-analytics) időtartama határozza meg, hogy a lekérdezés milyen mértékben keresi meg a legújabb eseményt (ebben a példában a 300 másodpercet).


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>Példa lekérdezésre: Két eseménytípus korrelációja ugyanazon az adatfolyamon belül

**Leírás**: Időnként a riasztásokat több olyan eseménytípus alapján kell létrehozni, amelyek egy adott időtartományban fordultak elő. Például a IoT-forgatókönyvek esetében, ha a ventilátor hőmérséklete 40-nál kisebb, és a maximális teljesítmény az elmúlt 3 percnél kevesebb, mint 10, akkor riasztást kell generálni.

**Bemenet**:

| time | deviceId | sensorName | value |
| --- | --- | --- | --- |
| "2018-01-01T16:01:00" | "Oven1" | ideiglenes |120 |
| "2018-01-01T16:01:00" | "Oven1" | Power |15 |
| "2018-01-01T16:02:00" | "Oven1" | ideiglenes |100 |
| "2018-01-01T16:02:00" | "Oven1" | Power |15 |
| "2018-01-01T16:03:00" | "Oven1" | ideiglenes |70 |
| "2018-01-01T16:03:00" | "Oven1" | Power |15 |
| "2018-01-01T16:04:00" | "Oven1" | ideiglenes |50 |
| "2018-01-01T16:04:00" | "Oven1" | Power |15 |
| "2018-01-01T16:05:00" | "Oven1" | ideiglenes |30 |
| "2018-01-01T16:05:00" | "Oven1" | Power |8 |
| "2018-01-01T16:06:00" | "Oven1" | ideiglenes |20 |
| "2018-01-01T16:06:00" | "Oven1" | Power |8 |
| "2018-01-01T16:07:00" | "Oven1" | ideiglenes |20 |
| "2018-01-01T16:07:00" | "Oven1" | Power |8 |
| "2018-01-01T16:08:00" | "Oven1" | ideiglenes |20 |
| "2018-01-01T16:08:00" | "Oven1" | Power |8 |

**Kimenet**:

| eventTime | deviceId | ideiglenes | alertMessage | maxPowerDuringLast3mins |
| --- | --- | --- | --- | --- | 
| "2018-01-01T16:05:00" | "Oven1" |30 | "Rövidzárlat-fűtő elemek" |15 |
| "2018-01-01T16:06:00" | "Oven1" |20 | "Rövidzárlat-fűtő elemek" |15 |
| "2018-01-01T16:07:00" | "Oven1" |20 | "Rövidzárlat-fűtő elemek" |15 |

**Megoldás**:

```SQL
WITH max_power_during_last_3_mins AS (
    SELECT 
        System.TimeStamp() AS windowTime,
        deviceId,
        max(value) as maxPower
    FROM
        input TIMESTAMP BY t
    WHERE 
        sensorName = 'power' 
    GROUP BY 
        deviceId, 
        SlidingWindow(minute, 3) 
)

SELECT 
    t1.t AS eventTime,
    t1.deviceId, 
    t1.value AS temp,
    'Short circuit heating elements' as alertMessage,
    t2.maxPower AS maxPowerDuringLast3mins
    
INTO resultsr

FROM input t1 TIMESTAMP BY t
JOIN max_power_during_last_3_mins t2
    ON t1.deviceId = t2.deviceId 
    AND t1.t = t2.windowTime
    AND DATEDIFF(minute,t1,t2) between 0 and 3
    
WHERE
    t1.sensorName = 'temp'
    AND t1.value <= 40
    AND t2.maxPower > 10
```

**Magyarázat**: Az első lekérdezés `max_power_during_last_3_mins`a [csúszó ablak](/stream-analytics-query/sliding-window-azure-stream-analytics) használatával keresi meg az összes eszközhöz tartozó energiaellátási érzékelő maximális értékét az elmúlt 3 percben. A második lekérdezés az első lekérdezéshez csatlakozik, hogy megkeresse az aktuális eseményhez kapcsolódó legutóbbi ablakban található energiaellátási értéket. Ezután, ha a feltételek teljesülnek, a rendszer riasztást hoz létre az eszközhöz.

## <a name="query-example-process-events-independent-of-device-clock-skew-substreams"></a>Példa lekérdezésre: Események feldolgozása az eszköz órajelének eldöntése függetlenül (alstreamek)

**Leírás**: Az események az esemény-előállítók közötti óra döntése, illetve a partíciók közötti időeltérések vagy a hálózati késések közötti időeltérések miatt megérkeznek. A következő példában a 2. TollID eszköz órája öt másodperccel az 1. TollID mögött, a 3. TollID pedig 10 másodperccel az 1. TollID mögött. 

**Bemenet**:

| LicensePlate | Fordítás | Time | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |Toyota |2015-07-27T00:00:05.0000000Z | 1 |
| QYF 9358 |Honda |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |BMW |2015-07-27T00:00:04.0000000Z | 2 |
| VFE 1616 |Toyota |2015-07-27T00:00:10.0000000Z | 1 |
| RMV 8282 |Honda |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |BMW |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |Alátámaszt |2015-07-27T00:00:07.0000000Z | 3 |

**Kimenet**:

| TollID | Count |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Megoldás**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

**Magyarázat**: A [timestamp by over](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering) záradék az egyes eszközök idővonalait külön alstreamek használatával vizsgálja. A rendszer az egyes TollID kimeneti eseményeit számítja ki a számítások alapján, ami azt jelenti, hogy az események sorrendben jelennek meg az egyes TollID, ahelyett, hogy az összes eszköz ugyanazon az óráján volt.

## <a name="query-example-remove-duplicate-events-in-a-window"></a>Példa lekérdezésre: Ismétlődő események eltávolítása egy ablakban

**Leírás**: Ha olyan műveletet hajt végre, mint például az átlagok kiszámítása egy adott időintervallumban, az ismétlődő eseményeket szűrni kell. A következő példában a második esemény az első másolat.

**Bemenet**:  

| DeviceId | Time | Attribútum | Érték |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000Z |Hőmérséklet |50 |
| 1 |2018-07-27T00:00:01.0000000Z |Hőmérséklet |50 |
| 2 |2018-07-27T00:00:01.0000000Z |Hőmérséklet |40 |
| 1 |2018-07-27T00:00:05.0000000Z |Hőmérséklet |60 |
| 2 |2018-07-27T00:00:05.0000000Z |Hőmérséklet |50 |
| 1 |2018-07-27T00:00:10.0000000Z |Hőmérséklet |100 |

**Kimenet**:  

| AverageValue | DeviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Megoldás**:

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

**Magyarázat**: [Darabszám (eltérő idő)](/stream-analytics-query/count-azure-stream-analytics) : az időoszlopban lévő különböző értékek számát adja vissza az időtartományon belül. Ezután használhatja a lépés kimenetét az eszközök átlagának kiszámításához az ismétlődések elvetésével.

## <a name="geofencing-and-geospatial-queries"></a>Geokerítések és térinformatikai lekérdezések
A Azure Stream Analytics beépített térinformatikai funkciókat biztosít, amelyek olyan forgatókönyvek megvalósítására használhatók, mint például a flotta-felügyelet, a Ride Sharing, a csatlakoztatott autók és az eszközök nyomon követése. A térinformatikai adatmennyiség GeoJSON vagy WKT formátumban is betölthető az esemény-adatfolyam vagy a hivatkozási adatforrások részeként. További információkért tekintse meg a [geokerítések és térinformatikai összesítési forgatókönyveket Azure stream Analytics](geospatial-scenarios.md) cikkben.

## <a name="language-extensibility-through-javascript-and-c"></a>Nyelvi bővíthetőség a JavaScripten keresztül ésC#
Az Azure stream Ananlytics lekérdezési Langugae a JavaScriptben vagy C# nyelveken írt egyéni funkciókkal bővíthetők. További információért lásd a foolowing cikkeket:
* [Felhasználó által definiált JavaScript-függvények Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [JavaScript felhasználó által definiált összesítések Azure Stream Analytics](stream-analytics-javascript-user-defined-aggregates.md)
* [A .NET szabványú, felhasználó által definiált függvények fejlesztése Azure Stream Analytics Edge-feladatokhoz](stream-analytics-edge-csharp-udf-methods.md)

## <a name="get-help"></a>Segítségkérés

További segítségre van szüksége, próbálja meg [Azure Stream Analytics-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)


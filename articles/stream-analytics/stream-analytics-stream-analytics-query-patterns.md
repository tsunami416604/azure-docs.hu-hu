---
title: Gyakori lekérdezési minták az Azure Stream Analytics szolgáltatásban
description: Ez a cikk azt ismerteti, számos gyakori lekérdezési minták és a terveket, amelyeket az Azure Stream Analytics-feladatok lehetnek hasznosak.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: 9c9a5f219af0d474e1608f98595abe027b894117
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58001743"
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Példák a Stream Analytics-használat gyakori minták lekérdezése

## <a name="introduction"></a>Bevezetés
Lekérdezések az Azure Stream Analytics egy SQL-szerű lekérdezési nyelvet a van megadva. A nyelvi szerkezetek vannak dokumentálva az [Stream Analytics lekérdezési nyelv leírása](https://msdn.microsoft.com/library/azure/dn834998.aspx) útmutató. 

A lekérdezés tervezési fejezhető ki egyszerű csatlakoztatott logikai egy bemeneti stream esemény adatok áthelyezése egy másik kimeneti adattárba. Vagy akkor teheti meg gazdag minta egyező és a historikus elemzés összesítések kiszámításához a TollApp példa különböző időtartományok keresztül. Több bemenetből származó adatokat kapcsolhat össze streamelési események kombinálásához, és statikus referenciaadatok között kereshet az eseményértékek bővítéséhez. Is adatokat tudjon írni több kimenetek.

Ez a cikk számos gyakori lekérdezési-minták található, a valós életből vett példák alapján megoldásait ismerteti. Folyamatban lévő és továbbra is az új mintákat folyamatosan frissül.

## <a name="work-with-complex-data-types-in-json-and-avro"></a>Összetett adattípusok kezelése JSON és AVRO formátumban. 
Az Azure Stream Analytics támogatja az események feldolgozását a fürt megosztott kötetei szolgáltatás, a JSON-t és az Avro adatformátumok a célnyelven.
JSON és az Avro összetett típusok, például a beágyazott objektumok (rekord) vagy a tömb tartalmazhat. Ezek az összetett adattípusok használatához, tekintse meg a [JSON-elemzés és az AVRO-adatok](stream-analytics-parsing-json.md) cikk.


## <a name="query-example-convert-data-types"></a>Lekérdezés. példa: Az adattípusok konvertálása
**Leírás**: Meghatározza azokat a tulajdonságokat a bemeneti streamben.
Például az autó súly beállításhalmazokkal bemeneti Stream érkezik, és alakítható át kell **INT** végrehajtásához **SUM** üzembe.

**Bemeneti**:

| Fordítás | Time | Tömeg |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**Kimeneti**:

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

**MAGYARÁZAT**: Használja a **CAST** utasításban a **súly** mezőben adja meg a jeho datovému typu. A támogatott adattípusok listájának megtekintéséhez [adattípusok (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835065.aspx).

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Lekérdezés. példa: Használat Like/Not szeretne egyező minta
**Leírás**: Ellenőrizze, hogy az esemény egy mező értéke megegyezik-e egy bizonyos minta.
Ellenőrizze például, hogy az eredményt adja vissza, amely a kezdődhet és végződhet 9 licenc lemezeket.

**Bemeneti**:

| Fordítás | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Kimeneti**:

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

**MAGYARÁZAT**: Használata a **PÉLDÁUL** utasítással ellenőrizze a **LicensePlate** mező érték. Azt kell kezdődnie egy A, majd nulla vagy több karaktert bármely karakterlánccal rendelkezik, és majd végződhet egy 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Lekérdezés. példa: Adja meg a logika különböző esetek/érték (a CASE-utasítások)
**Leírás**: Adja meg a különböző számítási mező, egy adott feltétel alapján.
Adjon meg például átadott karakterlánc leírását az hány autók győződjön meg arról, és egy különleges esetben az 1.

**Bemeneti**:

| Fordítás | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Kimeneti**:

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
        System.TimeStamp AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
```

**MAGYARÁZAT**: A **eset** kifejezés egyszerű kifejezések meghatározni az eredmény számára egy egy kifejezés hasonlítja össze. Ebben a példában a jármű-számot ad vissza egy másik karakterláncot leírást jármű-számot 1-től eltérő lehetővé teszi, mint 1 lehetővé teszi. 

## <a name="query-example-send-data-to-multiple-outputs"></a>Lekérdezés. példa: Több kimeneti adatok küldése
**Leírás**: Adatokat küldeni a kimenetet több célnak egyetlen feladat.
Például egy küszöbértéken alapuló riasztás adatok elemzése, és archiválja a blob storage-ba történő összes eseményt.

**Bemeneti**:

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

| Fordítás | Time | Darabszám |
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
        System.TimeStamp AS Time,
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

**MAGYARÁZAT**: A **INTO** záradékban arra utasítja a Stream Analytics ezen utasítás az adatok írása a kimenetek közül melyik.
Az első lekérdezés egy átmenő nevű kimenetre fogadott adatok **ArchiveOutput**.
A második lekérdezés nem néhány egyszerű összesítés és a szűrést, és azt egy alsóbb rétegbeli riasztási rendszer elküldi az eredményeket.

Vegye figyelembe, hogy a közös táblakifejezésekben (amely) eredményeit is felhasználhatja (például **WITH** utasítások) több kimeneti utasításokban. Ezzel a beállítással adhat kevesebb olvasók számára a bemeneti forrás a további előnnyel rendelkezik.
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

## <a name="query-example-count-unique-values"></a>Lekérdezés. példa: Egyedi értékek száma
**Leírás**: Az adatfolyam egy időtartományon belül egyedi mező értékeinek számát.
Például hogy hány egyedi lesz átadni a nem ingyenes érzékelőadatainak 2 másodperces ablakban autó?

**Bemeneti**:

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

**Megoldás:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP AS TIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```


**Magyarázat:**
**COUNT (DISTINCT teszi)** eltérő értékeket számát adja vissza a **győződjön meg arról,** oszlop egy időtartományon belül.

## <a name="query-example-determine-if-a-value-has-changed"></a>Lekérdezés. példa: Határozza meg, ha a egy értéke megváltozott-e.
**Leírás**: Tekintse meg a állapítsa meg, hogy a jelenlegi érték eltér az előző érték.
Például az az előző autó a nem ingyenes útközben, az aktuális autó azonos tétele?

**Bemeneti**:

| Fordítás | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Kimeneti**:

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

**MAGYARÁZAT**: Használat **LAG** és betekintés az adatfolyam-egy eseményhez vissza a **győződjön meg arról,** értéket. Ezután hasonlítsa össze, hogy a **győződjön meg arról,** érték az aktuális esemény, és a kimeneti esemény, ha ezek eltérnek.

## <a name="query-example-find-the-first-event-in-a-window"></a>Lekérdezés. példa: Keresse meg az első esemény ablakban
**Leírás**: Az első autó talál minden 10 perces időközt.

**Bemeneti**:

| LicensePlate | Fordítás | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Alátámaszt |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Kimeneti**:

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

Most hozzunk módosítsa a problémát, és keresse meg az első autó egy adott márka, minden 10 perces időközt.

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

## <a name="query-example-find-the-last-event-in-a-window"></a>Lekérdezés. példa: Keresse meg az utolsó esemény ablakban
**Leírás**: Az utolsó autó talál minden 10 perces időközt.

**Bemeneti**:

| LicensePlate | Fordítás | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Alátámaszt |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Kimeneti**:

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

**MAGYARÁZAT**: A lekérdezés két lépésből áll. Az elsőt a legújabb időbélyeg megkeresi a windows 10 perc. A második lépésben csatlakoztatja az eredeti adatfolyam az egyes ablakban az utolsó időbélyegek megfelelő események az első lekérdezés eredményeit. 

## <a name="query-example-detect-the-absence-of-events"></a>Lekérdezés. példa: Események hiányának észlelése
**Leírás**: Ellenőrizze, hogy a stream nem rendelkezik értékkel, amely megfelel egy bizonyos feltételnek.
Például 2 egymást követő ábrázolja az azonos ügyeljen a megadott díjmentesen utazás az utolsó 90 másodpercen belül?

**Bemeneti**:

| Fordítás | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Kimeneti**:

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

**MAGYARÁZAT**: Használat **LAG** és betekintés az adatfolyam-egy eseményhez vissza a **győződjön meg arról,** értéket. Hasonlítsa össze, hogy a **győződjön meg arról,** az aktuális esemény az értéket, és ezután kimeneti az eseményt, ha azok, azonos. Is **LAG** az előző autó kapcsolatos adatok eléréséhez.

## <a name="query-example-detect-the-duration-between-events"></a>Lekérdezés. példa: Az események közötti időtartam észlelése
**Leírás**: Keresse meg az adott esemény időtartama. Adja meg a webes kattintássorozat, például határozza meg, a szolgáltatás töltött időt.

**Bemeneti**:  

| Felhasználó | Szolgáltatás | Esemény | Time |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Indítás |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |Vége |2015-01-01T00:00:08.0000000Z |

**Kimeneti**:  

| Felhasználó | Szolgáltatás | Időtartam |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Megoldás**:

```SQL
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
```

**MAGYARÁZAT**: Használja a **utolsó** funkció lekéréséhez az utolsó **idő** értéket, ha az esemény típusa **Start**. A **utolsó** függvény **PARTITION BY [felhasználó]** jelzi, hogy az eredmény számított egyedi felhasználónként. A lekérdezés tartalmaz egy 1 óra közötti maximális küszöbértéke **Start** és **leállítása** események, igény szerint konfigurálható, de **(korlát DURATION(hour, 1)**.

## <a name="query-example-detect-the-duration-of-a-condition"></a>Lekérdezés. példa: Egy feltétel időtartama észlelése
**Leírás**: Keresse meg a feltétel történt mennyi ideig ki.
Tegyük fel például, hogy hibát eredményezett a egy helytelen súly (fent 20 000 font) rendelkező összes autók, és ki kell számítani a bejelentett hiba időtartama.

**Bemeneti**:

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

**Kimeneti**:

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

**MAGYARÁZAT**: Használja **LAG** bemeneti stream megtekintése 24 órán keresztül, és keresse meg where példányok **StartFault** és **StopFault** a súly < 20000 által felölelt vannak.

## <a name="query-example-fill-missing-values"></a>Lekérdezés. példa: Adja meg a hiányzó értékek
**Leírás**: Az események, amelyekből értékek hiányoznak, az eredmény egy adatfolyam rendszeres időközönként események.
Például hozzon létre egy eseményt 5 másodpercenként, amely a legutóbb észlelt adatpont jelentést.

**Bemeneti**:

| t | érték |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Kimenet (első 10 sort)**:

| windowend | lastevent.t | lastevent.Value |
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
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)
```

**MAGYARÁZAT**: Ez a lekérdezés 5 másodpercentként eseményeket hoz létre, és kiírja a korábban kapott utolsó esemény. A [Hopping ablak](https://msdn.microsoft.com/library/dn835041.aspx "Hopping ablak--az Azure Stream Analytics") időtartamát határozza meg, milyen vissza a lekérdezést úgy tűnik, keresse meg az utolsó esemény (ebben a példában 300 másodperc).


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>Lekérdezés. példa: Vesse össze az azonos streamen belül két esemény típusa
**Leírás**: Néha riasztások kell létrehozni egy bizonyos időtartományban történt több eseménytípus alapján.
Például egy IoT-forgatókönyvet az otthoni sütőben, a riasztást kell lennie jönnek létre, ha ventilátor hőmérséklet kevesebb, mint 40, és a teljesítmény az elmúlt 3 perc alatt kevesebb mint 10.

**Bemeneti**:

| time | deviceId | sensorName | érték |
| --- | --- | --- | --- |
| "2018-01-01T16:01:00" | "Oven1" | "temp" |120 |
| "2018-01-01T16:01:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:02:00" | "Oven1" | "temp" |100 |
| "2018-01-01T16:02:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:03:00" | "Oven1" | "temp" |70 |
| "2018-01-01T16:03:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:04:00" | "Oven1" | "temp" |50 |
| "2018-01-01T16:04:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:05:00" | "Oven1" | "temp" |30 |
| "2018-01-01T16:05:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:06:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:06:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:07:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:07:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:08:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:08:00" | "Oven1" | "power" |8 |

**Kimeneti**:

| eventTime | deviceId | TEMP | alertMessage | maxPowerDuringLast3mins |
| --- | --- | --- | --- | --- | 
| "2018-01-01T16:05:00" | "Oven1" |30 | "Rövid kapcsolatcsoport fűtés elemet" |15 |
| "2018-01-01T16:06:00" | "Oven1" |20 | "Rövid kapcsolatcsoport fűtés elemet" |15 |
| "2018-01-01T16:07:00" | "Oven1" |20 | "Rövid kapcsolatcsoport fűtés elemet" |15 |

**Megoldás**:

```SQL
WITH max_power_during_last_3_mins AS (
    SELECT 
        System.TimeStamp AS windowTime,
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

**MAGYARÁZAT**: Az első lekérdezés `max_power_during_last_3_mins`, használja a [csúszó ablak](https://msdn.microsoft.com/azure/stream-analytics/reference/sliding-window-azure-stream-analytics) található minden eszközhöz a power érzékelő maximális értéke az elmúlt 3 perc alatt. A második lekérdezés az első lekérdezés a kiemelt értéket keresi a legújabb ablakban megfelelő az aktuális esemény csatlakozik. És ezt követően a megadott feltételek teljesülnek, egy riasztás akkor jön létre az eszköz.

## <a name="query-example-process-events-independent-of-device-clock-skew-substreams"></a>Lekérdezés. példa: Független eszköz óra tevékenységdiagramon (részadatfolyamok) az események feldolgozása
**Leírás**: Esemény késői is érkezik, vagy üzemen kívüli esemény gyártók között eltérései miatti, óra döntése partíciók vagy a hálózati késés között. A következő példában az eszköz óra TollID 2 mögött TollID 1 eltelő 10 másodpercben, és az eszköz óra TollID 3 öt másodpercenként TollID 1 mögött. 


**Bemeneti**:

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

**Kimeneti**:

| TollID | Darabszám |
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

**MAGYARÁZAT**: A [TIMESTAMP BY OVER](https://msdn.microsoft.com/azure/stream-analytics/reference/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering) záradék minden egyes eszköz ütemtervet külön részadatfolyamok megvizsgál. A kimeneti eseményekben a minden egyes TollID jönnek létre, azok számítja ki, ami azt jelenti, hogy az események minden egyes TollID helyett, mintha minden eszköz az azonos óra alatt átrendezésekor megállapodást sorrendben vannak.

## <a name="query-example-remove-duplicate-events-in-a-window"></a>Lekérdezés. példa: Távolítsa el az ismétlődő események ablakban
**Leírás**: Átlag kiszámítása egy adott időtartományban események például egy művelet végrehajtása, ha ismétlődő események szűri.

**Bemeneti**:  

| Eszközazonosító | Time | Attribútum | Érték |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000Z |Hőmérséklet |50 |
| 1 |2018-07-27T00:00:01.0000000Z |Hőmérséklet |50 |
| 2 |2018-07-27T00:00:01.0000000Z |Hőmérséklet |40 |
| 1 |2018-07-27T00:00:05.0000000Z |Hőmérséklet |60 |
| 2 |2018-07-27T00:00:05.0000000Z |Hőmérséklet |50 |
| 1 |2018-07-27T00:00:10.0000000Z |Hőmérséklet |100 |

**Kimeneti**:  

| AverageValue | Eszközazonosító |
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
        SYSTEM.TIMESTAMP
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**MAGYARÁZAT**: [COUNT (DISTINCT idő)](https://docs.microsoft.com/stream-analytics-query/count-azure-stream-analytics) egyedi érték számát adja vissza a Time oszlopban egy időtartományon belül. Ezután használhatja ezt a lépést kimenete az átlag kiszámításakor eszközönként ismétlődések elvetésével.

## <a name="get-help"></a>Segítségkérés
További segítségre van szüksége, próbálja meg [Azure Stream Analytics-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)


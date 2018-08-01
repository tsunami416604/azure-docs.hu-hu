---
title: Gyakori lekérdezési minták az Azure Stream Analytics szolgáltatásban
description: Ez a cikk azt ismerteti, számos gyakori lekérdezési minták és a terveket, amelyeket az Azure Stream Analytics-feladatok lehetnek hasznosak.
services: stream-analytics
author: jseb225
manager: kfile
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: 4a37ac6cdf5d13556c6a3e293c19c00ad36e4495
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364165"
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Példák a Stream Analytics-használat gyakori minták lekérdezése

## <a name="introduction"></a>Bevezetés
Lekérdezések az Azure Stream Analytics egy SQL-szerű lekérdezési nyelvet a van megadva. A nyelvi szerkezetek vannak dokumentálva az [Stream Analytics lekérdezési nyelv leírása](https://msdn.microsoft.com/library/azure/dn834998.aspx) útmutató. 

A lekérdezés tervezési fejezhető ki egyszerű csatlakoztatott logikai egy bemeneti stream esemény adatok áthelyezése egy másik kimeneti adattárba. Vagy akkor teheti meg gazdag minta egyező és a historikus elemzés összesítések kiszámításához a TollApp példa különböző időtartományok keresztül. Csatlakozhat a streamelési események összevonása, és végezze el a statikus referenciaadatokra vonatkoznak az esemény értékek feldúsítani elleni keresések több bemenet adatait. Is adatokat tudjon írni több kimenetek.

Ez a cikk számos gyakori lekérdezési-minták található, a valós életből vett példák alapján megoldásait ismerteti. Folyamatban lévő és továbbra is az új mintákat folyamatosan frissül.

## <a name="work-with-complex-data-types-in-json-and-avro"></a>A JSON-t és az AVRO összetett adattípusok használata 
Az Azure Stream Analytics támogatja az események feldolgozását a fürt megosztott kötetei szolgáltatás, a JSON-t és az Avro adatformátumok a célnyelven.
JSON és az Avro összetett típusok, például a beágyazott objektumok (rekord) vagy a tömb tartalmazhat. Ezek az összetett adattípusok használatához, tekintse meg a [elmagyarázza, hogyan működnek az összetett adattípusok a JSON és az AVRO MSDN-lapján](
https://msdn.microsoft.com/azure/stream-analytics/reference/complex-data-types-stream-analytics).


## <a name="query-example-convert-data-types"></a>Példa: adattípus átalakítása
**Leírás**: meghatározza azokat a tulajdonságokat a bemeneti streamben.
Például az autó súly beállításhalmazokkal bemeneti Stream érkezik, és alakítható át kell **INT** végrehajtásához **SUM** üzembe.

**Bemeneti**:

| Győződjön meg arról | Time | Tömeg |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**Kimeneti**:

| Győződjön meg arról | Tömeg |
| --- | --- |
| Honda |3000 |

**Megoldás**:

    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**MAGYARÁZAT**: használatát egy **CAST** utasításban a **súly** mezőben adja meg a jeho datovému typu. A támogatott adattípusok listájának megtekintéséhez [adattípusok (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835065.aspx).

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Példa: használata hasonló/nem szeretne egyező minta
**Leírás**: Ellenőrizze, hogy egy mező értéke meg az esemény megfelel-e egy bizonyos minta.
Ellenőrizze például, hogy az eredményt adja vissza, amely a kezdődhet és végződhet 9 licenc lemezeket.

**Bemeneti**:

| Győződjön meg arról | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Kimeneti**:

| Győződjön meg arról | LicensePlate | Time |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Megoldás**:

    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'

**MAGYARÁZAT**: használja a **PÉLDÁUL** utasítás ellenőrizheti a **LicensePlate** mező érték. Azt kell kezdődnie egy A, majd nulla vagy több karaktert bármely karakterlánccal rendelkezik, és majd végződhet egy 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Példa: Adja meg a logika különböző esetek/érték (a CASE-utasítások)
**Leírás**: Adja meg a különböző számítási mező, egy adott feltétel alapján.
Adjon meg például átadott karakterlánc leírását az hány autók győződjön meg arról, és egy különleges esetben az 1.

**Bemeneti**:

| Győződjön meg arról | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Kimeneti**:

| CarsPassed | Time |
| --- | --- | --- |
| 1 Honda |2015-01-01T00:00:10.0000000Z |
| 2 Toyotas |2015-01-01T00:00:10.0000000Z |

**Megoldás**:

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

**MAGYARÁZAT**: A **eset** kifejezés egyszerű kifejezések meghatározni az eredmény számára egy egy kifejezés hasonlítja össze. Ebben a példában a jármű-számot ad vissza egy másik karakterláncot leírást jármű-számot 1-től eltérő lehetővé teszi, mint 1 lehetővé teszi. 

## <a name="query-example-send-data-to-multiple-outputs"></a>Példa: több kimeneti adatok küldése
**Leírás**: adatokat küldeni a kimenetet több célnak egyetlen feladat.
Például egy küszöbértéken alapuló riasztás adatok elemzése, és archiválja a blob storage-ba történő összes eseményt.

**Bemeneti**:

| Győződjön meg arról | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output1**:

| Győződjön meg arról | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output2**:

| Győződjön meg arról | Time | Darabszám |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000Z |3 |

**Megoldás**:

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

**MAGYARÁZAT**: A **INTO** záradékban arra utasítja a Stream Analytics ezen utasítás az adatok írása a kimenetek közül melyik.
Az első lekérdezés egy átmenő nevű kimenetre fogadott adatok **ArchiveOutput**.
A második lekérdezés nem néhány egyszerű összesítés és a szűrést, és azt egy alsóbb rétegbeli riasztási rendszer elküldi az eredményeket.

Vegye figyelembe, hogy a közös táblakifejezésekben (amely) eredményeit is felhasználhatja (például **WITH** utasítások) több kimeneti utasításokban. Ezzel a beállítással adhat kevesebb olvasók számára a bemeneti forrás a további előnnyel rendelkezik.
Példa: 

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

## <a name="query-example-count-unique-values"></a>Példa: egyedi értékek száma
**Leírás**: az adatfolyam egy időtartományon belül egyedi mező értékeinek számát.
Például hogy hány egyedi lesz átadni a nem ingyenes érzékelőadatainak 2 másodperces ablakban autó?

**Bemeneti**:

| Győződjön meg arról | Time |
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

````
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP AS TIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
````


**Magyarázat:**
**COUNT (DISTINCT teszi)** eltérő értékeket számát adja vissza a **győződjön meg arról,** oszlop egy időtartományon belül.

## <a name="query-example-determine-if-a-value-has-changed"></a>Példa: határozza meg, ha a egy értéke megváltozott-e.
**Leírás**: nézze meg állapítsa meg, hogy a jelenlegi érték eltér az előző érték.
Például az az előző autó a nem ingyenes útközben, az aktuális autó azonos tétele?

**Bemeneti**:

| Győződjön meg arról | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Kimeneti**:

| Győződjön meg arról | Time |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Megoldás**:

    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**MAGYARÁZAT**: használata **LAG** és betekintés az adatfolyam-egy eseményhez vissza a **győződjön meg arról,** értéket. Ezután hasonlítsa össze, hogy a **győződjön meg arról,** érték az aktuális esemény, és a kimeneti esemény, ha ezek eltérnek.

## <a name="query-example-find-the-first-event-in-a-window"></a>Példa: egy ablakban keresse meg az első esemény
**Leírás**: az első autó található minden 10 perces időközt.

**Bemeneti**:

| LicensePlate | Győződjön meg arról | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Alátámaszt |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Kimeneti**:

| LicensePlate | Győződjön meg arról | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |

**Megoldás**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1

Most hozzunk módosítsa a problémát, és keresse meg az első autó egy adott márka, minden 10 perces időközt.

| LicensePlate | Győződjön meg arról | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Alátámaszt |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Megoldás**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## <a name="query-example-find-the-last-event-in-a-window"></a>Példa: egy ablakban keresse meg az utolsó esemény
**Leírás**: a legutóbbi autó található minden 10 perces időközt.

**Bemeneti**:

| LicensePlate | Győződjön meg arról | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Alátámaszt |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Kimeneti**:

| LicensePlate | Győződjön meg arról | Time |
| --- | --- | --- |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Megoldás**:

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

**MAGYARÁZAT**: még két lépésre van a lekérdezésben. Az elsőt a legújabb időbélyeg megkeresi a windows 10 perc. A második lépésben csatlakoztatja az eredeti adatfolyam az egyes ablakban az utolsó időbélyegek megfelelő események az első lekérdezés eredményeit. 

## <a name="query-example-detect-the-absence-of-events"></a>A példában a lekérdezés: események hiányának észlelése
**Leírás**: Ellenőrizze, hogy a stream nem rendelkezik értékkel, amely megfelel egy bizonyos feltételnek.
Például 2 egymást követő ábrázolja az azonos ügyeljen a megadott díjmentesen utazás az utolsó 90 másodpercen belül?

**Bemeneti**:

| Győződjön meg arról | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Kimeneti**:

| Győződjön meg arról | Time | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**Megoldás**:

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

**MAGYARÁZAT**: használata **LAG** és betekintés az adatfolyam-egy eseményhez vissza a **győződjön meg arról,** értéket. Hasonlítsa össze, hogy a **győződjön meg arról,** az aktuális esemény az értéket, és ezután kimeneti az eseményt, ha azok, azonos. Is **LAG** az előző autó kapcsolatos adatok eléréséhez.

## <a name="query-example-detect-the-duration-between-events"></a>A példában a lekérdezés: események közötti időtartamot fogja észlelése
**Leírás**: keresse meg az adott esemény időtartama. Adja meg a webes kattintássorozat, például határozza meg, a szolgáltatás töltött időt.

**Bemeneti**:  

| Felhasználó | Szolgáltatás | Esemény | Time |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Indítás |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |Befejezés |2015-01-01T00:00:08.0000000Z |

**Kimeneti**:  

| Felhasználó | Szolgáltatás | Időtartam |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Megoldás**:

````
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
````

**MAGYARÁZAT**: használja a **utolsó** funkció lekéréséhez az utolsó **idő** értéket, ha az esemény típusa **Start**. A **utolsó** függvény **PARTITION BY [felhasználó]** jelzi, hogy az eredmény számított egyedi felhasználónként. A lekérdezés tartalmaz egy 1 óra közötti maximális küszöbértéke **Start** és **leállítása** események, igény szerint konfigurálható, de **(korlát DURATION(hour, 1)**.

## <a name="query-example-detect-the-duration-of-a-condition"></a>Példa: egy feltétel időtartama észlelése
**Leírás**: keresse meg a feltétel történt mennyi ideig ki.
Tegyük fel például, hogy hibát eredményezett a egy helytelen súly (fent 20 000 font) rendelkező összes autók, és ki kell számítani a bejelentett hiba időtartama.

**Bemeneti**:

| Győződjön meg arról | Time | Tömeg |
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

````
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
````

**MAGYARÁZAT**: használata **LAG** bemeneti stream megtekintése 24 órán keresztül, és keresse meg where példányok **StartFault** és **StopFault** a súlyozás szerint vannak ölel < 20000.

## <a name="query-example-fill-missing-values"></a>Példa: Adja meg a hiányzó értékek
**Leírás**: események, amelyekből értékek hiányoznak a Stream készít rendszeres időközönként események adatfolyam.
Például hozzon létre egy eseményt 5 másodpercenként, amely a legutóbb észlelt adatpont jelentést.

**Bemeneti**:

| T | érték |
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

    SELECT
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**MAGYARÁZAT**: Ez a lekérdezés 5 másodpercentként eseményeket hoz létre, és kiírja a korábban kapott utolsó esemény. A [Hopping ablak](https://msdn.microsoft.com/library/dn835041.aspx "Hopping ablak--az Azure Stream Analytics") időtartamát határozza meg, milyen vissza a lekérdezést úgy tűnik, keresse meg az utolsó esemény (ebben a példában 300 másodperc).


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>Példa: korrelációját, ha ugyanazt a streamen belül két esemény típusa
**Leírás**: riasztások igények hozható létre, amely egy bizonyos időtartományban történt több eseménytípus alapján.
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

````
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
````

**MAGYARÁZAT**: az első lekérdezés `max_power_during_last_3_mins`, használja a [csúszó ablak](https://msdn.microsoft.com/azure/stream-analytics/reference/sliding-window-azure-stream-analytics) található minden eszközhöz a power érzékelő maximális értéke az elmúlt 3 perc alatt. A második lekérdezés az első lekérdezés a kiemelt értéket keresi a legújabb ablakban megfelelő az aktuális esemény csatlakozik. És ezt követően a megadott feltételek teljesülnek, egy riasztás akkor jön létre az eszköz.

## <a name="query-example-process-events-independent-of-device-clock-skew-substreams"></a>Példa: független eszköz óra tevékenységdiagramon (részadatfolyamok) az események feldolgozása
**Leírás**: esemény késői is érkezik, vagy közötti eseménykészítőkből eltérései miatti sorrendje, nem az óra partíciók és a hálózati késés közötti döntése. A következő példában az eszköz óra TollID 2 mögött TollID 1 eltelő 10 másodpercben, és az eszköz óra TollID 3 öt másodpercenként TollID 1 mögött. 


**Bemeneti**:
| LicensePlate | Győződjön meg arról | Time | TollID |
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

````
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId

````

**MAGYARÁZAT**: A [TIMESTAMP BY OVER](https://msdn.microsoft.com/azure/stream-analytics/reference/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering) záradék minden egyes eszköz ütemtervet külön részadatfolyamok megvizsgál. A kimeneti eseményekben a minden egyes TollID jönnek létre, azok számítja ki, ami azt jelenti, hogy az események minden egyes TollID helyett, mintha minden eszköz az azonos óra alatt átrendezésekor megállapodást sorrendben vannak.


## <a name="get-help"></a>Segítségkérés
További segítségre van szüksége, próbálja meg [Azure Stream Analytics-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)


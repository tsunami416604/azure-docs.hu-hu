---
title: "Példa a gyakori használati minták a Stream Analytics lekérdezési |} Microsoft Docs"
description: "Gyakori Azure Stream Analytics lekérdezési minták"
keywords: "lekérdezés példák"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jenniehubbard
editor: cgronlun
ms.assetid: 6b9a7d00-fbcc-42f6-9cbb-8bbf0bbd3d0e
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/08/2017
ms.author: samacha
ms.openlocfilehash: cb0a948416983f33a4ca8d9211a3a114ba011685
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Példa a gyakori Stream Analytics használati minták lekérdezése
## <a name="introduction"></a>Bevezetés
Lekérdezések Azure Stream Analytics lekérdezési SQL-szerű nyelven van kifejezve. Ezeket a lekérdezéseket a rendszer részletes ismertetését lásd: a [Stream Analytics lekérdezési nyelvi referencia](https://msdn.microsoft.com/library/azure/dn834998.aspx) útmutató. Ez a cikk több közös lekérdezési kifejezések valós forgatókönyv alapuló megoldásokat ismerteti. Folyamatban lévő és továbbra is új mintákat folyamatosan frissül.

## <a name="query-example-convert-data-types"></a>Példa: adattípusok átalakítása
**Leírás**: meghatározza azokat a tulajdonságokat a bemeneti adatfolyam.
Például a car karakterláncként. a bemeneti adatfolyam hamarosan és alakítható át kell **INT** végrehajtásához **SUM** azt be.

**Bemeneti**:

| Ellenőrizze | Time | Súlyozás |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**Kimeneti**:

| Ellenőrizze | Súlyozás |
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

**MAGYARÁZAT**: használja a **TÍPUSKONVERZIÓ** utasítás a **súly** mezőben adja meg az adattípushoz. A támogatott adattípusok listájának [adattípusok (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835065.aspx).

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Példa: használata hasonló/nem szeretne egyező mintát
**Leírás**: Ellenőrizze, hogy egy mező értéke megtalálható az esemény megfelel-e egy bizonyos minta.
Ellenőrizze például, hogy az eredményt adja vissza, amely az A kezdődhet és végződhet 9 licenc lemezeket.

**Bemeneti**:

| Ellenőrizze | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Kimeneti**:

| Ellenőrizze | LicensePlate | Time |
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

**MAGYARÁZAT**: használja a **PÉLDÁUL** utasítás ellenőrizni a **LicensePlate** mező érték. Emellett kell egy A, indítsa el, majd tetszőleges karakterlánc nulla vagy több rendelkezik, és majd végződhet a 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Példa: megadása logikát a különböző esetekre és-értékek ("CASE" utasítás)
**Leírás**: Adjon meg egy másik számítási mező, egy adott feltétel alapján.
Például adja meg, hány autók azonos ellenőrizze karakterlánc leírását átadni, egy különleges esetben az 1.

**Bemeneti**:

| Ellenőrizze | Time |
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

**MAGYARÁZAT**: A **eset** záradék kiválaszthatjuk, hogy adjon meg egy másik számítási, bizonyos feltételeknek megfelelő (ebben az esetben a összesített ablakban autók száma).

## <a name="query-example-send-data-to-multiple-outputs"></a>Példa: adatokat küldeni a több kimenet
**Leírás**: adatokat küldeni a kimeneti több cél egyetlen feladat.
Például egy küszöbérték-alapú riasztás adatok elemzése, és archiválja a blob storage összes eseményeket.

**Bemeneti**:

| Ellenőrizze | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output1**:

| Ellenőrizze | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output2**:

| Ellenőrizze | Time | Darabszám |
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

**MAGYARÁZAT**: A **INTO** záradék közli a Stream Analytics mely a kimenetek ezen utasítás az adatokat írni.
Az első lekérdezés az adatok egy kimeneti, hogy mi érkezett csatlakoztatott **ArchiveOutput**.
A második lekérdezés nem néhány egyszerű összesítési és szűrés, és azt egy alsóbb rétegbeli riasztási rendszer történő küldése.

Vegye figyelembe, hogy a közös táblakifejezésekben (Táblakifejezés) eredményei is felhasználhatja (például **WITH** utasítások) több kimeneti utasításokban. Ez a beállítás a hozzáadott előnye, hogy a bemeneti forrás kevesebb olvasók megnyitása rendelkezik.
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
**Leírás**: az adatfolyam egy olyan időkeretet belül egyedi mező értékeinek száma.
Például hogy hány egyedi lesz továbbítja a 2-második ablakban a téren kiállítási autók?

**Bemeneti**:

| Ellenőrizze | Time |
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
**száma (különböző ellenőrizze)** különböző értékek számát adja vissza a **győződjön** belül egy olyan időkeretet oszlop.

## <a name="query-example-determine-if-a-value-has-changed"></a>Példa: határozza meg, hogy módosult-e egy értéket
**Leírás**: nézze meg állapítsa meg, hogy a jelenlegi érték eltér az előző értéket.
Például az az előző autó téren útközben, az aktuális autó azonos ellenőrizze?

**Bemeneti**:

| Ellenőrizze | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Kimeneti**:

| Ellenőrizze | Time |
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

**MAGYARÁZAT**: használata **LAG** , és a bemeneti adatfolyam egy esemény vissza való betekintés a **ellenőrizze** érték. Hasonlítsa össze a **ellenőrizze** megtalálható az aktuális esemény értékét, és kimeneti az eseményt, ha ezek eltérnek.

## <a name="query-example-find-the-first-event-in-a-window"></a>Példa: egy ablakban találja, az első esemény
**Leírás**: az első autója található minden 10 perces időközt.

**Bemeneti**:

| LicensePlate | Ellenőrizze | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Kimeneti**:

| LicensePlate | Ellenőrizze | Time |
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

Most tegyük módosítása a problémát, és keresse meg az első autója az egy adott győződjön minden 10 perces időközt.

| LicensePlate | Ellenőrizze | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
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

## <a name="query-example-find-the-last-event-in-a-window"></a>Példa: a legutóbbi esemény található egy ablakban
**Leírás**: az utolsó autó található minden 10 perces időközt.

**Bemeneti**:

| LicensePlate | Ellenőrizze | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Kimeneti**:

| LicensePlate | Ellenőrizze | Time |
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

**MAGYARÁZAT**: a lekérdezésben szereplő két lépésből áll. Az elsőt a legújabb időbélyeg megkeresi a windows 10 perc. A második lépésben csatlakoztatja az eredeti adatfolyam segítségével megkeresheti a megfelelő az utolsó időbélyegek minden ablakban az első lekérdezés eredményeit. 

## <a name="query-example-detect-the-absence-of-events"></a>Példa: események hiányára észlelése
**Leírás**: állítson be egy adatfolyam nincs érték, amely megfelel egy adott feltételnek.
Például 2 egymást követő autók azonos ellenőrizze a megadott téren közúti az utolsó 90 másodpercen belül?

**Bemeneti**:

| Ellenőrizze | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Kimeneti**:

| Ellenőrizze | Time | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
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

**MAGYARÁZAT**: használata **LAG** , és a bemeneti adatfolyam egy esemény vissza való betekintés a **ellenőrizze** érték. Összehasonlítja a **ELLENŐRIZZE** az aktuális esemény értéket, és majd kimeneti az esemény, ha azonos. Is **LAG** az előző autó kapcsolatos adatok eléréséhez.

## <a name="query-example-detect-the-duration-between-events"></a>Példa: események közötti időtartam észlelése
**Leírás**: egy adott esemény időtartama található. Például egy webes clickstream megadott, határozza meg a szolgáltatás fordított idő.

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

**MAGYARÁZAT**: használja a **utolsó** működnek, mint az utolsó beolvasása **idő** értéke, ha az esemény típusa történt **Start**. A **utolsó** függvény **PARTITION BY [user]** annak jelzésére, hogy az eredmény számított egyedi felhasználónként. A lekérdezés tartalmaz egy 1 órás közötti időeltérés maximális küszöbértéke **Start** és **leállítása** események, de igény szerint konfigurálható **(korlát DURATION(hour, 1)**.

## <a name="query-example-detect-the-duration-of-a-condition"></a>Példa: egy feltétel időtartama észlelése
**Leírás**: található out mennyi ideig egy állapot fordult elő.
Tegyük fel például, hogy programhiba eredményezett (fent 20 000 font) egy helytelen súlyozással rendelkező összes autók. A hiba időtartama számítási szeretnénk.

**Bemeneti**:

| Ellenőrizze | Time | Súlyozás |
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

**MAGYARÁZAT**: használata **LAG** 24 órán át a bemeneti adatfolyam tekintheti meg és keressen where példányok **StartFault** és **StopFault** vannak a súlyozással ölel < 20000.

## <a name="query-example-fill-missing-values"></a>Példa: Töltse ki a hiányzó értékek
**Leírás**: az események streamjét a hiányzó, a előállít egy az események streamjét a rendszeres időközönként.
Például generál egy eseményt 5 másodpercentként, az utoljára látott adatpont jelentéseket.

**Bemeneti**:

| t | érték |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Kimeneti (első 10 sor)**:

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


**MAGYARÁZAT**: Ez a lekérdezés 5 másodpercentként eseményeket hoz létre, és kiírja a korábban fogadott utolsó esemény. A [Hopping ablak](https://msdn.microsoft.com/library/dn835041.aspx "Hopping ablak--Azure Stream Analytics") időtartamát határozza meg, milyen távolságban vissza a lekérdezés keres az utolsó esemény (ebben a példában 300 másodperc) található.


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>Példa: két eseménytípust belül az azonos adatfolyam összefüggéseket
**Leírás**: néha igazolnia kell a riasztásokat a következő fordult elő egy adott időtartományt több eseménytípus alapján.
Például otthoni sütők IoT esetben szeretnénk hoz létre riasztást, ha ventilátor hőmérséklet kisebb, mint a 40, és az utolsó 3 perc alatt teljesítményhez lett kisebb, mint 10.

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
| "2018-01-01T16:05:00" | "Oven1" |30 | "Gyorskiértékelésű elemek melegítés" |15 |
| "2018-01-01T16:06:00" | "Oven1" |20 | "Gyorskiértékelésű elemek melegítés" |15 |
| "2018-01-01T16:07:00" | "Oven1" |20 | "Gyorskiértékelésű elemek melegítés" |15 |

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

**MAGYARÁZAT**: az első lekérdezés `max_power_during_last_3_mins`, használja a [csúszó ablak](https://msdn.microsoft.com/en-us/azure/stream-analytics/reference/sliding-window-azure-stream-analytics) az elmúlt 3 perc alatt a maximális érték az energiagazdálkodási érzékelő minden eszköz kereséséhez. A második lekérdezés az első lekérdezés az energiagazdálkodási értéket keresi a legújabb ablakban vonatkozó az aktuális esemény csatlakozik. És ezt követően megadott feltételek teljesülnek, riasztást küld az eszköz.


## <a name="get-help"></a>Segítségkérés
Ha további segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)


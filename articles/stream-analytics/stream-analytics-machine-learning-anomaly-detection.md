---
title: "Anomáliadetektálás Azure használati útmutatójában (előzetes verzió) |} Microsoft Docs"
description: "Használja a stream analytics és a gépi tanulási rendellenességek észlelését."
services: stream-analytics
documentationcenter: 
author: dubansal
manager: jhubbard
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: dubansal
ms.openlocfilehash: 43a2a9784668fad2aa5b1441cfd37751c0c240b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-anomalydetection-operator"></a>A ANOMALYDETECTION operátorral

> [!IMPORTANT]
> Ez a funkció előzetes verzió van. Ne használja éles környezetben.

A **ANOMALYDETECTION** operátor eseményfolyamokat rendellenességek észlelését is használható.
Például lehet, hogy hosszú ideig keresztül lassú csökkenése szabad memória, a memóriavesztés, vagy a webszolgáltatási kérelmeket, amelyek egy stabil száma előfordulhat, hogy jelentős mértékben növeli vagy csökkenti.

A megadott időtartam során ellenőrzi, hogy a következő típusú rendellenességek észlelését:

- Kétirányú szint módosítása
- Lassú pozitív trend
- Lassú negatív trend

Milyen távolságban vissza az előzményekben az aktuális esemény kell figyelembe venni, mennyi idő elteltével korlátozva, használja a **LIMIT DURATION** záradékban. **ANOMALYDETECTION** megadható az, hogy csak olyan események, amelyek egy bizonyos tulajdonsággal egyező vagy a feltétel használatával korlátozni a **amikor** záradékban.

Opcionálisan feldolgozható események külön alapján a kulcs van megadva a csoportok a **PARTITION BY** záradékban. Képzési és az előrejelzés történik egymástól függetlenül minden partíció.

## <a name="syntax"></a>Szintaxis

`ANOMALYDETECTION(\<scalar_expression\>) OVER ([PARTITION BY \<partition key\>] LIMIT DURATION(\<unit\>, \<length\>) [WHEN boolean_expression])` 


## <a name="example-usage"></a>Példa használati

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id \> 100) FROM input`|


## <a name="arguments"></a>Argumentumok

- **scalar_expression**

  A skaláris kifejezés, amely felett az anomáliadetektálás végbemegy. Egy kifejezés, amely egyetlen (skaláris) értéket ad vissza lebegőpontos vagy bigint típusú legyen. A helyettesítő karakteres kifejezést  **\***  nem engedélyezett. **scalar_expression** nem tartalmazhat más analitikai függvények vagy külső funkciók.

- **([When_clause] [partition_by_clause] limit_duration_clause) keresztül**

- **partition_by_clause** 

  A `PARTITION BY \<partition key\>` záradék osztja a tanulási és a képzési külön partíciók között. Más szóval egy külön modell használni / értékének `\<partition key\>` és tanulási és, hogy a modell a tanítási használják ezt az értéket csak eseményeket. Például:

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

  a rendszer betanítása, és egy olvasási szemben a más értékek csak az azonos érzékelő pontszám.

- **limit_duration záradék** időtartama (\<egység\>, \<hossza\>)

  Meghatározza, mennyi az aktuális esemény előzmények számítanak bele a **ANOMALYDETECTION** számítási. Tekintse meg a DATEDIFF támogatott egységek és azok rövidítések részletes leírását.

- **when_clause** 

  Egy logikai feltétel megadja az események számítanak bele a **ANOMALYDETECTION** számítási.

## <a name="return-types"></a>Visszatérési típusokat

A függvény minden három pontszámok kimenetként tartalmazó rekordot ad vissza. A különböző típusú anomáliadetektálási érzékelők társított tulajdonságok nevezzük:

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

Az egyedi értékek kívül a rekord kibontásához használja a **GetRecordPropertyValue** függvény. Példa:

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) \> 3.25` 


Az anomáliadetektálási egy adott típusú észlelt a anomáliadetektálási pontok egyikét ebbe a küszöbértéket. A küszöbérték lehet bármely lebegőpontos számnak \>= 0. Küszöbértéke egy érzékenységi és az vetett bizalmat közötti kompromisszumot. Például alacsonyabb küszöbértékkel Ehhez ellenőrizze észlelési érzékenyebb a módosításokat, és további riasztást állítson elő, mivel a magasabb küszöbértéket sikerült észlelés kevésbé érzékeny és több biztosnak, azonban néhány rendellenességeket maszk. A pontos küszöbérték használni a forgatókönyv függ. Nincs felső korlátja, de a javasolt tartománya 3,25 – 5.

## <a name="algorithm"></a>Algoritmus

**ANOMALYDETECTION** ablak szemantikáját, ami azt jelenti, hogy a számítási kerül, a függvény eseményenként végrehajtja a pontszám hozzák esemény késleltetett használ. A számítási alkatrészek cserélhetőségének Martingales, amelyek működnek, ha a terjesztés az esemény értékek módosultak ellenőrzésével alapul. Ha igen, a potenciális anomáliadetektálási talált. A visszaadott érték utalhat, hogy az adott anomáliadetektálási a megbízhatósági szint. Egy belső optimalizálás **ANOMALYDETECTION** az anomáliadetektálási pontszám olyan esemény alapján kiszámítja *d* való *2d* eseményeit, ahol *d*a megadott észlelési ablak mérete.

**ANOMALYDETECTION** egységes bemeneti idősorozatban vár. Az eseménystream egy átfedésmentes keresztül összesítése vagy ablak hopping egységes is végezhető. Forgatókönyvekben, ahol események közötti résnek mindig kisebb, mint az összesítési ablakban a átfedésmentes ablak is elegendő az idősorozatban egységes ellenőrizze. Résnek nagyobb is lehet, ha hézagok is ki kell tölteni az utolsó értékét ugróablakok többszöri használatával. Az alábbi példa mindkét eshetőség kezelhetik. Jelenleg a `FillInMissingValuesStep` lépés nem figyelmen kívül hagyja. Nem rendelkezik ezzel a lépéssel fordítási hiba okozza.

## <a name="performance-guidance"></a>Teljesítmény útmutató

- Használjon 6 SU feladatok. 
- Küldés események legalább 1 másodperc egymástól.
- A lekérdezés nem particionált használatával a **ANOMALYDETECTION** függvény is eredményt készül 25ms számítási késéssel átlagosan.
- A particionált lekérdezések által tapasztalt késést számától függ kissé partíciók, mivel a számítások száma magasabb. Azonban a késleltetés arra készül, ugyanaz, mint a nem particionált eset a hasonló események teljes száma a összes partíciójára.
- Nem valós idejű adatok olvasása közben nagy mennyiségű adatot okozhatnak gyorsan. Ezek az adatok feldolgozása jelenleg jelentősen lassabb. Ilyen esetekben a késés növekedése lineárisan a az ablak mérete vagy esemény időköz helyett az ablak adatpontok száma önmagában található. Nem valós idejű esetekben a késés csökkentése érdekében érdemes lehet egy kisebb ablak mérete. Azt is megteheti fontolja meg a feladat indítása az aktuális időponthoz képest. Néhány példa a nem particionált lekérdezésben késések fordulnak elő: 
    - az észlelési időszak 60 adatpontok eredményezhet a 10 másodperces késleltetése 3 MB/s átviteli sebességgel. 
    - 600 adatok pontokat a késés körülbelül 80 másodperc 0,4 MB/s átviteli sebességgel képes elérni.

## <a name="example"></a>Példa

A következő lekérdezés kimeneti riasztást, ha a rendszer észlelt egy anomáliadetektálási használható.
Ha a bemeneti adatfolyam nem egységes, az összesítő lépés segíthet irányítópulttá, egységes idősor. A példa **AVG** , de az összesítési típusát attól függ, a felhasználói forgatókönyvnek. Továbbá amikor idősor nagyobb, mint az összesítési ablak hézagok, lesz események eseményindító anomáliadetektálás (visszamenőleges késleltetett ablak szemantikáját) idősorozatban található. Ennek eredményeképpen egységességének feltételezve megszakad a következő esemény érkezésekor. Ilyen helyzetekben lévő idősorozatban található hézagok töltés úgy kell. Egy lehetséges módszer kerül az utolsó esemény minden Ugrás ablakban alább látható módon.

Amint azt megelőzően nem hagyja ki a `FillInMissingValuesStep` lépés a lépést. A lépés kihagyása fordítási hiba okozza.

    WITH AggregationStep AS 
    (
         SELECT
               System.Timestamp as tumblingWindowEnd,

               AVG(value) as avgValue

         FROM input
         GROUP BY TumblingWindow(second, 5)
    ),

    FillInMissingValuesStep AS
    (
          SELECT
                System.Timestamp AS hoppingWindowEnd,

                TopOne() OVER (ORDER BY tumblingWindowEnd DESC) AS lastEvent

         FROM AggregationStep
         GROUP BY HOPPINGWINDOW(second, 300, 5)

    ),

    AnomalyDetectionStep AS
    (

          SELECT
                hoppingWindowEnd,
                lastEvent.tumblingWindowEnd as lastTumblingWindowEnd,
                lastEvent.avgValue as lastEventAvgValue,
                system.timestamp as anomalyDetectionStepTimestamp,

                ANOMALYDETECTION(lastEvent.avgValue) OVER (LIMIT DURATION(hour, 1)) as
                scores

          FROM FillInMissingValuesStep
    )

    SELECT
          alert = 1,
          hoppingWindowEnd,
          lastTumblingWindowEnd,
          lastEventAvgValue,
          anomalyDetectionStepTimestamp,
          scores

    INTO output

    FROM AnomalyDetectionStep

    WHERE

        CAST(GetRecordPropertyValue(scores, 'BiLevelChangeScore') as float) \>= 3.25

        OR CAST(GetRecordPropertyValue(scores, 'SlowPosTrendScore') as float) \>=
        3.25

       OR CAST(GetRecordPropertyValue(scores, 'SlowNegTrendScore') as float) \>=
       3.25

## <a name="references"></a>Referencia

* [Anomáliadetektálás – segítségével a Machine Learning rendellenességeket észlelheti az adatsorozat időadatok](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)
* [Számítógép-tanulási Anomáliadetektálás API](https://docs.microsoft.com/en-gb/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [Idő adatsorozat Anomáliadetektálás](https://msdn.microsoft.com/library/azure/mt775197.aspx)

## <a name="get-support"></a>Támogatás kérése
Ha további segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Következő lépések

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)


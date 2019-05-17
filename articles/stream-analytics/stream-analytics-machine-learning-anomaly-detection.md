---
title: Anomáliadetektálás az Azure Stream Analytics (előzetes verzió)
description: Ez a cikk ismerteti, hogyan használhatja az Azure Stream Analytics és az Azure Machine Learning együtt észlelje a rendellenességeket.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.custom: seodec18
ms.openlocfilehash: 6f1b40e6224b9ed872700d526b954c605159d6bf
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65789629"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Anomáliadetektálás az Azure Stream Analytics szolgáltatásban

Elérhető a felhőben és az Azure IoT Edge segítségével, az Azure Stream Analytics biztosít beépített machine learning-alapú anomáliadetektálás észlelési képességek, amelyek segítségével figyelheti a két leggyakrabban előforduló rendellenességek: ideiglenes és állandó. Az a **AnomalyDetection_SpikeAndDip** és **AnomalyDetection_ChangePoint** funkciók, közvetlenül a Stream Analytics-feladatot a is végezhet rendellenesség-észlelést.

A machine learning-modellek tegyük fel, egységesen mintavételezett idősorozat. Ha az idősor nem egységes, beszúrhat egy összesítési lépés egy átfedésmentes ablak anomáliadetektálás hívása előtt.

A machine learning-műveletek nem támogatják a szezonalitás trendeket vagy több változós összefüggéseket.

## <a name="model-accuracy-and-performance"></a>Modell pontosságát és teljesítmény

Általában a modell pontosságát javítja a csúszóablakban további adatokkal. A megadott csúszóablakban adatait a normál értéktartományt adott időkeret részét számít. A modell csak úgy véli, Eseményelőzmények. Ellenőrizze, hogy az aktuális esemény rendellenes eltoltan keresztül. Ahogy mozog a csúszóablakban, régi értékek ürülnek ki a modell betanítása.

A függvények milyen, amint láthatta, amennyiben alapján normál létrehozásával működnek. Kiugró értékek a létrehozott normál, a megbízhatósági szint belül hasonlítja azonosítja. Az ablak mérete a normál működéshez a modell betanítását, így ha anomália található, felismerni képes lenne szükséges minimális események alapján.

Ne feledje, hogy a modell válaszidő az előzmények mérete nő, mert porovnání a múltban történt eseményekről megnövelt számú. Javasoljuk, hogy csak az események a jobb teljesítmény érdekében szükséges számát tartalmazzák.

A time series hiányosságok lehet a modell nem fogadott események bizonyos időpontokban a időben. Ebben a helyzetben a Stream Analytics használatával imputálási intézi. Az előzmények mérete, valamint egy időtartamot, az azonos csúszóablakban a rendszer kiszámítja, amellyel események várhatóan érkeznek átlagos sebességét.

## <a name="spike-and-dip"></a>Megnövekedett és dedikált IP-címmel

Egy time series eseménystream ideiglenes protokollmegvalósításokat, csúcsok és süllyedések ismertek. Csúcsok és a DIP figyelhető a Machine Learning-alapú operátorral [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Megnövekedett és a dip anomáliadetektálási – példa](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

Az azonos csúszóablakban, a második ugrásszerű kisebb, mint az elsőt, ha a kiszámított pontszámot a kisebb kiugrást a nem elegendő az első kiugrás belül a megbízhatósági szint esetében a pontszámot képest megadott jelentős. A modell megbízhatósági szint beállítás ilyen rendellenességek olvasásra csökkenő próbálhatja ki. Azonban ha elkezdi túl sok értesítéseket kaphat, egy magasabb megbízhatósági intervallum használhatja.

A következő példalekérdezés feltételezi, hogy a bemeneti arány egy 2 perces csúszóablakban másodpercenként egy esemény-120 eseményeit. A végső SELECT utasítás kibontása, és megjeleníti a pontszám és anomáliadetektálási 95 %-os megbízhatósági szint állapotát.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_SpikeAndDip(CAST(temperature AS float), 95, 120, 'spikesanddips')
            OVER(LIMIT DURATION(second, 120)) AS SpikeAndDipScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'Score') AS float) AS
    SpikeAndDipScore,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'IsAnomaly') AS bigint) AS
    IsSpikeAndDipAnomaly
INTO output
FROM AnomalyDetectionStep
```

## <a name="change-point"></a>Pont módosítása

Egy time series eseménystream állandó rendellenességek olyan módosításokat az értékek eloszlását az eseménystream, például megváltozik, és trendeket. A Stream Analytics, az ilyen rendellenességek észlelése használata a Machine Learning-alapú [AnomalyDetection_ChangePoint](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) operátor.

Állandó módosítások sokkal hosszabb ideig, mint a csúcsok és süllyedések és katasztrofális esemény arra utalhat. Állandó módosítások nem láthatók általában szemmel, de az észlelése a **AnomalyDetection_ChangePoint** operátor.

Az alábbi képen egy példa egy szint módosításának:

![Szint módosításának anomáliadetektálási – példa](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

Az alábbi képen egy példa egy trend módosítása:

![Trend módosítás anomáliadetektálási – példa](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

A következő példalekérdezés feltételezi, hogy a bemeneti arány egy esemény / másodperc egy 20 perces csúszóablakban 1200-as események előzményei mérettel. A végső SELECT utasítás kibontása, és megjeleníti a pontszám és anomáliadetektálási 80 %-os megbízhatósági szint állapotát.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_ChangePoint(CAST(temperature AS float), 80, 1200) 
        OVER(LIMIT DURATION(minute, 20)) AS ChangePointScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(ChangePointScores, 'Score') AS float) AS
    ChangePointScore,
    CAST(GetRecordPropertyValue(ChangePointScores, 'IsAnomaly') AS bigint) AS
    IsChangePointAnomaly
INTO output
FROM AnomalyDetectionStep

```

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Anomáliadetektálás gépi tanulás használatával az Azure Stream Analytics szolgáltatásban

A következő videó bemutatja, hogyan észleli az anomáliadetektálási Azure Stream Analytics a machine learning-függvények használatával valós idejű. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Anomaly-detection-using-machine-learning-in-Azure-Stream-Analytics/player]

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.ASpx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)


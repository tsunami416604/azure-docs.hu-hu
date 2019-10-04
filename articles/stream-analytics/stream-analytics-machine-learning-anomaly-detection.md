---
title: Anomáliadetektálás az Azure Stream Analytics szolgáltatásban
description: Ez a cikk ismerteti, hogyan használhatja az Azure Stream Analytics és az Azure Machine Learning együtt észlelje a rendellenességeket.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: e2fd226f1c605821f0fd595832b2cbe26d994fb4
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612336"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Anomáliadetektálás az Azure Stream Analytics szolgáltatásban

Elérhető a felhőben és az Azure IoT Edge segítségével, az Azure Stream Analytics biztosít beépített machine learning-alapú anomáliadetektálás észlelési képességek, amelyek segítségével figyelheti a két leggyakrabban előforduló rendellenességek: ideiglenes és állandó. Az a **AnomalyDetection_SpikeAndDip** és **AnomalyDetection_ChangePoint** funkciók, közvetlenül a Stream Analytics-feladatot a is végezhet rendellenesség-észlelést.

A machine learning-modellek tegyük fel, egységesen mintavételezett idősorozat. Ha az idősor nem egységes, beszúrhat egy összesítési lépés egy átfedésmentes ablak anomáliadetektálás hívása előtt.

A machine learning-műveletek nem támogatják szezonalitás trendeket vagy több változós összefüggéseket jelenleg.

## <a name="model-behavior"></a>Eszközmodell viselkedésének

Általában a modell pontosságát javítja a csúszóablakban további adatokkal. A megadott csúszóablakban adatait a normál értéktartományt adott időkeret részét számít. A modell csak úgy véli, Eseményelőzmények. Ellenőrizze, hogy az aktuális esemény rendellenes eltoltan keresztül. Ahogy mozog a csúszóablakban, régi értékek ürülnek ki a modell betanítása.

A függvények milyen, amint láthatta, amennyiben alapján normál létrehozásával működnek. Kiugró értékek a létrehozott normál, a megbízhatósági szint belül hasonlítja azonosítja. Az ablak mérete a normál működéshez a modell betanítását, így ha anomália található, felismerni képes lenne szükséges minimális események alapján.

A modell válaszidő növeli előzmények méretű, mert porovnání a múltban történt eseményekről megnövelt számú. Javasoljuk, hogy csak az események a jobb teljesítmény érdekében szükséges számát tartalmazzák.

A time series hiányosságok lehet a modell nem fogadott események bizonyos időpontokban a időben. Ebben a helyzetben a Stream Analytics imputálási logikai kapcsolattal történik. Az előzmények mérete, valamint egy időtartamot, az azonos csúszóablakban a rendszer kiszámítja, amellyel események várhatóan érkeznek átlagos sebességét.

Az anomáliadetektálási generátor elérhető [Itt](https://aka.ms/asaanomalygenerator) hírcsatorna adatokat a különböző anomáliadetektálási minták az Iot Hub segítségével. Az ASA-feladat beállíthatja a ezeket anomáliadetektálási észlelési funkciók az Iot-központ olvasni, és észlelje a rendellenességeket.

## <a name="spike-and-dip"></a>Megnövekedett és dedikált IP-címmel

Egy time series eseménystream ideiglenes protokollmegvalósításokat, csúcsok és süllyedések ismertek. Csúcsok és a DIP figyelhető a Machine Learning-alapú operátorral [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Megnövekedett és a dip anomáliadetektálási – példa](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

Az azonos csúszóablakban, a második ugrásszerű kisebb, mint az elsőt, ha a kiszámított pontszámot a kisebb kiugrást a nem elegendő az első kiugrás belül a megbízhatósági szint esetében a pontszámot képest megadott jelentős. A modell megbízhatósági szint ilyen rendellenességek észlelése csökkenő próbálhatja ki. Azonban ha elkezdi túl sok értesítéseket kaphat, egy magasabb megbízhatósági intervallum használhatja.

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

## <a name="performance-characteristics"></a>Teljesítményjellemzők

Ezek a modellek teljesítményének függ az előzmények mérete, a időszak, az esemény terhelés, és e funkció szintű particionálás szolgál. Ez a szakasz ismerteti ezeket a konfigurációkat és minták az Adatbetöltési díjait számoljuk fel 1 KB, 5 KB és 10 ezer esemény / másodperc átcsoportosítása hogyan.

* **Előzmények mérete** – ezek a modellek végre lineárisan **előzmények mérete**. Minél hosszabb a előzmények méretet, a hosszabb a modellek igénybe pontszámot rendelni az új eseményt kap. Ennek oka az, a modellek, hasonlítsa össze az egyes előzmények pufferben elmúlt eseményt az új esemény.
* **Ablak időtartamának** – a **ablak időtartamának** tükröznie kell mennyi ideig tart az előzmények mérete által megadott tetszőleges számú eseményeket fogadni. Anélkül, hogy sok eseményt az ablakban az Azure Stream Analytics imputálására lenne a hiányzó értékeket. Ezért a CPU-felhasználás az előzmények mérete függvénye.
* **Esemény terhelés** – minél nagyobb a **esemény terhelés**, annál több munkahelyi, amely végzi modellekkel, amely hatással van a CPU-felhasználás. A feladat azáltal, hogy zavaróan párhuzamos, feltéve, hogy az üzleti logika használata több bemeneti partíciók logikus kiterjeszthető.
* **Függvény szint particionálás** - **függvény szint particionálás** segítségével történik ```PARTITION BY``` belül a rendellenességek észlelése függvény hívásához szükséges. Az ilyen típusú particionálás hozzáadja az-terhelés a állapotban kell tartani a több modell egy időben igényei szerint. Függvény szint particionálás eszköz szintű particionálás ilyen esetekben használatos.

### <a name="relationship"></a>Kapcsolat
Az előzmények mérete, időszak hossza és esemény teljes terhelés kapcsolódnak egymáshoz, a következő módon:

windowDuration (milliszekundumban) = 1000 * historySize / (teljes bemeneti aránya / s / bemeneti partíciók száma)

A függvény által deviceId történő particionálása esetén hozzáadása "Partíció által deviceId", a rendellenességek észlelése függvény hívásához szükséges.

### <a name="observations"></a>Megfigyelés
Az alábbi táblázat tartalmazza az átviteli sebesség megfigyelések egy egyetlen csomópont (6 SU) a nem particionált esethez:

| Előzmények mérete (események) | Ablak időtartama (ms) | Összes bemeneti események másodpercenkénti száma |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2,200 |
| 600 | 728 | 1,650 |
| 6,000 | 10,910 | 1,100 |

Az alábbi táblázat tartalmazza a particionált esethez (6 SU) egy csomópont esetén az átviteli sebesség megfigyelések:

| Előzmények mérete (események) | Ablak időtartama (ms) | Összes bemeneti események másodpercenkénti száma | Eszközök száma |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1,091 | 1,100 | 10 |
| 600 | 10,910 | 1,100 | 10 |
| 6,000 | 218,182 | <550 | 10 |
| 60 | 21,819 | 550 | 100 |
| 600 | 218,182 | 550 | 100 |
| 6,000 | 2,181,819 | <550 | 100 |

Mintakód futtatásához a fenti konfigurációk nem particionált található a [Streamelési a méretezési csoport tárház](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) Azure minták. A kód létrehoz egy stream analytics-feladat a függvény szint particionálás, amely Eseményközpontot használja kimenetként és bemenetként. A bemeneti terhelés tesztcélú ügyfelek használatával jön létre. Minden bemeneti esemény egy 1 KB-os json-dokumentumot. Események (legfeljebb 1 KB-eszközök esetén) JSON-adatokat küld valamelyik IoT-eszköz szimulálása. Az előzmények mérete, időszak hossza és esemény teljes terhelés sokfélék 2 bemeneti partíció keresztül.

> [!Note]
> A pontosabb becslést testre szabhatja a saját forgatókönyvéhez igazítva mintákat.

### <a name="identifying-bottlenecks"></a>Szűk keresztmetszetek azonosítása
Az Azure Stream Analytics-feladat a metrika panel használatával a folyamatban, szűk keresztmetszetek azonosítása. Felülvizsgálat **bemeneti/kimeneti események** átviteli sebességet és ["Vízjel késleltetés"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) vagy **várakozó események** megtekintheti, ha a feladat viselkedéssel a bemeneti arány. Az Event Hubs-metrikák, keressen **kérelmek szabályozva** , és ennek megfelelően módosítsa a küszöbérték egységek. Cosmos DB metrikákkal, tekintse át a **felhasznált max. RU/s partíciókulcs-tartományonként** alatt kulcstartományokkal biztosításához a partíció átviteli sebesség egyenletesen felhasznált. Azure SQL Database monitorozása **naplózási IO** és **CPU**.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Anomáliadetektálás gépi tanulás használatával az Azure Stream Analytics szolgáltatásban

A következő videó bemutatja, hogyan észleli az anomáliadetektálási Azure Stream Analytics a machine learning-függvények használatával valós idejű. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Anomaly-detection-using-machine-learning-in-Azure-Stream-Analytics/player]

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)


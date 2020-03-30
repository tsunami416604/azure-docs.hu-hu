---
title: Anomáliaészlelés az Azure Stream Analytics szolgáltatásban
description: Ez a cikk ismerteti, hogyan használhatja az Azure Stream Analytics és az Azure Machine Learning együtt anomáliák észlelésére.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 51b9c827d453eef2e2e75e1aa5222204eaa38d0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525532"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Anomáliaészlelés az Azure Stream Analytics szolgáltatásban

A felhőben és az Azure IoT Edge-ben is elérhető Azure Stream Analytics beépített gépi tanulási alapú anomáliadetektálási képességeket kínál, amelyek a két leggyakrabban előforduló anomália figyelésére használhatók: ideiglenes és állandó. A **AnomalyDetection_SpikeAndDip** és **AnomalyDetection_ChangePoint** funkciókkal anomáliafelismerést közvetlenül a Stream Analytics-feladatban hajthat végre.

A gépi tanulási modellek egy egységesen mintavételezett idősorozatot feltételeznek. Ha az idősorozat nem egységes, az anomáliadetektálás hívása előtt beszúrhat egy aggregációs lépést egy bukdácsoló ablakkal.

A gépi tanulási műveletek jelenleg nem támogatják a szezonalitási trendeket vagy a többvariát-korrelációkat.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Anomáliadetektálás gépi tanulással az Azure Stream Analytics szolgáltatásban

Az alábbi videó bemutatja, hogyan észlelheti az anomáliát valós időben az Azure Stream Analytics gépi tanulási függvényeivel. 

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Real-Time-ML-Based-Anomaly-Detection-In-Azure-Stream-Analytics/player]

## <a name="model-behavior"></a>Modell viselkedése

Általában a modell pontossága javul, ha több adat van a csúszóablakban. A megadott csúszóablakban lévő adatokat a rendszer az adott időkeret normál értéktartományának részeként kezeli. A modell csak a csúszóablakon keresztüli eseményelőzményeket veszi figyelembe annak ellenőrzésére, hogy az aktuális esemény rendellenes-e. Acsúszóablak mozgása közben a régi értékek kikerülnek a modell betanításából.

A funkciók úgy működnek, hogy létrehoznak egy bizonyos normálist az eddig látottak alapján. A kiugró értékeket a megállapított normál értékhez hasonlítva, a kontinminetárakon belül azonosítják. Az ablak méretének a modell normál viselkedéshez való betanításához szükséges minimális eseményeken kell alapulnia, hogy anomália bekövetkeztekor képes legyen felismerni azt.

A modell válaszideje az előzmények méretével nő, mert nagyobb számú múltbeli eseményhez kell hasonlítania. Javasoljuk, hogy csak a jobb teljesítmény érdekében csak a szükséges számú eseményt tartalmazza.

Az idősorozat hézagai annak az eredménye lehet, hogy a modell bizonyos időpontban nem fogad eseményeket. Ezt a helyzetet a Stream Analytics imputálási logika használatával kezeli. Az előzmények mérete, valamint az időtartam ugyanahhoz a csúszóablakhoz az események érkezésének átlagos sebességének kiszámítására szolgál.

Az [itt](https://aka.ms/asaanomalygenerator) elérhető anomáliagenerátor segítségével egy iot hub különböző anomáliamintákkal rendelkező adatokat lehet táplálni. Egy ASA-feladat beállítható ezekkel az anomáliadetektálási függvényekkel, hogy olvassa el ezt a sok központot, és észlelje az anomáliákat.

## <a name="spike-and-dip"></a>Tüske és mártás

Az idősorozat eseményfolyamának ideiglenes anomáliáit tüskéknek és dipeknek nevezzük. A tüskék és a dipek a Machine Learning alapú operátor, [a AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
)segítségével figyelhetők meg.

![Példa a tüske- és dipanomáliára](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

Ugyanebben a csúszóablakban, ha egy második tüske kisebb, mint az első, a kisebb tüske számított pontszáma valószínűleg nem elég jelentős a megadott megbízhatósági szinten belüli első csúcs pontszámához képest. Megpróbálhatja csökkenteni a modell megbízhatósági szintjét az ilyen anomáliák észleléséhez. Ha azonban túl sok riasztást kap, nagyobb megbízhatósági intervallumot használhat.

A következő példa lekérdezés feltételezi, hogy egy egységes bemeneti sebesség egy esemény másodpercenként egy 2 perces csúszó ablakban, amelynek története 120 események. A végleges SELECT utasítás 95%-os megbízhatósági szinttel kinyeri és kiadja a pontszámot és az anomáliaállapotot.

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

Az idősorozat eseményfolyamában az állandó anomáliák az eseményfolyam értékeinek eloszlásában bekövetkező változások, például szintváltozások és trendek. A Stream Analytics ilyen anomáliák észlelése a Machine Learning alapú [AnomalyDetection_ChangePoint](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) operátor használatával.

Az állandó változások sokkal tovább tartanak, mint a tüskék és a mártások, és katasztrofális esemény(ek)et jelezhetnek. Az állandó változások általában szabad szemmel nem láthatók, de a **AnomalyDetection_ChangePoint** operátorral észlelhetők.

A következő kép egy szintváltozásra mutat be példát:

![Példa szintváltozási anomáliára](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

A következő kép egy trendváltozásra mutat be példát:

![Példa a trendváltozási anomáliára](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

A következő példa lekérdezés feltételezi, hogy egy egységes bemeneti sebesség egy esemény másodpercenként egy 20 perces csúszó ablak, amelynek előzménymérete 1200 események. A végleges SELECT utasítás 80%-os megbízhatósági szinttel kinyeri és adja ki a pontszámot és az anomáliaállapotát.

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

Ezek a modellek teljesítménye az előzmények méretétől, az ablak időtartamától, az esemény terhelésétől és attól függ, hogy a függvényszintű particionálás t használja-e. Ez a szakasz ismerteti ezeket a konfigurációkat, és mintákat tartalmaz az 1K, 5K és 10K események másodpercenkénti betöltési sebességének fenntartásához.

* **Előzmények mérete** - Ezek a modellek lineárisan teljesítenek **az előzmények méretével.** Minél hosszabb az előzmények mérete, annál hosszabb ideig tart a modellek pontszám egy új esemény. Ennek az az oka, hogy a modellek összehasonlítják az új eseményt az előzménypufferben szereplő egyes múltbeli eseményekkel.
* **Ablak időtartama** – Az **ablak időtartamának** tükröznie kell, hogy mennyi ideig tart annyi esemény fogadása, mint az előzmények mérete. Anélkül, hogy sok esemény az ablakban, az Azure Stream Analytics imputálná a hiányzó értékeket. Ezért a CPU-felhasználás az előzmények méretének függvénye.
* **Esemény terhelése** - Minél nagyobb az **esemény terhelése**, annál több munkát végeznek a modellek, amely hatással van a CPU-fogyasztás. A feladat kiskálázható azáltal, hogy kínosan párhuzamos, feltételezve, hogy van értelme az üzleti logika, hogy több bemeneti partíciókhasználata.
* **A függvényszintű particionálás** - Anomáliadetektálási függvény hívásán**Function level partitioning** ```PARTITION BY``` belül történik. Ez a fajta particionálás többletterhelést ad hozzá, mivel az állapotot egyszerre több modell esetében is fenn kell tartani. A függvényszintű particionálás olyan helyzetekben használatos, mint az eszközszintű particionálás.

### <a name="relationship"></a>Kapcsolat
Az előzmények mérete, az ablak időtartama és a teljes eseményterhelés a következő módon kapcsolódnak egymáshoz:

windowDuration (ms-ban) = 1000 * historySize / (Összes bemeneti esemény másodpercenként / bemeneti partíciók száma)

A függvény eszközazonosító szerint történő particionálásakor adja hozzá a "PARTITION BY deviceId" értéket az anomáliadetektálási függvényhíváshoz.

### <a name="observations"></a>Észrevételek
Az alábbi táblázat egyetlen csomópont (6 SU) átviteli megfigyeléseit tartalmazza a nem particionált esethez:

| Előzmények mérete (események) | Ablak időtartama (ms) | Bemeneti események száma másodpercenként |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2200 |
| 600 | 728 | 1,650 |
| 6.000 | 10,910 | 1100 |

Az alábbi táblázat egyetlen csomópont (6 SU) átviteli megfigyeléseit tartalmazza a particionált esethez:

| Előzmények mérete (események) | Ablak időtartama (ms) | Bemeneti események száma másodpercenként | Eszközök száma |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1,091 | 1100 | 10 |
| 600 | 10,910 | 1100 | 10 |
| 6.000 | 218,182 | <550 | 10 |
| 60 | 21,819 | 550 | 100 |
| 600 | 218,182 | 550 | 100 |
| 6.000 | 2,181,819 | <550 | 100 |

A fenti nem particionált konfigurációk futtatásához szükséges mintakód az Azure-minták [streamelési skálázási tártárában](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) található. A kód létrehoz egy stream elemzési feladat nem függvényszintű particionálás, amely az Event Hub bemeneti és kimeneti. A bemeneti terhelés tesztügyfelek használatával jön létre. Minden bemeneti esemény egy 1 KB-os json dokumentum. Események szimulálni egy IoT-eszköz json-adatokat küld (legfeljebb 1K eszközök). Az előzmények mérete, az ablak időtartama és a teljes eseményterhelés 2 bemeneti partíción belül változnak.

> [!Note]
> A pontosabb becslésérdekében testre szabhatja a mintákat a forgatókönyvnek megfelelően.

### <a name="identifying-bottlenecks"></a>A szűk keresztmetszetek azonosítása
Az Azure Stream Analytics-feladat Metrikák ablaktáblája segítségével azonosíthatja a folyamat szűk keresztmetszeteit. Tekintse át **a bemeneti/kimeneti eseményeket** az átviteli sebességhez és a ["Vízjel késleltetése"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) vagy **az eldugott eseményeket,** és nézze meg, hogy a feladat lépést tart-e a bemeneti sebességgel. Az Event Hub-metrikák esetében keresse **meg a szabályozott kérelmeket,** és ennek megfelelően módosítsa a küszöbértékegységeket. A Cosmos DB metrikák, tekintse át **a maximális felhasznált RU/s partíciókulcs-tartományban** az átviteli sebesség, győződjön meg arról, hogy a partíciókulcs-tartományok egyenletesen fogyasztva. Az Azure SQL DB esetén figyelheti **a Log IO** és a CPU **monitorát.**

## <a name="next-steps"></a>További lépések

* [Bevezetés az Azure Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)


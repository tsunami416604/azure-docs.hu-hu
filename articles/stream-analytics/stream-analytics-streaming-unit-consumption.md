---
title: Ismertetése és módosítása az Azure Stream Analytics szolgáltatásban a folyamatos átviteli egységek
description: Ez a cikk ismerteti a Streamelési egységek beállítás és más tényező befolyásolja a teljesítményt az Azure Stream Analytics szolgáltatásban.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2018
ms.openlocfilehash: b7abbd486e9c357a5bdba093214a3801f88c39ab
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45575898"
---
# <a name="understand-and-adjust-streaming-units"></a>Ismertetése és módosítása a folyamatos átviteli egységek

A számítási feladatok végrehajtásához lefoglalt erőforrások folyamatos átviteli egységek (su) jelöli. Minél nagyobb a szám, SUS-t, a további CPU és memória-erőforrások lefoglalva a feladatnak. Ez a kapacitás lehetővé teszi, hogy a lekérdezés logikára koncentrálhat, és felügyelnie a hardvert, hogy futtassa a Stream Analytics-feladat időben kivonatot.

Közel valós idejű adatfolyam-feldolgozás eléréséhez az Azure Stream Analytics-feladatok minden feldolgozás memória hajtsa végre. Kevés a memória, a folyamatos átviteli feladat sikertelen lesz. Ennek eredményeképpen egy éles feladat, fontos egy folyamatos átviteli feladat erőforrás-használat figyelése, és ellenőrizze, hogy nincs elegendő erőforrás le legyen foglalva, hogy a feladatok 24/7 rendszert.

Az SU % utilization metrika, amely 100 %-címtartományok a 0 %, a memóriát a számítási feladat ismerteti. A folyamatos átviteli feladatok minimális erőforrás-igényű Ez a metrika általában a 10-20 %-között. SU százalékos kihasználtsága alacsony, és a bemeneti események lekérése várakozó, valószínűleg a számítási feladathoz szükséges további számítási erőforrásokat, ami megköveteli, hogy ha több SUS-t. Legyen a legjobb, ha a SU metrika 80 % oldvolumesize alkalmanként adatforgalmi csúcsokhoz. A Microsoft javasolja, hogy egy riasztás beállítást 80 %-os SU Utilization metrika erőforrás-fogyási típus elkerülése érdekében. További információkért lásd: [oktatóanyag: Azure Stream Analytics-feladatok riasztásokat állíthat be](stream-analytics-set-up-alerts.md).

## <a name="configure-stream-analytics-streaming-units-sus"></a>Stream Analytics Streamelési egységek (su) konfigurálása
1. Jelentkezzen be a [Azure Portalon](http://portal.azure.com/)

2. Keresse meg a Stream Analytics-feladat, amelyet szeretne méretezni, és nyissa meg az erőforrások listájában. 

3. A feladat oldalát, az alatt a **konfigurálása** szakaszban kattintson **méretezési**. 

    ![Az Azure Stream Analytics feladat Portálkonfiguráció][img.stream.analytics.preview.portal.settings.scale]
    
4. A csúszka segítségével állítsa be a SUS-t a projekthez. Figyelje meg, hogy pedig csak adott SU-beállításait. 

## <a name="monitor-job-performance"></a>Feladatok teljesítményének monitorozása
Az Azure portal használatával, az átviteli feladat követheti nyomon:

![Az Azure Stream Analytics-feladatok figyelése][img.stream.analytics.monitor.job]

A várt átviteli sebességet a számítási feladatok számítja ki. Ha az átviteli sebességet a vártnál kevesebb, a bemeneti partíció hangolása, a lekérdezés beállításához és növekedtével a feladat.

## <a name="how-many-sus-are-required-for-a-job"></a>Hány SUs-feladat szükségesek?

Kiválasztása szükséges SUS-t egy adott feladat száma attól függ, hogy a bemeneti és a lekérdezés a feladaton belül definiált partíció konfigurációját. A **méretezési** lap lehetővé teszi a megfelelő számú SUS-t állítsa be. Ajánlott eljárás a szükségesnél több SUs lefoglalása. A Stream Analytics optimalizálja a késést és átviteli sebességet, illetve további memória cserébe.

Az ajánlott eljárás szerint általában a lekérdezések, amelyek nem használják 6 SUs történő indításához **PARTITION BY**. Majd vizsgálja meg a édes helyszíni egy próbálkozással, amelyben módosítása SUs száma után át mennyiségű adat, és vizsgálja meg az SU % Utilization metrika használatával. Egy Stream Analytics-feladat által használt streamelési egységek maximális száma attól függ, hogy a lekérdezésben, a feladat, illetve az egyes lépések a partíciók számának megadott lépéseket. További információ a korlátairól [Itt](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#calculate-the-maximum-streaming-units-of-a-job).

SUS-t a megfelelő számú kiválasztásával kapcsolatban további információkért tekintse meg ezt oldal: [Scale Azure Stream Analytics-feladatok az átviteli sebesség növelése](stream-analytics-scale-jobs.md)

> [!Note]
> Partíciókonfigurációjától szükség, az egy adott feladat a bemeneti partíció konfigurációját és a feladathoz meghatározott lekérdezéstől függ. Kiválaszthatja, SUS-t a kvóta egy feladat legfeljebb. Alapértelmezés szerint minden Azure-előfizetés az analytics-feladatok akár 200 SUs kvótával rendelkezik egy adott régióban. SUS-t az előfizetések mellett ez a kvóta növeléséhez forduljon [Support](http://support.microsoft.com). Feladatonként SUs érvényes értékei a következők 1, 3, 6, és legfeljebb 6-os léptékben.

## <a name="factors-that-increase-su-utilization"></a>Tényezőket, amelyek növelik a SU százalékos kihasználtsága 

A historikus (time-orientált) lekérdezés elemei a Stream Analytics által biztosított állapotalapú szereplők core készletét. Stream Analytics belsőleg a felhasználó nevében, ezeket a műveleteket állapotát kezeli a memóriát, a rugalmasság és a rendszerállapot-helyreállítás ellenőrzőpontok használata során szolgáltatásfrissítés kezelésével. Annak ellenére, hogy a Stream Analytics teljes körűen felügyeli az államok, számos ajánlásokat és tanácsokat, felhasználók figyelembe kell venni.

## <a name="stateful-query-logic-in-temporal-elements"></a>Állapot-nyilvántartó lekérdezés logikája historikus elemek
Az Azure Stream Analytics-feladat az egyedi képességét egyik állapot-nyilvántartó feldolgozó, például az ablakos összesítéseket, az időalapú illesztéseket és a historikus elemzési funkciók végrehajtásához. Ezen operátorok mindegyike megőrzi az állapotadatokat. Ezen lekérdezési elemek maximális ablak mérete hét nap. 

A historikus időszak fogalma a több Stream Analytics lekérdezési elemeket jelenik meg:
1. Ablakos összesítéseket: csoport által az Átfedésmentes, segítségével tehetjük meg, és a windows késleltetett

2. Időalapú illesztéseket: CSATLAKOZIK a DATEDIFF függvény

3. A historikus elemzési funkciók: ISFIRST utolsó és a LIMIT DURATION KÉSÉS

Az alábbi tényezők befolyásolják a használt memória (streamelési egységek metrika részben) által a Stream Analytics-feladatok:

## <a name="windowed-aggregates"></a>Ablakos összesítéseket
A használt memória (állapot mérete) az ablakos összesítő értéke nem mindig közvetlenül arányos az ablak mérete. Ehelyett a felhasznált memória arányos számosságát jelöli az adatok, vagy a csoportok az egyes időtartomány száma.


Például a következő lekérdezést, a szám társított `clusterid` számosságát jelöli a lekérdezést. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

Annak érdekében, hogy az előző lekérdezést a magas Számosság által okozott problémák enyhítése, elküldheti események Event Hubs segítségével `clusterid`, és a horizontális felskálázás a lekérdezés azáltal, hogy a rendszer feldolgozza az egyes bemeneti partíció külön **partíció ÁLTAL** az alábbi példában látható módon:

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

A rendszer több csoport között osztja el a lekérdezést a particionálása után. Hány eredményeként `clusterid` érkező minden egyes értékek csomópontokba csökken a csoport a számosságot kezelő. 

Event Hub-partíciókat a csoportosítási kulcsot ne legyen kevesebb lépéssel szükség szerint kell particionálni. További információkért lásd: [Event Hubs – áttekintés](../event-hubs/event-hubs-what-is-event-hubs.md). 

## <a name="temporal-joins"></a>A historikus illesztések
A historikus csatlakozzon (állapot mérete) felhasznált memória arányos az illesztés, amely a bemeneti gyakorisága vícekrát irányításához mozgassa hely mérete által a historikus irányításához mozgassa helyiségben események száma. Más szóval illesztések által felhasznált memória arányos az események átlagos száma szorozva DateDiff időtartomány.

A JOIN nem egyező események száma befolyásolja a lekérdezés a memóriahasználat. A következő lekérdezés a kattintásokat generáló oldalmegjelenéseket keresi:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

Ebben a példában is lehet, hogy sok jelennek meg, és néhány személy kattintson rá, és szükséges, hogy az összes esemény az időtartományban. A felhasznált memória arányos az ablak méretével és az események gyakoriságával. 

Ennek orvoslása érdekében események küldése az Event Hubs azáltal, hogy a rendszer által a join kulcsok (ebben az esetben azonosító), és a lekérdezés horizontális particionálása mindegyik külön bemeneti partíció feldolgozása **PARTITION BY** látható módon:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

A rendszer több csoport között osztja el a lekérdezést a particionálása után. Ennek eredményeképpen egyes érkező események száma csomópontokba csökken a állapotban tartani az illesztési ablakban méretét. 

## <a name="temporal-analytic-functions"></a>A historikus elemzési funkciók
Egy historikus analitikai függvény (állapot mérete) felhasznált memória arányos az események száma kettő többszörösének szerint idejére. Az analitikai függvények által felhasznált memória nem arányos az ablak mérete, de inkább particionálása az egyes időtartományban száma.

A szervizelés historikus való csatlakozás hasonlít. Ki lehet terjeszteni a lekérdezés használatával **PARTITION BY**. 

## <a name="out-of-order-buffer"></a>Üzemen kívüli puffer 
Felhasználói beállítható az üzemen kívüli puffer mérete, abban az esetben, ha rendezése a konfiguráció panelen. A puffer szolgál az ablak időtartamának bemenetek nyomvatartással és sorrendjét húzással módosíthatja. A puffer mérete arányos a bemeneti gyakorisága az üzemen kívüli ablak méretének által vícekrát. Az alapértelmezett ablak méretének értéke 0. 

Az üzemen kívüli puffer túlcsordulás javítása, horizontális felskálázás lekérdezés használatával **PARTITION BY**. A rendszer több csoport között osztja el a lekérdezést a particionálása után. Ennek eredményeképpen egyes érkező események száma csomópontokba csökken az egyes átrendezési puffer események száma. 

## <a name="input-partition-count"></a>A bemeneti partíciók száma 
Minden egyes bemeneti partíció a feladat bemeneti puffer rendelkezik. A bemeneti partíció nagyobb száma, a további erőforrás a feladat használ fel. Minden streamelési egység az Azure Stream Analytics nagyjából 1 MB/s bemeneti tud feldolgozni. Ezért optimalizálhatja összekapcsolja a Stream Analytics streamelési egységek száma az eseményközpont-számát. 

A feladat egy streamelési egységnek konfigurált általában egy Eseményközpontba két partícióval rendelkező (amely a legkisebb eseményközpont) elegendő. Ha az Event Hubs több partícióval rendelkezik, a Stream Analytics-feladat több erőforrást használ fel, de nem feltétlenül használ az Event Hub által biztosított további átviteli. 

A folyamatos átviteli egységek 6 feladatok 4 vagy 8 partíciók az eseményközpont szükség lehet. Azonban ne használjon túl sok felesleges partíció óta, ami túlzott erőforrás-használatot. Ha például egy Eseményközpontba 16 partícióval rendelkező vagy nagyobb a Stream Analytics-feladat, amely rendelkezik a folyamatos átviteli egység 1. 

## <a name="reference-data"></a>Referenciaadat 
Az ASA hivatkozási adatok gyors keresése a memóriába. A jelenlegi implementációja minden egyes join művelet referenciaadatok másolatot készít a referencia-adatok a memóriában, még akkor is, ha csatlakozik a ugyanaz a referenciaadatokat többször. A lekérdezések **PARTITION BY**, mindegyik partíció rendelkezik a referenciaadatok másolatát, így a partíciók teljesen leválasztott. A többszöröző hatással bír, a memóriahasználat gyorsan beszerezheti nagyon magas, ha csatlakozik a referenciaadatok többször, több partíción.  

### <a name="use-of-udf-functions"></a>Az UDF-függvények használatával
Amikor hozzáad egy UDF-függvény, az Azure Stream Analytics betölti a JavaScript futásidejű a memóriába. Ez hatással lesz az SU %.

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analyticsben párhuzamosítható lekérdezések létrehozása](stream-analytics-parallelization.md)
* [Átviteli sebesség növelése érdekében az Azure Stream Analytics-feladatok méretezése](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

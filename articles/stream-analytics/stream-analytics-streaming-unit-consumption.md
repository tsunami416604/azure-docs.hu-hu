---
title: Ismertetés és állítsa be az Azure Stream Analytics adatfolyam-egységek
description: Ez a cikk ismerteti a Streaming Units beállítás és az egyéb Azure Stream Analytics a teljesítményt befolyásoló tényezők.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2018
ms.openlocfilehash: c96e9825cddd0b60e67cd4752fab8f440ceaae76
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="understand-and-adjust-streaming-units"></a>Ismerje meg, és módosítsa a folyamatos átviteli egységek

Az Azure Stream Analytics összesíti a teljesítmény "weight" a futó feladat az adatfolyam-egységek (SUS-t). SUs határoz meg a feladat végrehajtásához felhasznált számítási erőforrások. A streamelési egységek segítségével leírható a relatív eseményfeldolgozási kapacitás a processzor, a memória, valamint az olvasási és írási sebesség összesített metrikája alapján. A kapacitás megadható, hogy a lekérdezés logika koncentrálhat, és időben feladat futtatásához a Stream Analytics hardver kezelése kell kivonatok.

Adatfolyam-feldolgozási kis késleltetésű eléréséhez Azure Stream Analytics-feladatok minden feldolgozás memória hajtsa végre. Ha kevés a memória, a folyamatos átviteli feladat sikertelen lesz. Ennek eredményeképpen termelési feladat, fontos a folyamatos átviteli feladatnak Erőforrás kihasználtsága figyelésére, és győződjön meg arról, hogy nincs elegendő erőforrás a 24 és Windows 7 rendszerben futó feladatok megőrzése érdekében lefoglalt.

A metrika beállításnak: 0 és 100 %-os százalékos több. A folyamatos átviteli feladat minimális tárhely a SU % kihasználtsági mérőszáma általában 10-20 %-között. Legyen a legjobb, ha a mérték, hogy figyelembe vegye az alkalmi igényeiben jelentkező 80 %. A Microsoft azt javasolja, hogy egy riasztás beállítást 80 % SU kihasználtsági metrika erőforrás-fogyási megelőzése érdekében. További információkért lásd: [oktatóanyag: Azure Stream Analytics-feladatok beállítása](stream-analytics-set-up-alerts.md).

## <a name="configure-stream-analytics-streaming-units-sus"></a>A Stream Analytics adatfolyam-egységek (SUs) konfigurálása
1. Jelentkezzen be [Azure-portálon](http://portal.azure.com/)

2. Az erőforrások listájához keresse meg a Stream Analytics-feladat, amely méretezhető, és nyissa meg. 

3. A feladatok lapon alatt a **konfigurálása** elemcsoportban válasszon **méretezési**. 

    ![Az Azure Stream Analytics feladat beállítása][img.stream.analytics.preview.portal.settings.scale]
    
4. A csúszka segítségével állítsa be a SUS-t, a feladathoz. Figyelje meg, hogy SU vonatkozó beállításokat. 

## <a name="monitor-job-performance"></a>Feladat teljesítmény figyelése
Az Azure portál használatával, nyomon követheti a feladat átviteli sebesség:

![Az Azure Stream Analytics feladatok figyelése][img.stream.analytics.monitor.job]

A várt munkaterhelések átviteli sebessége a kiszámításához. A teljesítményt, kisebb a vártnál, ha a bemeneti partíció hangolására, a lekérdezés beállításához, majd adja hozzá SUS-t a feladatot.

## <a name="how-many-sus-are-required-for-a-job"></a>Hány SUs szükségesek egy feladatot?

Kiválasztása szükséges SUS-t egy adott feladat száma attól függ, hogy a partíció konfigurációját a bemeneti és a lekérdezés, amely a feladat van meghatározva. A **méretezési** lap lehetővé teszi, hogy meg kell adnia a megfelelő számú SUS-t. Akkor célszerű foglaljon le több SUs, mint amennyi szükséges. A Stream Analytics program optimalizálja a késés és átviteli használ további memória lefoglalásakor.

Az ajánlott eljárás általában a lekérdezések, amelyek nem használják a 6 SUS-t használ első lépésként **PARTITION BY**. Majd megállapítja, hogy étkezési helyszíni egy módszerrel próbálkozást, amelyben módosítása SUs száma fázis reprezentatív adatmennyiséget, és vizsgálja meg a SU % kihasználtsági metrika után.

SUS-t a megfelelő számú kiválasztására vonatkozó további információkért lásd: ezen a lapon: [Scale Azure Stream Analytics-feladatok átviteli sebesség növelése](stream-analytics-scale-jobs.md)

> [!Note]
> Hány SUs kiválasztása kell megadni a bemeneti adatok partíció konfigurációját és a lekérdezés a feladathoz megadott egy adott feladat függ. Kiválaszthatja, akár egy feladat a SUS-t a kvótáját. Alapértelmezés szerint minden Azure-előfizetéssel rendelkezik a kvóta az analytics-feladatok 200 SUS egy adott régióban. Ez a kvóta túl az előfizetések SUs növeléséhez forduljon [Microsoft Support](http://support.microsoft.com). Érvényes SUs Feladatonkénti értékei 1, 3, 6, és a 6 lépésekben.

## <a name="factors-that-increase-su-utilization"></a>Tényezőket, amelyek növelik a SU kihasználtsága (%) 

A historikus (idő irányú) lekérdezés elemei a Stream Analytics által biztosított állapot-nyilvántartó operátorok sor. A Stream Analytics kezeli ezeket a műveleteket a felhasználó nevében, a belső állapotának való kezelésekor a rendszermemóriát, a rugalmasság és a rendszerállapot-helyreállítás ellenőrzőpontok frissítése során. Annak ellenére, hogy a Stream Analytics teljesen kezeli az állapotok, számos kapcsolatos bevált gyakorlatokat, felhasználók figyelembe kell venni.

## <a name="stateful-query-logic-in-temporal-elements"></a>Historikus elemek állapottartó lekérdezés a logikai
Az Azure Stream Analytics-feladat az egyedi képesség egyik állapot-nyilvántartó műveleteket, például az ablakos összesítéseket, időalapú illesztéseket és historikus analitikai függvények végrehajtásához. Ezen operátorok mindegyikének tartja az állapotadatokat. A lekérdezés egyikkel maximális ablak mérete hét nap. 

Az ideiglenes ablakot fogalma több Stream Analytics lekérdezési elemek jelenik meg:
1. Ablakos összesítéseket: csoport által az Átfedésmentes, Hopping, és a késleltetett windows

2. A historikus illesztések: CSATLAKOZIK a DATEDIFF függvény

3. A historikus analitikai függvények: ISFIRST, LAST és a LIMIT DURATION KÉSÉS

Az alábbi tényezők befolyásolják a használt memória (részben adatfolyam-egységek metrika) által Stream Analytics-feladatok:

## <a name="windowed-aggregates"></a>Ablakos összesítéseket
A használt memória (állapot mérete) az ablakos összesítő nincs mindig közvetlenül arányos az ablak mérete. Ehelyett a használt memória az adatok, vagy minden alkalommal ablakban csoportok száma számosságot arányos.


Például a következő lekérdezésben a szám társított `clusterid` van számossága alapján a lekérdezést. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

Ahhoz, hogy az előző lekérdezés nagy számosságot által okozott problémák javítását célzó, elküldheti események által particionálva Eseményközpont `clusterid`, és a lekérdezés azáltal, hogy a rendszer minden egyes külön bemeneti partíció feldolgozása kibővítési **partíció ÁLTAL** az alábbi példában látható módon:

   ```sql
   SELECT count(*) 
   FROM PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

A rendszer több csoport között osztja el a lekérdezést a particionálása után. Ennek eredményeképpen a száma `clusterid` minden csomópont beérkező értékek csökken ilyen módon csökkenti a csoport számossága operátor. 

Event Hub partíciók kell particionálni a csoportosítási gombot, hogy ne kelljen egy csökkentse a lépéshez. További információkért lásd: [Event Hubs – áttekintés](../event-hubs/event-hubs-what-is-event-hubs.md). 

## <a name="temporal-joins"></a>A historikus illesztések
A használt memória (állapot mérete) historikus illesztés azzal arányos, hogy az illesztés, amely bemeneti eseménygyakoriság többszörösének által irányításához mozgassa hely méretét historikus irányításához mozgassa helyiségben események száma. Más szóval az illesztések által használt memória azzal arányos, hogy a DateDiff időtartományát megszorozza események átlagos száma.

Az illesztés nem egyező események száma a lekérdezés memóriahasználata hatással. A következő lekérdezés a kattintásokat generáló oldalmegjelenéseket keresi:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

Ebben a példában az is lehet, hogy nagy mennyiségű ads látható és néhány személy kattintson rá, és megtartja az eseményeket a időszak szükséges. A felhasznált memória arányos az ablak méretével és az események gyakoriságával. 

Ez kijavítani, elküldje az eseményeket az Event Hubs particionálva illesztési kulcsok (azonosító: Ebben az esetben), és a lekérdezés kibővítési azáltal, hogy a rendszer minden egyes külön bemeneti partíció feldolgozása **PARTITION BY** látható módon:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

A rendszer több csoport között osztja el a lekérdezést a particionálása után. Ennek eredményeképpen minden csomópontjába érkező események száma csökken ilyen módon csökkenti a állapotban tartani az illesztés ablakban méretét. 

## <a name="temporal-analytic-functions"></a>A historikus analitikai függvények
A használt memória (állapot mérete) egy ideiglenes elemzési függvény az események gyakorisága arányos többszörösének időtartamának. Az analitikai függvények által használt memória nincs arányos az ablak mérete, de ahelyett, hogy partícióazonosító száma az egyes időszak.

A szervizelés historikus illesztési hasonlít. Ki lehet terjeszteni a lekérdezés segítségével **PARTITION BY**. 

## <a name="out-of-order-buffer"></a>Nem megfelelő sorrendben puffer 
Felhasználó beállíthatja úgy a nem megfelelő sorrendben puffer mérete, abban az esetben, ha rendelési konfigurációs ablaktáblán. A puffer bemenetek tartsa a az ablak időtartama, és újrarendezéshez szolgál. A puffer mérete azzal arányos, hogy a bemeneti eseménygyakoriság többszörösének által az üzemen kívüli ablak mérete. Az alapértelmezett ablak mérete 0. 

A nem megfelelő sorrendben puffer túlcsordulás kijavítani, terjessze ki a lekérdezés segítségével **PARTITION BY**. A rendszer több csoport között osztja el a lekérdezést a particionálása után. Ennek eredményeképpen minden csomópontjába érkező események száma csökken ilyen módon csökkenti az egyes újrarendelési pufferben lévő események száma. 

## <a name="input-partition-count"></a>Bemeneti partíciók száma 
Adjon meg egy feladat minden egyes bemeneti partíció puffer rendelkezik. A feladat használ fel, a nagyobb számú bemeneti partíciót, a több erőforrás. Adatfolyam-továbbítási tárolóegységekhez Azure Stream Analytics körülbelül 1 MB/s bemeneti tud feldolgozni. Ezért egyeztetésével adatfolyam-egységek az Eseményközpont a partíciók számát a Stream Analytics száma is optimalizálhatja. 

A feladat egy streamelési egységet konfigurált általában elegendő-e az Eseményközpontok két partíciókkal rendelkező (amely a minimális Event hub). Ha az Event Hubs további partíciókkal rendelkezik, a Stream Analytics-feladat további erőforrásokat használ fel, de nem feltétlenül használja az Eseményközpont által biztosított további átviteli sebesség. 

Adatfolyam-egységek 6-os feladat szükség lehet az eseményközpontból 4 vagy 8 partíciókat. Túl sok felesleges partíciók azonban ne, óta, ami túl sok erőforrás kihasználtsága. Például az Eseményközpontok 16 partíciókat vagy nagyobb a Stream Analytics-feladat, amelynek 1 streamelési egységet. 

## <a name="reference-data"></a>Referenciaadat 
Az ASA referenciaadatok gyors keresési a memóriába. Az aktuális megvalósításáról referenciaadatok illesztési műveletben másolatot készít azokról a referenciaadatok a memóriában, még akkor is, ha csatlakozik a hivatkozás ugyanazokat az adatokat több alkalommal. A lekérdezések **PARTITION BY**, mindegyik partíció rendelkezik egy példányát a referenciaadatok úgy, hogy a partíciók teljesen leválasztott. A többszöröző kezdve memóriahasználata gyorsan megjeleníthet nagyon magas, ha csatlakozik a referenciaadatok több partícióval rendelkező több alkalommal.  

### <a name="use-of-udf-functions"></a>Az UDF-funkciók használata
Amikor egy olyan UDF függvényt, Azure Stream Analytics a JavaScript futásidejű a memóriába tölti be. Ez milyen hatással van a SU %.

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics párhuzamosítható lekérdezések létrehozása](stream-analytics-parallelization.md)
* [Átviteli sebesség növelése az Azure Stream Analytics-feladatok méretezése](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

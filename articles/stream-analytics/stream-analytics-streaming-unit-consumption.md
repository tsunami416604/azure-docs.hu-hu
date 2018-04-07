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
ms.date: 04/20/2017
ms.openlocfilehash: ede0c0aa7b0e795760123246366f947889224b2d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="understand-and-adjust-streaming-units"></a>Ismerje meg, és módosítsa a folyamatos átviteli egységek

Az Azure Stream Analytics összesíti a teljesítmény "weight" a futó feladat az adatfolyam-egységek (SUS-t). SUs határoz meg a feladat végrehajtásához felhasznált számítási erőforrások. A streamelési egységek segítségével leírható a relatív eseményfeldolgozási kapacitás a processzor, a memória, valamint az olvasási és írási sebesség összesített metrikája alapján. Ez a lekérdezés programot, és eltávolítja, nem szükséges tudni, hogy tárolási réteg teljesítménnyel kapcsolatos szempontok memóriát lefoglalni a feladat manuális és a core-processzorszám hozzávetőleges időben a feladat futtatásához szükséges koncentrálhat kapacitás lehetővé.

Adatfolyam-feldolgozási kis késleltetésű eléréséhez Azure Stream Analytics-feladatok minden feldolgozás memória hajtsa végre. Ha kevés a memória, a folyamatos átviteli feladat sikertelen lesz. Ennek eredményeképpen termelési feladat, fontos a folyamatos átviteli feladatnak Erőforrás kihasználtsága figyelésére, és győződjön meg arról, hogy nincs elegendő erőforrás a 24 és Windows 7 rendszerben futó feladatok megőrzése érdekében lefoglalt.

A metrika beállításnak: 0 és 100 %-os százalékos több. A folyamatos átviteli feladat minimális tárhely a SU % kihasználtsági mérőszáma általában 10-20 %-között. Legyen a legjobb, ha a mérték, hogy figyelembe vegye az alkalmi igényeiben jelentkező 80 %.  A metrika riasztást állíthat be (lásd: [metrika riasztások beállítása itt](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal)).



## <a name="configure-stream-analytics-streaming-units-sus"></a>A Stream Analytics adatfolyam-egységek (SUs) konfigurálása
1. Jelentkezzen be [Azure-portálon](http://portal.azure.com/)
2. Az erőforrások listájához keresse meg a Stream Analytics-feladat, amely méretezhető, és nyissa meg. 
3. Kattintson a feladatok lapján, konfigurálása, **méretezési**. 

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



## <a name="factors-increasing-su-utilization"></a>SU kihasználtság százalékos arányát növelése tényezők 
### <a name="stateful-query-logic"></a>Állapot-nyilvántartó lekérdezés logika 
Az Azure Stream Analytics-feladat az egyedi képesség egyik állapot-nyilvántartó műveleteket, például az ablakos összesítéseket, időalapú illesztéseket és historikus analitikai függvények végrehajtásához. Ezen operátorok mindegyikének tartja az egyes állapotok. 
#### <a name="windowed-aggregates"></a>Ablakos összesítéseket
Az ablakos összesítő állapot mérete (cardinality) csoportok a csoport üzemeltető számával arányos. Például a következő lekérdezésben a clusterid társított értéke számossága alapján a lekérdezést. 

    SELECT count(*)
    FROM input 
    GROUP BY  clusterid, tumblingwindow (minutes, 5)


Ahhoz, hogy az előző lekérdezés nagy számosságot által okozott problémák javítását célzó, elküldheti események Eseményközpont particionálva "clusterid" és a lekérdezés kibővítési azáltal, hogy a rendszer minden egyes külön bemeneti partíció feldolgozása **PARTITION BY**  az alábbi példában látható módon:


    SELECT count(*) 
    FROM PARTITION BY PartitionId
    GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)

A rendszer több csoport között osztja el a lekérdezést a particionálása után. Ennek eredményeképpen minden csomópont beérkező clusterid száma csökken ilyen módon csökkenti a csoport számossága operátor. 

Event Hub partíciók kell particionálni a csoportosítási gombot, hogy ne kelljen egy csökkentse a lépéshez. További részleteket ismertetnek [Itt](https://docs.microsoft.com/azure/event-hubs/event-hubs-overview). 
#### <a name="temporal-join"></a>A historikus illesztés
A historikus csatlakozzon állapot mérete azzal arányos, hogy az illesztés, amely bemeneti eseménygyakoriság többszörösének által irányításához mozgassa hely méretét historikus irányításához mozgassa helyiségben események száma. 

Az illesztés nem egyező események száma a lekérdezés memóriahasználata hatással. A következő lekérdezés a kattintásokat generáló oldalmegjelenéseket keresi:

    SELECT clicks.id
    FROM clicks 
    INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.

Ebben a példában az is lehet, hogy nagy mennyiségű ads látható és néhány személy kattintson rá, és megtartja az eseményeket a időszak szükséges. A felhasznált memória arányos az ablak méretével és az események gyakoriságával. 

Ez kijavítani, elküldje az eseményeket az Event Hubs particionálva illesztési kulcsok (azonosító: Ebben az esetben), és a lekérdezés kibővítési azáltal, hogy a rendszer minden egyes külön bemeneti partíció feldolgozása **PARTITION BY** látható módon:

    SELECT clicks.id
    FROM clicks PARTITION BY PartitionId
    INNER JOIN impressions PARTITION BY PartitionId 
    ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
</code>

A rendszer több csoport között osztja el a lekérdezést a particionálása után. Ennek eredményeképpen minden csomópontjába érkező események száma csökken ilyen módon csökkenti a állapotban tartani az illesztés ablakban méretét. 
#### <a name="temporal-analytic-function"></a>A historikus analitikai függvényhívás
Az állapot egy ideiglenes analitikai függvényhívás mérete azzal arányos, hogy az események gyakorisága többszörösének időtartamának. A szervizelés historikus illesztési hasonlít. Ki lehet terjeszteni a lekérdezés segítségével **PARTITION BY**. 

### <a name="out-of-order-buffer"></a>Nem megfelelő sorrendben puffer 
Felhasználó beállíthatja úgy a nem megfelelő sorrendben puffer mérete, abban az esetben, ha rendelési konfigurációs ablaktáblán. A puffer bemenetek tartsa a az ablak időtartama, és újrarendezéshez szolgál. A puffer mérete azzal arányos, hogy a bemeneti eseménygyakoriság többszörösének által az üzemen kívüli ablak mérete. Az alapértelmezett ablak mérete 0. 

Ez kijavítani, terjessze ki a lekérdezés segítségével **PARTITION BY**. A rendszer több csoport között osztja el a lekérdezést a particionálása után. A találatok között az egyes csomópontok érkező események száma csökken ilyen módon csökkenti az egyes újrarendelési pufferben lévő események száma. 

### <a name="input-partition-count"></a>Bemeneti partíciók száma 
Adjon meg egy feladat minden egyes bemeneti partíció puffer rendelkezik. A feladat használ fel, a nagyobb számú bemeneti partíciót, a több erőforrás. Az egyes SU Azure Stream Analytics képes a körülbelül 1 MB/s bemeneti, ezért érdemes ASA SU számú partíció számát az Eseményközpont. 1SU feladat általában elegendő-e az Eseményközpontok a 2 partíció (amely a minimális Event hub). Ha az Event Hubs további partíciókkal rendelkezik, az ASA munkahelye további erőforrásokat használ fel, de nem feltétlenül használja az Eseményközpont által biztosított további átviteli sebesség. 6SU feladat szükség lehet az eseményközpontból 4 vagy 8 partíciókat. Az Eseményközpontok 16 partíciókkal rendelkező vagy használatával nagyobb egy 1SU a feladat gyakran túl sok erőforrás kihasználtsága hozzájárul, és el kell kerülni. 

### <a name="reference-data"></a>Referenciaadat 
Az ASA referenciaadatok gyors keresési a memóriába. Az aktuális megvalósításáról referenciaadatok illesztési műveletben másolatot készít azokról a referenciaadatok a memóriában, még akkor is, ha csatlakozik a hivatkozás ugyanazokat az adatokat több alkalommal. A lekérdezések **PARTITION BY**, mindegyik partíció rendelkezik egy példányát a referenciaadatok úgy, hogy a partíciók teljesen leválasztott. A többszöröző kezdve memóriahasználata gyorsan megjeleníthet nagyon magas, ha csatlakozik a referenciaadatok több partícióval rendelkező több alkalommal.  

#### <a name="use-of-udf-functions"></a>Az UDF-funkciók használata
Amikor egy olyan UDF függvényt, Azure Stream Analytics a JavaScript futásidejű a memóriába tölti be. Ez milyen hatással van a SU %.


## <a name="get-help"></a>Segítségkérés
Ha további segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics párhuzamosítható lekérdezések létrehozása](stream-analytics-parallelization.md)
* [Átviteli sebesség növelése az Azure Stream Analytics-feladatok méretezése](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

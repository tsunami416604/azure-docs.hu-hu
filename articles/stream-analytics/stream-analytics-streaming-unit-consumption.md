---
title: Streamelési egységek az Azure Stream Analytics szolgáltatásban
description: Ez a cikk ismerteti a streamelési egységek beállítás át, és egyéb tényezők, amelyek hatással vannak a teljesítmény az Azure Stream Analytics.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 397e455c8b6a1097e2a32473036e1acd2bbdf2eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267351"
---
# <a name="understand-and-adjust-streaming-units"></a>A streamelési egységek ismertetése és módosítása

A streamelési egységek (SUs) a Stream Analytics-feladat végrehajtásához lefoglalt számítási erőforrásokat jelölik. Minél magasabb az SU-k száma, annál több processzor- és memória-erőforrás van lefoglalva a feladathoz. Ez a kapacitás lehetővé teszi, hogy összpontosítson a lekérdezési logika és absztrakt, hogy kezelni kell a hardver futtatásához a Stream Analytics-feladat időben.

A kis késésű streamfeldolgozás érdekében az Azure Stream Analytics-feladatok minden feldolgozást a memóriában hajtanak végre. Ha elfogy a memória, a streamelési feladat sikertelen lesz. Ennek eredményeképpen egy éles feladat, fontos, hogy figyelje a streamelési feladat erőforrás-használat, és győződjön meg arról, hogy elegendő erőforrás van lefoglalva ahhoz, hogy a feladatok fut nak 24/7.

Az SU % kihasználtsági metrika, amely 0% és 100% között mozog, a számítási feladatok memóriafelhasználását írja le. A minimális helyigényű streamelési feladat hoz, ez a metrika általában 10% és 20% között van. Ha az SU%-használat alacsony, és a bemeneti események eldugulnak, a számítási feladatok valószínűleg több számítási erőforrást igényelnek, ami megköveteli az SUs-ok számának növelését. A legjobb, ha az SU metrika alatt 80%, hogy figyelembe alkalmi tüskék. A Microsoft azt javasolja, hogy az erőforrások kimerülésének megelőzése érdekében 80%-os SU-kihasználtsági metrika riasztást. További információ: [Oktatóanyag: Riasztások beállítása az Azure Stream Analytics-feladatokhoz.](stream-analytics-set-up-alerts.md)

## <a name="configure-stream-analytics-streaming-units-sus"></a>Stream Analytics streamelési egységek (SUs) konfigurálása
1. Bejelentkezés az [Azure Portalra](https://portal.azure.com/)

2. Az erőforrások listájában keresse meg a skálázni kívánt Stream Analytics-feladatot, majd nyissa meg. 

3. A feladatlap **Konfigurálás a** címsoralatt válassza a **Méretezés lehetőséget.** 

    ![Azure portal Stream Analytics feladatkonfigurációja][img.stream.analytics.preview.portal.settings.scale]
    
4. A csúszka segítségével állítsa be a feladat SUs.Use the slider to set the SUs for the job. Figyelje meg, hogy csak bizonyos SU-beállításokat. 
5. Módosíthatja a feladathoz rendelt SUs-ok számát, még akkor is, ha fut. Ez nem lehetséges, ha a feladat [nem particionált kimenetet](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#query-using-non-partitioned-output) használ, vagy [többlépéses lekérdezést használ különböző PARTITION BY értékekkel.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#multi-step-query-with-different-partition-by-values) Lehet, hogy csak a választás egy sor SU értékek, amikor a feladat fut. 

## <a name="monitor-job-performance"></a>Feladatteljesítményének figyelése
Az Azure Portal használatával nyomon követheti egy feladat átviteli:

![Az Azure Stream Analytics figyelőfeladatai][img.stream.analytics.monitor.job]

Számítsa ki a számítási feladatok várható átviteli átadóját. Ha az átviteli hang nem a vártnál, állítsa be a bemeneti partíciót, hangolja be a lekérdezést, és adja hozzá a SUs-t a feladathoz.

## <a name="how-many-sus-are-required-for-a-job"></a>Hány SU-ra van szükség egy feladathoz?

Az adott feladathoz szükséges SUs-ok számának kiválasztása a bemenetek partíciókonfigurációjától és a feladaton belül definiált lekérdezéstől függ. A **Méretezés** lap lehetővé teszi a megfelelő számú SUs beállítását. Ajánlott a szükségesnél több SUS-t kiosztani. A Stream Analytics feldolgozómotor optimalizálja a késés és az átviteli kapacitás rovására további memória lefoglalása.

Az ajánlott eljárás általában 6 SUs-szal kezdődik a **PARTITION**BY-t nem használó lekérdezésekhez. Ezután határozza meg az édes folt ot egy próba- és hibamódszerrel, amelyben módosítja az SUs-ok számát, miután reprezentatív mennyiségű adatot adott át, és megvizsgálja az SU%-kihasználtsági metrikát. A Stream Analytics-feladat által használható streamelési egységek maximális száma a feladathoz definiált lekérdezésben megadott lépések számától és az egyes lépésekben lévő partíciók számától függ. A korlátokról [itt](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#calculate-the-maximum-streaming-units-of-a-job)olvashat bővebben.

A megfelelő számú SUs kiválasztásáról ezen az oldalon talál: [Az Azure Stream Analytics-feladatok méretezése az átviteli forgalom növelése érdekében](stream-analytics-scale-jobs.md)

> [!Note]
> Az adott feladathoz szükséges sus-ok kiválasztása a bemenetek partíciókonfigurációjától és a feladathoz definiált lekérdezéstől függ. A kvótát a sus-ban választhatja ki egy feladathoz. Alapértelmezés szerint minden Azure-előfizetés legfeljebb 500 SUs kvótával rendelkezik egy adott régió összes elemzési feladatához. Ha a kvótán túl is növelni szeretné az előfizetések SUs-számát, forduljon a [Microsoft támogatási szolgálatához.](https://support.microsoft.com) A SUs érvényes értékei feladatonként: 1, 3, 6 és legfeljebb 6.

## <a name="factors-that-increase-su-utilization"></a>A streamelési egységek százalékos kihasználtságát növelő tényezők 

A temporális (időorientált) lekérdezési elemek a Stream Analytics által biztosított állapotalapú operátorok alapvető készletei. A Stream Analytics a felhasználó nevében belsőleg kezeli ezeknek a műveleteknek az állapotát a memóriafelhasználás, a rugalmasságellenőrzőpontok és az állapot-helyreállítás kezelése révén a szolgáltatás frissítésekén. Annak ellenére, hogy a Stream Analytics teljes mértékben kezeli az államokat, számos ajánlott eljárásra vonatkozó javaslat van, amelyeket a felhasználóknak figyelembe kell venniük.

Vegye figyelembe, hogy egy feladat összetett lekérdezési logikával magas SU%kihasználtságú lehet, még akkor is, ha nem folyamatosan fogad bemeneti eseményeket. Ez a bemeneti és kimeneti események hirtelen kiugrása után fordulhat elő. A feladat továbbra is fenntarthatja a memóriában lévő állapotot, ha a lekérdezés összetett.

SU% kihasználtság a hirtelen csepp -hoz 0 részére egy rövid időköz előtt jövő hát -hoz várt szintek. Ez átmeneti hibák vagy a rendszer által kezdeményezett frissítések miatt következik be. Egy feladat streamelési egységeinek számának növelése nem csökkentheti az SU% kihasználtságot, ha a lekérdezés nem [teljesen párhuzamos.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization)

Egy adott időszak kihasználtságának összehasonlításakor használja az [eseménysebesség-mérőszámokat.](stream-analytics-monitoring.md) Az InputEvents és a OutputEvents metrikák azt mutatják, hogy hány eseményt olvasott be és dolgozott fel. Vannak olyan metrikák, amelyek a hibaesemények számát is jelzik, például a deszerializálási hibákat. Ha az események száma egy időben egység növekszik, SU% növekszik a legtöbb esetben.

## <a name="stateful-query-logicin-temporal-elements"></a>Állapotalapú lekérdezési logika időbeli elemekben
Az Azure Stream Analytics-feladat egyik egyedülálló képessége az állapotalapú feldolgozás, például ablakos aggregátumok, időbeli illesztések és időbeli analitikus függvények végrehajtása. Ezen operátorok mindegyike megőrzi az állapotinformációkat.A lekérdezési elemek maximális ablakmérete hét nap. 

A temporális ablak koncepciója több Stream Analytics-lekérdezési elemben jelenik meg:
1. Ablakos aggregátumok: GROUP BY Of Tumbling, Hopping, and Sliding ablakok

2. Időbeli illesztések: JOIN with DATEDIFF függvény

3. Időbeli analitikus függvények: ISFIRST, LAST és HACS LIMIT IDŐTARTAM-szal

A következő tényezők befolyásolják a Stream Analytics-feladatok által használt memóriát (a streamelési egységek metrikus részét):

## <a name="windowed-aggregates"></a>Ablakos aggregátumok
Az ablakos összesítések felhasznált memóriája (állapotmérete) nem mindig arányos az ablak méretével. Ehelyett a felhasznált memória arányos az adatok számosságával vagy az egyes időablakban lévő csoportok számával.


A következő lekérdezésben például a `clusterid` társított szám a lekérdezés számossága. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

Az előző lekérdezés magas számossága által okozott problémák enyhítése érdekében eseményeket `clusterid`küldhet a használatával particionált Event Hub szolgáltatásba, és horizontálisfelskálázhatja a lekérdezést, mivel lehetővé teszi a rendszer számára, hogy minden bemeneti partíciót külön dolgozzon fel a **PARTITION BY** használatával, amint az az alábbi példában látható:

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

A rendszer több csoport között osztja el a lekérdezést a particionálása után. Ennek eredményeképpen az `clusterid` egyes csomópontokba érkező értékek száma csökken, ezáltal csökkentve a csoport számosságát operátoronként. 

Az Event Hub-partíciókat a csoportosítási kulcsnak particionálnia kell, hogy ne kelljen csökkenteni a lépést. További információt az [Eseményközpontok – áttekintés című témakörben talál.](../event-hubs/event-hubs-what-is-event-hubs.md) 

## <a name="temporal-joins"></a>Időbeli csatlakozik
Az időbeli illesztés felhasznált memóriája (állapotmérete) arányos az illesztés időbeli kígyózó helyiségében lévő események számával, ami az esemény bemeneti arányának és a kígyódíj méretének szorzata. Más szóval az illesztések által felhasznált memória arányos a DateDiff időtartomány és az átlagos eseménysebesség szorzatával.

Az illesztésben lévő nem egyező események száma befolyásolja a lekérdezés memóriakihasználtságát. A következő lekérdezés a kattintásokat generáló oldalmegjelenéseket keresi:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

Ebben a példában lehetséges, hogy sok hirdetés jelenik meg, és kevesen kattintanak rá, és az összes eseményt az időablakban kell tartani. A felhasznált memória arányos az ablak méretével és az események gyakoriságával. 

Ennek megoldásához küldjön eseményeket az event hubpartisított az illesztési kulcsok (azonosító ebben az esetben), és horizontális felskálázás a lekérdezést azáltal, hogy lehetővé teszi a rendszer számára, hogy dolgozza fel az egyes bemeneti partíciókat külön-külön **a PARTITION BY** használatával, ahogy az ábrán látható:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

A rendszer több csoport között osztja el a lekérdezést a particionálása után. Ennek eredményeképpen az egyes csomópontokba érkező események száma csökken, ezáltal csökkentve az illesztési ablakban tárolt állapot méretét. 

## <a name="temporal-analytic-functions"></a>Temporális analitikus függvények
Az időbeli analitikus függvény felhasznált memóriája (állapotmérete) arányos az eseménysebesség és az időtartam szorzatával.Az analitikus függvények által felhasznált memória nem arányos az ablak méretével, hanem a partíciók száma minden alkalommal.

A szervizelés hasonló az időbeli illesztéshez. A lekérdezést a **PARTITION BY**használatával méretezheti ki. 

## <a name="out-of-order-buffer"></a>Nem működik a puffer 
A felhasználó beállíthatja a nem sorrendben lévő puffer méretét az Eseményrendezés konfigurációs ablaktábláján. A puffer az ablak időtartama alatt a bemenetek tárolására és átrendezésére szolgál. A puffer mérete arányos az esemény bemeneti sebességének szorzatával a nem sorrendben lévő ablak méretével. Az alapértelmezett ablakméret 0. 

A nem sorrendben lévő puffer túlcsordulásának elhárításához horizontálisra kell használni a lekérdezést a **PARTITION BY**használatával. A rendszer több csoport között osztja el a lekérdezést a particionálása után. Ennek eredményeképpen az egyes csomópontokba érkező események száma csökken, ezáltal csökkentve az egyes újrarendelési pufferekben lévő események számát. 

## <a name="input-partition-count"></a>Bemeneti partíciók száma 
A feladatbemenet minden bemeneti partíciója rendelkezik pufferrel. Minél nagyobb a bemeneti partíciók száma, annál több erőforrást használ fel a feladat. Az Azure Stream Analytics nagyjából 1 MB/s bemenetet tud feldolgozni. Ezért optimalizálhatja a Stream Analytics streamelési egységek számát az Event Hub partícióinak számával. 

Általában egy feladat konfigurált egy streamelési egység elegendő egy event hub két partícióval (amely a minimális Event Hub). Ha az Event Hub több partíciót, a Stream Analytics-feladat több erőforrást használ fel, de nem feltétlenül használja az Event Hub által biztosított extra átviteli. 

Egy feladat 6 streamelési egységek, előfordulhat, hogy 4 vagy 8 partíciót az Event Hub. Kerülje azonban a túl sok felesleges partíciót, mivel ez túlzott erőforrás-használatot okoz. Például egy Event Hub 16 partícióval vagy nagyobb egy Stream Analytics-feladat, amely 1 streamelési egység. 

## <a name="reference-data"></a>Referenciaadatok 
Az ASA referenciaadatai betöltődnek a memóriába a gyors keresés hez. Az aktuális implementációval minden illesztési művelet referenciaadatokkal megőrzi a referenciaadatok másolatát a memóriában, még akkor is, ha ugyanazt a referenciaadatokat többször is csatlakoztatja. A **PARTITION BY-vel**rendelkező lekérdezések esetében minden partíció rendelkezik a referenciaadatok másolatával, így a partíciók teljesen levannak választva. A multiplikátor effektussal a memóriahasználat gyorsan nagyon magasra léphet, ha több partícióval többször csatlakozik referenciaadatokkal.  

### <a name="use-of-udf-functions"></a>UDF-függvények használata
UDF-függvény hozzáadásakor az Azure Stream Analytics betölti a JavaScript-futásidejű a memóriába. Ez hatással lesz az SU%.

## <a name="next-steps"></a>További lépések
* [Párhuzamosítható lekérdezések létrehozása az Azure Stream Analytics szolgáltatásban](stream-analytics-parallelization.md)
* [Az Azure Stream Analytics-feladatok méretezése az átviteli forgalom növelése érdekében](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

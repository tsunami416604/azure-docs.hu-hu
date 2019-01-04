---
title: És skálázás az Azure Stream Analytics-feladatok
description: Ez a cikk azt ismerteti, hogyan méretezzünk át egy Stream Analytics-feladat a bemeneti adatok particionálása, a lekérdezést, és folyamatos átviteli egységek feladat beállítása.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: f4307da2e74846507cafb9f767a6ccae855e42a2
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53554673"
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>Azure Stream Analytics-feladat, növelheti a teljesítményt méretezése
Ez a cikk bemutatja, hogyan finomhangolása egy Stream Analytics-lekérdezés növeléséhez a Streaming Analytics-feladatokhoz. Ez az útmutató segítségével méretezheti a feladatot, amely magasabb terhelés kezeléséhez, és több rendszererőforrást (például a nagyobb sávszélességet, több Processzor-erőforrások, több memória) előnyeit.
Egy előfeltétel szükség lehet az alábbi cikkekben:
-   [A streamelési egységek ismertetése és módosítása](stream-analytics-streaming-unit-consumption.md)
-   [Párhuzamosítható feladatok létrehozása](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>1. – a lekérdezés funkcióban természetüknél fogva teljes párhuzamosítható bemeneti partíciók között
Ha a bemeneti partíciók között a lekérdezés teljes természetüknél fogva párhuzamosítható, követheti a következő lépéseket:
1.  A lekérdezés kell zavaróan párhuzamos használatával hozhat létre **PARTITION BY** kulcsszót. További információkért keresse fel a zavaróan párhuzamos feladatok területén [ezen az oldalon](stream-analytics-parallelization.md).
2.  A lekérdezésben használt kimeneti típusokat, attól függően néhány kimeneti előfordulhat, hogy vagy nem lehet párhuzamosítható, vagy további konfiguráció zavaróan párhuzamos van szüksége. Ha például SQL, az SQL dw-vel és a Power bi kimenetek nem, párhuzamosítható. A kimeneti fogadónak küldése előtt mindig egyesítésekor kimenetek. Blobok, táblák, ADLS, Service Bus és Azure-függvény automatikus méretezésnek megfelelően. Cosmos DB és az Eseményközpont kell rendelkeznie a PartitionKey konfiguráció beállítva, hogy az egyezzen a **PARTITION BY** (általában PartitionId) mezőt. Az Eseményközpontok felé is figyelmet extra az összes bemenetet és felett partíciók között elkerülése érdekében minden kimenetet a partíciók számának megfelelően. 
3.  A lekérdezés futtatása **6 SU** (azaz a teljes kapacitását egyetlen számítási csomópont) mérésére maximális elérhető átviteli sebességet, és használatakor **GROUP BY**, mérésére (cardinality) a feladat hány csoportokat is kezelni. Általános jelenség, a rendszer erőforráskorlátok szerezze meg a feladat a következők.
    - SU % utilization metrika több mint 80 %-át. Ez azt jelzi, hogy memóriahasználata magas. Ez a metrika növekedése hozzájáruló tényezők leírt [Itt](stream-analytics-streaming-unit-consumption.md). 
    -   Kimeneti időbélyeg megállapodást valós idő elmaradásban van. Attól függően, a lekérdezés logikáját a kimeneti időbélyeg előfordulhat, hogy egy logikai értékű eltolás a valós idő. Azonban azok kell halad nagyjából azonos mellett. Ha a kimeneti időbélyeg további elmaradásban van, és további mögött, egy jelző, a rendszer overworking van. Alsóbb rétegbeli kimeneti fogadó szabályozási, és a magas CPU-kihasználtság eredménye lehet. Nem biztosítunk CPU utilization metrika jelenleg, így nehéz megkülönböztetni a két lehet.
        - Ha a probléma fogadó szabályozás miatt, szükség lehet a kimeneti partíciók számának növelése (és is adjon meg, hogy a feladat teljes mértékben párhuzamosítható partíciók), vagy növelje az erőforrások, a fogadó (például a cosmosdb-hez a Kérelemegységek számát).
    - Feladatábra, van egy metrikánként partíció várakozó események minden egyes bemenet. A várakozó események metrika is növekszik, ha is azt jelzi, hogy a rendszererőforrás által korlátozott (vagy kimeneti fogadó szabályozás vagy a magas CPU miatt).
4.  Miután eldöntötte 6 SU feladat elérheti korlátozásait, meg is extrapolálja költségráfordításokkal egyenes arányban a feldolgozási kapacitás, a feladat, amikor több SUS-t, feltéve, hogy nem kell semmilyen adatot döntés, amely lehetővé teszi bizonyos partíció "Forró".

> [!NOTE]
> Válassza ki a folyamatos átviteli egységek megfelelő számát: Stream Analytics egy feldolgozó csomópont hozzáadott 6 su hoz létre, mert a legcélszerűbb, hogy a bemeneti partíciók száma osztója a csomópontok számát, a partíciók egyenlően elosztott csomópontjai között.
> A 6 mért például 4 SU feladat érhető el, 4 MB/s feldolgozási sebesség és a bemeneti partíciók száma. Kiválaszthatja, hogy a feladat futtatásához 12 SU körülbelül 8 MB/s-feldolgozási sebesség eléréséhez, vagy 24 SU elérése érdekében 16 MB/s. Ezután eldöntheti, mikor növelje az SU milyen érték, a feladat a bemeneti forgalom továbbítása.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>2. Ha a lekérdezés nem zavaróan párhuzamos eset.
Ha a lekérdezés nem zavaróan párhuzamos, követheti a következő lépéseket.
1.  Nem a lekérdezés kezdő **PARTITION BY** először a elkerülése érdekében a particionálás összetettségét, és futtassa a lekérdezés 6 SU és mérhető, mint a maximális terhelés [1. eset](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  A várható terhelés átviteli távon érhet el, ha az elkészült. Azt is megteheti választhatja mérésére SU és 1 SU, a SU, a forgatókönyv esetén használható minimális száma: 3 futó ugyanazt a feladatot.
3.  Ha a kívánt átviteli sebesség nem érhető el, próbálja meg a lekérdezés felosztása több lépést, ha lehetséges, ha nem több lépést már rendelkezik, és legfeljebb 6 SU lefoglalni az egyes lépések a lekérdezésben. Például ha 3 lépést foglal le a "Méretezés" lehetőséget a 18 SU rendelkezik.
4.  Ilyen feladatok futtatásakor Stream Analytics használatával az egyes lépések a saját dedikált 6 SU-erőforrások-csomópont. 
5.  Ha Ön továbbra is még nem érhető el a betöltési célt, próbál használni **PARTITION BY** kezdve a lépések közelebb a bemenetet. A **GROUP BY** operátor szerinti szűrése, amely nem lehet természetes módon particionálható, használhatja a helyi és globális összesített minta végrehajtásához egy particionált **GROUP BY** követ egy nem particionált **CSOPORTOSÍTÁSI szempont** . Például ha meg szeretné számolni hány autók áthaladna egyes díjmentesen érzékelőadatainak át 3 percenként, és az adatok mennyisége túl mi van tudja kezelni 6 SU.

Lekérdezés:

 ```SQL
 WITH Step1 AS (
 SELECT COUNT(*) AS Count, TollBoothId, PartitionId
 FROM Input1 Partition By PartitionId
 GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
 )
 SELECT SUM(Count) AS Count, TollBoothId
 FROM Step1
 GROUP BY TumblingWindow(minute, 3), TollBoothId
 ```
A fenti lekérdezés díjmentesen érzékelőadatainak partíciónként / autók számbavételi, és majd adja hozzá a szám az összes partíció együtt.

Miután particionálva, a lépés minden partíció esetében legfeljebb 6 SU, mindegyik partíció 6 kellene lefoglalni SU a maximális, így az egyes partíciók elhelyezhető a saját feldolgozó csomóponton.

> [!Note]
> Ha a lekérdezés nem lehet particionálni, további további SU több lépést lekérdezésben előfordulhat, hogy mindig növelhető az átviteli sebesség. Teljesítmény eléréséhez egyik módja, hogy minimalizálható a kezdeti lépések, helyi és globális összesített mintát, használja a fenti 5. lépésében leírtak szerint.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>3. eset független lekérdezések rengeteg fut egy feladat.
Az egyes Szoftverszállítói használati esetek, ahol a szolgáltatás több költséghatékony több bérlő egyetlen feladatban adatok feldolgozásához, minden bérlő, külön bemeneti és kimeneti-fordulhatnak elő még néhány (például 20) futó független lekérdezések egyetlen feladatban. Feltételezzük, minden egyes ilyen segédlekérdezés terhelés viszonylag kis. Ebben az esetben is kövesse az alábbi lépéseket.
1.  Ebben az esetben ne használjon **PARTITION BY** a lekérdezésben
2.  Csökkentse a bemeneti partíciók száma a legkisebb lehetséges értéke 2, Event Hub használatakor.
3.  A lekérdezés 6 SU futtassa. Minden egyes segédlekérdezés a várható terhelés mellett adjon hozzá annyi ilyen segédlekérdezések a lehető mindaddig, amíg a feladat eléri rendszer erőforráskorlátok. Tekintse meg [1. eset](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) ebben az esetben a hibajelenségek keresése.
4.  Ha a segédlekérdezés korlát feletti mért találkozik, indítsa el egy új feladatot ad hozzá a segédlekérdezés. A független lekérdezések száma függvényében feladatok száma viszonylag lineáris, feltéve, hogy nem kell minden döntés terhelés kell lennie. Majd előrejelzési, hogy hány 6 SU feladatok kell futtatnia, amelyet szeretne szolgálja ki a bérlők számának függvényében.
5.  Referencia-adatok illesztési az ilyen lekérdezések használatakor union együtt, mielőtt csatlakoztatná azonos bemenetei adatokra hivatkoznak. Szükség esetén, majd ossza ki az eseményeket. Minden hivatkozás adatok illesztési ellenkező esetben valószínűleg habosító feleslegesen a memóriahasználat mentése, memóriában tartja a referenciaadatok másolatát.

> [!Note] 
> A bérlők számától el az egyes feladatokban szereplő?
> Ez a lekérdezés minta gyakran nagy számú segédlekérdezések rendelkezik, és nagyon nagy és összetett topológia eredményez. A feladat a vezérlő nem lehet tudja kezelni az ilyen nagy topológiákat. Célravezető, mint a 40-bérlők 1 SU feladat maradjon, és 60 bérlők 3 SU és 6 SU feladatok. Túllépte a kapacitást, a vezérlő, ha a feladat nem indul sikeresen megtörtént.



## <a name="get-help"></a>Segítségkérés
További segítségre van szüksége, próbálja meg [Azure Stream Analytics-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301


---
title: Felskálázás és kiáramlás az Azure Stream Analytics-feladatokban
description: Ez a cikk ismerteti, hogyan skálázhat egy Stream Analytics-feladat a bemeneti adatok particionálása, a lekérdezés finomhangolása és a feladat streamelési egységek beállítása.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: d828103bef8e57f5d0cdfe6c243c52e2d0526663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257546"
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>Azure Stream Analytics-feladat méretezése az átviteli forgalom növelése érdekében
Ez a cikk bemutatja, hogyan hangolhatja be a Stream Analytics-lekérdezéseket a Streaming Analytics-feladatok átviteli golfkapcsolatának növelése érdekében. A következő útmutató segítségével skálázhatja a feladatot a nagyobb terhelés kezeléséhez, és kihasználhatja a több rendszererőforrás (például nagyobb sávszélesség, több CPU-erőforrás, több memória) előnyeit.
Előfeltételként előfordulhat, hogy el kell olvasnia a következő cikkeket:
-   [A streamelési egységek ismertetése és módosítása](stream-analytics-streaming-unit-consumption.md)
-   [Párhuzamosítható feladatok létrehozása](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>1. eset – A lekérdezés eredendően teljesen párhuzamosítható a bemeneti partíciók között
Ha a lekérdezés eredendően teljesen párhuzamosítható a bemeneti partíciók között, kövesse az alábbi lépéseket:
1.  Szerző a lekérdezést, hogy kínosan párhuzamos segítségével **PARTITION BY** kulcsszó. További részleteket a Kínosan párhuzamos feladatok szakaszban [talál ezen az oldalon.](stream-analytics-parallelization.md)
2.  A lekérdezésben használt kimeneti típusoktól függően előfordulhat, hogy egyes kimenetek nem párhuzamosíthatók, vagy további konfigurációra van szükség ahhoz, hogy kínosan párhuzamosak legyenek. A PowerBI-kimenet például nem párhuzamosítható. A kimenetek mindig egyesülnek, mielőtt a kimeneti fogadóba küldenék. Blobok, táblák, ADLS, Service Bus és az Azure-függvény automatikusan párhuzamos. Sql és SQL DW kimenetek egy lehetőség a párhuzamosítás. Az Event Hubnak rendelkeznie kell a PartitionKey konfigurációjával, hogy megfeleljen a **PARTITION BY** mezőnek (általában PartitionId). Az Event Hub esetében is fordítson különös figyelmet az összes bemenet és az összes kimenet partícióinak számának egyeztetésére, hogy elkerülje a partíciók közötti keresztirányú átfutást. 
3.  Futtassa a lekérdezést **6 SU-val** (amely egyetlen számítási csomópont teljes kapacitása) a maximális elérhető átviteli teljesítmény mérésére, és ha **GROUP BY-t**használ, mérje meg, hogy a feladat hány csoportot (számosságot) képes kezelni. A rendszererőforrás-korlátokat elérő feladat általános tünetei a következők.
    - Su % kihasználtsági metrika több mint 80%. Ez azt jelzi, hogy a memóriahasználat magas. A mérőszám növekedéséhez hozzájáruló tényezőket [itt](stream-analytics-streaming-unit-consumption.md)ismertetjük . 
    -   A kimeneti időbélyeg a falióra idejéhez képest elmarad. A lekérdezési logikától függően a kimeneti időbélyeg logikai eltolása lehet a falióra idő. Ugyanakkor nagyjából azonos ütemben kell előrehaladniuk. Ha a kimeneti időbélyeg egyre inkább lemarad, az azt jelzi, hogy a rendszer túlműködik. Ez lehet az alsóbb rétegbeli kimeneti fogadó szabályozásának eredménye, vagy magas CPU-kihasználtság. Jelenleg nem biztosítunk CPU-kihasználtsági metrikát, így nehéz lehet megkülönböztetni a kettőt.
        - Ha a probléma miatt a süllyedés szabályozása, előfordulhat, hogy növelnie kell a kimeneti partíciók számát (és a bemeneti partíciókat, hogy a feladat teljesen párhuzamosítható), vagy növelje az erőforrások mennyiségét a fogadó (például a CosmosDB kérelemegységek száma).
    - A feladatdiagramon minden bemenethez partíciónkénti hátralék eseménymetrika található. Ha a hátralék esemény metrikája folyamatosan növekszik, ez is azt jelzi, hogy a rendszererőforrás korlátozott (vagy a kimeneti fogadó szabályozása miatt, vagy a magas CPU miatt).
4.  Miután meghatározta a 6 SU-feladat korlátait, lineárisan extrapolálhatja a feladat feldolgozási kapacitását, ahogy további SUs-okat ad hozzá, feltételezve, hogy nincs olyan adatdöntés, amely bizonyos partíciókat "forróvá" tesz.

> [!NOTE]
> Válassza ki a megfelelő számú streamelési egységek: Mivel a Stream Analytics létrehoz egy feldolgozási csomópont minden 6 SU hozzáadott, a legjobb, ha a csomópontok száma a bemeneti partíciók száma, így a partíciók egyenletesen elosztható a csomópontok között.
> Például mérte a 6 SU-feladat képes elérni 4 MB/s feldolgozási sebesség, és a bemeneti partíció száma 4. Választhat, hogy a feladat futtatásához 12 SU eléréséhez nagyjából 8 MB/s feldolgozási sebesség, vagy 24 SU eléréséhez 16 MB/ s. Ezután eldöntheti, hogy mikor növelje a feladat SU-számát, milyen értékre, a bemeneti sebesség függvényében.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>2. eset - Ha a lekérdezés nem kínosan párhuzamos.
Ha a lekérdezés nem kínosan párhuzamos, kövesse az alábbi lépéseket.
1.  Kezdje egy PARTITION **BY nélküli** lekérdezéssel, hogy elkerülje a particionálás összetettségét, és futtassa a lekérdezést 6 SU-val a maximális terhelés mérésére az [1.](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions)
2.  Ha el tudja érni a várható terhelést az átviteli ciklusban, akkor kész. Másik lehetőségként dönthet úgy, hogy ugyanazt a feladatot mérje 3 SU és 1 SU, hogy megtudja, a minimális számú SU, amely működik a forgatókönyv.
3.  Ha nem tudja elérni a kívánt átviteli, próbálja meg felbontani a lekérdezést több lépésben, ha lehetséges, ha nem rendelkezik több lépéssel már, és lefoglalni akár 6 SU minden lépés a lekérdezésben. Például, ha 3 lépésben, lefoglalja 18 SU a "Méretezés" opciót.
4.  Egy ilyen feladat futtatásakor a Stream Analytics minden lépést a saját csomópontján helyez el dedikált 6 SU-erőforrással. 
5.  Ha még mindig nem érte el a terhelési célt, megpróbálhatja használni **a PARTITION-t** a bemenethez közelebbi lépésekből indulva. A **csoport által** operátor, amely nem feltétlenül particionálható, használhatja a helyi /globális összesítő minta elvégzésére particionált GROUP **BY** majd egy nem particionált GROUP **BY**. Ha például 3 percenként hány autó megy keresztül az egyes fizetős fülkékön, és az adatok mennyisége meghaladja a 6 SU által kezelhetőt.

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
A fenti lekérdezésben partíciónként számolja az autókat fizetőfülkénként, majd összeadja az összes partíció számát.

Particionálás után a lépés minden egyes partíciója esetén legfeljebb 6 SU-t foglal le, minden partíció, amelynek 6 SU-ja a maximális, így minden partíció a saját feldolgozási csomópontjára helyezhető.

> [!Note]
> Ha a lekérdezés nem particionálható, további SU hozzáadása egy többlépéses lekérdezésnem mindig javítja az átviteli kapacitást. A teljesítmény megszerzésének egyik módja a hangerő csökkentése a kezdeti lépésekben a helyi/globális aggreggregátumminta használatával, az 5.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>3. eset – Sok független lekérdezést futtat egy feladatban.
Bizonyos független független ítő használati esetekben, ahol költséghatékonyabb az adatok feldolgozása több bérlőtől egy feladatban, külön bemenetek és kimenetek használatával minden bérlő, előfordulhat, hogy a végén fut jó néhány (például 20) független lekérdezések egyetlen feladatban. A feltételezés az, hogy minden ilyen segédlekérdezés terhelése viszonylag kicsi. Ebben az esetben az alábbi lépéseket hajthatja végre.
1.  Ebben az esetben ne használja a **PARTITION BY-t** a lekérdezésben
2.  Az Event Hub használata esetén csökkentse a bemeneti partíciók számát a lehető legalacsonyabb 2 értékre.
3.  Futtassa a lekérdezést 6 SU-val. Az egyes segédlekérdezések várható terhelésével adjon hozzá annyi ilyen segédlekérdezést, amennyit csak lehet, amíg a feladat eléri a rendszererőforrás-korlátokat. Az [1.](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions)
4.  Ha elérte a fent mért segédlekérdezési korlátot, kezdje el hozzáadni a segédlekérdezést egy új feladathoz. A független lekérdezések számának függvényében futtatandó feladatok számának meglehetősen lineárisnak kell lennie, feltéve, hogy nincs terhelési döntés. Ezután előre jelezheti, hogy hány SU-feladatot kell futtatnia a kiszolgálni kívánt bérlők számának függvényében.
5.  Amikor referenciaadatokat használ, egyesítse a bemeneteket, mielőtt ugyanazokhoz a referenciaadatokhoz csatlakozna. Ezután ossza szét az eseményeket, ha szükséges. Ellenkező esetben minden referenciaadat-illesztés a hivatkozási adatok másolatát a memóriában tartja, ami valószínűleg feleslegesen felfújja a memóriahasználatot.

> [!Note] 
> Hány bérlőt kell berakni az egyes feladatba?
> Ez a lekérdezési minta gyakran nagy számú allekérdezést tartalmaz, és nagyon nagy és összetett topológiát eredményez. Előfordulhat, hogy a feladat vezérlője nem képes kezelni egy ilyen nagy topológiát. Általános szabály, hogy maradjon 40 bérlő alatt 1 SU-feladat, és 60 bérlők 3 SU és 6 SU-feladatok. Ha túllépi a vezérlő kapacitását, a feladat nem indul el sikeresen.



## <a name="get-help"></a>Segítségkérés
További segítségért próbálja ki [az Azure Stream Analytics fórumunkat.](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
* [Bevezetés az Azure Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
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


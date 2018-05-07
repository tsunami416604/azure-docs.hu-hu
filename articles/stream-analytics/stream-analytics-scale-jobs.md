---
title: Skálázás be és ki az Azure Stream Analytics-feladatok
description: Ez a cikk ismerteti a Stream Analytics-feladat bővítse a bemeneti adatok particionálás, a lekérdezés hangolása és adatfolyam-egységek feladat beállítása.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: 2868ebd459f937f8621086b16c63f89842f376be
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>Egy Azure Stream Analytics-feladat átviteli sebesség növelése méretezése
Ez a cikk bemutatja, hogyan finomhangolhatják a Stream Analytics lekérdezési Streaming Analytics-feladatok átviteli sebesség növelése. Ez az útmutató segítségével méretezni a feladat magasabb terhelés kezelésére, és több rendszererőforrást (például a nagyobb sávszélességet, több Processzor-erőforrások, több memória) előnyeit.
Előfeltételként szükség lehet a következő cikkeket:
-   [A streamelési egységek ismertetése és módosítása](stream-analytics-streaming-unit-consumption.md)
-   [Párhuzamosítható feladatok létrehozása](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Bemeneti partíciók teljesen eredendően párhuzamosítható eset 1 – a lekérdezés nem
Ha a lekérdezés nem teljesen eredendően párhuzamosítható bemeneti partíciók, kövesse az alábbi lépéseket:
1.  A lekérdezést úgy, hogy embarrassingly párhuzamos használatával szerzői **PARTITION BY** kulcsszó. További részletek a Embarrassingly párhuzamos feladat szakaszban [ezen az oldalon](stream-analytics-parallelization.md).
2.  Attól függően, hogy a lekérdezésben használt kimeneti típusok, néhány kimeneti vagy lehet nem párhuzamosítható, vagy további beállításra embarrassingly párhuzamos kell. Például az SQL, az SQL DW és a Power bi kimenetek nincsenek párhuzamosítható. Kimenetek kimeneti fogadóját küldése előtt mindig összefésülése megtörténjen. Blobot, táblát, ADLS, Service Bus és Azure-függvény automatikusan párhuzamos működésű. CosmosDB és Eseményközpont meg kell adni a PartitionKey konfigurációkészletet felelnek meg a **PARTITION BY** (általában PartitionId) mezőben. Az Event Hubs is különös extra egyezik meg a partíciók az összes bemenet és mindegyikhez felett partíciók közötti elkerülése érdekében. 
3.  A lekérdezés futtatása **6 SU** (Ez az egyetlen számítási csomópont teljes kapacitásának) mérésére maximális elérhető átviteli sebesség és használata **GROUP BY**, mérik (cardinality) a feldolgozás csoportok számát is kezelni. A feladat szerezze meg a rendszer erőforrás korlátok általános tünetei a következők.
    - SU % kihasználtsági metrika több mint 80 %. Ez processzorterhelése memória méretét jelzi. Ez a metrika növekedése a tényezők ismertetjük [Itt](stream-analytics-streaming-unit-consumption.md). 
    -   Kimeneti időbélyeg esik valós idő tekintetében. Attól függően, hogy a lekérdezés logika a kimeneti időbélyeg előfordulhat, hogy a valós idő a logikai értékű eltolás. Azonban ezek kell előrehaladás nagyjából az azonos díj. Ha a kimeneti időbélyeg további esik, és további mögött, akkor azt jelzi, hogy a rendszer overworking van. Azok a alárendelt kimeneti fogadó szabályozási vagy magas CPU-felhasználás eredményt. Nem nyújtunk CPU-kihasználtság metrika jelenleg, a két megkülönböztetéséhez nehézkes lehet.
        - Ha a probléma fogadó szabályozás miatt, szükség lehet a kimeneti partíciók számának növelése (és is a partíciók teljesen párhuzamosítható tartani a feladat bemeneti), vagy növelje a gyűjtő (például CosmosDB kérelem egységek száma) erőforrásainak mennyiségét.
    - A feladat ábra esetében egy partíció várakozó esemény metrikáját minden beviteli /. A várakozó események metrika tartja a növelését, esetén is azt jelzi, hogy a rendszer erőforrás által korlátozott (vagy kimeneti fogadó sávszélesség-szabályozás, vagy a nagy CPU miatt).
4.  Miután megadta, hogy egy 6 SU feladat érheti határain, úgy extrapolálják lineárisan a feldolgozási kapacitás, a feladat hozzáadása során több SUS-t, feltéve, hogy nincs döntés adatokat, amelyek bizonyos partíció "Forró".

> [!NOTE]
> Válassza ki a megfelelő Streaming Units számát: a Stream Analytics a feldolgozási főcsomópont hoz létre, az egyes 6 SU hozzáadni, mert érdemes ellenőrizze a bemeneti partíciók száma osztója a csomópontok számát, a partíciók egyenletes eloszlású a csomópontjai között.
> Például a 6 mért SU feladat érhető el a 4 MB/s feldolgozási sebesség és a bemeneti partíciók száma érték a 4. Ha szeretné, a feladat futtatásához 12 SU körülbelül 8 MB/s feldolgozási sebességét eléréséhez, vagy 24 SU 16 MB/s eléréséhez. Ezután eldöntheti, ha a feladat milyen értéket, és a bemeneti függvényében SU számának növeléséhez.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>2 – Ha a lekérdezés nem embarrassingly párhuzamos eset.
Ha a lekérdezés nem embarrassingly párhuzamos, követheti az alábbi lépéseket.
1.  Kezdő nélküli lekérdezéssel **PARTITION BY** először a elkerüléséhez a particionálás összetettségét, és futtassa a lekérdezést 6 SU méréséhez és a legnagyobb terhelést [1. eset](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  Ha a kifejezés átviteli érhető el a várható terhelést, akkor elkészült. Másik lehetőségként választhatja, hogy mérésére SU és 1 SU tudja meg, amely a forgatókönyvnek SU minimális száma 3 futó ugyanazt a feladatot.
3.  Ha nem érhetők el a kívánt teljesítményt, próbálja meg a lekérdezés felosztása több lépést, ha lehetséges, ha nem rendelkezik már több lépést, és akár 6 SU lefoglalni az egyes lépések a lekérdezésben. Például, ha 3 lépést, a "Méretezése" beállítás 18 SU lefoglalni.
4.  E feladat futtatásakor a Stream Analytics minden lépés dedikált 6 SU erőforrásokkal saját csomóponton helyezi. 
5.  Ha Ön továbbra is még nem érhető el a terhelés célként, megkísérli használni **PARTITION BY** kiindulva lépések közelebb a bemeneti. A **GROUP BY** operátor szerinti szűrése, amely nem feltétlenül természetes particionálható, használhatja a helyi/globális összesített mintát végrehajtásához egy particionált **GROUP BY** követ egy másik **csoportosítás** . Például ha azt szeretné, ha a hány autók áthaladás minden téren kiállítási át 3 percenként, és az adatok mennyisége túl mi van kezelhetik 6 SU.

Lekérdezés:

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

A fenti lekérdezés téren kiállítási partíciónként / autók számbavételi, és majd adja hozzá a számláló az összes partíció együtt.

Miután particionálása, minden partíció a lépés lefoglalni legfeljebb 6 SU, mindegyik partíció rendelkezik a 6 SU a maximális, így minden partíció saját feldolgozási főcsomópont lehet tenni.

> [!Note]
> Ha a lekérdezés nem lehet particionálni, további további SU több lépéseket lekérdezésben előfordulhat, hogy mindig növelhető az átviteli sebesség. Egy ahhoz, hogy a teljesítmény módja csökkenthető a helyi/globális összesített mintázat, kezdeti lépéseket 5. lépésben a fent leírt módon.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Eset 3 - fut egy feladat nagy mennyiségű független lekérdezések.
Az egyes ISV használja az esetekben, ahol gazdaságosabb feldolgozott adatok több bérlő egyetlen feladatokban, külön bemenetekhez és kimenetekhez használatával az egyes bérlők számára használható meglehetősen néhány (például 20) futtató független lekérdezések egyetlen feladatokban. A feltételezése minden ilyen allekérdezés terhelés viszonylag kis. Ebben az esetben is kövesse az alábbi lépéseket.
1.  Ebben az esetben ne használjon **PARTITION BY** a lekérdezés
2.  Az Event Hubs használata csökkentheti a bemeneti partíciók száma a legkisebb lehetséges értéke 2.
3.  A lekérdezés futtatása a 6 SU. A várt terhelés az összes segédlekérdezésben adja hozzá annyi ilyen segédlekérdezések lehetséges, amíg a feladat elérte van-e rendszer erőforrás korlátok. Tekintse meg [1. eset](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) a a tüneteket, ha ez történik.
4.  Miután hogy elérte-e a segédlekérdezés korlát feletti mért, indítsa el a segédlekérdezés ad hozzá egy új feladatot. A független lekérdezések továbbítása feladatok száma nem lehet viszonylag lineáris, feltéve, hogy nincs minden döntés betöltése. Előre jelzett a hány 6 SU feladatok a bérlők milyen kiszolgálására számának függvényében futtatnia kell majd.
5.  Hivatkozás adatok illesztési ilyen lekérdezések használatakor meg kell Unió a bemenetek együtt, mielőtt csatlakoztatná azonos referenciaadatokkal, majd ossza fel az eseményeket, szükség esetén. Ellenkező esetben minden hivatkozás adatok illesztési másolatot készít azokról a referenciaadatok a memóriában, valószínűleg habosító feleslegesen a memóriahasználat fel.

> [!Note] 
> Hány bérlő minden feladat elhelyezése?
> A lekérdezés mintát gyakran segédlekérdezések nagy számú, és nagyon nagy és összetett topológia eredményez. A feladat a tartományvezérlő nem lehet ilyen nagy topológia kezelésére képes. A szokásos megoldás, mint 1 SU feladat 40 bérlők maradnak, és 60 bérlők 3 SU és 6 SU feladatok. Ha a tartományvezérlő kapacitásának túllépte a feladat nem indítható sikeresen megtörtént.


## <a name="an-example-of-stream-analytics-throughput-at-scale"></a>A Stream Analytics átviteli léptékű példa
Segítségével megtudhatja hogyan Stream Analytics-feladatok méretezése, egy kísérlet málna Pi eszköz visszajelzései alapján végre azt. Ehhez a kísérlethez tudassa velünk látni az átviteli sebesség több adatfolyam-továbbítási egységek és partíciók hatását.

Ebben a forgatókönyvben az eszköz érzékelőadatait (ügyfelek) küld az eseményközpontba. Streaming Analytics feldolgozza az adatokat, és küld riasztást vagy statisztika kimenetként egy másik eseményközpontot. 

Az ügyfél elküldi a érzékelői adatok JSON formátumban. A kimeneti adatok is JSON formátumban van. Az adatok így néz ki:

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

A következő lekérdezés riasztást küld, ha egy világos funkció ki van kapcsolva a következőkre használható:

    SELECT AVG(lght), "LightOff" as AlertText
    FROM input TIMESTAMP BY devicetime 
    PARTITION BY PartitionID
    WHERE lght< 0.05 GROUP BY TumblingWindow(second, 1)

### <a name="measure-throughput"></a>Mérték átviteli sebesség

Ebben a környezetben a Stream Analytics egy rögzített időn belül feldolgozott bemeneti adatok mennyisége esetén átviteli sebesség. (Jelenleg mért 10 perc.) A bemeneti adatok a legjobb feldolgozási átviteli sebesség eléréséhez az adatfolyam-bemenetre, mind a lekérdezés volt particionálva. Azt a részét **COUNT()** mérésére bemeneti események feldolgozása megtörtént a lekérdezésben. Győződjön meg arról, hogy a feladat nem egyszerűen várakozási bemeneti események elérni, hogy a bemeneti eseményközpont minden partíciója 300 MB, bemeneti adatokat a történt meg.

A következő táblázat a streamelési egységek számának növekedését azt, és a megfelelő partíció található, az event hubs látott eredményeket jeleníti meg.  

<table border="1">
<tr><th>Bemeneti partíciók</th><th>Kimeneti partíciók</th><th>Folyamatos átviteli egységek</th><th>Fenntartható
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4.06 MB/s</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8.06 MB/s</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38.32 MB/s</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172.67 MB/s</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454.27 MB/s</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609.69 MB/s</td>
</tr>
</table>

És a következő diagram megjeleníti a képi megjelenítés SUS-t és az átvitel közötti kapcsolat.

![img.stream.analytics.perfgraph][img.stream.analytics.perfgraph]

## <a name="get-help"></a>Segítségkérés
Ha további segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

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

[microsoft.support]: http://support.microsoft.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


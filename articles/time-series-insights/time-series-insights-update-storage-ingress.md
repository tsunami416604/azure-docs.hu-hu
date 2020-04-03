---
title: Adattárolás és be- és be- és be- és be- és be- és be- és be- és be- és bevitel előzetes verzióban – Azure Time Series Insights | Microsoft dokumentumok
description: Ismerje meg az adatok tárolását és be- és be- és be- és bevitelét az Azure Time Series Insights előzetes verziójában.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/10/2020
ms.custom: seodec18
ms.openlocfilehash: 8987cbe6860422ff92119a9f3b13a0a365e6d1a4
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618326"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Adattárolás és be- és be- és be- és bevitel az Azure Time Series Insights előzetes verzióban

Ez a cikk az Azure Time Series Insights előzetes verziójának adattárolásának és be- és be- és be- és be- és be- és be- és be- és be- ésbetöltésének frissítéseit ismerteti. Ismerteti az alapul szolgáló tárolási struktúra, fájlformátum és a Time Series ID tulajdonság. Az alapul szolgáló be- ésbefizetési folyamat, ajánlott eljárások és az aktuális előnézeti korlátozások is ismertetik.

## <a name="data-ingress"></a>Adatok be- és be- és adatai

Az Azure Time Series Insights-környezet egy *betöltési motort* tartalmaz az idősorozat-adatok gyűjtéséhez, feldolgozásához és tárolásához. 

Vannak olyan szempontok, amelyeket figyelembe kell venni, hogy biztosítsa az összes bejövő adatok feldolgozása, a magas bejövő töltési skála, és minimálisra csökkenti a *betöltési késés* (az idő által a Time Series Insights olvasni és feldolgozni az adatokat az eseményforrásból) [a környezet tervezésekor.](time-series-insights-update-plan.md)

A Time Series Insights előzetes adatbetöltési szabályzatai határozzák meg, hogy az adatok honnan és milyen formátumból származhatnak.

### <a name="ingress-policies"></a>A be- és énekre vonatkozó irányelvek

*Az adatok betöltése* magában foglalja, hogyan küldi el az adatokat egy Azure Time Series Insights előzetes környezetbe. 

A legfontosabb konfigurációkat, formázásokat és gyakorlati tanácsokat az alábbiakban foglaljuk össze.

#### <a name="event-sources"></a>Eseményforrások

Az Azure Time Series Insights előzetes verzió a következő eseményforrásokat támogatja:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Az Azure Time Series Insights előzetes verzió példányonként legfeljebb két eseményforrást támogat.

> [!IMPORTANT] 
> * Nagy kezdeti késést tapasztalhat, amikor eseményforrást csatol az előzetes verziójú környezethez. 
> Az eseményforrás késése az IoT Hubban vagy az Event Hubban jelenleg lévő események számától függ.
> * A nagy késés alábbhagy az eseményforrás-adatok első betöltése után. Küldjön be egy támogatási jegyet az Azure Portalon keresztül, ha folyamatos nagy késést tapasztal.

#### <a name="supported-data-format-and-types"></a>Támogatott adatformátum és -típusok

Az Azure Time Series Insights támogatja az Azure IoT Hubról vagy az Azure Event Hubs-ból küldött UTF-8 kódolású JSON-t. 

A támogatott adattípusok a következők:

| Adattípus | Leírás |
|---|---|
| **Bool** | Két állapot egyikével rendelkező `true` adattípus: vagy `false`. |
| **Datetime** | Egy pillanatnyi időt jelöl, amelyet általában dátumként és napszakban fejeznek ki. [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) formátumban kifejezve. |
| **double** | Dupla pontosságú, 64 bites [IEEE 754](https://ieeexplore.ieee.org/document/8766229) lebegőpontos. |
| **sztring** | Unicode karakterekből álló szöveges értékek.          |

#### <a name="objects-and-arrays"></a>Objektumok és tömbök

Az esemény hasznos adatának részeként összetett típusokat, például objektumokat és tömböket küldhet, de az adatok tároláskor összeolvasztási folyamaton mennek keresztül. 

A JSON-események alakításáról, az összetett típusok küldéséről és a beágyazott objektumok összeolvasztásáról a [JSON be- és lekérdezési műveletek, valamint](./time-series-insights-update-how-to-shape-events.md) a tervezés és optimalizálás segítése című lekérdezés részletes ismertetése című részben található.

### <a name="ingress-best-practices"></a>A be- és nagybe– ajánlott eljárások

Javasoljuk, hogy a következő ajánlott eljárásokat alkalmazza:

* Konfigurálja az Azure Time Series Insights és az ugyanabban a régióban található Bármely IoT Hub vagy Event Hub konfigurálása a potenciális késés csökkentése érdekében.

* [Tervezze meg a méretezési igényeket](time-series-insights-update-plan.md) a várható betöltési arány kiszámításával és annak ellenőrzésével, hogy az az alább felsorolt támogatott díjba esik-e.

* Ismerje meg, hogyan optimalizálhatja és alakíthatja a JSON-adatokat, valamint az előnézet jelenlegi korlátait, ha elolvassa, [hogyan formálhatja a JSON-t a bejövő forgalom és a lekérdezés számára.](./time-series-insights-update-how-to-shape-events.md)

### <a name="ingress-scale-and-preview-limitations"></a>A be- ésforgalom méretezési és előnézeti korlátozásai 

Az Azure Time Series Insights előzetes be- és információinak korlátozásait az alábbiakban ismertetjük.

> [!TIP]
> Olvassa el [az előnézeti környezet megtervezése című részt](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) az összes előzetes verziós korlát átfogó listájáért.

#### <a name="per-environment-limitations"></a>Környezetre vonatkozó korlátozások

A bejövő díjak általában a szervezetben található eszközök számának, az eseménykibocsátási gyakoriságnak és az egyes események méretének tényezőjeként tekinthetők:

*  **Berendezések száma** × **Az esemény kibocsátási gyakorisága** × Az egyes események **mérete**.

Alapértelmezés szerint a Time Series Insights előzetes verzió a beérkező adatokat **a Time Series Insights-környezetben legfeljebb 1 megabájt (MBps)** sebességgel tudja beadni. További korlátozások vannak [hubpartíciónként.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#hub-partitions-and-per-partition-limits)

> [!TIP] 
> * Akár 16 Mb/s sebesség betöltési sebességéhez is környezetvédelmi támogatás biztosítható.
> * Lépjen kapcsolatba velünk, ha nagyobb átviteli hangra van szüksége egy támogatási jegy azure portalon keresztül történő elküldésével.
 
* **1. példa:**

    A Contoso Shipping 100 000 olyan eszközzel rendelkezik, amelyek percenként háromszor bocsátanak ki eseményt. Egy esemény mérete 200 bájt. Egy Iot Hub négy partíciót használnak a Time Series Insights eseményforrásként.

    * A Time Series Insights-környezetbe a betöltési arány a következő lenne: **100 000 eszköz * 200 bájt/esemény * (3/60 esemény/mp) = 1 Mb/s**.
    * A partíciónkénti betöltési sebesség 0,25 Mb/s lenne.
    * A Contoso Shipping betöltési díja az előzetes verzió méretarányának korlátozásán belül lenne.

* **2. példa**

    A Contoso Fleet Analytics 60 000 olyan eszközzel rendelkezik, amelyek másodpercenként bocsátanak ki egy eseményt. Egy 4-es partíciós számlálóval rendelkező Event Hubot használnak a Time Series Insights eseményforrásként. Egy esemény mérete 200 bájt.

    * A környezet betöltési sebessége a következő lenne: **60 000 eszköz * 200 bájt/esemény * 1 esemény/mp = 12 Mb/s**.
    * A partíciónkénti sebesség 3 MBps lenne.
    * A Contoso Fleet Analytics betöltési sebessége túllépi a környezet és a partíciókorlátokat. A Time Series Insights az Azure Portalon keresztül kérelmet nyújthat be a környezetük betöltési arányának növeléséhez, és létrehozhat egy Event Hub-ot, amely több partíciót tartalmaz az előzetes verzió korlátain belül.

#### <a name="hub-partitions-and-per-partition-limits"></a>Hub-partíciók és partíciónkénti korlátok

A Time Series Insights-környezet tervezésekor fontos figyelembe venni a Time Series Insights-hoz kapcsolódó eseményforrás(ok) konfigurációját. Az Azure IoT Hub és az Event Hubs egyaránt használja a partíciókat a horizontális méretezés engedélyezése az eseményfeldolgozáshoz. 

A *partíció* a hubon tartott események rendezett sorozata. A partíciók száma a hub létrehozási fázisában van beállítva, és nem módosítható. 

Az Event Hubs particionálási gyakorlati tanácsok, tekintse [át, hogy hány partícióra van szükségem?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> Az Azure Time Series Insights által használt legtöbb IoT Hubs csak négy partíciót kell használnia.

Akár új központot hoz létre a Time Series Insights-környezethez, akár egy meglévőt használ, ki kell számítania a partíciónkénti betöltési arányt annak megállapításához, hogy az az előzetes verziókorlátain belül van-e. 

Az Azure Time Series Insights előzetes verziója jelenleg **0,5 Mb/s partíciónkénti általános korláttal rendelkezik.**

#### <a name="iot-hub-specific-considerations"></a>Az IoT Hub-specifikus szempontok

Amikor egy eszközt az IoT Hubban hoz létre, véglegesen hozzá van rendelve egy partícióhoz. Ennek során az IoT Hub képes garantálni az eseményrendezést (mivel a hozzárendelés soha nem változik).

A rögzített partíció-hozzárendelés is hatással van a Time Series Insights-példányok, amelyek az IoT Hub ról küldött adatok at. Ha több eszközről érkező üzeneteket továbbít a hubugyanazzal az átjáróeszköz-azonosítóval, előfordulhat, hogy ugyanabban a partíción érkeznek, ugyanakkor potenciálisan túllépi a partícióskálánkénti korlátokat. 

**Hatás**:

* Ha egy partíció tartós betöltési sebességet tapasztal az előzetes verziós korláton, lehetséges, hogy a Time Series Insights nem szinkronizálja az összes eszköz telemetriai adatok at az IoT Hub adatmegőrzési időszak túllépése előtt. Ennek eredményeképpen az elküldött adatok elveszhetnek, ha a betöltési korlátokat következetesen túllépik.

A körülmények enyhítése érdekében a következő ajánlott eljárásokat javasoljuk:

* A megoldás üzembe helyezése előtt kiszámíthatja a környezetenkénti és a partíciónkénti betöltési díjakat.
* Győződjön meg arról, hogy az IoT Hub-eszközök terheléselosztása a lehető legnagyobb mértékben.

> [!IMPORTANT]
> Az IoT Hubot eseményforrásként használó környezetekben számítsa ki a betöltési sebességet a használatban lévő központi eszközök számával, hogy megbizonyosodjon arról, hogy a sebesség az előzetes verzióban partíciónkénti 0,5 MBps alá esik.
> * Még ha több esemény érkezik egyszerre, az előnézeti korlát ot nem lépi túl a program.

  ![IoT hub partíciódiagram](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

A hub átviteli és partícióinak optimalizálásáról az alábbi forrásokból tudhat meg többet:

* [IoT hub méretezése](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Eseményközpont-méretezés](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Eseményközpont-partíciók](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Adattárolás

Amikor létrehoz egy Time Series Insights előzetes *verziós készletkészlet-készletkörnyezetet,* két Azure-erőforrást hoz létre:

* Egy Azure Time Series Insights előzetes környezet, amely konfigurálható a meleg adattároláshoz.
* Az Azure Storage általános célú V1 blobfiók a hideg adattároláshoz.

A meleg boltban lévő adatok csak a [Time Series Query](./time-series-insights-update-tsq.md) és az Azure Time Series Insights előzetes [kezelője](./time-series-insights-update-explorer.md)segítségével érhetők el. A melegtárban a Time Series Insights környezet létrehozásakor kiválasztott [megőrzési időszakon](./time-series-insights-update-plan.md#the-preview-environment) belül a legutóbbi adatokat fogja tartalmazni.

A Time Series Insights Preview a hűtőház adatait [a Parketta fájlformátumban](#parquet-file-format-and-folder-structure)menti az Azure Blob storage-ba. A Time Series Insights Preview kizárólag ezeket a hűtőház-adatokat kezeli, de közvetlenül normál parkettafájlként is elolvashatja.

> [!WARNING]
> Az Azure Blob storage-fiók tulajdonosaként, ahol a hűtőház adatai találhatók, teljes hozzáféréssel rendelkezik a fiók összes adatához. Ez a hozzáférés írási és törlési engedélyeket is tartalmaz. Ne szerkesztje vagy törölje a Time Series Insights preview által írt adatokat, mert ez adatvesztést okozhat.

### <a name="data-availability"></a>Adatok rendelkezésre állása

Az Azure Time Series Insights előzetes partíciók és indexeli az adatokat az optimális lekérdezési teljesítmény érdekében. Az adatok az indexelés után mind a meleg (ha engedélyezve van) és a hűtőtárolóból lekérdezésre elérhetők. A betöltés alatt álló adatok mennyisége hatással lehet a rendelkezésre állásra.

> [!IMPORTANT]
> Az előzetes verzió során az adatok elérhetővé vájtának előtt legfeljebb 60 másodperces időszak jelenhet meg. Ha 60 másodpercen túl jelentős késést tapasztal, küldjön be egy támogatási jegyet az Azure Portalon keresztül.

## <a name="azure-storage"></a>Azure Storage

Ez a szakasz ismerteti az Azure Storage-adatok az Azure Time Series Insights előzetes verziójával kapcsolatos részleteket.

Az Azure Blob storage alapos leírását olvassa el a [Storage-blobok bevezetés című részében.](../storage/blobs/storage-blobs-introduction.md)

### <a name="your-storage-account"></a>A tárfiók

Amikor létrehoz egy Azure Time Series Insights preview PAYG-környezetet, egy Azure Storage általános célú V1 blobfiók jön létre a hosszú távú hűtőtárolóként.  

Az Azure Time Series Insights előzetes verzió az Azure Storage-fiókban az egyes eseményekről legfeljebb két példányt őriz meg. Egy példány tárolja a betöltési idő által rendezett eseményeket, mindig lehetővé téve az eseményekhez való hozzáférést egy idősorrendben. Idővel a Time Series Insights Preview is létrehoz egy újraparticionált másolatot az adatokról, hogy optimalizálja a performant Time Series Insights lekérdezés. 

A nyilvános előzetes verzió során az adatok határozatlan ideig tárolhatók az Azure Storage-fiókban.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>A Time Series Insights-blobok írása és szerkesztése

A lekérdezési teljesítmény és az adatok rendelkezésre állásának biztosítása érdekében ne edzese vagy törölje a Time Series Insights előzetes verzióban létrehozott blobokat.

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>A Time Series Insights előzetes verzióbeli hűtőház adatainak elérése 

A Mellett, hogy hozzáfér az adatokhoz a [Time Series Insights előzetes verziókezelőés](./time-series-insights-update-explorer.md) a Time Series [Query,](./time-series-insights-update-tsq.md)érdemes lehet az adatok at közvetlenül a Parketta tárolt fájlokat a hűtőházban. Például elolvashatja, átalakíthatja és megtisztíthatja az adatokat egy Jupyter-jegyzetfüzetben, majd használhatja azt az Azure Machine Learning-modell betanításához ugyanabban a Spark-munkafolyamatban.

Az adatok eléréséhez közvetlenül az Azure Storage-fiók, olvasási hozzáférést kell a Time Series Insights előzetes adatok tárolására használt fiókhoz. Ezután a kiválasztott adatokat a Parketta fájl létrehozási ideje alapján olvashatja el, amely a `PT=Time` [Parketta fájlformátum](#parquet-file-format-and-folder-structure) szakaszban az alábbiakban ismertetett mappában található.  Az olvasási hozzáférés engedélyezéséről a [tárfiók erőforrásaihoz való hozzáférés kezelése című](../storage/blobs/storage-manage-access-to-resources.md)témakörben olvashat bővebben.

#### <a name="data-deletion"></a>Adatok törlése

Ne törölje a Time Series Insights előzetes fájljait. A kapcsolódó adatokat csak a Time Series Insights előzetes verziójából kezelheti.

### <a name="parquet-file-format-and-folder-structure"></a>Parketta fájlformátum és mappastruktúra

Parketta egy nyílt forráskódú oszlopos fájlformátum célja a hatékony tárolás és a teljesítmény. A Time Series Insights előzetes verzió a Parquet segítségével engedélyezi a Time Series ID-alapú lekérdezési teljesítményt nagy méretekben.  

A Parketta fájltípussal kapcsolatos további információkért olvassa el a [Parketta dokumentációját](https://parquet.apache.org/documentation/latest/).

A Time Series Insights preview az adatok másolatait az alábbiak szerint tárolja:

* Az első, kezdeti példány tã¶rtÃgÃ¶sszejÃtOrZÃTããÃTãtãttttttt, a kajálta idoszaka nagyjából az érkezés sorrendjében történik. Ezek az adatok `PT=Time` a mappában találhatók:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* A második, újraparticionált példány tikulyasorozat-azonosítók szerint van csoportosítva, és a `PT=TsId` mappában található:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

Mindkét esetben a Parketta fájl időtulajdonsága megfelel a blob létrehozási idejének. A mappában lévő `PT=Time` adatok a fájlba való írást követően nem változnak. A mappában lévő `PT=TsId` adatok idővel lekérdezésre lesznek optimalizálva, és nem statikusak.

> [!NOTE]
> * `<YYYY>`négyjegyű évszámmal.
> * `<MM>`kétjegyű havi ábrázolásra.
> * `<YYYYMMDDHHMMSSfff>`időbélyeg-ábrázolásra`YYYY`kerül, amely négyjegyű évszámmal`MM`( ), kétjegyű hónap ( ), kétjegyű nap (`DD`kétjegyű óra ( kétjegyű perc`HH`(`MM`), kétjegyű második (`SS`), és háromjegyű ezredmásodperc ( ).`fff`

A Time Series Insights előzetes eseményei a következőképpen vannak leképezve a parkettafájl tartalmára:

* Minden esemény egyetlen sorra van leképezve.
* Minden sor tartalmazza az **időbélyeg** oszlop egy esemény időbélyegzővel. Az időbélyegző tulajdonság soha nem null. Alapértelmezés szerint az **esemény várólistára helyezett idő,** ha az időbélyegző tulajdonság nincs megadva az esemény forrásában. A tárolt időbélyegző mindig UTC-ben van.
* Minden sor tartalmazza a Time Series ID (TSID) oszlop(ok) meghatározásakor, amikor a Time Series Insights környezet ben jön létre. A TSID tulajdonság `_string` neve tartalmazza az utótagot.
* A telemetriai adatokként küldött összes többi tulajdonság a `_string` tulajdonság `_bool` típusától függően oszlopnevekhez van rendelve, amelyek a tulajdonságtípustól függően végződnek (karakterlánc), (logikai), `_datetime` (datetime) vagy `_double` (dupla) végződésű.
* Ez a leképezési séma a fájlformátum első, **V=1-ként** hivatkozott és az azonos nevű alapmappában tárolt fájlrendszerre vonatkozik. A szolgáltatás fejlődésével ez a leképezési séma változhat, és a hivatkozás neve is csökkenhet.

## <a name="next-steps"></a>További lépések

- Olvassa [el, hogyan alakíthatja a JSON-t a be- és lekérdezéshez.](./time-series-insights-update-how-to-shape-events.md)

- További információ az új [adatmodellezésről.](./time-series-insights-update-tsm.md)

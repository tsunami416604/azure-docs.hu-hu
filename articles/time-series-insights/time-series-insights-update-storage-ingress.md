---
title: Adattárolás és bejövő forgalom előzetes verzióban – Azure Time Series Insights | Microsoft Docs
description: Tudnivalók az adattárolásról és a bejövő forgalomról Azure Time Series Insights előzetes verzióban.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: d3bfb589ec4c152b136e8e1f432864b719c97d58
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85509319"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Adattárolás és bejövő forgalom Azure Time Series Insights előzetes verzióban

Ez a cikk az adattárolási és a bejövő Azure Time Series Insights előzetes verziójának frissítéseit ismerteti. Ismerteti a mögöttes tárolási struktúrát, a fájlformátumot és az idősorozat-azonosító tulajdonságot. A rendszer az alapul szolgáló beáramlási folyamatot, az ajánlott eljárásokat és az aktuális előzetes verzióra vonatkozó korlátozásokat is tárgyalja.

## <a name="data-ingress"></a>Bejövő adatforgalom

Az Azure Time Series Insights-környezet egy betöltési *motort* tartalmaz az idősoros adatok gyűjtéséhez, feldolgozásához és tárolásához.

Néhány szempontot figyelembe kell venni a beérkező adatok feldolgozásának biztosításához, a magas beáramlási skála eléréséhez és a betöltési *késés* minimalizálásához (az Time Series Insights által az esemény forrásával kapcsolatos adatok olvasására és feldolgozására szolgáló idő) a [környezet tervezésekor](time-series-insights-update-plan.md).

Time Series Insights előnézeti adatok bejövő házirendjei határozzák meg, hogy az adatok honnan származnak, és milyen formátumban kell megadni az adatok formátumát.

### <a name="ingress-policies"></a>Bejövő házirendek

A *bejövő adatforgalom* azt jelenti, hogyan történik az adatküldés Azure Time Series Insights előzetes verziójú környezetbe.

A legfontosabb konfiguráció, a formázás és az ajánlott eljárások összegzése alább látható.

#### <a name="event-sources"></a>Eseményforrások

Azure Time Series Insights előzetes verzió a következő eseményforrás-forrásokat támogatja:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Azure Time Series Insights az előzetes verzió legfeljebb két eseményforrás használatát támogatja példányok esetében. Egy eseményforrás összekapcsolásakor az ÁME-környezet beolvassa az IOT vagy az Event hub-ban jelenleg tárolt összes eseményt a legrégebbi eseménytől kezdve.

> [!IMPORTANT]
>
> * Előfordulhat, hogy magas kezdeti késleltetést tapasztal, amikor egy eseményforrás az előzetes verziójú környezethez van csatolva.
> Az eseményforrás késése az IoT Hub vagy az Event hub aktuális eseményeinek számától függ.
> * A magas késleltetés az eseményforrás-adat első betöltését követően fog megjelenni. Ha folyamatos, magas késést tapasztal, küldjön támogatási jegyet a Azure Portalon keresztül.

#### <a name="supported-data-format-and-types"></a>Támogatott adatformátumok és típusok

Azure Time Series Insights támogatja az Azure IoT Hub vagy az Azure Event Hubs által eljuttatott UTF-8 kódolású JSON-t. 

A támogatott adattípusok a következők:

| Adattípus | Description |
|---|---|
| **logikai** | Olyan adattípus, amely két állapot egyikét adja meg: `true` vagy `false` . |
| **dateTime** | Egy azonnali időpontot jelöl, amely általában dátum és napszak szerint van megadva. [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) formátumban kifejezve. |
| **hosszú** | Aláírt 64 bites egész szám  |
| **double** | Kétszeres pontosságú 64 bites [IEEE 754](https://ieeexplore.ieee.org/document/8766229) lebegőpontos pont. |
| **sztring** | Szöveges értékek, amelyek Unicode-karakterből állnak.          |

> [!IMPORTANT]
>
> * Az ÁME-környezet erősen van beírva. Ha az eszközök vagy címkék mind az integrált, mind a nem integrált adatok küldését végzik, az eszköz tulajdonságának értékét két különálló dupla és hosszú oszlop tárolja, és az [Egyesítés () függvényt](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) kell használni API-hívások készítésekor és az idősorozat-modell változó kifejezésének definiálásakor.

#### <a name="objects-and-arrays"></a>Objektumok és tömbök

Az esemény hasznos adatainak részeként összetett típusokat (például objektumokat és tömböket) is elküldhet, de az adatok tárolása során a rendszer egy összeolvasztási folyamatot fog végezni.

Részletes információk arról, hogyan formázhatja a JSON-eseményeket, hogyan küldhet összetett típusokat és beágyazott objektumok összeolvasztását, hogy [Hogyan alakíthatja át a JSON-t a bejövő és a lekérdezési](./time-series-insights-update-how-to-shape-events.md) műveletekhez, hogy segítséget nyújtson a tervezéshez és optimalizáláshoz.

### <a name="ingress-best-practices"></a>Beáramló ajánlott eljárások

Javasoljuk, hogy a következő ajánlott eljárásokat alkalmazza:

* A lehetséges késés csökkentése érdekében konfigurálja Azure Time Series Insights és bármely IoT Hub vagy Event hub-t ugyanabban a régióban.

* [Tervezze meg a méretezési igényeket](time-series-insights-update-plan.md) a várható betöltési arány kiszámításával és annak ellenőrzésével, hogy az az alább felsorolt támogatott díjszabás alá esik-e.

* Megtudhatja, hogyan optimalizálhatja és formázhatja a JSON-adatait, valamint az előzetes verzió jelenlegi korlátozásait, ha beolvassa, [Hogyan formázhatja a JSON-t a bejövő és a lekérdezési](./time-series-insights-update-how-to-shape-events.md)művelethez.

* A streaming betöltést csak a közel valós idejű és a legutóbbi adatmennyiségek esetében használja, a folyamatos adatátvitelek nem támogatottak.

#### <a name="historical-data-ingestion"></a>Korábbi adatfeldolgozás

Azure Time Series Insights előzetes verzióban jelenleg nem támogatott a folyamatos átviteli folyamat használata a korábbi adatimportáláshoz. Ha a korábbi adatait importálnia kell a környezetbe, kövesse az alábbi irányelveket:

* Ne továbbítsa párhuzamosan az élő és a korábbi adatforrásokat. A lekéréses adatmennyiség miatt a lekérdezés teljesítménye csökken.
* A legjobb teljesítmény érdekében időben berendezheti a múltbeli adatmennyiséget.
* Maradjon az alábbi betöltési átviteli sebességre vonatkozó korlátok között.
* Ha az adatok régebbiek, mint a meleg tárolási megőrzési időszak, tiltsa le a meleg tárolást.

### <a name="ingress-scale-and-preview-limitations"></a>Beáramló méretezési és előzetes korlátozások

Azure Time Series Insights előzetes bejövő korlátozások az alábbiakban olvashatók.

> [!TIP]
> Tekintse meg az előzetes verzióra vonatkozó összes korlát átfogó listáját az [előnézeti környezet megtervezése](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) című cikkből.

#### <a name="per-environment-limitations"></a>/Környezet korlátozásai

Általánosságban elmondható, hogy a bejövő forgalom díjszabása a szervezet eszközeinek száma, az esemény-emisszió gyakorisága, valamint az egyes események mérete:

*  **Az eszközök száma** × **esemény kibocsátásának gyakorisága** × **az egyes események mérete**.

Alapértelmezés szerint a Time Series Insights-előnézet a bejövő adatmennyiséget **legfeljebb 1 megabájt/másodperc (Mbps)** sebességgel képes befogadni Time Series Insights környezetben. A [hub-partíciók esetében](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#hub-partitions-and-per-partition-limits)további korlátozások vannak érvényben.

> [!TIP]
>
> * A sávszélesség-támogatás a 16 MBps-ig terjedő sebességek igény szerinti megadásával adható meg.
> * Vegye fel velünk a kapcsolatot, ha a támogatási jegyet a Azure Portalon keresztül küldi el, ha nagyobb átviteli sebességre van szüksége.
 
* **1. példa:**

    A contoso szállítása 100 000 olyan eszközzel rendelkezik, amely percenként három alkalommal bocsát ki eseményt. Az események mérete 200 bájt. A IoT Hub négy partíciót használnak a Time Series Insights-esemény forrásaként.

    * A Time Series Insights-környezet betöltési sebessége a következő: **100 000 eszköz * 200 bájt/esemény * (3/60 esemény/másodperc) = 1 MB/s**.
    * A másodpercenkénti betöltési arány 0,25 MBps.
    * A contoso szállításának betöltési sebessége az előzetes verzióra vonatkozó korlátozáson belül lenne.

* **2. példa:**

    A contoso Fleet Analytics 60 000 olyan eszközt tartalmaz, amely másodpercenként egy eseményt bocsát ki. Egy Event hub-t használnak, amelynek a partícióinak száma 4, Time Series Insights eseményforrás. Az események mérete 200 bájt.

    * A környezet betöltési sebessége a következő: **60 000 eszköz * 200 bájt/esemény * 1 esemény/mp = 12 Mbps**.
    * A/partíciók aránya 3 MBps.
    * A contoso Fleet Analytics betöltési aránya a környezet és a partíciók korlátain alapul. Kérést küldhetnek Time Series Insightson keresztül Azure Portal a környezet betöltési arányának növeléséhez, és létrehozhat egy olyan Event hub-t, amely több partícióval rendelkezik az előzetes verzió korlátain belül.

#### <a name="hub-partitions-and-per-partition-limits"></a>Hub-partíciók és partíciós korlátok

Time Series Insights környezet megtervezésekor fontos figyelembe venni azon eseményforrás (ok) konfigurációját, amelyekhez csatlakozni fog Time Series Insightshoz. Mind az Azure-IoT Hub, mind a Event Hubs partíciókat használ az események feldolgozásához szükséges horizontális méretezés lehetővé tételéhez. 

A *partíciók* a központban tárolt események rendezett sorrendje. A partíciók száma a központ létrehozási fázisában van beállítva, és nem módosítható.

Event Hubs particionálással kapcsolatos ajánlott eljárások esetében ellenőrizze, [hogy hány partícióra van szükségem?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> A Azure Time Series Insights leggyakrabban használt IoT huboknak négy partícióra van szükségük.

Akár új központot hoz létre a Time Series Insights-környezethez, akár egy meglévőt használ, ki kell számítania a partíciók betöltési arányát annak megállapításához, hogy az előnézeti korlátokon belül van-e. 

A Azure Time Series Insights előzetes **verziójának jelenleg a 0,5 Mbps-ra vonatkozó általános korlátja**van.

#### <a name="iot-hub-specific-considerations"></a>IoT Hub-specifikus megfontolások

Ha egy eszköz a IoT Hubban jön létre, akkor véglegesen hozzá van rendelve egy partícióhoz. Ennek során a IoT Hub képes biztosítani az események rendezését (mivel a hozzárendelés soha nem változik).

A rögzített partíciós hozzárendelések olyan Time Series Insights példányokat is érintenek, amelyek IoT Hub alsóbb rétegből érkező adatok betöltését hajtják végre. Ha több eszközről származó üzeneteket továbbítanak a központba ugyanazzal az átjáró-eszköz azonosítójával, akkor előfordulhat, hogy ugyanazon a partíción érkeznek, amely a partíciós méretezési korlátokat is meghaladja.

**Hatás**:

* Ha egy partíció az előzetes verzióra vonatkozó korláton belül tartósan teljesíti a betöltési sebességet, lehetséges, hogy a Time Series Insights nem fogja szinkronizálni az összes eszközt a IoT Hub adatmegőrzési időszak túllépése előtt telemetria. Ennek eredményeképpen a továbbított adatmennyiségek elvesznek, ha a betöltési korlátokat folyamatosan túllépik.

Ennek a körülménynek a mérséklése érdekében a következő ajánlott eljárásokat javasoljuk:

* A megoldás üzembe helyezése előtt számítsa ki a környezet és a partíciók betöltési díjait.
* Győződjön meg arról, hogy a IoT Hub-eszközök terheléselosztása a lehető legtávolabbi mértékben történik.

> [!IMPORTANT]
> Az IoT Hub esemény forrásaként használó környezetek esetében a használatban lévő hub-eszközök számával Számítsuk ki a betöltési arányt, hogy az előzetes verzióban a feltöltési sebesség az 0,5 MBps-ra esik.
>
> * Még ha több esemény is érkezik egyszerre, az előzetes verzióra vonatkozó korlátot a rendszer nem fogja meghaladni.

  ![IoT Hub partíciós diagram](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Az alábbi forrásokból tájékozódhat a hub átviteli sebességének és partícióinak optimalizálásáról:

* [IoT Hub skála](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Event hub-skála](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Event hub-partíciók](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Adattárolás

Time Series Insights előzetes *utólagos* elszámolású (TB) SKU-környezet létrehozásakor két Azure-erőforrást hoz létre:

* Egy Azure Time Series Insights előnézeti környezet, amely a meleg adattároláshoz konfigurálható.
* Egy Azure Storage általános célú v1 blob-fiók a hideg adattároláshoz.

A meleg tárolóban tárolt adatai csak a [Time Series lekérdezés](./time-series-insights-update-tsq.md) és a [Azure Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md)használatával érhetők el. A meleg áruház a Time Series Insights környezet létrehozásakor kiválasztott [megőrzési időszakon](./time-series-insights-update-plan.md#the-preview-environment) belül friss adatokkal fog szerepelni.

Time Series Insights az előnézet a hűtőházi tároló adatait az Azure Blob Storage-ba menti a [Parquet fájlformátumban](#parquet-file-format-and-folder-structure). Time Series Insights az előnézet kizárólag a hűtőházi adattárolási adattárakat kezeli, de közvetlenül a standard parketta-fájlként is elérhető.

> [!WARNING]
> Az Azure Blob Storage-fiók tulajdonosaként, ahol a hűtőházi adattárolási adat található, teljes hozzáférése van a fiókban lévő összes adathoz. Ez a hozzáférés írási és törlési engedélyeket is tartalmaz. Ne szerkessze vagy törölje az előnézeti írással Time Series Insights adatot, mert az adatvesztést eredményezhet.

### <a name="data-availability"></a>Adatok rendelkezésre állása

Az optimális lekérdezési teljesítmény érdekében Azure Time Series Insights előnézeti partíciókat és indexeli az adataikat. Az adatok elérhetővé válnak mind a meleg (ha engedélyezve), mind a hűtőházi tároló lekérdezéséhez az indexelés után. A betöltött adatmennyiség hatással lehet erre a rendelkezésre állásra.

> [!IMPORTANT]
> Az előzetes verzió ideje alatt akár 60 másodperces időszakot is megtapasztalhat, mielőtt az adatmennyiség elérhetővé válik. Ha 60 másodpercen túli jelentős késés tapasztalható, küldjön egy támogatási jegyet a Azure Portalon keresztül.

## <a name="azure-storage"></a>Azure Storage

Ez a szakasz a Azure Time Series Insights előzetes verziójának megfelelő Azure Storage-adatokat ismerteti.

Az Azure Blob Storage részletes ismertetését olvassa el a [Storage Blobok bemutatása](../storage/blobs/storage-blobs-introduction.md)című cikkből.

### <a name="your-storage-account"></a>A Storage-fiók

Azure Time Series Insights előzetes verziójú TB-környezet létrehozásakor létrejön egy Azure Storage általános célú v1 blob-fiók, amely a hosszú távú hűtőházi tárolóként jön létre.  

Azure Time Series Insights előzetes verzióban az Azure Storage-fiókban az egyes események két példánya is megmarad. Az egyik másolat a betöltési idő alapján rendezi az eseményeket, így mindig lehetővé teszi az események elérését egy időben rendezett sorrendben. Az idő múlásával Time Series Insights előzetes verzióban az adatok újraparticionált másolata is létrejön, így optimalizálható a végrehajtás Time Series Insights a lekérdezés.

A nyilvános előzetes verzióban az Azure Storage-fiókban az adatai határozatlan ideig tárolódnak.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Time Series Insights Blobok írása és szerkesztése

A lekérdezés teljesítményének és az adatelérhetőségnek a biztosításához ne szerkessze vagy töröljön minden olyan blobot, amelyet Time Series Insights előnézet hoz létre.

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>Hozzáférés az Time Series Insights előzetes verziójának hűtőházi tárolására

Az adatoknak a [Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md) és a [Time Series lekérdezésből](./time-series-insights-update-tsq.md)való elérése mellett előfordulhat, hogy közvetlenül a hűtőházi tárolóban tárolt parketta-fájlokból is el szeretné érni az adatait. Például elolvashatja, átalakíthatja és megtisztíthatja az Jupyter-jegyzetfüzetben tárolt adatait, majd felhasználhatja a Azure Machine Learning modellnek ugyanabban a Spark-munkafolyamatban való betanításához.

Az adatok közvetlenül az Azure Storage-fiókból való eléréséhez olvasási hozzáféréssel kell rendelkeznie a Time Series Insights előzetes verzió adatainak tárolásához használt fiókhoz. Ezután a Parquet fájl létrehozási ideje alapján elolvashatja a kiválasztott adatmennyiséget az `PT=Time` alább ismertetett mappában található parketta- [fájl formátuma](#parquet-file-format-and-folder-structure) szakaszban.  A Storage-fiókhoz való olvasási hozzáférés engedélyezésével kapcsolatos további információkért lásd: [a Storage-fiók erőforrásaihoz való hozzáférés kezelése](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Adattörlés

Ne törölje a Time Series Insights előnézeti fájljait. A kapcsolódó adatok csak Time Series Insights előzetes verzióban kezelhetők.

### <a name="parquet-file-format-and-folder-structure"></a>A parketta fájlformátuma és a mappa szerkezete

A Parquet egy nyílt forráskódú, oszlopos fájlformátum, amely hatékony tárolást és teljesítményt nyújt. Time Series Insights az előzetes verzióban a parketta használatával engedélyezhető a Time Series ID-alapú lekérdezési teljesítmény a skálán.  

A Parquet fájltípussal kapcsolatos további információkért olvassa el a [parketta dokumentációját](https://parquet.apache.org/documentation/latest/).

Time Series Insights az alábbi módon tárolja az adatai másolatait:

* Az első, a kezdeti másolat a betöltési idő alapján van particionálva, és nagyjából megérkezésük sorrendjében tárolja az adatot. Ez az adat a `PT=Time` mappában található:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* A második, újraparticionált másolat az idősorozat-azonosítók szerint van csoportosítva, és a `PT=TsId` mappában található:

  `V=1/PT=TsId/<TSI_INTERNAL_STRUCTURE>/<TSI_INTERNAL_NAME>.parquet`

A mappában lévő Blobok neveinek időbélyeg-értéke az `PT=Time` ÁME (az események időbélyege nem).

A mappában lévő adatok a `PT=TsId` lekérdezéshez az idő múlásával lesznek optimalizálva, és nem statikus. Az újraparticionálás során ugyanezek az események több blobban is előfordulhatnak. Emellett a Blobok elnevezése a jövőben is változhat.

> [!NOTE]
>
> * `<YYYY>`leképezi a négy számjegyű év ábrázolását.
> * `<MM>`leképezi a kétjegyű hónapok ábrázolását.
> * `<YYYYMMDDHHMMSSfff>`leképezi a kétjegyű (), kétjegyű hónap (), kétjegyű (), kétjegyű (), kétjegyű (), `YYYY` `MM` `DD` `HH` `MM` kétszámjegyű második ( `SS` ) és három számjegyű ezredmásodperc ( `fff` ) közötti időbélyegzőt ábrázoló ábrázolást.

Time Series Insights előnézeti események a következő módon vannak leképezve a parketta-fájl tartalmára:

* Minden esemény egyetlen sorra van leképezve.
* Minden sor tartalmazza az **időbélyegző** oszlopot egy esemény időbélyegzővel. Az időbélyegző tulajdonság soha nem null értékű. Alapértelmezés szerint az **Event várólistán lévő idő** , ha nincs megadva az időbélyeg tulajdonság az eseményforrás számára. A tárolt időbélyegző mindig UTC szerint van.
* Minden sor tartalmazza a Time Series Insights-környezet létrehozásakor definiált idősoros azonosító (TSID) oszlop (oka) t. A TSID-tulajdonság neve tartalmazza az `_string` utótagot.
* A telemetria-adatként elküldett egyéb tulajdonságok `_string` `_bool` a tulajdonság típusától függően (string), (Boolean), `_datetime` (datetime) vagy `_double` (Double) oszlopokra vannak leképezve.
* Ez a leképezési séma a **(z) V = 1** néven hivatkozott fájlformátum első verziójára vonatkozik, és az azonos nevű alapmappában tárolódik. A szolgáltatás fejlődése során ez a leképezési séma változhat, és a hivatkozási név megnő.

## <a name="next-steps"></a>További lépések

- Olvassa el [, hogyan formázhatja a JSON-t a bejövő és a lekérdezéshez](./time-series-insights-update-how-to-shape-events.md).

- További információ az új [adatmodellezésről](./time-series-insights-update-tsm.md).

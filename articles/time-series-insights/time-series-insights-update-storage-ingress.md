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
ms.date: 12/31/2019
ms.custom: seodec18
ms.openlocfilehash: f00529d00312fd6acb045de698590047f991bec7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714293"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Adattárolás és bejövő forgalom Azure Time Series Insights előzetes verzióban

Ez a cikk az adattárolási és a bejövő Azure Time Series Insights előzetes verziójának frissítéseit ismerteti. Lefedi a mögöttes tárolási struktúrát, a fájlformátumot és az idősorozat-azonosító tulajdonságot. Emellett tárgyalja az alapul szolgáló beáramlási folyamatot, az ajánlott eljárásokat és az aktuális előzetes verzióra vonatkozó korlátozásokat.

## <a name="data-ingress"></a>Bejövő adatforgalom

Az Azure Time Series Insights-környezet egy betöltési motort tartalmaz az idősoros adatok gyűjtéséhez, feldolgozásához és tárolásához. A környezet megtervezése során figyelembe kell venni, hogy az összes bejövő adatot feldolgozzák, és hogy magas beáramlási arányt és kis mennyiségű adatfeldolgozási késleltetést kell elérni (az ÁME által az eseményből származó adatok olvasására és feldolgozására tett idő forrás). 

Time Series Insights előzetes verzióban az adatbeviteli szabályzatok határozzák meg, hogy az adatok honnan származnak, és milyen formátumban kell megadni az adatok formátumát.

### <a name="ingress-policies"></a>Bejövő házirendek

#### <a name="event-sources"></a>Eseményforrás

Time Series Insights előzetes verzió a következő eseményforrás-forrásokat támogatja:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Time Series Insights az előzetes verzió legfeljebb két eseményforrás használatát támogatja példányok esetében.

> [!WARNING] 
> * Előfordulhat, hogy magas kezdeti késleltetést tapasztal, amikor egy eseményforrás az előzetes verziójú környezethez van csatolva. 
> Az eseményforrás késése az IoT Hub vagy az Event hub aktuális eseményeinek számától függ.
> * A magas késleltetés az eseményforrás-adat első betöltését követően fog megjelenni. Ha továbbra is nagy késéssel jár, vegye fel velünk a kapcsolatot egy támogatási jegy elküldésével a Azure Portalon keresztül.

#### <a name="supported-data-format-and-types"></a>Támogatott adatformátumok és típusok

Azure Time Series Insights támogatja az Azure IoT Hub vagy az Azure Event Hubs által küldött UTF8-kódolású JSON-t. 

Alább látható a támogatott adattípusok listája.

| Adattípus | Leírás |
|-----------|------------------|-------------|
| bool      |   A következő két állapot egyike: true vagy FALSE.       |
| Dátum és idő    |   Egy azonnali időpontot jelöl, amely általában dátum és napszak szerint van megadva. A dátum/idő értékének ISO 8601 formátumúnak kell lennie.      |
| double    |   Kétszeres pontosságú 64 bites IEEE 754 lebegőpontos pont
| sztring    |   Szöveges értékek, amelyek Unicode-karakterből állnak.          |

#### <a name="objects-and-arrays"></a>Objektumok és tömbök

Az esemény hasznos adatainak részeként összetett típusokat (például objektumokat és tömböket) is küldhet, de az adatok a tárolás során egy összeolvasztási folyamaton keresztül fognak esni. A JSON-események formázásával, valamint az összetett típus és a beágyazott objektumok összeolvasztásával kapcsolatos további információkért tekintse meg az oldalt, [amely bemutatja, hogyan formázhatja a JSON-t a bejövő és a lekérdezési](./time-series-insights-update-how-to-shape-events.md)adatokhoz.


### <a name="ingress-best-practices"></a>Beáramló ajánlott eljárások

Javasoljuk, hogy a következő ajánlott eljárásokat alkalmazza:

* Konfigurálja Time Series Insights és az IoT Hub vagy az Event hub-t ugyanabban a régióban, hogy csökkentse a hálózatban felmerülő betöltési késést.
* Tervezze meg a méretezési igényeket a várható betöltési arány kiszámításával és annak ellenőrzésével, hogy az az alább felsorolt támogatott díjszabás alá esik
* Megtudhatja, hogyan optimalizálhatja és formázhatja a JSON-adatait, valamint az előzetes verzió jelenlegi korlátozásait, ha beolvassa, [Hogyan formázhatja a JSON-t a bejövő és a lekérdezési](./time-series-insights-update-how-to-shape-events.md)művelethez.

### <a name="ingress-scale-and-limitations-in-preview"></a>Átáramló méretezés és korlátozások az előzetes verzióban

#### <a name="per-environment-limitations"></a>/Környezet korlátozásai

Általánosságban elmondható, hogy a bejövő forgalom díjszabása a szervezet eszközeinek száma, az esemény-emisszió gyakorisága, valamint az egyes események mérete:

*  **Az eszközök száma** × **esemény kibocsátásának gyakorisága** × **az egyes események mérete**.

Alapértelmezés szerint a Time Series Insights előzetes verziója legfeljebb 1 megabájt/másodperc (MBps) sebességgel képes befogadni a bejövő adatot az **ÁME-környezetekben**. Vegye fel velünk a kapcsolatot, ha ez nem felel meg a követelményeknek, egy támogatási jegyet a Azure Portalban akár 16 MBps-t is támogatunk.
 
1\. példa: a contoso-szállítás 100 000 olyan eszközzel rendelkezik, amely percenként három alkalommal bocsát ki eseményt. Az események mérete 200 bájt. Egy 4-es partíciót használó Event hub-t használnak az ÁME-esemény forrásaként.
Az ÁME-környezet betöltési sebessége a következő: 100 000-es eszközök * 200 bájt/esemény * (3/60 esemény/másodperc) = 1 MBps.
A másodpercenkénti betöltési arány 0,25 MBps.
A contoso szállításának betöltési sebessége az előzetes verzióra vonatkozó korlátozáson belül lenne.
 
2\. példa: a contoso Fleet Analytics 60 000 olyan eszközt tartalmaz, amely másodpercenként egy eseményt bocsát ki. Az ÁME-eseményforrás IoT Hub 24 partíciók számát használják. Az események mérete 200 bájt.
A környezet betöltési sebessége a következő: 20 000 eszköz * 200 bájt/esemény * 1 esemény/mp = 4 MBps.
A particionálási sebesség 1 MB/s.
A contoso-flotta elemzéséhez az Azure Portal egy dedikált környezetre vonatkozó kérelmet kell benyújtania az ÁME-nek.

#### <a name="hub-partitions-and-per-partition-limits"></a>Hub-partíciók és partíciós korlátok

Az ÁME-környezet tervezésekor fontos figyelembe venni azon eseményforrás (ok) konfigurációját, amelyekhez csatlakozni fog az ÁME-hez. Mind az Azure-IoT Hub, mind a Event Hubs partíciókat használ az események feldolgozásához szükséges horizontális méretezés lehetővé tételéhez.  A partíciók a központban tárolt események rendezett sorrendje. A partíciók száma a IoT vagy a Event Hubs létrehozási fázisában van beállítva, és nem módosítható. A partíciók számának meghatározásával kapcsolatos további információkért tekintse meg a Event Hubs "GYIK, hogy hány partícióra van szükségem? Az IoT Hubt használó ÁME-környezetek esetében általában a legtöbb IoT-hubhoz 4 partícióra van szükség. Függetlenül attól, hogy létrehoz egy új hubot az ÁME-környezethez, vagy egy meglévőt használ, ki kell számítania a partíciók betöltési arányát annak megállapításához, hogy az előnézeti korlátokon belül van-e. Az ÁME előzetes verziója jelenleg 0,5 MB/s-os **partíciós** korláttal rendelkezik. Hivatkozásként használja az alábbi példákat, és ha Ön IoT Hub felhasználó, vegye figyelembe a következő IoT Hub-specifikus megfontolást.

#### <a name="iot-hub-specific-considerations"></a>IoT Hub-specifikus megfontolások

Egy eszköz a IoT Hub való létrehozásakor egy partícióhoz van rendelve, és a partíció-hozzárendelés nem változik. Ezzel a IoT Hub képes biztosítani az események rendezését. Ez azonban bizonyos helyzetekben az ÁME-t is érinti az alsóbb rétegbeli olvasóként. Ha több eszközről érkező üzeneteket továbbítanak a központba ugyanazzal az átjáró-AZONOSÍTÓval, amely ugyanabban a partícióban érkezik, így a partíciók méretezési korlátozását is meghaladhatja. 

**Hatás**: Ha egy partíció tartósan betöltési arányt tapasztal az előzetes verzió korlátozásával, fennáll a valószínűsége annak, hogy az ÁME-olvasó soha nem fog megjelenni a IoT hub adatmegőrzési időszak túllépése előtt. Ez adatvesztést eredményezhet.

A következőket javasoljuk: 

* A megoldás üzembe helyezése előtt számítsa ki a környezetet és a partíciók betöltési arányát
* Győződjön meg arról, hogy a IoT Hub-eszközök (és így a partíciók) terheléselosztása a legtávolabbi lehetséges kiterjesztéssel történik

> [!WARNING]
> Az IoT Hub esemény forrásaként használó környezetek esetében a használatban lévő hub-eszközök számával Számítsuk ki a betöltési arányt, hogy az előzetes verzióban a feltöltési sebesség az 0,5 MBps-ra esik.

  ![IoT Hub partíciós diagram](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

A következő hivatkozásokra kattintva további információkat találhat az átviteli egységekről és a partícióról:

* [IoT Hub skála](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Event hub-skála](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Event hub-partíciók](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Adattárolás

Time Series Insights előzetes utólagos elszámolású SKU-környezet létrehozásakor két Azure-erőforrást hoz létre:

* Olyan Time Series Insights előnézeti környezet, amely opcionálisan tartalmazhatja a meleg tárolási képességeket.
* Egy Azure Storage általános célú v1 blob-fiók a hideg adattároláshoz.

A meleg tárolóban tárolt adatai csak a [Time Series lekérdezés](./time-series-insights-update-tsq.md) és a [Azure Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md)használatával érhetők el. 

Time Series Insights az előnézet a hűtőházi tároló adatait az Azure Blob Storage-ba menti a [Parquet fájlformátumban](#parquet-file-format-and-folder-structure). Time Series Insights az előnézet kizárólag a hűtőházi adattárolási adattárakat kezeli, de közvetlenül a standard parketta-fájlként is elérhető.

> [!WARNING]
> Az Azure Blob Storage-fiók tulajdonosaként, ahol a hűtőházi adattárolási adat található, teljes hozzáférése van a fiókban lévő összes adathoz. Ez a hozzáférés írási és törlési engedélyeket is tartalmaz. Ne szerkessze vagy törölje az előnézeti írással Time Series Insights adatot, mert az adatvesztést eredményezhet.

### <a name="data-availability"></a>Az adatelérhetőség

Az optimális lekérdezési teljesítmény érdekében Time Series Insights előnézeti partíciókat és indexeli az adataikat. A lekérdezés az indexelés után elérhetővé válik. A betöltött adatmennyiség hatással lehet erre a rendelkezésre állásra.

> [!IMPORTANT]
> Az előzetes verzió ideje alatt akár 60 másodperces időszakot is megtapasztalhat, mielőtt az adatmennyiség elérhetővé válik. Ha 60 másodpercen túli jelentős késés tapasztalható, küldjön egy támogatási jegyet a Azure Portalon keresztül.

## <a name="azure-storage"></a>Azure Storage

Ez a szakasz a Azure Time Series Insights előzetes verziójának megfelelő Azure Storage-adatokat ismerteti.

Az Azure Blob Storage részletes ismertetését olvassa el a [Storage Blobok bemutatása](../storage/blobs/storage-blobs-introduction.md)című cikkből.

### <a name="your-storage-account"></a>A Storage-fiók

Time Series Insights előzetes utólagos elszámolású környezet létrehozásakor az Azure Storage általános célú v1 blob-fiókját a rendszer hosszú távú, hűtőházi tárolóként hozza létre.  

Time Series Insights az előnézet az Azure Storage-fiókban az egyes események két példányát teszi közzé. A kezdeti másolatban a betöltési idő szerint rendezett események szerepelnek, és a rendszer mindig megőrzi azokat, így más szolgáltatásokkal is elérheti azt. A nyers parketta-fájlok feldolgozásához a Spark, a Hadoop és más ismerős eszközöket használhatja. 

Time Series Insights előzetes verzió újraparticionálja a Parquet fájlokat az Time Series Insights lekérdezés optimalizálásához. Az adatbázis újraparticionált példánya is mentve lesz.

A nyilvános előzetes verzióban az Azure Storage-fiókban az adatai határozatlan ideig tárolódnak.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Time Series Insights Blobok írása és szerkesztése

A lekérdezés teljesítményének és az adatelérhetőségnek a biztosításához ne szerkessze vagy töröljön minden olyan blobot, amelyet Time Series Insights előnézet hoz létre.

#### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Adatok elérése és exportálása Time Series Insights előzetes verzióból

Előfordulhat, hogy a Time Series Insights Preview Explorerben a más szolgáltatásokkal együtt használt adatnézeteket szeretné elérni. Az adataival például létrehozhat egy jelentést Power BI vagy betaníthat egy gépi tanulási modellt Azure Machine Learning Studio használatával. Az adatait használhatja a Jupyter-jegyzetfüzetek átalakításához, megjelenítéséhez és modellezéséhez is.

Az adatai három általános módon érhetők el:

* A Time Series Insights Preview Explorerben. Az adatok CSV-fájlként is exportálhatók az Intézőből. További információ: [Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md).
* A Time Series Insights Preview API-ból Get Events lekérdezés használatával. Ha többet szeretne megtudni erről az API-ról, olvassa el a [Time Series-lekérdezés](./time-series-insights-update-tsq.md)című témakört.
* Közvetlenül egy Azure Storage-fiókból. Olvasási hozzáféréssel kell rendelkeznie a Time Series Insights előnézeti adataihoz való hozzáféréshez használt fiókhoz. További információért olvassa el [a Storage-fiók erőforrásaihoz való hozzáférés kezelése](../storage/blobs/storage-manage-access-to-resources.md)című témakört.

#### <a name="data-deletion"></a>Adattörlés

Ne törölje a Time Series Insights előnézeti fájljait. A kapcsolódó adatok csak Time Series Insights előzetes verzióban kezelhetők.

### <a name="parquet-file-format-and-folder-structure"></a>A parketta fájlformátuma és a mappa szerkezete

A Parquet egy nyílt forráskódú, oszlopos fájlformátum, amely hatékony tároláshoz és teljesítményhez lett tervezve. Time Series Insights az előzetes verzió a parkettát használja az alábbi okok miatt. Idősorozat-azonosító alapján particionálja az adatmennyiséget a lekérdezési teljesítményhez a skálán.  

A Parquet fájltípussal kapcsolatos további információkért olvassa el a [parketta dokumentációját](https://parquet.apache.org/documentation/latest/).

Time Series Insights az alábbi módon tárolja az adatai másolatait:

* Az első, a kezdeti másolat a betöltési idő alapján van particionálva, és nagyjából megérkezésük sorrendjében tárolja az adatot. Az adat a `PT=Time` mappában található:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* A második, újraparticionált másolatot az idősorozat-azonosítók csoportosításával particionálja, és a `PT=TsId` mappában találhatók:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

Mindkét esetben az időértékek a blob létrehozási idejére vonatkoznak. A `PT=Time` mappában lévő adatértékek megmaradnak. A `PT=TsId` mappában lévő adatok az idő függvényében lesznek optimalizálva, és nem maradnak statikusak.

> [!NOTE]
> * `<YYYY>` térképeket egy négyjegyű év ábrázolásához.
> * `<MM>` térképeket egy kétjegyű hónap ábrázolásához.
> * `<YYYYMMDDHHMMSSfff>` térképeket egy időbélyegzős ábrázoláshoz négyjegyű (`YYYY`), kétjegyű hónap (`MM`), kétjegyű nap (`DD`), kétjegyű óra (`HH`), kétjegyű perc (`MM`), kétszámjegyű második (`SS`) és háromjegyű ezredmásodperc (`fff`) alapján.

Time Series Insights előnézeti események a következő módon vannak leképezve a parketta-fájl tartalmára:

* Minden esemény egyetlen sorra van leképezve.
* Minden sor tartalmazza az **időbélyegző** oszlopot egy esemény időbélyegzővel. Az időbélyegző tulajdonság soha nem null értékű. Alapértelmezés szerint az **Event várólistán lévő időt** adja meg, ha az időbélyegző tulajdonság nincs megadva az esemény forrásában. Az időbélyegző mindig UTC szerint van.
* Minden sor tartalmazza az idősorozat-azonosító oszlop (oka) t, amely a Time Series Insights környezet létrehozásakor van meghatározva. A tulajdonság neve tartalmazza a `_string` utótagot.
* A telemetria-adatként elküldett összes többi tulajdonságot a tulajdonság típusától függően `_string` (string), `_bool` (Boolean), `_datetime` (datetime) vagy `_double` (Double) végződésű oszlopokra kell leképezni.
* Ez a leképezési séma a fájl formátumának első verziójára vonatkozik, amelyre a **V = 1**hivatkozik. A szolgáltatás fejlődése során előfordulhat, hogy a név növekszik.

## <a name="next-steps"></a>Következő lépések

- Olvassa el [, hogyan formázhatja a JSON-t a bejövő és a lekérdezéshez](./time-series-insights-update-how-to-shape-events.md).

- További információ az új [adatmodellezésről](./time-series-insights-update-tsm.md).

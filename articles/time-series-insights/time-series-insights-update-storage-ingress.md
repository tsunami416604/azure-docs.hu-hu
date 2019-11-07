---
title: Adattárolás és bejövő forgalom Azure Time Series Insights előzetes verzióban | Microsoft Docs
description: Az adattárolás és a bejövő forgalom megismerése Azure Time Series Insights előzetes verzióban.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: d0cdd78aaa2b58743e16a2e7cfe213a9daed85ff
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605886"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Adattárolás és bejövő forgalom Azure Time Series Insights előzetes verzióban

Ez a cikk az adattárolási és a bejövő Azure Time Series Insights előzetes verziójának frissítéseit ismerteti. Lefedi a mögöttes tárolási struktúrát, a fájlformátumot és az idősorozat-azonosító tulajdonságot. Emellett tárgyalja az alapul szolgáló beáramlási folyamatot, az ajánlott eljárásokat és az aktuális előzetes verzióra vonatkozó korlátozásokat.

## <a name="data-ingress"></a>Bejövő adatforgalom

Az Azure Time Series Insights-környezet egy betöltési motort tartalmaz az idősoros adatok gyűjtéséhez, feldolgozásához és tárolásához. A környezet megtervezése során figyelembe kell venni, hogy az összes bejövő adatot feldolgozzák, és hogy magas beáramlási arányt és kis mennyiségű adatfeldolgozási késleltetést kell elérni (az ÁME által az eseményből származó adatok olvasására és feldolgozására tett idő forrás). Time Series Insights előzetes verzióban az adatbeviteli szabályzatok határozzák meg, hogy az adatok honnan származnak, és milyen formátumban kell megadni az adatok formátumát.

### <a name="ingress-policies"></a>Bejövő házirendek

Time Series Insights előzetes verzió a következő eseményforrás-forrásokat támogatja:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Time Series Insights az előzetes verzió legfeljebb két eseményforrás használatát támogatja példányok esetében.
  
Azure Time Series Insights támogatja az Azure IoT Hub vagy az Azure Event Hubs használatával küldött JSON-t.

> [!WARNING] 
> Amikor új eseményforrás csatolását a Time Series Insights előzetes verziójának környezetéhez, a IoT Hub vagy az Event hub eseményeinek számától függően előfordulhat, hogy magas kezdeti betöltési késést tapasztal. Az adatok betöltése esetén ezt a nagy késést kell elvárnia, de ha a felhasználói élmény azt jelzi, hogy az adott esetben a támogatási jegyet a Azure Portalon keresztül küldi el, vegye fel velünk a kapcsolatot.

## <a name="ingress-best-practices"></a>Beáramló ajánlott eljárások

Javasoljuk, hogy a következő ajánlott eljárásokat alkalmazza:

* Konfigurálja a Time Series Insights és az IoT hub-t vagy az Event hub-t ugyanabban a régióban. Ez csökkenti a hálózat miatt felmerülő betöltési késést.
* Tervezze meg a méretezési igényeket a várható betöltési arány kiszámításával és annak ellenőrzésével, hogy az az alább felsorolt támogatott díjszabás alá esik
* Megtudhatja, hogyan optimalizálhatja és formázhatja a JSON-adatait, valamint az előzetes verzió jelenlegi korlátozásait, ha beolvassa, [Hogyan formázhatja a JSON-t a bejövő és a lekérdezési](./time-series-insights-update-how-to-shape-events.md)művelethez.

### <a name="ingress-scale-and-limitations-in-preview"></a>Átáramló méretezés és korlátozások az előzetes verzióban

Alapértelmezés szerint a Time Series Insights-előnézet legfeljebb 1 megabájt/másodperc (MB/s) méretű kezdeti bejövő forgalom használatát támogatja. Ha szükséges, akár 16 MB/s adatátviteli sebesség is rendelkezésre áll, vegye fel velünk a kapcsolatot, ha erre szükség van, ha beküld egy támogatási jegyet a Azure Portal. Emellett a partíciós korlát 0,5 MB/s. Ennek az a következménye, hogy az ügyfelek IoT Hub kifejezetten a IoT Hub eszköz egy partíciója közötti affinitást használják. Azokban az esetekben, amikor egy átjáró-eszköz a saját eszköz-AZONOSÍTÓjának és kapcsolati karakterláncának használatával továbbít üzeneteket a hubhoz, fennáll a veszélye annak, hogy elérte az 0,5 MB/s korlátot, mivel az üzenetek egyetlen partíción érkeznek meg, még akkor is, ha az esemény adattartalma eltérő TS-t használ Azonosítók. Általánosságban elmondható, hogy a bejövő forgalom aránya a szervezetben lévő eszközök számának, az esemény-kibocsátás gyakoriságának és az esemény méretének a tényezője. A betöltési arány kiszámításakor IoT Hub felhasználóknak a használatban lévő hub-kapcsolatok számát kell használniuk, nem a szervezet összes eszközét. A továbbfejlesztett méretezési támogatás folyamatban van. Ezt a dokumentációt a rendszer frissíti, hogy tükrözze ezeket a módosításokat. 

> [!WARNING]
> Az IoT Hub eseményforrásként használó környezetek esetében a használatban lévő hub-eszközök száma alapján számítja ki a betöltési arányt.

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
> A Time Series Insights közelgő általánosan elérhető (GA) kiadása 60 másodpercen belül elérhetővé teszi az adatoknak az esemény forrásból való beolvasása után. Az előzetes verzió ideje alatt hosszabb időt is igénybe vehet, amíg az adatszolgáltatás elérhetővé válik. Ha 60 másodpercen túli jelentős késés tapasztalható, küldjön egy támogatási jegyet a Azure Portalon keresztül.

## <a name="azure-storage"></a>Azure Storage

Ez a szakasz a Azure Time Series Insights előzetes verziójának megfelelő Azure Storage-adatokat ismerteti.

Az Azure Blob Storage részletes ismertetését olvassa el a [Storage Blobok bemutatása](../storage/blobs/storage-blobs-introduction.md)című cikkből.

### <a name="your-storage-account"></a>A Storage-fiók

Time Series Insights előzetes utólagos elszámolású környezet létrehozásakor az Azure Storage általános célú v1 blob-fiókját a rendszer hosszú távú, hűtőházi tárolóként hozza létre.  

Time Series Insights az előnézet az Azure Storage-fiókban az egyes események két példányát teszi közzé. A kezdeti másolatban a betöltési idő szerint rendezett események szerepelnek, és a rendszer mindig megőrzi azokat, így más szolgáltatásokkal is elérheti azt. A nyers parketta-fájlok feldolgozásához a Spark, a Hadoop és más ismerős eszközöket használhatja. 

Time Series Insights előzetes verzió újraparticionálja a Parquet fájlokat az Time Series Insights lekérdezés optimalizálásához. Az adatbázis újraparticionált példánya is mentve lesz.

A nyilvános előzetes verzióban az Azure Storage-fiókban az adatai határozatlan ideig tárolódnak.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Time Series Insights Blobok írása és szerkesztése

A lekérdezés teljesítményének és az adatelérhetőségnek a biztosításához ne szerkessze vagy töröljön minden olyan blobot, amelyet Time Series Insights előnézet hoz létre.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Adatok elérése és exportálása Time Series Insights előzetes verzióból

Előfordulhat, hogy a Time Series Insights Preview Explorerben a más szolgáltatásokkal együtt használt adatnézeteket szeretné elérni. Az adataival például létrehozhat egy jelentést Power BI vagy betaníthat egy gépi tanulási modellt Azure Machine Learning Studio használatával. Az adatait használhatja a Jupyter-jegyzetfüzetek átalakításához, megjelenítéséhez és modellezéséhez is.

Az adatai három általános módon érhetők el:

* A Time Series Insights Preview Explorerben. Az adatok CSV-fájlként is exportálhatók az Intézőből. További információ: [Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md).
* A Time Series Insights Preview API-ból. Az API-végpont `/getRecorded`címen érhető el. Ha többet szeretne megtudni erről az API-ról, tekintse meg a [Time Series lekérdezését](./time-series-insights-update-tsq.md).
* Közvetlenül egy Azure Storage-fiókból. Olvasási hozzáféréssel kell rendelkeznie a Time Series Insights előnézeti adataihoz való hozzáféréshez használt fiókhoz. További információ: [a Storage-fiók erőforrásaihoz való hozzáférés kezelése](../storage/blobs/storage-manage-access-to-resources.md).

### <a name="data-deletion"></a>Adattörlés

Ne törölje a Time Series Insights előnézeti fájljait. A kapcsolódó adatok csak Time Series Insights előzetes verzióról kezelhetők.

## <a name="parquet-file-format-and-folder-structure"></a>A parketta fájlformátuma és a mappa szerkezete

A Parquet egy nyílt forráskódú, oszlopos fájlformátum, amely hatékony tároláshoz és teljesítményhez lett tervezve. Time Series Insights az előzetes verzió a parkettát használja az alábbi okok miatt. Idősorozat-azonosító alapján particionálja az adatmennyiséget a lekérdezési teljesítményhez a skálán.  

A Parquet fájltípussal kapcsolatos további információkért tekintse meg a [parketta dokumentációját](https://parquet.apache.org/documentation/latest/).

Time Series Insights az alábbi módon tárolja az adatai másolatait:

* Az első, a kezdeti másolat a betöltési idő alapján van particionálva, és nagyjából megérkezésük sorrendjében tárolja az adatot. Az adat a `PT=Time` mappában található:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* A második, újraparticionált másolatot az idősorozat-azonosítók csoportosításával particionálja, és a `PT=TsId` mappában találhatók:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

Mindkét esetben az időértékek a blob létrehozási idejére vonatkoznak. A `PT=Time` mappában lévő adatértékek megmaradnak. A `PT=TsId` mappában lévő adatok az idő függvényében lesznek optimalizálva, és nem maradnak statikusak.

> [!NOTE]
> * `<YYYY>` térképeket egy négyjegyű év ábrázolásához.
> * `<MM>` térképeket egy kétjegyű hónap ábrázolásához.
> * `<YYYYMMDDHHMMSSfff>` térképeket egy időbélyegzős ábrázoláshoz négyjegyű (`YYYY`), kétjegyű hónap (`MM`), kétjegyű nap (`DD`), kétjegyű óra (`HH`), kétjegyű perc (`MM`), kétszámjegyű második (`SS`) és három számjegyből álló ezredmásodperc (`fff`).

Time Series Insights előnézeti események a következő módon vannak leképezve a parketta-fájl tartalmára:

* Minden esemény egyetlen sorra van leképezve.
* Minden sor tartalmazza az **időbélyegző** oszlopot egy esemény időbélyegzővel. Az időbélyegző tulajdonság soha nem null értékű. Alapértelmezés szerint az **Event várólistán lévő időt** adja meg, ha az időbélyegző tulajdonság nincs megadva az esemény forrásában. Az időbélyegző mindig UTC szerint van.
* Minden sor tartalmazza az idősorozat-azonosító oszlopot, amely a Time Series Insights környezet létrehozásakor van meghatározva. A tulajdonság neve tartalmazza a `_string` utótagot.
* A telemetria-adatként elküldett összes többi tulajdonságot a tulajdonság típusától függően `_string` (string), `_bool` (Boolean), `_datetime` (datetime) vagy `_double` (Double) végződésű oszlopokra kell leképezni.
* Ez a leképezési séma a fájl formátumának első verziójára vonatkozik, amelyre a **V = 1**hivatkozik. A szolgáltatás fejlődése során előfordulhat, hogy a név növekszik.

## <a name="next-steps"></a>További lépések

- Olvassa el [Azure Time Series Insights előnézeti tárolót és a bejövő](./time-series-insights-update-storage-ingress.md)forgalmat.

- További információ az új [adatmodellezésről](./time-series-insights-update-tsm.md).

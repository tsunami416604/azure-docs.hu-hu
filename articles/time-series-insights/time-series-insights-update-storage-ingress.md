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
ms.date: 10/23/2019
ms.custom: seodec18
ms.openlocfilehash: 0b61e194bdea5fd8272ffc0fc9e16a2d80d3cf60
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989704"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Adattárolás és bejövő forgalom Azure Time Series Insights előzetes verzióban

Ez a cikk az adattárolási és a bejövő Azure Time Series Insights előzetes verziójának frissítéseit ismerteti. Lefedi a mögöttes tárolási struktúrát, a fájlformátumot és az idősorozat-azonosító tulajdonságot. Emellett ismerteti az alapul szolgáló bejövő folyamatokat, az átviteli sebességet és a korlátozásokat.

## <a name="data-ingress"></a>Bejövő adatforgalom

Time Series Insights előzetes verzióban az adatbeviteli szabályzatok határozzák meg, hogy az adatok honnan származnak, és milyen formátumban kell megadni az adatok formátumát.

[![Time Series-modell áttekintése](media/v2-update-storage-ingress/tsi-data-ingress.png)](media/v2-update-storage-ingress/tsi-data-ingress.png#lightbox)

### <a name="ingress-policies"></a>Bejövő házirendek

Time Series Insights előzetes verzió támogatja ugyanazokat az eseményforráskat, amelyek Time Series Insights jelenleg a következőket támogatják:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Time Series Insights az előzetes verzió legfeljebb két eseményforrás használatát támogatja példányok esetében.
  
Azure Time Series Insights támogatja az Azure IoT Hub vagy az Azure Event Hubs használatával küldött JSON-t. A IoT JSON-adatai optimalizálásával megismerheti a [JSON formázását](./time-series-insights-send-events.md#supported-json-shapes).

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
> A Time Series Insights általánosan elérhető kiadása 60 másodpercen belül elérhetővé teszi az adatoknak az esemény forrásból való beolvasása után. Az előzetes verzió ideje alatt hosszabb időt is igénybe vehet, amíg az adatszolgáltatás elérhetővé válik. Ha 60 másodpercen túli jelentős késés tapasztalható, vegye fel velünk a kapcsolatot.

### <a name="scale"></a>Méretezés

Alapértelmezés szerint a Time Series Insights-előnézet legfeljebb 1 megabájt/másodperc (MB/s) méretű kezdeti bejövő forgalom használatát támogatja. Ha szüksége van rá, legfeljebb 16 MB/s sebesség érhető el. Ha továbbfejlesztett méretezési támogatásra van szüksége, vegye fel velünk a kapcsolatot.

További bejövő és méretezési képességeket kaphat az eseményforrás számára:

* [IoT Hub](../iot-hub/iot-hub-scaling.md)
* [Event Hubs](../event-hubs/event-hubs-scalability.md)

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

## <a name="next-steps"></a>Következő lépések

- Olvassa el [Azure Time Series Insights előnézeti tárolót és a bejövő](./time-series-insights-update-storage-ingress.md)forgalmat.

- További információ az új [adatmodellezésről](./time-series-insights-update-tsm.md).

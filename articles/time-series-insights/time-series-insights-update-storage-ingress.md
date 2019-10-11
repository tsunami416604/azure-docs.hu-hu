---
title: Adattárolás és bejövő forgalom Azure Time Series Insights előzetes verzióban | Microsoft Docs
description: Az adattárolás és a bejövő forgalom megismerése Azure Time Series Insights előzetes verzióban.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: seodec18
ms.openlocfilehash: 9af53728ee038a6511c434aeedfdb9afdab6d04b
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273887"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Adattárolás és bejövő forgalom Azure Time Series Insights előzetes verzióban

Ez a cikk az adattárolási és a bejövő Azure Time Series Insights előzetes verziójának változásait ismerteti. Lefedi a mögöttes tárolási struktúrát, a fájlformátumot és az idősorozat-azonosító tulajdonságot. A cikk a beáramlási folyamat, az átviteli sebesség és a korlátozások hátterét is ismerteti.

## <a name="data-ingress"></a>Bejövő adatforgalom

Azure Time Series Insights a bejövő adatforgalmi szabályzatok határozzák meg, hogy az adatok honnan és milyen formátumban legyenek kiválasztva.

[@no__t – 1Time adatsorozat-modell áttekintése](media/v2-update-storage-ingress/tsi-data-ingress.png)](media/v2-update-storage-ingress/tsi-data-ingress.png#lightbox)

### <a name="ingress-policies"></a>Bejövő házirendek

A Time Series Insights-előnézet ugyanazokat az eseményforrás-és fájltípusokat támogatja, amelyek Time Series Insights jelenleg a következőket támogatják:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)
  
Azure Time Series Insights támogatja az Azure IoT Hub vagy az Azure Event Hubs használatával küldött JSON-t. A IoT JSON-adatai optimalizálásával megismerheti a [JSON formázását](./time-series-insights-send-events.md#supported-json-shapes).

### <a name="data-storage"></a>Adattárolás

Time Series Insights előzetes utólagos elszámolású SKU-környezet létrehozásakor két erőforrást hoz létre:

* Time Series Insights környezet.
* Az Azure Storage általános célú v1-fiókja, ahol az adat tárolva lesz.

A Time Series Insights előzetes verziója az Azure Blob Storage-t használja a Parquet fájltípussal. Time Series Insights kezeli az összes adatműveletet, beleértve a Blobok létrehozását, indexelését és az adatok particionálását az Azure Storage-fiókban. Ezeket a blobokat egy Azure Storage-fiók használatával hozza létre.

A többi Azure Storage-blobhoz hasonlóan a Time Series Insights létrehozott Blobok lehetővé teszik a különböző integrációs forgatókönyvek elolvasását és írását.

### <a name="data-availability"></a>Az adatelérhetőség

A Time Series Insights-előnézet egy blob méretű optimalizálási stratégia használatával indexeli az adatmennyiséget. A lekérdezés az indexelés után elérhetővé válik, amely az adatforgalom mennyiségének és a sebességének a függvénye.

> [!IMPORTANT]
> * Az Time Series Insights általános elérhetőségi (GA) kiadás 60 másodpercen belül elérhetővé teszi az adatgyűjtést az esemény forrásának megérkezése után.
> * Az előzetes verzió ideje alatt hosszabb időt várhat az adatok elérhetővé tételéhez.
> * Ha bármilyen jelentős késést tapasztal, vegye fel velünk a kapcsolatot.

### <a name="scale"></a>Méretezés

A Time Series Insights előzetes verzió támogatja a kezdeti bejövő forgalom (MB/s) mennyiségét másodpercenként legfeljebb 1 másodpercenként (Mbps). A továbbfejlesztett méretezési támogatás folyamatban van. A dokumentáció frissítését tervezzük, hogy azok tükrözzék ezeket a frissítéseket.

## <a name="parquet-file-format"></a>Parketta fájlformátum

A parketta a következőhöz tervezett, oszlopos, adatfájl-formátum:

* Együttműködési lehetőség
* Lemezterület-hatékonyság
* Lekérdezés hatékonysága

Time Series Insights a parkettát választotta, mivel hatékony adattömörítési és kódolási sémákat biztosít, és a nagy mennyiségű összetett adat kezelésére képes.

A Parquet fájltípussal kapcsolatos további információkért olvassa el a [parketta dokumentációját](https://parquet.apache.org/documentation/latest/).

További információ az Azure-beli Parquet fájlformátumról: [támogatott fájltípusok az Azure Storage](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#parquet-format)-ban.

### <a name="event-structure-in-parquet"></a>Esemény szerkezete a parkettában

Time Series Insights a Blobok másolatait a következő két formátumban hozza létre és tárolja:

1. Az első, a kezdeti másolatot a rendszer az érkezési idő szerint particionálja:

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * BLOB létrehozási ideje a Blobok számára az érkezési idő szerint particionálva.

1. A második, újraparticionált másolat az idősorozat-azonosító dinamikus csoportosításával van particionálva:

    * `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * A Blobok minimális esemény-időbélyege az idősorozat-AZONOSÍTÓval particionált blobokhoz.

> [!NOTE]
> * @no__t – 0 a 4 számjegyű év ábrázolására.
> * @no__t – 0 a kétjegyű hónapok ábrázolására.
> * @no__t – 0 – 4 számjegyből álló (`YYYY`) időbélyeg-ábrázolási leképezés 2 jegyű hónap (`MM`), 2 jegyű nap (`DD`), 2 jegyű óra (`HH`), 2 jegyű perc (`MM`), 2 jegyű másodperc (`SS`) és 3 számjegyű ezredmásodperc (`fff`).

Time Series Insights az események a következő módon vannak leképezve a parketta fájl tartalmára:

* Minden esemény egyetlen sorra van leképezve.
* Beépített **timestamp** típusú oszlop egy esemény időbélyegzővel. Az időbélyeg tulajdonság soha nem null értékű. Alapértelmezés szerint az **eseményforrás várólistán lévő** , ha az időbélyegző tulajdonság nincs megadva az esemény forrásában. Az időbélyeg UTC-ben van. 
* Az oszlopokra leképezett minden egyéb tulajdonság `_string` (string), `_bool` (Boolean), `_datetime` (datetime) és `_double` (Double) karakterrel végződik, a tulajdonság típusától függően.
* Ez a fájlformátum első verziójának leképezési sémája, amely a következőre hivatkozik: **V = 1**. Mivel ez a funkció fejlődik, a név a következőre lesz növelve: **v = 2**, **V = 3**stb.

## <a name="azure-storage"></a>Azure Storage

Ez a szakasz a Azure Time Series Insights vonatkozó Azure Storage-adatokat ismerteti.

Az Azure Blob Storage szolgáltatás részletes leírását a Storage- [Blobok bevezetését](../storage/blobs/storage-blobs-introduction.md)ismertető cikkből olvashatja.

### <a name="your-storage-account"></a>A Storage-fiók

Time Series Insights utólagos elszámolású környezet létrehozásakor két erőforrást hoz létre: egy Time Series Insights-környezetet és egy Azure Storage általános célú v1-fiókot, ahol az adat tárolva lesz. Úgy döntöttünk, hogy az Azure Storage általános célú v1-es verzióját az együttműködés, az ár és a teljesítmény miatt az alapértelmezett erőforrásként tesszük elérhetővé.

Time Series Insights az Azure Storage-fiókban az egyes események két példányát teszi közzé. A rendszer mindig megőrzi a kezdeti másolatot, így gyorsan lekérdezheti azt más szolgáltatások használatával. Egyszerűen használhatja a Spark, a Hadoop és az egyéb ismerős eszközöket az idősorozat-azonosítók között a nyers Parquet-fájlokon keresztül, mert ezek a motorok támogatják az alapszintű fájlnevek szűrését. A Blobok év és hónap szerinti csoportosítása hasznos módszer a Blobok egy adott időtartományon belüli listázására egy egyéni feladatokhoz.

Time Series Insights továbbá a Time Series Insights API-k optimalizálásához újraparticionálja a parketta fájljait. A rendszer a legutóbb újraparticionált fájlt is menti.

A nyilvános előzetes verzióban az Azure Storage-fiókban az adatai határozatlan ideig tárolódnak.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Time Series Insights Blobok írása és szerkesztése

A lekérdezés teljesítményének és az adatelérhetőségnek a biztosításához ne szerkessze vagy törölje a Time Series Insights által létrehozott blobokat.

> [!TIP]
> A Time Series Insights teljesítménye hátrányosan befolyásolhatja a Blobok túl gyakori olvasását vagy írását.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Adatok elérése és exportálása Time Series Insights előzetes verzióból

Előfordulhat, hogy a Time Series Insights Preview Explorerben tárolt, más szolgáltatásokkal együtt használt információhoz szeretne hozzáférni. Előfordulhat például, hogy az adatok használatával szeretne jelentést készíteni a Power BIban, hogy a gépi tanulást a Azure Machine Learning Studio használatával hajtsa végre, vagy egy Jupyter notebookot használó notebook-alkalmazásban használja.

Az adatai három általános módon érhetők el:

* A Time Series Insights Preview Explorerben: az adatok exportálását CSV-fájlként is elvégezheti a Time Series Insights Preview Explorerben. További információ: [Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md).
* A Time Series Insights Preview API-kkal: az API-végpont a `/getRecorded` címen érhető el. Ha többet szeretne megtudni erről az API-ról, tekintse meg a [Time Series lekérdezését](./time-series-insights-update-tsq.md).
* Közvetlenül egy Azure Storage-fiókból (alább).

#### <a name="from-an-azure-storage-account"></a>Azure Storage-fiókból

* Olvasási hozzáféréssel kell rendelkeznie a Time Series Insights adataihoz való hozzáféréshez használt fiókhoz. További információ: [a Storage-fiók erőforrásaihoz való hozzáférés kezelése](../storage/blobs/storage-manage-access-to-resources.md).
* További információ az Azure Blob Storage-ból származó adatok olvasásának közvetlen módjairól: [Azure-megoldás kiválasztása adatátvitelhez](../storage/common/storage-choose-data-transfer-solution.md).
* Adatok exportálása Azure Storage-fiókból:
    * Először ellenőrizze, hogy a fiókja megfelel-e az adatexportáláshoz szükséges követelményeknek. További információ: [tároló importálási és exportálási követelményei](../storage/common/storage-import-export-requirements.md).
    * Az adatok Azure Storage-fiókból való exportálásának egyéb módjairól további információt az [adatok importálása és exportálása a blobokból](../storage/common/storage-import-export-data-from-blobs.md)című témakörben talál.

### <a name="data-deletion"></a>Adattörlés

Ne törölje a blobokat. Nem csupán az adatok naplózására és karbantartására használhatók, a Time Series Insights előzetes verzióban az egyes Blobok blob-metaadatai is megmaradnak.

## <a name="partitions"></a>Partíciók

Minden Time Series Insights előnézeti környezetnek rendelkeznie kell egy **idősorozat-azonosító** tulajdonsággal, valamint egy olyan **timestamp** tulajdonsággal, amely egyedileg azonosítja azt. Az idősorozat-azonosító logikai partícióként funkcionál az adataihoz, és a Time Series Insights előzetes környezetében természetes határt biztosít az adatelosztáshoz a fizikai partíciók között. A fizikai partíciókat Time Series Insights előzetes verzió felügyeli egy Azure Storage-fiókban.

A Time Series Insights dinamikus particionálást használ a tárolók és a lekérdezési teljesítmény optimalizálása érdekében a partíciók eldobásával és újbóli létrehozásával. A Time Series Insights előzetes verziójának dinamikus particionálási algoritmusa megkísérli megakadályozni, hogy egyetlen fizikai partíció több, különálló és logikai partícióra vonatkozó adatokkal is rendelkezik. Ez azt jelenti, hogy a particionálási algoritmus minden olyan, egyetlen idősorozat-AZONOSÍTÓhoz tartozó adattal rendelkezik, amely kizárólag a Parquet-fájlokban van, és nem lenne átfedésben más idősorozat-azonosítókkal. A dinamikus particionálási algoritmus emellett megpróbálja megőrizni az események eredeti sorrendjét egyetlen idősorozat-AZONOSÍTÓn belül.

Kezdetben a bejövő időpontnál az adat az időbélyeg szerint van particionálva, így az adott időtartományon belüli egyetlen logikai partíció több fizikai partíció között is elosztható. Egyetlen fizikai partíció is tartalmazhat több vagy minden logikai partíciót. A Blobok méretének korlátozásai miatt, még az optimális particionálásnál is, egyetlen logikai partíció több fizikai partíciót is elfoglalhat.

> [!NOTE]
> Alapértelmezés szerint az időbélyegző értéke az üzenet *várólistán lévő időpontja* a konfigurált eseményforrás esetében.

Ha korábbi vagy kötegelt üzeneteket tölt fel, rendelje hozzá az adataival együtt tárolni kívánt értéket az időbélyegző tulajdonsághoz, amely a megfelelő időbélyegre van leképezve. Az időbélyeg tulajdonság megkülönbözteti a kis-és nagybetűket. További információ: [Time Series Model](./time-series-insights-update-tsm.md).

### <a name="physical-partitions"></a>Fizikai partíciók

A fizikai partíciók a Storage-fiókban tárolt blokk-Blobok. A Blobok tényleges mérete eltérő lehet, mivel a méret a leküldéses aránytól függ. A Blobok azonban várhatóan körülbelül 20 MB-50 MB méretűek lesznek. Ez azt eredményezte, hogy a Time Series Insights csapata 20 MB méretet választott a lekérdezési teljesítmény optimalizálása érdekében. Ez a méret az idő múlásával változhat, a fájlmérettől és a bejövő adatforgalomtól függően.

> [!NOTE]
> * A Blobok mérete 20 MB.
> * Az Azure-Blobok időnként újraparticionálva vannak a jobb teljesítmény érdekében, mivel eldobásra és újból létrejön.
> * Ugyanezek a Time Series Insights-adathalmazok két vagy több blobban is szerepelhetnek.

### <a name="logical-partitions"></a>Logikai partíciók

A logikai partíció egy olyan partíció, amely egy fizikai partíción belül tárolja az egyetlen partíciós kulcs értékével társított összes adatmennyiséget. A Time Series Insights előnézet logikailag particionálja az egyes blobokat két tulajdonság alapján:

* **Idősorozat-azonosító**: az esemény-adatfolyamban és a modellben található összes Time Series Insights-adategységhez tartozó partíciós kulcs.
* **Időbélyeg**: a kezdeti bejövő forgalomon alapuló idő.

A Time Series Insights előnézet a két tulajdonságon alapuló, elvégezhető lekérdezéseket biztosít. Ez a két tulajdonság a leghatékonyabb módszert is biztosítja a Time Series Insights-adatszolgáltatások gyors kézbesítéséhez.

Fontos, hogy kiválassza a megfelelő idősorozat-azonosítót, mert az egy nem módosítható tulajdonság. További információ: [Time Series-azonosítók kiválasztása](./time-series-insights-update-how-to-id.md).

## <a name="next-steps"></a>Következő lépések

- Olvassa el a [Azure Time Series Insights előnézet tárolót és a bejövő](./time-series-insights-update-storage-ingress.md)forgalmat.

- További információ az új [adatmodellezésről](./time-series-insights-update-tsm.md).

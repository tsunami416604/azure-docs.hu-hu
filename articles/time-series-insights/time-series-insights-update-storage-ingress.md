---
title: Az Azure Time Series Insights előzetes verziója data - adatok tárolási és a bejövő forgalom az Azure Time Series Insights – előzetes |} A Microsoft Docs
description: Az adattárolás és a bejövő forgalom az Azure Time Series Insights – előzetes ismertetése.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/05/2018
ms.custom: seodec18
ms.openlocfilehash: 9504e62ea99c835f43f0d86ec2cfa57a9afcb4e4
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269991"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Az adattárolás és a bejövő forgalom az Azure Time Series Insights előzetes verziója

Ez a cikk ismerteti az adattárolás és a bejövő forgalom módosításait az Azure Time Series Insights előzetes verziója. Ez fedezi az alapul szolgáló tárolási struktúra, a formátumot, és a Time Series azonosító tulajdonságot. A cikk emellett ismerteti a mögöttes bejövő folyamat, az átviteli sebesség és a korlátozások.

## <a name="data-storage"></a>Adattárolás

A Time Series Insights előzetes verziója használatalapú Termékváltozat környezet létrehozásakor hoz létre két erőforrás:

* Egy Time Series Insights-környezetet.
* Azure Storage-általános célú V1 fiók az adatok tárolásához.

Time Series Insights előzetes verziója a parquet eszközökben típusa Azure Blob storage használ. A Time Series Insights létrehozása a blobok, beleértve az műveletek kezeli, indexelési és az Azure storage-fiókban az adatok particionálása. Ezek a blobok Azure storage-fiók használatával hoz létre.

Más Azure Storage-blobokkal, például a Time Series Insights által létrehozott blobok lehetővé teszik olvasási és írási őket a különböző integrációs forgatókönyvek támogatásához.

> [!TIP]
> Time Series Insights teljesítményt hátrányosan érintheti, ha olvasni vagy írni a blobok túl gyakran.

Az Azure Blob storage áttekintését lásd: [Storage-blobok bemutatása](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

A parquet eszközökben fájltípus kapcsolatos további információkért lásd: [támogatott fájltípusok az Azure Storage-ban](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#Parquet-format).

## <a name="parquet-file-format"></a>Parquet fájlformátum

Parquet Oszlopalapú, adatok fájlformátum készült:

* Együttműködési lehetőség
* Hatékony területfelhasználásának
* Lekérdezés hatékonyságát

A Time Series Insights Parquet választottunk, mert az adattömörítés hatékony biztosít, és a kódolási sémát, a bővített teljesítményéről, amely képes kezelni a tömeges összetett adatokat.

Szeretné jobban megismerni a Parquet fájlformátum, lásd: [Parquet dokumentáció](https://parquet.apache.org/documentation/latest/).

## <a name="event-structure-in-parquet"></a>Esemény struktúra a parquet eszközökben

A Time Series Insights hoz létre, és a blobok példányban tárolja az alábbi két formátumokban:

1. Az első, a kezdeti másolat érkezési ideje particionálva van:

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Létrehozás ideje BLOB érkezési ideje dokumentumtárolási blobok számára.

1. A második, pedig újraparticionálhatók másolási Time Series azonosító dinamikus csoportja által particionálva van:

    * `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * A Time Series-azonosító. dokumentumtárolási blobok blob minimális esemény időbélyege

> [!NOTE]
> * `<YYYY>` 4 számjegyű év jelképeként leképezések.
> * `<MM>` 2 számjegyből hónap jelképeként leképezések.
> * `<YYYYMMDDHHMMSSfff>` képez le egy időbélyeg képviselettel 4 számjegyű év (`YYYY`), 2 számjegyből hónap (`MM`), 2 számjegyből nap (`DD`), 2 számjegyű óra (`HH`), 2 számjegyből perc (`MM`), 2 számjegyből másodperc (`SS`), és 3 számjegyből ezredmásodperces (`fff`).

Time Series Insights-események vannak leképezve Parquet fájl tartalmát a következő:

* Minden esemény egyetlen sor rendeli hozzá.
* Beépített **időbélyeg** egy esemény időbélyegzője oszlopot. Az időbélyeg-tulajdonság soha nem null értékű. A rendszer alapértelmezés szerint **esemény forrása sorba helyezésekor** Ha az időbélyeg-tulajdonság nincs megadva az adatforrás. Az időbélyeg (UTC) van. 
* Oszlopok vannak leképezve az összes többi tulajdonság végén `_string` (karakterlánc), `_bool` (logikai), `_datetime` (datetime), és `_double` (dupla), a tulajdonság típusától függően.
* Ez az első verziója a fájlformátum, amely ezt a leképezés rendszere **V = 1**. Alakul ki ezt a szolgáltatást, mert a név fog növeli **V = 2**, **V = 3**, és így tovább.

## <a name="partitions"></a>Partíciók

Time Series Insights előzetes verziója környezeteket rendelkeznie kell egy Time Series azonosító tulajdonságot és a egy időbélyeg-tulajdonság egyedi azonosítására alkalmas. A Time Series azonosító funkcionál, az adatok logikai partíció, és egy természetes határt a Time Series Insights előzetes verziója környezetet biztosít az adatok eloszthatók a fizikai partíciók. Fizikai partíciók felügyeleti Time Series Insights előzetes verziója kezeli az Azure storage-fiók.

A Time Series Insights használja a dinamikus particionálás elvetését, majd ismét létrehozza a partíció által és a lekérdezésfeldolgozási teljesítmény optimalizálása érdekében. Az Time Series Insights előzetes verziója dinamikus particionálási algoritmus próbál megakadályozza, hogy egyetlen fizikai partíciók több, az adatok különböző, logikai partíciót. Más szóval a particionálási algoritmus biztosítja, hogy minden adatot adott egy egyetlen alkalommal Azonosítót kizárólag szerepel a Parquet-fájlokat, anélkül, hogy más Time Series-azonosítóval rendelkező közbeékeléses folyamatban van. A dinamikus particionálási algoritmust is megpróbálja megőrizni az eredeti rendelés események egy egyetlen Time Series-azonosító.

Kezdetben bejövő időpontban adatok particionálása a tárhelyek időbélyegző szerint, hogy egy adott időtartományt belül egyetlen, logikai partíció több fizikai partíciók között oszlik el. Egyetlen fizikai partíciók a legtöbb vagy összes logikai partíciót is tartalmazhat. Blob fájlméretre vonatkozó korlátozások mellett is optimális particionálási miatt egyetlen logikai partíciót is foglalhat több fizikai partíciókra.

> [!NOTE]
> Alapértelmezés szerint az időbélyegző-érték az az üzenet *sorba helyezésekor* a konfigurált eseményből. 

Ha előzményadatok vagy kötegelt üzenetek videófájl, rendelje hozzá az érték, amely leképezi a megfelelő időbélyeg időbélyeg-tulajdonság az adatokat tárolni szeretné. Az időbélyeg-tulajdonság a kis-és nagybetűket. További információkért lásd: [Idősorozat-modell](./time-series-insights-update-tsm.md).

## <a name="physical-partitions"></a>Fizikai partíciók

Egy fizikai partíciónak a storage-fiókban tárolt blokkblobok. A tényleges méret blobok eltérőek lehetnek, mivel a mérete attól függ, a leküldéses sebességét. Azonban várhatóan kell 50 MB-ra körülbelül 20 MB méretű blobok. Az elvárás a Time Series Insights csapata 20 MB-os jelölje ki a lekérdezési teljesítmény optimalizálása mérete vezetett. Ez a méret fájl mérete és az adatok beérkezését sebességének idővel módosulhatnak.

> [!NOTE]
> * Blobok, 20 MB-os mérete.
> * Azure-blobok vannak alkalmanként pedig újraparticionálhatók a jobb teljesítmény érdekében által éppen eltávolítja, majd újra létrehozza.
> * A Time Series Insights ugyanazokat az adatokat is is szerepel a két vagy több blobot.

## <a name="logical-partitions"></a>Logikai partíciók

A logikai partíció egy partíción belül egy fizikai partíciónak, amely egy adott partíciókulcs-értékhez társított minden adatot tárol. Time Series Insights előzetes verziója logikailag particionálja minden egyes blob két tulajdonság alapján:

* **Time Series azonosító**: A partíciókulcs belül az eseménystream, valamint az összes Time Series Insights adatait.
* **Időbélyeg**: Az az idő alapján a kezdeti bejövő forgalom.

Time Series Insights előzetes verziója biztosít nagy teljesítményű lekérdezések, amelyek a két tulajdonság alapulnak. A két tulajdonság is biztosítanak a gyors kézbesítéséhez a Time Series Insights adatait a leghatékonyabb módszert.

Fontos, válasszon egy megfelelő idő Azonosítót, egy a tulajdonság nem módosítható, mert. További információkért lásd: [válasszon idő sorozat azonosítók](./time-series-insights-update-how-to-id.md).

## <a name="your-azure-storage-account"></a>Az Azure storage-fiók

### <a name="storage"></a>Storage

Amikor létrehoz egy használatalapú fizetéses Time Series Insights-környezetet, létrehozhat két erőforrás: a Time Series Insights-környezet és az Azure Storage-általános célú V1 fiók az adatok tárolásához. Győződjön meg arról, általános célú Azure Storage V1 az alapértelmezett erőforrás miatt az együttműködés, ára és teljesítménye választottuk. 

A Time Series Insights eseményekhez két másolatát közzéteszi az Azure storage-fiókban. A kezdeti másolat mindig megőrződik, hogy kérdezhet le róla performantly más szolgáltatások használatával. Könnyen használható Spark, a Hadoopról és más jól ismert eszközökkel Time Series azonosítók között nyers a Parquet-fájlokat, mert ezek motorok támogatja az alapszintű szűrés fájlnév. Év és hónap szerint blobok csoportosítási módja a hasznos belül egy adott időtartományt a vonatkozó egyéni feladat blobok listázása. 

Emellett a Time Series Insights repartitions a Parquet-fájlokat, optimalizálása a Time Series Insights API-k esetében. A közelmúltban repartitioned fájlt is menti.

A nyilvános előzetes verzióban adatok tárolt határozatlan ideig az Azure storage-fiókban.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Írás és Szerkesztés a Time Series Insights-blobok

Lekérdezési teljesítmény és az adatok rendelkezésre állás biztosítása érdekében nem szerkesztése vagy törlése a Time Series Insights által létrehozott blobot sem.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Elérése és exportálása az Time Series Insights előzetes verziója

Előfordulhat, hogy szeretné elérni a Time Series Insights előzetes verziója Explorert, hogy más szolgáltatásokkal együtt használja a tárolt adatokat. Például érdemes használni az adatok a jelentés a Power bi-ban, hajtsa végre az Azure Machine Learning Studio használatával a gépi tanulás, vagy egy Jegyzetfüzet alkalmazásban, a Jupyter notebookok használata.

Általános háromféleképpen férhet hozzá az adataihoz:

* A Time Series Insights előzetes verziója Explorerből.
* Time Series Insights előzetes API-kon keresztül.
* Közvetlenül az Azure storage-fiók.

#### <a name="from-the-time-series-insights-preview-explorer"></a>A Time Series Insights előzetes verziója Intézőből

A Time Series Insights előzetes verziója Explorer CSV-fájlként is exportálhat adatokat. További információkért lásd: [Time Series Insights előzetes verziója explorer](./time-series-insights-update-explorer.md).

#### <a name="from-the-time-series-insights-preview-apis"></a>Time Series Insights előzetes API-kon keresztül

Az API-végpont címen érhető el `/getRecorded`. Ez az API-val kapcsolatos további tudnivalókért lásd: [Time Series lekérdezés](./time-series-insights-update-tsq.md).

#### <a name="from-an-azure-storage-account"></a>Az Azure storage-fiók

* A Time Series Insights-adatokhoz való hozzáféréshez használ fiók olvasási hozzáférése van szüksége. További információkért lásd: [a storage-fiók erőforrásokhoz való hozzáférés kezelése](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources).

* Olvassa el az adatok Azure Blob storage-ból való közvetlen módon kapcsolatos további információkért lásd: [az adatok a storage-fiókból mozgatása](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

* Adatok exportálása az Azure storage-fiók:

    * Először győződjön meg arról, hogy a fiók megfelel-e az adatexportálási a szükséges követelményeknek. További információkért lásd: [tárolási importálása és exportálása a követelmények](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements).

    * Adatok exportálása az Azure storage-fiók egyéb módjaival kapcsolatos további tudnivalókért lásd: [BLOB importálási és exportálási adata](https://docs.microsoft.com/azure/storage/common/storage-import-export-data-from-blobs).

### <a name="data-deletion"></a>Adatok törlése

Blobok, nem törölhető, mert a Time Series Insights előzetes verziója a benne lévő blobok metaadatait tárolja.

## <a name="ingress"></a>Bejövő forgalom

### <a name="time-series-insights-ingress-policies"></a>Time Series Insights bejövő házirendek

Time Series Insights előzetes verziója, amely a Time Series Insights jelenleg ugyanazon eseményforrásokból és fájltípus esetében támogatja.

Támogatott eseményforrások a következők:

- Azure IoT Hub
- Azure Event Hubs
  
  > [!NOTE]
  > Az Azure Event Hubs-példányok támogatják a Kafka.

Támogatott fájltípusok:

* JSON: A támogatott JSON-alakzatok, hogy képes kezelni kapcsolatos további információkért lásd: [alakzat JSON hogyan](./time-series-insights-send-events.md#json).

### <a name="data-availability"></a>Az adatok elérhetősége

Time Series Insights előzetes verziója egy blob-méret optimalizálása stratégia használatával indexeli az adatokat. Adatok indexelése után, mennyi adatot érkezik, és milyen sebesség a alapuló lekérdezhetők válnak.

> [!IMPORTANT]
> * A Time Series Insights általánosan (elérhetővé tétel GA) kiadásban elérhetővé adatok eseményforrás lenyomásával 60 másodpercen belül. 
> * Az előzetes verzióban várhatóan hosszabb ideig, mielőtt az adatokat szeretné elérhetővé tenni. 
> * Ha bármely jelentős késést tapasztal, ügyeljen arra, lépjen kapcsolatba velünk.

### <a name="scale"></a>Méretezés

Time Series Insights előzetes verziója támogatja a legfeljebb 6 megabit / másodperc (Mbps) környezetenként egy kezdeti bejövő méretezési csoporthoz. Továbbfejlesztett skálázási támogatást folyamatban. Tervezzük frissíteni, hogy ezek a fejlesztések dokumentációnkat.

## <a name="next-steps"></a>További lépések

Olvassa el a [Azure Time Series Insights előzetes verzió, a bejövő és kimenő tárolási](./time-series-insights-update-storage-ingress.md).

Olvassa el az új [adatmodellezés](./time-series-insights-update-tsm.md).

<!-- Images -->
[1]: media/v2-update-storage-ingress/storage-architecture.png
[2]: media/v2-update-storage-ingress/parquet-files.png
[3]: media/v2-update-storage-ingress/blob-storage.png

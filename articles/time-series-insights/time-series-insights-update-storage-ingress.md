---
title: Az adattárolás és az Azure Time Series Insights (előzetes verzió) a bejövő forgalom |} A Microsoft Docs
description: Az adattárolás és az Azure Time Series Insights (előzetes verzió) a bejövő forgalom ismertetése
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/05/2018
ms.openlocfilehash: e2440f6aa32710730e8b015bef1e7c583f7063e2
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001870"
---
# <a name="data-storage-and-ingress-in-the-azure-time-series-insights-preview"></a>Az adattárolás és a bejövő forgalom az Azure Time Series Insights (előzetes verzió)

Ez a cikk ismerteti az Azure Time Series Insights (TSI) előzetes verzióból való adattárolás a bejövő és kimenő módosítások. Ez fedezi az alapul szolgáló tárolási struktúra formátumú, és **Time Series azonosító** tulajdonság. Emellett ismerteti a mögöttes bejövő folyamat, az átviteli sebesség és a korlátozások.

## <a name="data-storage"></a>Adattárolás

Az Azure TSI előzetes létrehozásakor (**PAYG Termékváltozat**) környezetben hoz létre két erőforrás:

* Az Azure TSI-környezetben.
* Azure Storage-általános célú V1 fiók az adatok tárolásához.

A TSI (előzetes verzió) az Azure Blob Storage és a parquet eszközökben fájl típusa. Az Azure TSI létrehozása a blobok, beleértve az műveletek kezeli, indexelési és az Azure Storage-fiókban az adatok particionálása. Ezek a blobok Azure Storage-fiók használatával jön létre.

Mint bármely más Azure Storage-blobból olvassa el, és támogatják a különböző integrációs forgatókönyveket az Azure TSI-létrehozott blobok írni.

> [!TIP]
> A TSI teljesítmény kedvezőtlen hatással lehet olvasása vagy írása a blobok túl gyakran.

Azure Blob Storage áttekintése, olvassa el a [Storage-blobok bemutatása](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

A parquet eszközökben fájltípus kapcsolatos további információkért lásd: [támogatott fájltípusok az Azure Storage-ban](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#Parquet-format).

## <a name="parquet-file-format"></a>Parquet fájlformátum

Parquet Oszlopalapú, adatok, fájlformátumot, amely készült:

* Együttműködési lehetőség
* Hatékony területfelhasználásának
* Lekérdezés hatékonyságát

Az Azure TSI Parquet úgy döntött, mivel biztosít hatékony az adattömörítés és a kódolási sémát a bővített teljesítményének tömeges összetett adatokat.

A Parquet fájlformátum lényege, hogy kapjanak, olvassa el a [Parquet dokumentációs](https://parquet.apache.org/documentation/latest/).

## <a name="event-structure-in-parquet"></a>Esemény struktúra a parquet eszközökben

A blobok Azure TSI által létrehozott két példányban lesz tárolva a következő formátumok:

1. Az első, egy kezdeti másolatot fog particionálandó érkezési ideje:

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Létrehozás ideje BLOB érkezési ideje dokumentumtárolási blobok számára.

1. A második repartitioned másolatot, a dinamikus csoportosítás kell dokumentumtárolási **Time Series azonosító**:

    * `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * A blobok dokumentumtárolási blob minimális esemény időbélyegzője **Time Series azonosító**.

> [!NOTE]
> * `<YYYY>` 4 számjegyű év jelképeként leképezések.
> * `<MM>` 2 számjegyből hónap jelképeként leképezések.
> * `<YYYYMMDDHHMMSSfff>` képez le egy időbélyeg képviselettel 4 számjegyű év (`YYYY`), 2 számjegyből hónap (`MM`), 2 számjegyből nap (`DD`), 2 számjegyű óra (`HH`), 2 számjegyből perc (`MM`), 2 számjegyből másodperc (`SS`), és 3 számjegyből ezredmásodperces (`fff`).

Az Azure TSI-események vannak leképezve Parquet fájl tartalmát a következő:

* Minden esemény egyetlen sor rendeli hozzá.
* Beépített **időbélyeg** egy esemény időbélyegzője oszlopot. A **időbélyeg** tulajdonság soha nem null értékű.  A rendszer alapértelmezés szerint **esemény forrása sorba helyezésekor** Ha a **időbélyeg** tulajdonság nincs megadva az esemény forrását.  **Időbélyeg** UTC formátumban van.  
* Minden egyéb tulajdonságok térképet oszlopok megszűnik az `_string` (karakterlánc), `_bool` (logikai), `_datetime` (datetime), és `_double` (dupla) tulajdonság típusától függően.
* Ez a fájl formátuma első verziója, és azt nevezzük **V = 1**.  Ha ez a funkció haladásával a neve annak megfelelően növelhető a **V = 2**, **V = 3**, és így tovább.

## <a name="partitions"></a>Partíciók

Minden egyes Azure TSI (előzetes verzió) környezetben kell rendelkeznie egy **Time Series azonosító** tulajdonság és a egy **időbélyeg** tulajdonság, amely azonosítja. A **Time Series azonosító** funkcionál, az adatok logikai partíció, és biztosít, az Azure TSI (előzetes verzió) egy természetes határ az adatok eloszthatók a fizikai partíciókra. Fizikai partíciók felügyeleti kezeli az Azure TSI (előzetes verzió) egy Azure Storage-fiókban.

Az Azure TSI használja a dinamikus particionálás elvetését, majd újra létre kellene hoznia a partíció szerint tárolási kihasználtságát és a lekérdezési teljesítmény optimalizálása érdekében. Az Azure TSI (előzetes verzió) dinamikus particionálási algoritmust, hogy az adatok több, hogy egyetlen fizikai partíciók megkísérli különböző, logikai partíciót. Más szóval a particionálási algoritmus megtartja minden adatot adott egyetlen **Time Series azonosító** kizárólag szerepel a parquet eszközökben fájl(ok) nélkül más folyamatban közbeékeléses **Time Series azonosítók**. A dinamikus particionálási algoritmust is igyekszik megőrizni az eseményeket egy eredeti sorrendjét **Time Series azonosító**.

Kezdetben bejövő időpontban adatok particionálása által a **időbélyeg** így egyetlen, logikai partíció egy adott időtartományt belül több fizikai partíciók között lehetnek elosztva. Egyetlen fizikai partíciók számos vagy az összes logikai partíciót is tartalmazhatnak.  A blob mérete korlátozások miatt még optimális particionálási egyetlen logikai partíció sikerült foglalhat el több fizikai partíciókra.

> [!NOTE]
> A **időbélyeg** értéke az üzenet **sorba helyezésekor** az alapértelmezés szerint a konfigurált esemény forrása.  

Ha előzményadatok vagy kötegelt üzenetek tölt fel, hogy ki kell jelölnie a **időbélyeg** , amely leképezi a megfelelő az adatok a tulajdonság **időbélyeg** adatait tárolni kívánt érték.  A **időbélyeg** tulajdonság a kis-és nagybetűket. További információkért olvassa el a [Idősorozat-modell cikk](./time-series-insights-update-tsm.md).

## <a name="physical-partitions"></a>Fizikai partíciók

Egy fizikai partíciója egy blokkblobot az Azure Storage tárolja. A tényleges méret blobok változnak, attól függ, a leküldéses gyakoriság, azonban blobokon át a rendszer mintegy 20 – 50 MB-nál. A szervezeti elvárások miatt a TSI-csapat kijelölt 20 MB-tal a mérete, a lekérdezési teljesítmény optimalizálása érdekében. Ez sikerült módosítani a fájlmérettől és a sebesség, beérkező adatok alapján.

> [!NOTE]
> * Blobok, 20 MB-os mérete.
> * Azure-blobok vannak alkalmanként particionálni a jobb teljesítmény érdekében fel eldobták és újból létrehozták.
> * Azt is vegye figyelembe a TSI ugyanazokat az adatokat, hogy a blobokat több szerepel.

## <a name="logical-partitions"></a>Logikai partíciók

A logikai partíció egy partíción belül egy fizikai partíciónak, amely egy adott partíciókulcs-értékhez társított minden adatot tárol. A TSI (előzetes verzió) logikailag particionálja minden egyes blob két tulajdonság alapján:

1. **Time Series azonosító** -belül az eseménystream, valamint az összes TSI-adatok a partíciókulcs.
1. **Időbélyeg** – a kezdeti beérkező alapján.

Az Azure TSI (előzetes verzió) biztosít a nagy teljesítményű lekérdezések, amelyek a két tulajdonság alapulnak. A két tulajdonság is meg a leghatékonyabb módszert a TSI-adatok gyors kézbesítéséhez.

Fontos, hogy válassza ki a megfelelő **Time Series azonosító**, mert egy nem módosítható tulajdonság.  Lásd: [kiválasztása Time Series azonosítók](./time-series-insights-update-how-to-id.md) további információt.

## <a name="your-azure-storage-account"></a>Az Azure Storage-fiók

### <a name="storage"></a>Storage

Amikor létrehoz egy TSI **PAYG** környezetben hoz létre a két erőforrás – a TSI-környezet és az Azure storage-általános célú V1 fiók az adatok tárolásához. Győződjön meg az Azure Storage általános célú V1 miatt az együttműködés, ára és teljesítménye az alapértelmezett választottuk.  

Az Azure TSI legfeljebb két másolatot minden egyes esemény közzé az Azure Storage-fiókban. A kezdeti másolat mindig megőrződik kérdezhet le róla performantly használó egyéb szolgáltatások biztosításához. Így a Spark, a Hadoop, vagy más jól ismert eszközökkel is egyszerűen használható **Time Series azonosítók** nyers Parquet keresztül fájlokat, mivel ezek motor támogatja alapszintű fájl neve szűrés. Blobok csoportosítására év és hónap szerint hasznos blob lista adott időtartományt a vonatkozó egyéni feladat belül gyűjtéséhez.  

Ezenkívül a TSI fog particionálja a Parquet-fájlokat, optimalizálása az Azure TSI API-t, és a közelmúltban repartitioned fájlt is menti a rendszer.

A nyilvános előzetes verzióban adatokat kell tárolni határozatlan ideig az Azure Storage-fiókban.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Írás és Szerkesztés a Time Series Insights-blobok

Annak biztosítása érdekében a lekérdezési teljesítmény és az adatok rendelkezésre állását, nem szerkesztése vagy törlése a TSI által létrehozott blobot sem.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Elérése és exportálása az a Time Series Insights (előzetes verzió)

Előfordulhat, hogy szeretné más szolgáltatásokkal összefüggésben használandó (előzetes verzió) az Azure TSI explorer található adatokhoz. Például előfordulhat, hogy szeretné az adatok felhasználhatók a jelentést a Power BI, az Azure Machine Learning Studio használatával a gépi tanulás végrehajtásához vagy egy Jegyzetfüzet alkalmazás Jupyter notebookok stb.

Nincsenek adatok eléréséhez három általános elérési utak:

* A (előzetes verzió) az Azure TSI Explorerben engedélyezett.
* Az Azure TSI (előzetes verzió) API-kat.
* Közvetlenül az Azure Storage-fiókot.

### <a name="from-the-time-series-insights-preview-explorer"></a>A Time Series Insights (előzetes verzió) Intézőből

Az (előzetes verzió) – TSI Explorer CSV-fájlként is exportálhat adatokat. Tudjon meg többet a [az (előzetes verzió) – TSI explorer](./time-series-insights-update-explorer.md).

### <a name="from-the-time-series-insights-preview-apis"></a>A Time Series Insights (előzetes verzió) API-kon keresztül

Az API-végpont címen érhető el `/getRecorded`. Ez az API-val kapcsolatos további tudnivalókért olvassa el [Time Series lekérdezés](./time-series-insights-update-tsq.md).

### <a name="from-an-azure-storage-account"></a>Az Azure Storage-fiók

1. Olvasási hozzáférés engedélyezve van, függetlenül a fiók fog használni a TSI adatok eléréséhez szüksége. Az Azure Blob Storage olvasási hozzáférésének engedélyezésére vonatkozó további tudnivalókért olvassa el a [tárolási erőforrásokhoz való hozzáférés kezelése](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources).

1. További információk a közvetlen módon adatokat olvasni az Azure Blob Storage-ból, [adatok áthelyezése az Azure Storage szolgáltatásba vagy onnan](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

1. Adatok exportálása az Azure Storage-fiók:

    * Először győződjön meg arról, hogy a fiók rendelkezik a szükséges követelményeket, megfelel-e adatok exportálása. Olvasási [tárolási importálása és exportálása a követelmények](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements) további információt.
    * Adatok exportálása az Azure Storage-fiók regisztrálásához meg egyéb módjaival kapcsolatos [tárolási adatok importálása és exportálása a blobok](https://docs.microsoft.com/azure/storage/common/storage-import-export-data-from-blobs)

### <a name="data-deletion"></a>Adatok törlése

Ne törölje blobok, mivel az Azure TSI (előzetes verzió) TSI frissítés belül a blobok metaadatait tárolja.

## <a name="ingress"></a>Bejövő forgalom

### <a name="azure-time-series-insights-ingress-policies"></a>Az Azure Time Series Insights bejövő házirendek

Az Azure TSI (előzetes verzió) támogatja az ugyanazon eseményforrásokból és fájltípus esetében, amelyet ma hajtja végre.

Támogatott eseményforrások a következők:

- Azure IoT Hub
- Azure Event Hubs
  
  > [!NOTE]
  > Az Azure Event Hubs-példányok támogatják a Kafka.

Támogatott fájltípusok:

* JSON: Több a támogatott JSON-alakzatok tudjuk kezelni tud, lásd: a [alakzat JSON hogyan](./time-series-insights-send-events.md#json) dokumentációját.

### <a name="data-availability"></a>Az adatok elérhetősége

Nyilvános előzetes verzióban elérhető Azure TSI (előzetes verzió) indexeli az adatokat a blob-méret optimalizálása stratégia használatával. Ez azt jelenti, hogy adatok lekérdezése (amely alapul mennyi adatot és milyen sebesség a hamarosan elérhető) indexelése követően érhető el.

> [!IMPORTANT]
> * Általánosan elérhető a TSI elérhetővé teszi adatait, eseményforrás lenyomásával 60 másodpercen belül.  
> * Az előzetes verzió használata során várhatóan hosszabb ideig, mielőtt az adatokat szeretné elérhetővé tenni megtekintéséhez.  
>   * Ha bármely jelentős késést tapasztal, forduljon hozzánk.

### <a name="scale"></a>Méretezés

Az Azure TSI (előzetes verzió) egy kezdeti bejövő méretezési csoport legfeljebb 6 MB/s környezetenként támogatja. Továbbfejlesztett skálázási támogatást folyamatban. Dokumentáció, hogy ezek a fejlesztések frissülni fog.

## <a name="next-steps"></a>További lépések

Olvassa el a [Storage (előzetes verzió) az Azure TSI a bejövő és kimenő](./time-series-insights-update-storage-ingress.md).

Olvassa el az új [adatmodellezés](./time-series-insights-update-tsm.md).

<!-- Images -->
[1]: media/v2-update-storage-ingress/storage-architecture.png
[2]: media/v2-update-storage-ingress/parquet-files.png
[3]: media/v2-update-storage-ingress/blob-storage.png

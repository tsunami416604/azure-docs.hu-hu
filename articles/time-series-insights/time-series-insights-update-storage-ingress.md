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
ms.date: 11/27/2018
ms.openlocfilehash: 6635558d1b7cf664084c103e3b3b37b44c022fa6
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856550"
---
# <a name="data-storage-and-ingress-in-the-azure-time-series-insights-preview"></a>Az adattárolás és a bejövő forgalom az Azure Time Series Insights (előzetes verzió)

Ez a cikk ismerteti az adattárolás és a bejövő forgalom módosításait az Azure Time Series Insights (előzetes verzió). Ez fedezi az alapul szolgáló tárolási struktúra formátumú, és **Time Series azonosító** tulajdonság. Emellett ismerteti a mögöttes bejövő folyamat, az átviteli sebesség és a korlátozások.

## <a name="data-storage"></a>Adattárolás

A Time Series Insights frissítése létrehozásakor (**PAYG termékváltozat**) környezetben hoz létre két erőforrás:

1. Az Azure TSI-környezetben.
1. Egy Azure storage általános célú V1 fiók az adatok tárolásához.

A TSI (előzetes verzió) az Azure Blob Storage és a parquet eszközökben fájl típusa. Az Azure TSI létrehozása a blobok, beleértve az műveletek kezeli, indexelési és az Azure Storage-fiókban az adatok particionálása. Ezek a blobok Azure Storage-fiók használatával jön létre. Annak érdekében, hogy az összes esemény lekérdezhetők, nagy teljesítményű módon. A TSI-frissítés támogatni fogja az Azure Storage általános célú V1 és V2 "Forró" konfigurációs beállításokat.  

Mint bármely más Azure Storage-blobból olvassa el, és támogatják a különböző integrációs forgatókönyveket az Azure TSI-létrehozott blobok írni.

> [!TIP]
> Fontos megjegyezni, hogy a TSI teljesítményt hátrányosan érintheti olvasása vagy írása a blobok túl gyakran.

Az Azure Blob Storage működésével áttekintéséhez olvassa el a [Storage-blobok bemutatása](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

A parquet eszközökben fájltípus kapcsolatos további információkért tekintse át a [támogatott fájltípusok az Azure Storage-ban](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#Parquet-format).

## <a name="parquet-file-format"></a>Parquet fájlformátum

Parquet Oszlopalapú adatfájlformátumának, amely készült:

* Együttműködési lehetőség
* Hatékony területfelhasználásának
* Lekérdezés hatékonyságát

Az Azure TSI Parquet úgy döntött, mivel biztosít hatékony az adattömörítés és a kódolási sémát a bővített teljesítményének tömeges összetett adatokat.

Az egy jobb megértéséhez, hogy milyen a Parquet-fájlok formátuma szól, látogasson el a [hivatalos Parquet oldal](https://parquet.apache.org/documentation/latest/).

## <a name="event-structure-in-parquet"></a>Esemény struktúra a parquet eszközökben

A blobok Azure TSI által létrehozott két példányban lesz tárolva a következő formátumok:

1. Az első, egy kezdeti másolatot fog particionálandó érkezési ideje:

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI internal suffix>.parquet`
    * Létrehozás ideje BLOB érkezési ideje dokumentumtárolási blobok számára.

1. A második repartitioned másolatot, dinamikus csoportosítás a time series-azonosító szerint fog kell particionálni:

    • `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_< TSI internal suffix >.parquet`
    * Minimális esemény időbélyegzője a blobok a time series segítségével a blob azonosítója.

> [!NOTE]
> * `<YYYY>` év leképezések.
> * `<MM>` hónap leképezések.
> * `<YYYYMMDDHHMMSSfff>` teljes időbélyeg ezredmásodpercben vannak leképezve.

Az Azure TSI-események vannak leképezve Parquet fájl tartalmát a következő:

* Minden esemény egyetlen sor rendeli hozzá.
* Beépített **időbélyeg** egy esemény időbélyegzője oszlopot. A **időbélyeg** tulajdonság soha nem null értékű.  A rendszer alapértelmezés szerint **esemény forrása sorba helyezésekor** Ha a **időbélyeg** tulajdonság nincs megadva az esemény forrását.  **Időbélyeg** UTC formátumban van.  
* Minden egyéb tulajdonságok térképet oszlopok megszűnik az `_string` (karakterlánc), `_bool` (logikai), `_datetime` (datetime), és `_double` (dupla) tulajdonság típusától függően.
* Ez a fájl formátuma első verziója, és azt nevezzük ***V = 1**.  Ha ez a funkció haladásával a neve annak megfelelően növelhető a **V = 2**, **V = 3**, és így tovább.

## <a name="how-to-partition"></a>Particionálása

Minden egyes Azure TSI (előzetes verzió) környezetben kell rendelkeznie egy **Time Series azonosító** tulajdonság és a egy **időbélyeg** tulajdonság, amely azonosítja. A **Time Series azonosító** funkcionál, az adatok logikai partíció, és biztosít, az Azure TSI (előzetes verzió) egy természetes határ az adatok eloszthatók a fizikai partíciókra. Fizikai partíciók felügyeleti kezeli az Azure TSI (előzetes verzió) egy Azure Storage-fiókban.

Az Azure TSI használja a dinamikus particionálás elvetését, majd újra létre kellene hoznia a partíció szerint tárolási kihasználtságát és a lekérdezési teljesítmény optimalizálása érdekében. A TSI (előzetes verzió) dinamikus particionálási algoritmust nagy hangsúlyt fektet a kellene több különböző logikai partíció adatait egyetlen fizikai partíciók elkerülése érdekében. Más szóval a particionálási algoritmus célja, hogy egy kapcsolódó összes adat vagy **Time Series azonosító** megtalálható kizárólag Parquet-fájlokat anélkül, hogy más folyamatban közbeékeléses **Time Series azonosítók**. A dinamikus particionálási algoritmust is nagy hangsúlyt fektet a egyetlen események eredeti sorrendjének megőrzése **Time Series azonosító**.

Kezdetben bejövő időpontban adatok particionálása által a **időbélyeg** így egyetlen, logikai partíció egy adott időtartományt belül több fizikai partíciók között lehetnek elosztva. Egyetlen fizikai partíciók számos vagy az összes logikai partíciót is tartalmazhatnak.  A blob mérete korlátozások miatt még optimális particionálási egyetlen logikai partíció sikerült foglalhat el több fizikai partíciókra.

> [!NOTE]
> A **időbélyeg** értéke az üzenet **sorba helyezésekor** az alapértelmezés szerint a konfigurált esemény forrása.  

Ha előzményadatok vagy kötegelt üzenetek tölt fel, hogy ki kell jelölnie a **időbélyeg** , amely leképezi a megfelelő az adatok a tulajdonság **időbélyeg** adatait tárolni kívánt érték.  A **időbélyeg** tulajdonság a kis-és nagybetűket. További információkért olvassa el a [Idősorozat-modell cikk](./time-series-insights-update-tsm.md).

## <a name="physical-partition"></a>Fizikai partíciónként

Egy fizikai partíciója egy blokkblobot az Azure Storage tárolja. A tényleges méret blobok változnak, attól függ, a leküldéses gyakoriság, azonban blobokon át a rendszer mintegy 20 – 50 MB-nál. A szervezeti elvárások miatt a TSI-csapat kijelölt 20 MB-tal a mérete, a lekérdezési teljesítmény optimalizálása érdekében. Ez sikerült módosítani a fájlmérettől és a sebesség, beérkező adatok alapján.

> [!NOTE]
> * Blobok, 20 MB-os mérete.
> * Azure-blobok vannak alkalmanként particionálni a jobb teljesítmény érdekében fel eldobták és újból létrehozták.
> * Azt is vegye figyelembe a TSI ugyanazokat az adatokat, hogy a blobokat több szerepel.

## <a name="logical-partition"></a>Logikai partíció

A logikai partíció egy partíción belül egy fizikai partíciónak, amely egy adott partíciókulcs-értékhez társított minden adatot tárol. A TSI (előzetes verzió) logikailag particionálja minden egyes blob két tulajdonság alapján:

1. **Time Series azonosító** -belül az eseménystream, valamint az összes TSI-adatok a partíciókulcs.
1. **Időbélyeg** – a kezdeti beérkező alapján.

Az Azure TSI (előzetes verzió) biztosít a nagy teljesítményű lekérdezések, amelyek a két tulajdonság alapulnak. A két tulajdonság is meg a leghatékonyabb módszert a TSI-adatok gyors kézbesítéséhez.

Fontos, hogy válassza ki a megfelelő **Time Series azonosítók**, mert egy nem módosítható tulajdonság.  Lásd: [kiválasztása Time Series azonosítók](./time-series-insights-update-how-to-id.md) további információt.

## <a name="your-azure-storage-account"></a>Az Azure Storage-fiók

### <a name="storage"></a>Storage

A TSI PAYG környezet létrehozásakor létrehozhat két erőforrás – a TSI-környezet és a egy Azure storage általános célú V1 fiók az adatok tárolásához. A jövőben Azure Storage-általános célú V1 fiókokat a meglévő Azure-ügyfelek korlátozott lesz, ezért minden új ügyfél kiépítési a TSI (előzetes verzió) környezet Azure-bA lesz alapértelmezés szerint az Azure általános célú V2 "Forró" tárfiók létrehozása.  Azt választotta, hogy az Azure Storage általános célú V1 az alapértelmezett miatt az együttműködés, ára és teljesítménye.  

Az Azure TSI legfeljebb két másolatot minden egyes esemény közzé az Azure Storage-fiókban. A kezdeti másolat mindig megőrződik kérdezhet le róla performantly használó egyéb szolgáltatások biztosításához. Így a Spark, a Hadoop, vagy más jól ismert eszközökkel is egyszerűen használható **Time Series azonosítók** nyers Parquet keresztül fájlokat, mivel ezek motor támogatja alapszintű fájl neve szűrés. Blobok csoportosítására év és hónap szerint hasznos blob lista adott időtartományt a vonatkozó egyéni feladat belül gyűjtéséhez.  

Ezenkívül a TSI fog particionálja a Parquet-fájlokat, optimalizálása az Azure TSI API-t, és a közelmúltban repartitioned fájlt is menti a rendszer.

A nyilvános előzetes verzióban adatokat kell tárolni határozatlan ideig az Azure Storage-fiókban. Adatok törlésének lehetőségére is megjelenik a jövőben lehetővé teszi a nagyobb fokú ellenőrzést a régi adatok kezeléséhez.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Írás és Szerkesztés a Time Series Insights-blobok

Annak biztosítása érdekében a lekérdezési teljesítmény és az adatok rendelkezésre állását, nem szerkesztése vagy törlése a TSI által létrehozott blobot sem.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Elérése és exportálása az a Time Series Insights (előzetes verzió)

Előfordulhat, hogy szeretné elérni az Azure TSI (előzetes verzió) Explorert, hogy más szolgáltatásokkal együtt tárolt adatokat. Például előfordulhat, hogy szeretné az adatok felhasználhatók a jelentést a Power BI, az Azure Machine Learning Studio használatával a gépi tanulás végrehajtásához vagy egy Jegyzetfüzet alkalmazás Jupyter notebookok stb.

Nincsenek adatok eléréséhez három általános elérési utak:

* A (előzetes verzió) az Azure TSI Explorerben engedélyezett.
* Az Azure TSI (előzetes verzió) API-kat.
* Közvetlenül az Azure Storage-fiókot.

### <a name="from-the-time-series-insights-preview-explorer"></a>A Time Series Insights (előzetes verzió) Intézőből

A TSI-ből (előzetes verzió) Explorer származó CSV-fájlként is exportálhat adatokat. Tudjon meg többet a [a TSI (előzetes verzió) Explorer](./time-series-insights-update-explorer.md).

### <a name="from-the-time-series-insights-preview-apis"></a>A Time Series Insights (előzetes verzió) API-kon keresztül

Az API-végpont címen érhető el `/getRecorded`. Ez az API-val kapcsolatos további tudnivalókért olvassa el [Time Series lekérdezés](./time-series-insights-update-tsq.md).

### <a name="from-an-azure-storage-account"></a>Az Azure Storage-fiók

1. Olvasási hozzáférés engedélyezve van, függetlenül a fiók fog használni a TSI adatok eléréséhez szüksége. Az Azure Blob Storage olvasási hozzáférésének engedélyezésére vonatkozó további tudnivalókért olvassa el a [tárolási erőforrásokhoz való hozzáférés kezelése](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources).

1. További információk a közvetlen módon adatokat olvasni az Azure Blob Storage-ból, [adatok áthelyezése az Azure Storage szolgáltatásba vagy onnan](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

1. Adatok exportálása az Azure Storage-fiók:

    * Először győződjön meg arról, hogy a fiók rendelkezik a szükséges követelményeket, megfelel-e adatok exportálása. Olvasási [tárolási importálása és exportálása a követelmények](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements) további információt.
    * Adatok exportálása az Azure Storage-fiók regisztrálásához meg egyéb módjaival kapcsolatos [tárolási adatok importálása és exportálása a blobok](https://docs.microsoft.com/azure/storage/common/storage-import-export-data-from-blobs)

### <a name="blob-storage-considerations"></a>A BLOB storage kapcsolatos szempontok

* Az Azure storage rendelkezik olvasási és írási korlátok alapján hogyan nehéz a TSI (előzetes verzió) használata.  
* Az Azure TSI privát előzetes verzió még nem biztosít bármilyen Parquet blob metaadat-tároló külső adatfeldolgozó rendszerek támogatása. Azonban azt a vizsgált, és előfordulhat, hogy támogassa a jövőben.  
* Olvassa el az Azure-blobok segítségével tudják feldolgozni az adatokat idő-ügyfeleknek kell.
* Az Azure TSI Private Preview hajt végre, a jobb teljesítmény érdekében Blobadatok dinamikus újraparticionálása. Mindez által elvetését, majd újra létre kellene hoznia a blobokat. A legtöbb szolgáltatás fogja legjobban kiszolgálása az internetszolgáltatójuk által az eredeti fájl használatával.  
* A TSI (előzetes verzió) adatok blobok között is duplikálódnak.

### <a name="data-deletion"></a>Adatok törlése

Az Azure TSI privát előzetes verzió jelenleg nem támogatja az adatok törlése, de a jövőben lesz. Támogatást általánosan elérhető, de potenciálisan hamarabb várhatóan. Értesítjük a felhasználókat, ha adatok törlését is nyújtunk támogatást.

Ne törölje blobok, mivel a Time Series Insights (előzetes verzió) TSI frissítés belül a blobok metaadatait tárolja.

## <a name="ingress"></a>Bejövő forgalom

### <a name="azure-time-series-insights-ingress-policies"></a>Az Azure Time Series Insights bejövő házirendek

Az Azure TSI privát előzetes verzió támogatja az ugyanazon eseményforrásokból és fájltípus esetében, amelyet ma hajtja végre.

Támogatott eseményforrások a következők:

* Azure IoT Hub
* Azure Event Hubs
  * Megjegyzés: Az Azure Event Hubs-példányok támogatják a Kafka.

Támogatott fájltípusok:

* JSON
  * Több támogatott JSON-alakzatok a tudjuk kezelni tud, lásd: a [Time Series lekérdezés](./time-series-insights-update-tsq.md) dokumentációját.

### <a name="data-availability"></a>Az adatok elérhetősége

Az Azure TSI Private Preview indexeli az adatokat a blob-méret optimalizálása stratégia használatával. Ez azt jelenti, hogy adatok lekérdezése (amely alapul mennyi adatot és milyen sebesség a hamarosan elérhető) indexelése követően érhető el. Ahogy azt a nyilvános előzetes verzióban megközelítést, logikai hozzáadódik minden néhány másodpercben (amely lehetővé teszi adatok lekérdezések gyorsabb és megbízhatóbb) keresse meg az új események.

> [!IMPORTANT]
> * Nyilvános előzetes verzió – TSI elérhetővé adatokat, és szerezze meg a eseményforrás 60 másodpercen belül.  
> * Tekintse meg az adatok előtt hosszabb ideig várhatóan privát előzetes verzió használata során legyen elérhető.  
>   * Ha bármely jelentős késést tapasztal, forduljon hozzánk.

### <a name="scale"></a>Méretezés

Terveink szerint már az Azure TSI Private Preview 6Mbps környezetenként támogatására. Továbbfejlesztett skálázási támogatást a jövőbeli kiadásokhoz tervezett.

[!INCLUDE [tsi-update-docs](../../includes/time-series-insights-update-documents.md)]

## <a name="next-steps"></a>További lépések

Olvassa el a [Azure TSI (előzetes verzió) tárolási a bejövő és kimenő](./time-series-insights-update-storage-ingress.md).

Olvassa el az új [Idősorozat-modell](./time-series-insights-update-tsm.md).

<!-- Images -->
[1]: media/v2-update-storage-ingress/storage-architecture.png
[2]: media/v2-update-storage-ingress/parquet-files.png
[3]: media/v2-update-storage-ingress/blob-storage.png
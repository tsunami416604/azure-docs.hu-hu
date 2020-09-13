---
title: A Storage áttekintése – Azure Time Series Insights Gen2 | Microsoft Docs
description: Tudnivalók az adattárolásról Azure Time Series Insights Gen2.
author: esung22
ms.author: elsung
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/31/2020
ms.custom: seodec18
ms.openlocfilehash: c05de0462dde2b09e0e01919dfc691a85df153fa
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89483269"
---
# <a name="data-storage"></a>Adattárolás

Azure Time Series Insights Gen2-környezet létrehozásakor két Azure-erőforrást hoz létre:

* Egy Azure Time Series Insights Gen2 környezet, amely konfigurálható a meleg adattároláshoz.
* Egy Azure Storage-fiók a hideg adattároláshoz.

A meleg tárolóban tárolt adatai csak a [Time Series lekérdezési API](./time-series-insights-update-tsq.md) -k és a [Azure Time Series Insights Explorer](./time-series-insights-update-explorer.md)használatával érhetők el. A meleg áruház a Azure Time Series Insights Gen2-környezet létrehozásakor kiválasztott [megőrzési időszakon](./time-series-insights-update-plan.md#the-preview-environment) belül friss adatokkal fog szerepelni.

Azure Time Series Insights a Gen2 a hűtőházi tároló adatait az Azure Blob Storage-ba menti a [Parquet fájlformátumban](#parquet-file-format-and-folder-structure). Azure Time Series Insights a Gen2 kizárólag a hűtőházi adattárolási adattárakat kezeli, de közvetlenül a standard Parquet-fájlokként is elérhető.

> [!WARNING]
> Az Azure Blob Storage-fiók tulajdonosaként, ahol a hűtőházi adattárolási adat található, teljes hozzáférése van a fiókban lévő összes adathoz. Ez a hozzáférés írási és törlési engedélyeket is tartalmaz. Ne szerkessze vagy törölje azokat az adatAzure Time Series Insights Gen2, amelyek adatvesztést okozhatnak.

## <a name="data-availability"></a>Adatok rendelkezésre állása

Azure Time Series Insights Gen2 partíciókat és indexeli az optimális lekérdezési teljesítményt. Az adatok elérhetővé válnak mind a meleg (ha engedélyezve), mind a hűtőházi tároló lekérdezéséhez az indexelés után. A betöltött adatmennyiség hatással lehet erre a rendelkezésre állásra.

> [!IMPORTANT]
> Előfordulhat, hogy az adatmennyiség akár 60 másodpercig is eltarthat, amíg az adatmennyiség elérhetővé válik. Ha 60 másodpercen túli jelentős késés tapasztalható, küldjön egy támogatási jegyet a Azure Portalon keresztül.

## <a name="azure-storage"></a>Azure Storage

Ez a szakasz a Azure Time Series Insights Gen2 vonatkozó Azure Storage-adatokat ismerteti.

Az Azure Blob Storage részletes ismertetését olvassa el a [Storage Blobok bemutatása](../storage/blobs/storage-blobs-introduction.md)című cikkből.

### <a name="your-storage-account"></a>A Storage-fiók

Azure Time Series Insights Gen2-környezet létrehozásakor létrejön egy Azure Storage-fiók a hosszú távú hűtőházi tárolóban.  

Azure Time Series Insights a Gen2 az Azure Storage-fiók minden eseményének két példányát megőrzi. Az egyik másolat a betöltési idő alapján rendezi az eseményeket, így mindig lehetővé teszi az események elérését egy időben rendezett sorrendben. Az idő múlásával Azure Time Series Insights Gen2 is létrehoz egy újraparticionált másolatot az adatokról, így optimalizálva a végrehajtás alatt álló lekérdezéseket.

Az összes adatait a rendszer határozatlan ideig tárolja az Azure Storage-fiókjában.

#### <a name="writing-and-editing-blobs"></a>Blobok írása és szerkesztése

A lekérdezés teljesítményének és az adatelérhetőségnek a biztosításához ne szerkessze vagy törölje azokat a blobokat, amelyeket Azure Time Series Insights Gen2 hozott létre.

#### <a name="accessing-cold-store-data"></a>A hűtőházi adattárolási adatok elérése

Az adatoknak a [Azure Time Series Insights Explorer](./time-series-insights-update-explorer.md) és az [idősorozat lekérdezési API](./time-series-insights-update-tsq.md)-kkal való elérésén kívül az adatok közvetlenül a hűtőházi tárolóban tárolt Parquet-fájlokból is elérhetők. Például elolvashatja, átalakíthatja és megtisztíthatja az Jupyter-jegyzetfüzetben tárolt adatait, majd felhasználhatja a Azure Machine Learning modellnek ugyanabban a Spark-munkafolyamatban való betanításához.

Az adatok közvetlenül az Azure Storage-fiókból való eléréséhez olvasási hozzáféréssel kell rendelkeznie a Azure Time Series Insights Gen2-adatok tárolására használt fiókhoz. Ezután a Parquet fájl létrehozási ideje alapján elolvashatja a kiválasztott adatmennyiséget az `PT=Time` alább ismertetett mappában található parketta- [fájl formátuma](#parquet-file-format-and-folder-structure) szakaszban.  A Storage-fiókhoz való olvasási hozzáférés engedélyezésével kapcsolatos további információkért lásd: [a Storage-fiók erőforrásaihoz való hozzáférés kezelése](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Adattörlés

Ne törölje a Azure Time Series Insights Gen2-fájljait. A kapcsolódó adatok csak Azure Time Series Insights Gen2 belül kezelhetők.

### <a name="parquet-file-format-and-folder-structure"></a>A parketta fájlformátuma és a mappa szerkezete

A Parquet egy nyílt forráskódú, oszlopos fájlformátum, amely hatékony tárolást és teljesítményt nyújt. Azure Time Series Insights a Gen2 a parketta használatával engedélyezi az idősoros azonosító-alapú lekérdezési teljesítményt a skálán.  

A Parquet fájltípussal kapcsolatos további információkért olvassa el a [parketta dokumentációját](https://parquet.apache.org/documentation/latest/).

Azure Time Series Insights Gen2 az alábbi módon tárolja az adatai másolatait:

* Az első, a kezdeti másolat a betöltési idő alapján van particionálva, és nagyjából megérkezésük sorrendjében tárolja az adatot. Ez az adat a `PT=Time` mappában található:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* A második, újraparticionált másolat az idősorozat-azonosítók szerint van csoportosítva, és a `PT=TsId` mappában található:

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

A mappában lévő Blobok neveinek timestamp a `PT=Time` Azure Time Series Insights Gen2, és nem az események időbélyege.

A mappában lévő adatok a `PT=TsId` lekérdezéshez az idő múlásával lesznek optimalizálva, és nem statikus. Az újraparticionálás során előfordulhat, hogy egyes események több blobban is szerepelnek. A mappában lévő Blobok elnevezése nem garantált, hogy változatlan maradjon.

Általánosságban elmondható, hogy ha közvetlenül a Parquet-fájlokon keresztül fér hozzá az adataihoz, használja a `PT=Time` mappát.  A jövőbeli funkciók lehetővé teszik a mappa hatékony elérését `PT=TsId` .

> [!NOTE]
>
> * `<YYYY>` leképezi a négy számjegyű év ábrázolását.
> * `<MM>` leképezi a kétjegyű hónapok ábrázolását.
> * `<YYYYMMDDHHMMSSfff>` leképezi a kétjegyű (), kétjegyű hónap (), kétjegyű (), kétjegyű (), kétjegyű (), `YYYY` `MM` `DD` `HH` `MM` kétszámjegyű második ( `SS` ) és három számjegyű ezredmásodperc ( `fff` ) közötti időbélyegzőt ábrázoló ábrázolást.

Azure Time Series Insights Gen2-események a következő módon vannak leképezve a parketta-fájl tartalmára:

* Minden esemény egyetlen sorra van leképezve.
* Minden sor tartalmazza az **időbélyegző** oszlopot egy esemény időbélyegzővel. Az időbélyegző tulajdonság soha nem null értékű. Alapértelmezés szerint az **Event várólistán lévő idő** , ha nincs megadva az időbélyegző tulajdonság az eseményforrás számára. A tárolt időbélyegző mindig UTC szerint van.
* Minden sor tartalmazza a Azure Time Series Insights Gen2-környezet létrehozásakor meghatározott idősoros azonosító (TSID) oszlop (oka) t. A TSID-tulajdonság neve tartalmazza az `_string` utótagot.
* A telemetria-adatként elküldett összes többi tulajdonság a `_bool` tulajdonság típusától függően (logikai), `_datetime` (időbélyegző), ( `_long` hosszú), `_double` (Double), ( `_string` String) vagy `dynamic` (dinamikus) oszlopokra van leképezve.  További információt a [támogatott adattípusokról](./concepts-supported-data-types.md)szóló témakörben olvashat.
* Ez a leképezési séma a **(z) V = 1** néven hivatkozott fájlformátum első verziójára vonatkozik, és az azonos nevű alapmappában tárolódik. A szolgáltatás fejlődése során ez a leképezési séma változhat, és a hivatkozási név megnő.

## <a name="next-steps"></a>Következő lépések

* További információ az [adatmodellezésről](./time-series-insights-update-tsm.md).

* Tervezze meg [Azure Time Series Insights Gen2-környezetét](./time-series-insights-update-plan.md).

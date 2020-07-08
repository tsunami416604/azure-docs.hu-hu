---
title: A Storage áttekintése – Azure Time Series Insights | Microsoft Docs
description: Ismerkedjen meg Azure Time Series Insights adattárolással.
author: esung22
ms.author: elsung
manager: diegoviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: fc6b6b42293b4f2028f1a12af950e96e28febc87
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085565"
---
# <a name="data-storage"></a>Adattárolás

Time Series Insights *utólagos* elszámolású (TB) SKU-környezet létrehozásakor két Azure-erőforrást hoz létre:

* Egy Azure Time Series Insights környezet, amely konfigurálható a meleg adattároláshoz.
* Egy Azure Storage-fiók a hideg adattároláshoz.

A meleg tárolóban tárolt adatai csak a [Time Series lekérdezés](./time-series-insights-update-tsq.md) és a [Azure Time Series Insights Explorer](./time-series-insights-update-explorer.md)használatával érhetők el. A meleg áruház a Time Series Insights környezet létrehozásakor kiválasztott [megőrzési időszakon](./time-series-insights-update-plan.md#the-preview-environment) belül friss adatokkal fog szerepelni.

Azure Time Series Insights menti a hűtőházi tároló adatait az Azure Blob Storage-ba a [Parquet fájlformátumban](#parquet-file-format-and-folder-structure). A Time Series Insights kizárólag a hűtőházi adattárolási adattárakat kezeli, de közvetlenül a standard Parquet-fájlokként is elérhető.

> [!WARNING]
> Az Azure Blob Storage-fiók tulajdonosaként, ahol a hűtőházi adattárolási adat található, teljes hozzáférése van a fiókban lévő összes adathoz. Ez a hozzáférés írási és törlési engedélyeket is tartalmaz. Ne szerkessze és ne törölje a Azure Time Series Insights írást, mert az adatvesztést eredményezhet.

## <a name="data-availability"></a>Adatok rendelkezésre állása

Az optimális lekérdezési teljesítmény érdekében Azure Time Series Insights partíciókat és indexeli az adataikat. Az adatok elérhetővé válnak mind a meleg (ha engedélyezve), mind a hűtőházi tároló lekérdezéséhez az indexelés után. A betöltött adatmennyiség hatással lehet erre a rendelkezésre állásra.

> [!IMPORTANT]
> Előfordulhat, hogy az adatmennyiség akár 60 másodpercig is eltarthat, amíg az adatmennyiség elérhetővé válik. Ha 60 másodpercen túli jelentős késés tapasztalható, küldjön egy támogatási jegyet a Azure Portalon keresztül.

## <a name="azure-storage"></a>Azure Storage

Ez a szakasz a Azure Time Series Insights vonatkozó Azure Storage-adatokat ismerteti.

Az Azure Blob Storage részletes ismertetését olvassa el a [Storage Blobok bemutatása](../storage/blobs/storage-blobs-introduction.md)című cikkből.

### <a name="your-storage-account"></a>A Storage-fiók

Azure Time Series Insights TB-környezet létrehozásakor létrejön egy Azure Storage-fiók a hosszú távú hűtőházi tárolóban.  

Azure Time Series Insights az Azure Storage-fiókban az egyes események két példányát is megőrzi. Az egyik másolat a betöltési idő alapján rendezi az eseményeket, így mindig lehetővé teszi az események elérését egy időben rendezett sorrendben. Az idő múlásával a Time Series Insights az adatok újraparticionált másolatát is létrehozhatja az elvégezhető Time Series Insights lekérdezés optimalizálásához.

Az összes adatait a rendszer határozatlan ideig tárolja az Azure Storage-fiókjában.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Time Series Insights Blobok írása és szerkesztése

A lekérdezés teljesítményének és az adatelérhetőségnek a biztosításához ne szerkessze vagy törölje a Time Series Insights által létrehozott blobokat.

#### <a name="accessing-time-series-insights-cold-store-data"></a>A Time Series Insights a hűtőházi adattárhoz való hozzáférés

A [Time Series Insights Explorer](./time-series-insights-update-explorer.md) és az [idősorozat-lekérdezés](./time-series-insights-update-tsq.md)adatainak elérésén kívül az adatok közvetlenül a hűtőházi tárolóban tárolt parketta-fájlokból is elérhetők. Például elolvashatja, átalakíthatja és megtisztíthatja az Jupyter-jegyzetfüzetben tárolt adatait, majd felhasználhatja a Azure Machine Learning modellnek ugyanabban a Spark-munkafolyamatban való betanításához.

Az adatok közvetlenül az Azure Storage-fiókból való eléréséhez olvasási hozzáféréssel kell rendelkeznie a Time Series Insights előzetes verzió adatainak tárolásához használt fiókhoz. Ezután a Parquet fájl létrehozási ideje alapján elolvashatja a kiválasztott adatmennyiséget az `PT=Time` alább ismertetett mappában található parketta- [fájl formátuma](#parquet-file-format-and-folder-structure) szakaszban.  A Storage-fiókhoz való olvasási hozzáférés engedélyezésével kapcsolatos további információkért lásd: [a Storage-fiók erőforrásaihoz való hozzáférés kezelése](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Adattörlés

Ne törölje a Azure Time Series Insights fájlokat. A kapcsolódó adatok csak Azure Time Series Insights belül kezelhetők.

### <a name="parquet-file-format-and-folder-structure"></a>A parketta fájlformátuma és a mappa szerkezete

A Parquet egy nyílt forráskódú, oszlopos fájlformátum, amely hatékony tárolást és teljesítményt nyújt. A Azure Time Series Insights a parketta használatával engedélyezi az idősoros azonosító-alapú lekérdezési teljesítményt a skálán.  

A Parquet fájltípussal kapcsolatos további információkért olvassa el a [parketta dokumentációját](https://parquet.apache.org/documentation/latest/).

Azure Time Series Insights az alábbi módon tárolja az adatai másolatait:

* Az első, a kezdeti másolat a betöltési idő alapján van particionálva, és nagyjából megérkezésük sorrendjében tárolja az adatot. Ez az adat a `PT=Time` mappában található:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* A második, újraparticionált másolat az idősorozat-azonosítók szerint van csoportosítva, és a `PT=TsId` mappában található:

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

A mappában lévő Blobok neveinek időbélyegzője a `PT=Time` Azure Time Series Insights, és nem az események időbélyege.

A mappában lévő adatok a `PT=TsId` lekérdezéshez az idő múlásával lesznek optimalizálva, és nem statikus. Az újraparticionálás során előfordulhat, hogy egyes események több blobban is szerepelnek. A mappában lévő Blobok elnevezése nem garantált, hogy változatlan maradjon. 

Általánosságban elmondható, hogy ha közvetlenül a Parquet-fájlokon keresztül fér hozzá az adataihoz, használja a `PT=Time` mappát.  A jövőbeli funkciók lehetővé teszik a mappa hatékony elérését `PT=TsId` . 

> [!NOTE]
>
> * `<YYYY>`leképezi a négy számjegyű év ábrázolását.
> * `<MM>`leképezi a kétjegyű hónapok ábrázolását.
> * `<YYYYMMDDHHMMSSfff>`leképezi a kétjegyű (), kétjegyű hónap (), kétjegyű (), kétjegyű (), kétjegyű (), `YYYY` `MM` `DD` `HH` `MM` kétszámjegyű második ( `SS` ) és három számjegyű ezredmásodperc ( `fff` ) közötti időbélyegzőt ábrázoló ábrázolást.

Time Series Insights előnézeti események a következő módon vannak leképezve a parketta-fájl tartalmára:

* Minden esemény egyetlen sorra van leképezve.
* Minden sor tartalmazza az **időbélyegző** oszlopot egy esemény időbélyegzővel. Az időbélyegző tulajdonság soha nem null értékű. Alapértelmezés szerint az **Event várólistán lévő idő** , ha nincs megadva az időbélyegző tulajdonság az eseményforrás számára. A tárolt időbélyegző mindig UTC szerint van.
* Minden sor tartalmazza a Time Series Insights-környezet létrehozásakor definiált idősoros azonosító (TSID) oszlop (oka) t. A TSID-tulajdonság neve tartalmazza az `_string` utótagot.
* A telemetria-adatként elküldett összes többi tulajdonság a `_bool` tulajdonság típusától függően (logikai), `_datetime` (időbélyegző), ( `_long` hosszú), `_double` (Double), ( `_string` String) vagy `dynamic` (dinamikus) oszlopokra van leképezve.  További információt a [támogatott adattípusokról](./concepts-supported-data-types.md)szóló témakörben olvashat.
* Ez a leképezési séma a **(z) V = 1** néven hivatkozott fájlformátum első verziójára vonatkozik, és az azonos nevű alapmappában tárolódik. A szolgáltatás fejlődése során ez a leképezési séma változhat, és a hivatkozási név megnő.

## <a name="next-steps"></a>További lépések

* További információ az [adatmodellezésről](./time-series-insights-update-tsm.md).

* Tervezze meg [Azure Time Series Insights előnézeti környezetét](./time-series-insights-update-plan.md).

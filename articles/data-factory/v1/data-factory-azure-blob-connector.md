---
title: "Adatok másolása az Azure Blob Storage |} Microsoft Docs"
description: "Ismerje meg, hogy a blob-adatok másolása az Azure Data Factory. A minta: adatok másolása az Azure Blob Storage és az Azure SQL Database."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f66ddecd6b999400b05a4b00aa781ffef3f7887d
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Másolja az adatokat, vagy az Azure Blob Storage Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](data-factory-azure-blob-connector.md)
> * [2. verzió – Előzetes verzió](../connector-azure-blob-storage.md)

> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [Azure Blob Storage-összekötőt, a V2](../connector-azure-blob-storage.md).


Ez a cikk ismerteti, hogyan a másolási tevékenység során az Azure Data Factory és az Azure Blob Storage-adatok másolása. Buildekről nyújtanak a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, amelynek során adatátvitel a másolási tevékenység az általános áttekintést.

## <a name="overview"></a>Áttekintés
Bármely támogatott forrás adattárolóból Azure Blob Storage vagy az Azure Blob Storage bármely támogatott fogadó adattárolóhoz adatainak másolhatja. A következő táblázat adattárolókhoz támogatott adatforrások listáját tartalmazza, vagy a másolási tevékenység által fogadók esetében. Adatok áthelyezése például **a** egy SQL Server-adatbázist vagy egy Azure SQL-adatbázis **való** egy Azure blobtárolóba. És adatainak másolhatja **a** Azure blob storage **való** Azure SQL Data Warehouse vagy egy Azure Cosmos DB gyűjteményt. 

## <a name="supported-scenarios"></a>Támogatott esetek
Adatokat másolhat **az Azure Blob Storage** tárolja a következő adatokat:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Adatok másolása a következő adatokat tárolja **Azure Blob Storage**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]
 
> [!IMPORTANT]
> Másolási tevékenység támogatja az adatok másolását a/az általános célú Azure Storage-fiókok és a gyakran használt adatok/ritkán Blob Storage tárolóban. A tevékenység támogatja **blokk, hozzáfűzése, vagy olvasása lapblobokat**, azonban a **csak a blokkblobokat írása**. Prémium szintű Storage nem támogatott, a fogadó, mert a lapblobokat ezt támogatja.
> 
> Másolási tevékenység adatot nem töröl a forrás után a rendszer sikeresen átmásolja az adatokat a célhelyre. Ha sikeres másolatot megszüntetését követően törölheti a forrásadatok van szüksége, hozzon létre egy [egyéni tevékenység](data-factory-use-custom-activities.md) törli az adatokat, és az adatcsatorna használja a tevékenységet. Egy vonatkozó példáért lásd: a [Delete blob vagy mappa mintát a Githubon](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity). 

## <a name="get-started"></a>Bevezetés
A másolási tevékenység, amely helyezi át az adatokat az Azure Blob Storage vagy a különböző eszközök/API-k használatával létrehozhat egy folyamatot.

Hozzon létre egy folyamatot a legegyszerűbb módja használatára a **másolása varázsló**. Ennek a cikknek a [forgatókönyv](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) adatok másolása az Azure Blob Storage-helyre máshová Azure Blob Storage folyamat létrehozásához. Az adatok másolása az Azure Blob Storage Azure SQL Database adatcsatorna létrehozásával oktatóanyagok esetén lásd: [oktatóanyag: hozzon létre egy folyamatot, másolása varázslóval](data-factory-copy-data-wizard-tutorial.md).

Az alábbi eszközöket használhatja a folyamatokat létrehozni: **Azure-portálon**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**, és **REST API**. Lásd: [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját.

Akár az eszközök vagy API-k, hajtsa végre a következő lépésekkel hozza létre egy folyamatot, amely mozgatja az adatokat a forrás-tárolóban a fogadó tárolóban:

1. Hozzon létre egy **adat-előállító**. Egy adat-előállító tartalmazhat egy vagy több folyamatok. 
2. Hozzon létre **összekapcsolt szolgáltatások** bemeneti és kimeneti adatok csatolásához tárolja a a data factory. Például ha a másolt adatok az az Azure blob storage Azure SQL-adatbázishoz, hoz létre az Azure storage-fiók és az Azure SQL adatbázis összekapcsolása a data factory két összekapcsolt szolgáltatások. Azure Blob Storage jellemző csatolt szolgáltatás tulajdonságait, lásd: [szolgáltatástulajdonságok kapcsolódó](#linked-service-properties) szakasz. 
2. Hozzon létre **adatkészletek** a másolási művelet bemeneti és kimeneti adatok. A példában az előző lépésben említett hozzon létre egy adatkészlet adja meg a blob-tároló és a bemeneti adatokat tartalmazó mappát. És hoz létre, ha meg szeretné adni az SQL-tábla az Azure SQL-adatbázis, amely tárolja az adatokat a blob-tároló átmásolja egy másik DataSet adatkészletben. Adott Azure Blob-tároló adatkészlet tulajdonságai, lásd: [adatkészlet tulajdonságai](#dataset-properties) szakasz.
3. Hozzon létre egy **csővezeték** , amely fogad egy bemeneti adatkészlet és egy kimeneti adatkészletet másolási tevékenységgel. A korábban említett példában BlobSource forrás-és SqlSink akár használhatja a fogadó a másolási tevékenységhez. Ehhez hasonlóan az Azure SQL Database az Azure Blob Storage másolása, használható SqlSource és BlobSink a másolási tevékenység. Tekintse meg a másolási tevékenység tulajdonságok adott Azure Blob Storage [tevékenység Tulajdonságok másolása](#copy-activity-properties) szakasz. További részletek a tárolóban használatáról a forrás vagy a fogadó a hivatkozásra a adattároló az előző szakaszban.  

A varázsló használatakor a Data Factory entitások (összekapcsolt szolgáltatások adatkészletek és a feldolgozási sor) JSON-definíciók automatikusan létrejönnek. Eszközök/API-k (kivéve a .NET API-t) használata esetén adja meg a Data Factory entitások a JSON formátum használatával.  A mintában használt adatok másolása az Azure Blob Storage az adat-előállító entitások JSON-definíciók, lásd: [JSON példák](#json-examples-for-copying-data-to-and-from-blob-storage  ) című szakaszát.

A következő szakaszok részletesen bemutatják, amely segítségével határozza meg a Data Factory entitások adott Azure Blob Storage JSON-tulajdonságok.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok
Az összekapcsolt szolgáltatások használatával egy Azure Storage összekapcsolása egy Azure data factory két típusa van. Ezek: **AzureStorage** társított szolgáltatás és **AzureStorageSas** társított szolgáltatás. Az Azure tárolás társított szolgáltatása az adat-előállítóban globális hozzáférést biztosít az Azure Storage. Mivel az Azure Storage SAS (közös hozzáférésű Jogosultságkód) kapcsolódó szolgáltatás korlátozott/időhöz kötött hozzáféréssel a data factory biztosítja az Azure Storage. Nincsenek más különbségek a következő két összekapcsolt szolgáltatások között. Válassza ki az igényeinek megfelelő társított szolgáltatás. A következő szakaszokban további részleteket a következő két összekapcsolt szolgáltatások.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Adja meg a bemeneti vagy kimeneti adatok az Azure Blob-tároló adatkészlet, állítsa be a type tulajdonságot az adathalmaz: **AzureBlob**. Állítsa be a **linkedServiceName** tulajdonság nevére, az Azure Storage vagy az Azure Storage SAS DataSet társított szolgáltatás.  A DataSet tulajdonságait adja meg a **blob tároló** és a **mappa** a blob Storage tárolóban.

JSON-szakaszok & meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például struktúra, a rendelkezésre állás és a házirend a DataSet adatkészlet JSON hasonlítanak minden adatkészlet esetében (Azure SQL, az Azure blob, Azure-tábla, stb.).

Adat-előállítót a következő alapú CLS-kompatibilis .NET típusú értékek támogatja a például az Azure blob séma olvasható az adatforrásokhoz tartozó "structure" írja be az adatokat biztosító: Int16, Int32, Int64, egyetlen, Double, Decimal, Byte [], Bool, String, Guid, Datetime, Datetimeoffset, Timespan. Adat-előállító automatikusan típuskonverziók hajt végre, amikor adatokat a forrás-tárolóban a fogadó tárolóban.

A **typeProperties** szakasz eltérő adatkészlet egyes típusai és információkat nyújt a hely formátumra stb, az adatok az adattárban. A typeProperties szakasz típusú adatkészlet **AzureBlob** adatkészlet tulajdonságai a következők:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| folderPath |A tároló és a blob-tároló mappa elérési útja. Példa: myblobcontainer\myblobfolder\ |Igen |
| fileName |A blob neve. Fájlnév nem, kötelező, és a kis-és nagybetűket.<br/><br/>Ha megad egy fájlnevet, a tevékenység (például a Másolás) működik a megadott Blob.<br/><br/>Ha nincs megadva fájlnév, másolása összes BLOB bemeneti adatkészlet folderPath tartalmazza.<br/><br/>Ha **Fájlnév** nincs megadva egy kimeneti adatkészlet és **preserveHierarchy** nincs megadva a tevékenység fogadó, a létrehozott fájl nevét a következő lenne ebben a formátumban: adatok.<Guid>. txt (például:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nem |
| partitionedBy |partitionedBy egy nem kötelező tulajdonság. Használhatja a dinamikus folderPath és fájlnevét idő adatsorozat adatok megadása. Például folderPath is paraméteres adatok óránkénti. Tekintse meg a [partitionedBy tulajdonság szakaszában](#using-partitionedBy-property) részletek és a példákat. |Nem |
| Formátumban | A következő formátumban típusok támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság a formátuma a következő értékek egyikét. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [az Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquet formátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszok. <br><br> Ha azt szeretné, hogy **másolja a fájlokat-van** közötti fájlalapú tárolók (bináris másolhatja azokat), hagyja ki a Formátum szakasz mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem |
| Tömörítés | Adja meg a típus és az adatok tömörítése szintjét. Támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. Támogatott szintek a következők: **Optimal** és **leggyorsabb**. További információkért lásd: [formátumú és tömörítést az Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |

### <a name="using-partitionedby-property"></a>PartitionedBy tulajdonság használatával
Az előző szakaszban említett, megadhat egy dinamikus folderPath és a fájlnév idő adatsorozat adatokhoz a **partitionedBy** tulajdonság, [adat-előállító funkciók és a rendszer változók](data-factory-functions-variables.md).

Idő adatsorozat adatkészleteket, az ütemezés és a szeletek további információkért lásd: [létrehozása adatkészletek](data-factory-create-datasets.md) és [ütemezés & végrehajtási](data-factory-scheduling-and-execution.md) cikkeket.

#### <a name="sample-1"></a>1. példa

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Ebben a példában {szelet} adat-előállító rendszer változó SliceStart (YYYYMMDDHH) formátumban megadott érték helyére. A szelet kezdete a SliceStart hivatkozik. A folderPath nem azonos az egyes szeletek. Például: wikidatagateway/wikisampledataout/2014100103 vagy wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>2. példa

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

Ebben a példában év, hónap, nap és SliceStart idején ki kell olvasni a külön változókat, amelyek folderPath és a fájlnév tulajdonság.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [létrehozása folyamatok](data-factory-create-pipelines.md) cikk. Az összes tevékenység tulajdonságai, például nevét, leírását, valamint bemeneti és kimeneti adatkészletek és házirendek érhetők el. Mivel a tulajdonságok érhetők el a **typeProperties** szakasz a tevékenység tevékenységek minden típusának függenek. A másolási tevékenység során két érték források és mosdók típusától függően. Ha egy Azure Blob Storage-ból adatokat helyez át, a másolási tevékenység beállítása a forrástípus **BlobSource**. Hasonlóképpen, ha adatokat az Azure Blob Storage, beállítása a fogadó típusa a másolási tevékenység **BlobSink**. Ez a témakör BlobSource és BlobSink által támogatott tulajdonságokról.

**BlobSource** támogatja a következő tulajdonságok a **typeProperties** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| Rekurzív |Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappát. |TRUE hamis (alapértelmezés) |Nem |

**BlobSink** támogatja a következő tulajdonságok **typeProperties** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| copyBehavior |Másolás viselkedését határozza meg, ha az adatforrás BlobSource vagy a fájlrendszer. |<b>PreserveHierarchy</b>: őrzi meg a fájl hierarchia a célmappában. A következő forrásfájl forrásmappához relatív elérési a relatív elérési út a cél-fájlját és a célmappa megegyezik.<br/><br/><b>FlattenHierarchy</b>: a forrásmappából a fájlok a célmappában első szintjét is. A fájlok céljaként automatikusan létrehozott nevet adni. <br/><br/><b>Mergefiles típusú</b>: egy fájl összes fájlt a forrásmappából egyesíti. Ha a fájl/Blob neve meg van adva, az egyesített neve legyen a megadott név; Ellenkező esetben lenne automatikusan létrehozott fájl nevét. |Nem |

**BlobSource** is támogatja a visszamenőleges kompatibilitás érdekében a két tulajdonság.

* **treatEmptyAsNull**: Megadja, hogy null vagy üres karakterlánc null értékként kezelje-e.
* **skipHeaderLineCount** -határozza meg, hogy hány sort kell figyelmen kívül hagyja. Azt is alkalmazható csak amikor bemeneti adatkészlet által használt szöveges.

Hasonlóképpen **BlobSink** támogatja a következő tulajdonságot a visszamenőleges kompatibilitás érdekében.

* **blobWriterAddHeader**: Megadja, hogy az oszlopdefiníciók fejlécet hozzá egy kimeneti adatkészlet írása közben.

Adatkészletek mostantól támogatja a következő tulajdonságok funkcióit megvalósító: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

A következő táblázat az új adatkészlet tulajdonságai helyett a blob-forrás/fogadó tulajdonságok használatával nyújt útmutatást.

| Másolja az Activity tulajdonság | A DataSet tulajdonság |
|:--- |:--- |
| a BlobSource skipHeaderLineCount |skipLineCount és firstRowAsHeader. Sorok először kimarad, és az első sor fejléc, olvassa el. |
| a BlobSource treatEmptyAsNull |a bemeneti adatkészlet treatEmptyAsNull |
| a BlobSink blobWriterAddHeader |a kimeneti adatkészlet firstRowAsHeader |

Lásd: [megadása szöveges](data-factory-supported-file-and-compression-formats.md#text-format) szakasz ezeket a tulajdonságokat részletes tájékoztatást.    

### <a name="recursive-and-copybehavior-examples"></a>rekurzív és copyBehavior példák
Ez a szakasz ismerteti az eredményül kapott viselkedéstől rekurzív és copyBehavior kombinációk a másolási művelet.

| Rekurzív | copyBehavior | Viselkedésről |
| --- | --- | --- |
| igaz |preserveHierarchy |A forrásmappa mappa1, az alábbi szerkezettel: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célmappában mappa1 forrásaként azonos struktúrájú jön létre.<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| igaz |flattenHierarchy |A forrásmappa mappa1, az alábbi szerkezettel: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a cél az alábbi szerkezettel mappa1 jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File5 |
| igaz |mergefiles típusú |A forrásmappa mappa1, az alábbi szerkezettel: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a cél az alábbi szerkezettel mappa1 jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + fájl3 + File4 + 5 fájl tartalmát egy fájl automatikusan létrehozott fájlnévvel egyesülnek |
| false |preserveHierarchy |A forrásmappa mappa1, az alábbi szerkezettel: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célmappa mappa1 jön létre a következő struktúra<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>Fájl3, File4 és File5 Subfolder1 nem átveszik. |
| false |flattenHierarchy |A forrásmappa mappa1, az alábbi szerkezettel:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célmappa mappa1 jön létre a következő struktúra<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/><br/><br/>Fájl3, File4 és File5 Subfolder1 nem átveszik. |
| false |mergefiles típusú |A forrásmappa mappa1, az alábbi szerkezettel:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célmappa mappa1 jön létre a következő struktúra<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Egy fájl automatikusan létrehozott fájlnévvel egyesített file1 + File2 tartalma. automatikusan létrehozott nevet a file1 kiszolgálón<br/><br/>Fájl3, File4 és File5 Subfolder1 nem átveszik. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Forgatókönyv: Másolása varázsló használata a Blob Storage az adatok másolása
Vizsgáljuk meg gyorsan az adatok az Azure blob storage és a másolása. Ebben a bemutatóban forrás- és célkiszolgálón adatokat tárolja, típus: Azure Blob Storage tárolóban. A kimenetátirányítási mechanizmusával Ez a forgatókönyv adatokat egy mappából ugyanabban a blob-tárolóban egy másik mappába másolja. Ez a forgatókönyv nem szándékosan egyszerű mutatjuk be, beállítások vagy tulajdonságok forrás vagy a fogadó Blob Storage használata esetén. 

### <a name="prerequisites"></a>Előfeltételek
1. Hozzon létre egy általános célú **Azure Storage-fiók** Ha Ön nem rendelkezik ilyennel. A blob storage használata egyaránt **forrás** és **cél** adattároló ebben a forgatókönyvben. Ha még nem rendelkezik Azure Storage-fiókkal, a létrehozás folyamatáért lásd a [tárfiók létrehozását](../../storage/common/storage-create-storage-account.md#create-a-storage-account) ismertető cikket.
2. Hozzon létre egy blob-tároló nevű **adfblobconnector** tárfiókban. 
4. Hozza létre a **bemeneti** a a **adfblobconnector** tároló.
5. Hozzon létre egy fájlt **emp.txt** az a következő tartalmat, és töltse fel, hogy a **bemeneti** mappa eszközökkel, mint [Azure Tártallózó](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```
### <a name="create-the-data-factory"></a>A data factory létrehozása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson **+ új** kattintson a bal felső sarkának **Eszközintelligencia + analitika**, és kattintson a **adat-előállító**.
3. A **New data factory** (Új data factory) panelen:   
    1. Adja meg **ADFBlobConnectorDF** a a **neve**. Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a hibaüzenetet kapja: `*Data factory name “ADFBlobConnectorDF” is not available`, változtassa meg az adat-előállítóban (például yournameADFBlobConnectorDF) nevét, és próbálja meg újra létrehozni. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.
    2. Jelölje ki az Azure-**előfizetést**.
    3. Az erőforráscsoport, válassza ki a **használata meglévő** jelöljön ki egy meglévő erőforráscsoportot (vagy) az **hozzon létre új** egy erőforráscsoport nevének megadása.
    4. Válassza ki a Data Factory **helyét**.
    5. A panel alján jelölje be a **Pin to dashboard** (Rögzítés az irányítópulton) jelölőnégyzetet.
    6. Kattintson a **Create** (Létrehozás) gombra.
3. A létrehozásának befejezése után megjelenik a **adat-előállító** panelen a következő ábrán látható módon: ![Data factory kezdőlap](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Másolás varázsló
1. A Data Factory kezdőlapján kattintson a **[előzetes verzió] adatok másolása** elindíthatja a csempe **másolása varázsló** egy külön lapján.    
    
    > [!NOTE]
    >    Ha azt látja, hogy a webböngésző akadt-e a "Engedélyező...", tiltsa le vagy törölje a jelet **külső cookie-k blokkolását, és a helyadatok** beállítása (vagy) engedélyezve legyen, és hozzon létre egy kivételt **login.microsoftonline.com**, és ezután próbálja meg újból elindítani a varázslót.
2. A **Properties** (Tulajdonságok) oldalon:
    1. Adja meg **CopyPipeline** a **feladatnév**. A feladatnév: a kimenetátirányítási mechanizmusával a data factory nevét.
    2. Adjon meg egy **leírás** a feladathoz (választható).
    3. A **feladat ütemben történik vagy ütemezett feladat**, tartsa a **futtatása rendszeres ütemezés szerint** lehetőséget. Ha szeretné végrehajtani a feladatot csak egyszer ütemezés ismételten a Futtatás helyett, jelölje be **futtassa egyszer most**. Választ ki, ha **futtassa egyszer most** beállítást, a [egyszeri folyamat](data-factory-create-pipelines.md#onetime-pipeline) jön létre. 
    4. Megtarthatja a **ismétlődő mintát**. Ez a feladat futtatása naponta a következő lépésben megadhatja a kezdő és befejező időpontja között.
    5. Módosítsa a **kezdő dátum/idő** való **04/21/2017**. 
    6. Módosítsa a **záró dátum és idő** való **04/25/2017**. Érdemes lehet írja be a dátum, keresse meg azt a naptár helyett.     
    8. Kattintson a **Tovább** gombra.
      ![Másolja az eszköz - Tulajdonságok lap](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png) 
3. A **Source data store** (Forrásadattár) oldalon kattintson az **Azure Blob Storage** csempére. Az oldal használatával megadhatja a forrásadattárat a másolási feladathoz. Használhatja egy meglévő adattár társított szolgáltatását, vagy megadhat egy új adattárat. Egy meglévő társított szolgáltatást használja, a kiválasztott **a meglévő összekapcsolt szolgáltatások** válassza ki a megfelelő társított szolgáltatás. 
    ![Másolja az eszköz - forrás egy adattárolási lap](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. A **Specify the Azure Blob storage account** (Az Azure Blob Storage-fiók megadása) oldalon:
   1. Az automatikusan létrehozott nevet **kapcsolatnév**. A kapcsolat nevének megadása típusú társított szolgáltatás neve: Azure Storage. 
   2. Győződjön meg arról, hogy az **Account selection method** (Fiókválasztási módszer) mezőben a **From Azure subscriptions** (Azure-előfizetésekből) lehetőség van kiválasztva.
   3. Válassza ki az Azure-előfizetéssel, vagy hagyja **válassza ki az összes** a **Azure-előfizetés**.   
   4. A kiválasztott előfizetéshez elérhető Azure Storage-fiókok listájából válasszon ki egy **Azure Storage-fiókot**. Másik lehetőségként tárolási fiók beállításainak manuális megadása kiválasztásával **manuálisan adja meg** választás, a **kijelöléséről fiók**.
   5. Kattintson a **Tovább** gombra. 
      ![Eszköz másolni - adja meg az Azure Blob storage-fiók](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. A **Choose the input file or folder** (A bemeneti fájl vagy mappa kiválasztása) oldalon:
   1. Kattintson duplán a **adfblobcontainer**.
   2. Válassza ki **bemeneti**, és kattintson a **válasszon**. Ebben a bemutatóban válassza ki a bemeneti mappát. Kiválaszthatja a emp.txt fájl a mappában helyette. 
      ![Másolja az eszköz – a bemeneti fájl vagy mappa kiválasztása](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. Az a **válassza ki azt a bemeneti fájl vagy mappa** lap:
    1. Ellenőrizze, hogy a **fájl vagy mappa** értéke **adfblobconnector/bemeneti**. Ha a fájlok runbookok, 2017/04/01, 2017/04/02, és így tovább, írja be például adfblobconnector/bemeneti / {year} / {month} / {day} fájlra vagy mappára. A szövegmező kívül TAB billentyű megnyomásával, lásd: három legördülő listában formátumok (éééé) év, hónap (hh) és nap (nn). 
    2. Ne adja meg az **fájl rekurzív módon másolja**. Válassza ki ezt a beállítást a célhelyre másolja a fájlokat a rekurzív módon bejárás mappák segítségével. 
    3. Nem a **bináris másolási** lehetőséget. Ezt a beállítást a célként megadott forrásfájl bináris elkészítésére. Nem jelölt ki ez a forgatókönyv, hogy a további beállítások a következő lapokon megjelenik. 
    4. Ellenőrizze, hogy a **tömörítési típus** értéke **nincs**. Válassza az ehhez a beállításhoz tartozó értéket, ha a forrásfájlok tömörített a támogatott formátumok egyikében. 
    5. Kattintson a **Tovább** gombra.
    ![Másolja az eszköz – a bemeneti fájl vagy mappa kiválasztása](./media/data-factory-azure-blob-connector/chose-input-file-folder.png) 
7. A **File format settings** (Fájlformátum beállításai) oldalon a fájl elemzése során a varázsló által automatikusan észlelt elválasztó karakterek és séma láthatók. 
    1. Erősítse meg a következő beállításokat: egy. A **fájlformátum** értéke **szövegformátum**. A támogatott formátumok a legördülő listában tekintheti meg. Például: JSON, Avro, ORC, Parquet.
        b. A **oszlop elválasztó** értéke `Comma (,)`. A legördülő listából válassza ki a Data Factory által támogatott más oszlop határolójeleket tekintheti meg. Egy egyéni elválasztó karakter is megadható.
        c. A **sor elválasztó** értéke `Carriage Return + Line feed (\r\n)`. A legördülő listából válassza ki a Data Factory által támogatott más sor határolójeleket tekintheti meg. Egy egyéni elválasztó karakter is megadható.
        d. A **sorszám kihagyása** értéke **0**. Ha azt szeretné, hogy néhány sorok figyelmen kívül hagyja a fájl elején, adja meg itt.
        e.  A **adatok első sora oszlopneveket tartalmaz** nincs beállítva. Ha a forrásfájlok oszlopnevek az első sort tartalmaz, akkor válassza ezt a lehetőséget.
        f. A **üres oszlopérték tekinti null** beállítás.
    2. Bontsa ki a **speciális beállítások** elérhető speciális beállítás megjelenítéséhez.
    3. A lap alján, tekintse meg a **előzetes** adatok emp.txt fájlból.
    4. Kattintson a **SÉMA** fül megtekintéséhez a séma, amely a varázsló következtetni rá, ha megnézi a forrás-fájlt.
    5. Az elválasztó karakterek megtekintése és adatok előzetes megtekintése után kattintson a **Next** (Tovább) gombra.
    ![Eszköz - fájl formátuma beállítások másolása](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)  
8. Az a **cél adattároló lap**, jelölje be **Azure Blob Storage**, és kattintson a **következő**. Az Azure Blob Storage mind a forrás és cél adattárolókhoz a forgatókönyv használ.    
    ![Eszköz - célkiszolgáló kijelölése adattár másolása](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. A **adja meg az Azure Blob storage-fiók** lap:
   1. Adja meg **AzureStorageLinkedService** a a **kapcsolatnév** mező.
   2. Győződjön meg arról, hogy az **Account selection method** (Fiókválasztási módszer) mezőben a **From Azure subscriptions** (Azure-előfizetésekből) lehetőség van kiválasztva.
   3. Jelölje ki az Azure-**előfizetést**.  
   4. Válassza ki az Azure storage-fiók. 
   5. Kattintson a **Tovább** gombra.     
10. Az a **válassza ki azt a kimeneti fájl vagy mappa** lap: 
    6. Adja meg **mappa elérési útja** , **adfblobconnector/kimeneti / {year} / {month} / {day}**. Adja meg **lapon**.
    7. Az a **év**, jelölje be **éééé**.
    8. Az a **hónap**, győződjön meg arról, hogy van-e állítva **MM**.
    9. Az a **nap**, győződjön meg arról, hogy van-e állítva **nn**.
    10. Ellenőrizze, hogy a **tömörítési típus** értéke **nincs**.
    11. Ellenőrizze, hogy a **viselkedés másolása** értéke **fájlok egyesítése**. Ha a kimeneti fájl ugyanazzal a névvel már létezik, az új tartalom kerüljön végén ugyanazt a fájlt.
    12. Kattintson a **Tovább** gombra.
    ![Másolja az eszköz - a kimeneti fájl vagy mappa](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. Az a **fájl formázási beállítások** lapon tekintse át a beállításokat, és kattintson a **következő**. A további beállítások egyik egy fejléc hozzáadását a kimeneti fájl. Ha ezt a beállítást választja, a fejlécsor hozzá rendelkező oszlopok neveinek a séma, a forrás. Az alapértelmezett oszlopnevek átnevezheti a séma a következő adatforrás megtekintésekor. Például megváltoztathatja az első oszlop Utónév és Vezetéknév második oszlopnak. Ezt követően a kimeneti fájl jön létre ezekkel a nevekkel fejléccel oszlopnevekként. 
    ![Eszköz - cél formátum beállításainak fájl másolása](media/data-factory-azure-blob-connector/file-format-destination.png)
12. A a **Teljesítménybeállítások** lapján ellenőrizze, hogy **egységek cloud** és **másolatok párhuzamos** vannak beállítva, hogy **automatikus**, és kattintson a Tovább gombra. Ezek a beállítások kapcsolatos részletekért lásd: [másolása tevékenység teljesítmény- és hangolási útmutató](data-factory-copy-activity-performance.md#parallel-copy).
    ![Eszköz - teljesítmény beállítások másolása](media/data-factory-azure-blob-connector/copy-performance-settings.png) 
14. Az a **összegzés** lapon tekintse át az összes beállítást (a feladat tulajdonságai, a beállításokat a forrás és cél és a beállítások), és kattintson a **következő**.
    ![Másolja az eszköz - összegző lapja](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Tekintse át a **Summary** (Összegzés) oldalon szereplő információkat, majd kattintson a **Finish** (Befejezés) gombra. A varázsló létrehoz két társított szolgáltatást, két adathalmazt (bemeneti és kimeneti), valamint egy folyamatot a data factoryban (ahonnét elindította a Másolás varázslót).
    ![Másolja az eszköz - központi telepítés lap](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>A figyelő az adatcsatorna (feladatok)

1. Kattintson a hivatkozásra `Click here to monitor copy pipeline` a a **telepítési** lap. 
2. Megjelenik a **felügyeletéhez és kezeléséhez az alkalmazás** egy külön lapján.  ![Megfigyelés és kezelés alkalmazás](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Módosítás a **start** időt legfelül `04/19/2017` és **end** beosztásba `04/27/2017`, és kattintson a **alkalmaz**. 
4. Az öt tevékenység windows kell megjelennie a **tevékenység WINDOWS** listája. A **WindowStart** alkalommal minden nap közé tartozik folyamat kezdetétől a csővezeték-befejezési időpontjával. 
5. Kattintson a **frissítése** gombra kattint, az a **tevékenység WINDOWS** lista néhány alkalommal amíg meg nem jelenik a tevékenység windows állapotának a Kész értékre van állítva. 
6. Most győződjön meg arról, hogy a kimeneti fájlok adfblobconnector tároló kimeneti mappában vannak-e létre. A következő gyökérmappa-szerkezetében kimeneti mappában kell megjelennie: 
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25    
    ```
Figyelése és kezelése az adat-előállítók kapcsolatos részletes információkért lásd: [figyelése és kezelése a Data Factory-folyamathoz](data-factory-monitor-manage-app.md) cikk. 
 
### <a name="data-factory-entities"></a>Data Factory entitások
Most lépjen vissza a lap a Data Factory kezdőlapján. Figyelje meg, hogy van két összekapcsolt szolgáltatások, két adatkészletet, és a data factory egyik adatcsatornáinak most. 

![Data Factory kezdőlapján entitások](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Kattintson a **Szerző és központi telepítése** elindíthatja a Data Factory Editor. 

![A Data Factory szerkesztője](media/data-factory-azure-blob-connector/data-factory-editor.png)

A következő adat-előállító entitások az adat-előállítóban kell megjelennie: 

 - Két társított szolgáltatások. Egyet a forrás- és a cél a másik egy. Az összekapcsolt szolgáltatások tekintse meg az azonos Azure Storage-fiók ebben a forgatókönyvben. 
 - Két adatkészletet. Egy bemeneti adatkészlet és egy kimeneti adatkészletet. Ebben a bemutatóban mindkét blob tárolóhoz használni de különböző mappák (bemeneti és kimeneti) hivatkozik.
 - Egy folyamat. A folyamat, amely egy blob-forrás- és egy blob fogadó használja az adatok másolása az Azure blob helyére máshová az Azure blob másolási tevékenységet tartalmaz. 

Az alábbi szakaszok nyújtanak további információt arról ezeket az entitásokat. 

#### <a name="linked-services"></a>Társított szolgáltatások
Két összekapcsolt szolgáltatások kell megjelennie. Egyet a forrás- és a cél a másik egy. Ebben a bemutatóban mindkét definíciók ugyanúgy néznek ki, kivéve a neveket. A **típus** társított szolgáltatás beállítása **AzureStorage**. A társított szolgáltatás definíciójának legfontosabb tulajdonsága nem a **connectionString**, amely használják adat-előállító csatlakozni az Azure Storage-fiók futásidőben. Figyelmen kívül hagyja a hubName tulajdonság a-definícióban. 

##### <a name="source-blob-storage-linked-service"></a>Forrás blob storage társított szolgáltatás
```json
{
    "name": "Source-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

##### <a name="destination-blob-storage-linked-service"></a>Cél blob storage társított szolgáltatás

```json
{
    "name": "Destination-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

Az Azure tárolás társított szolgáltatásának kapcsolatos további információkért lásd: [szolgáltatástulajdonságok kapcsolódó](#linked-service-properties) szakasz. 

#### <a name="datasets"></a>Adathalmazok
Két adatkészletet van: egy bemeneti adatkészlet és egy kimeneti adatkészletet. A DataSet adatkészlet típusúra **AzureBlob** is. 

A bemeneti adatkészlet mutat a **bemeneti** mappában található a **adfblobconnector** blob tároló. A **külső** tulajdonsága **igaz** ehhez az adatkészlethez az adatok nem előállított, a másolási tevékenység során, amely ehhez az adatkészlethez bemeneti adatokként a folyamat. 

A kimeneti adatkészlet mutat a **kimeneti** a azonos blob-tároló mappa. A kimeneti adatkészletet is használ az évet, hónapot és napot a **SliceStart** rendszerváltozó dinamikusan kiértékelése a kimeneti fájl elérési útját. Függvények és a Data Factory által támogatott rendszerváltozók listáját lásd: [adat-előállító funkciók és rendszerváltozók](data-factory-functions-variables.md). A **külső** tulajdonsága **hamis** (alapértelmezett érték), mert ez az adatkészlet hozzák a feldolgozási sor. 

Azure Blob-adathalmazra által támogatott tulajdonságokról kapcsolatos további információkért lásd: [adatkészlet tulajdonságai](#dataset-properties) szakasz.

##### <a name="input-dataset"></a>Bemeneti adatkészlet

```json
{
    "name": "InputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Source-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/input/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

##### <a name="output-dataset"></a>Kimeneti adatkészlet

```json
{
    "name": "OutputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Destination-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/output/{year}/{month}/{day}",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
            "partitionedBy": [
                { "name": "year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }
            ]
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

#### <a name="pipeline"></a>Folyamat
A folyamat csak egy tevékenység rendelkezik. A **típus** a tevékenység értéke **másolási**.  A tevékenység típusa tulajdonságai nincsenek két részből áll, egy forrás és a többi fogadó egy. A forrás típusa **BlobSource** , a tevékenység egy blob-tároló az adatok másolása. A fogadó típusa **BlobSink** adatok másolása egy blob Storage tevékenységként. A másolási tevékenység veszi InputDataset-z4y bemeneteként és OutputDataset-z4y kimeneteként. 

BlobSource és BlobSink által támogatott tulajdonságokról kapcsolatos további információkért lásd: [tevékenység Tulajdonságok másolása](#copy-activity-properties) szakasz. 

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "MergeFiles",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-z4y"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-z4y"
                    }
                ],
                "policy": {
                    "timeout": "1.00:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 3,
                    "longRetry": 0,
                    "longRetryInterval": "00:00:00"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "Activity-0-Blob path_ adfblobconnector_input_->OutputDataset-z4y"
            }
        ],
        "start": "2017-04-21T22:34:00Z",
        "end": "2017-04-25T05:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>Adatok másolása, és a Blob Storage JSON példák  
Az alábbi példák megadják minta JSON-definíciókat tartalmazzon, segítségével hozzon létre egy folyamatot [Azure-portálon](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Adatok másolása az Azure Blob Storage és az Azure SQL Database mutatnak. Azonban az adatok átmásolhatók **közvetlenül** a forrásokban, sem a megadott nyelő [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenység során az Azure Data Factory használatával.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>JSON-NÁ. példa: Adatok másolása az Blob-tároló az SQL-adatbázis
A következő példában:

1. A társított szolgáltatás típusa [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](#linked-service-properties).
3. Bemeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](#dataset-properties).
4. Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. A [csővezeték](data-factory-create-pipelines.md) , a másolási tevékenység által használt [BlobSource](#copy-activity-properties) és [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties).

A minta másolatok idősorozat adatokat az Azure blob-Azure SQL táblázat óránként. A mintákat a következő szakaszok ismertetik ezeket a mintákat használt JSON-tulajdonságok.

**Az Azure SQL társított szolgáltatásnak:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Az Azure tárolás társított szolgáltatásának:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Az Azure Data Factory két típusú Azure Storage társított szolgáltatásokat támogat: **AzureStorage** és **AzureStorageSas**. Az első címtárra a kapcsolati karakterlánc, amely tartalmazza a fiókkulcs ad meg, és a későbbi egy, a közös hozzáférésű Jogosultságkód (SAS) Uri megadása. Lásd: [összekapcsolt szolgáltatások](#linked-service-properties) című szakaszban talál információt.  

**Az Azure Blob bemeneti adatkészletet:**

Adatok van felvett egy új blobból minden órában (gyakoriság: óra, időköz: 1). A mappa elérési útját és nevét a BLOB dinamikusan értékeli ki a kezdési időt a szelet által feldolgozott alapján. A mappa elérési útját használja év, hónap és nap részét kezdési idejét, valamint fájl nevét a kezdő időpontja óra részét. "external": "true" beállítás arról értesíti az, hogy a tábla az adat-előállítóban külső, és egy tevékenység adat-előállító nem hozzák adat-Előállítóban.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Az Azure SQL kimeneti adatkészlet:**

A mintaadatok másolatok táblához "MyTable" Azure SQL adatbázis neve. A tábla létrehozása az Azure SQL adatbázis azonos számú oszlopot tartalmaz a Blob CSV-fájl várt. Új sorok hozzáadásakor a tábla minden órában.

```json
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**A másolási tevékenység során a Blob-forrás és fogadó SQL-feldolgozási folyamat:**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **BlobSource** és **fogadó** típusúra **SqlSink**.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>JSON-NÁ. példa: Adatok másolása az Azure SQL Azure-blobba
A következő példában:

1. A társított szolgáltatás típusa [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](#linked-service-properties).
3. Bemeneti [dataset](data-factory-create-datasets.md) típusú [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](#dataset-properties).
5. A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) és [BlobSink](#copy-activity-properties).

A minta-idősoros adatok az Azure SQL tábla másolja az Azure blob óránként. A mintákat a következő szakaszok ismertetik ezeket a mintákat használt JSON-tulajdonságok.

**Az Azure SQL társított szolgáltatásnak:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Az Azure tárolás társított szolgáltatásának:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Az Azure Data Factory két típusú Azure Storage társított szolgáltatásokat támogat: **AzureStorage** és **AzureStorageSas**. Az első címtárra a kapcsolati karakterlánc, amely tartalmazza a fiókkulcs ad meg, és a későbbi egy, a közös hozzáférésű Jogosultságkód (SAS) Uri megadása. Lásd: [összekapcsolt szolgáltatások](#linked-service-properties) című szakaszban talál információt.  

**Az Azure SQL bemeneti adatkészlet:**

A minta azt feltételezi, hogy létrehozott egy tábla "MyTable" Azure SQL, egy "timestampcolumn" nevű adatsorozat időadatok oszlopot tartalmaz.

"External" beállítása: "true" tájékoztatja Data Factory szolgáltatásnak, hogy a tábla külső data factoryval való, és nem hozzák adat-előállító tevékenység.

```json
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Az Azure Blob kimeneti adatkészlet:**

Adatot ír egy új blob minden órában (gyakoriság: óra, időköz: 1). A mappa elérési útját a BLOB a szelet által feldolgozott kezdési ideje alapján dinamikusan történik. A mappa elérési útját használja, év, hónap, nap és a kezdési idő órában részeit.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": { "type": "DateTime",  "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**A másolási tevékenység során az SQL-forrás és fogadó Blob egy folyamaton belül:**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **SqlSource** és **fogadó** típusúra **BlobSink**. A megadott SQL-lekérdezést a **SqlReaderQuery** tulajdonság kiválasztása az adatok másolása az elmúlt órában.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
              {
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureSQLInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureBlobOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                      },
                      "sink": {
                        "type": "BlobSink"
                      }
                },
                   "scheduler": {
                      "frequency": "Hour",
                      "interval": 1
                },
                "policy": {
                      "concurrency": 1,
                      "executionPriorityOrder": "OldestFirst",
                      "retry": 0,
                      "timeout": "01:00:00"
                }
              }
         ]
    }
}
```

> [!NOTE]
> Képezze le a fogadó adatkészletből oszlopok forrás adatkészletből oszlopokat, lásd: [Azure Data Factory dataset oszlopai leképezési](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Teljesítmény- és hangolása
Lásd: [másolási tevékenység teljesítmény- és hangolása útmutató](data-factory-copy-activity-performance.md) tájékozódhat az kulcsfontosságú szerepet játszik adatátvitelt jelölik a (másolási tevékenység során) az Azure Data Factory és különböző módokon optimalizálhatja azt, hogy hatás teljesítményét.

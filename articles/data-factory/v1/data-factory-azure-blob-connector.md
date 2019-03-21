---
title: Adatok másolása és- tárolókról az Azure Blob Storage |} A Microsoft Docs
description: 'Megtudhatja, hogyan másolhat blob adatokat az Azure Data Factoryban. A minta használata: Hogyan másolhat adatokat, és az Azure Blob Storage és az Azure SQL Database.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ea4cf03b368cebbfc7d1229be28014b54f2c11d0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58004317"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Másolja az adatokat, vagy az Azure Data Factory használatával az Azure Blob Storage-ból
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-azure-blob-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-azure-blob-storage.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [a v2-ben az Azure Blob Storage-összekötő](../connector-azure-blob-storage.md).


Ez a cikk bemutatja, hogyan kell használni a másolási tevékenység az Azure Data Factoryban az adatok másolásához, és az Azure Blob Storage-ból. Épül a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikket, amely megadja az adatok áthelyezését a másolási tevékenységgel rendelkező általános áttekintése.

## <a name="overview"></a>Áttekintés
Másolhatja az adatokat bármely támogatott forrás adattárból az Azure Blob Storage vagy az Azure Blob Storage bármilyen támogatott fogadó adattárba. Az alábbi táblázat források támogatott adattárak listáját tartalmazza, vagy a másolási tevékenység által fogadók. Adatok áthelyezése például **a** egy SQL Server-adatbázist vagy egy Azure SQL database **való** egy Azure blob storage. És az adatokat másolja **a** az Azure blob storage-bA **való** az Azure SQL Data Warehouse és a egy Azure Cosmos DB-gyűjteményben.

## <a name="supported-scenarios"></a>Támogatott esetek
Adatokat másolja **Azure Blob Storage-ból** tárolja, a következő adatokat:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

A következő adattárakból származó adatokat másolja **az Azure Blob Storage**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!IMPORTANT]
> A másolási tevékenység másolhatja az adatokat, és az általános célú Azure Storage-fiókok és a gyakori és ritka elérésű Blob storage támogatja. A tevékenység támogatja **letiltása, hozzáfűzése, vagy olvasása lapblobokat**, azonban a **csak blokkblobok írása**. Az Azure Premium Storage nem támogatott fogadóként, mert azt-je támogat lapblobokat.
>
> A másolási tevékenység nem törli adatokat a forrás-után az adatok sikerült átmásolni a célhelyre. Ha a forrás-adatok törlése után a sikeres másolási van szüksége, hozzon létre egy [egyéni tevékenység](data-factory-use-custom-activities.md) törölheti az adatokat, és használja a tevékenységet a folyamat. Egy vonatkozó példáért tekintse meg a [Delete blob vagy mappa minta a Githubon](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity).

## <a name="get-started"></a>Bevezetés
Létrehozhat egy folyamatot egy másolási tevékenységgel, amely áthelyezi az adatokat és-tárolókról az Azure Blob Storage más eszközök/API-k használatával.

A folyamat létrehozásának legegyszerűbb módja az, hogy használja a **másolása varázsló**. A cikk egy [forgatókönyv](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) folyamat adatokat másol egy Azure Blob Storage-helyre egy másik Azure Blob Storage-helyre létrehozására szolgáló. A folyamat adatokat másol egy Azure Blob Storage az Azure SQL Database létrehozására, olvassa el [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md).

A következő eszközök használatával hozzon létre egy folyamatot: **Az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, **Azure Resource Manager-sablon**, **.NET API**, és  **REST API-val**. Lásd: [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját.

Az eszközök vagy az API-kat használja, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani:

1. Hozzon létre egy **adat-előállító**. Adat-előállító egy vagy több folyamattal is tartalmazhat.
2. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához. Például ha a másolt adatokat egy Azure blob storage-ból egy Azure SQL Database-adatbázishoz, létrehozhat két társított szolgáltatást az Azure storage-fiók és Azure SQL database összekapcsolása a data factory. Konkrétan az Azure Blob Storage társított szolgáltatás tulajdonságait, lásd: [társított szolgáltatások tulajdonságai](#linked-service-properties) szakaszban.
2. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli. A példában az előző lépésben említett megadja a blobtárolót és a bemeneti adatokat tartalmazó mappát egy adatkészletet hoz létre. És adja meg az SQL-táblát az Azure SQL Database tárolja az adatokat a blob storage-tárhelyéből, egy másik adatkészletet hoz létre. Konkrétan az Azure Blob Storage-adatkészlet tulajdonságai, lásd: [adatkészlet tulajdonságai](#dataset-properties) szakaszban.
3. Hozzon létre egy **folyamat** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként. A példában azt korábban említettük használhat BlobSource a forrás-és SqlSink fogadóként a másolási tevékenység. Hasonlóképpen, az Azure SQL Database-ből az Azure Blob Storage másolása, SqlSource és a használata BlobSink a másolási tevékenység. Másolási tevékenység tulajdonságai konkrétan az Azure Blob Storage, lásd: [másolási tevékenység tulajdonságai](#copy-activity-properties) szakaszban. A forrás vagy a fogadó adattár használatát részletekért kattintson a hivatkozásra az adattár az előző szakaszban.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) JSON-definíciói az Ön számára. Eszközök/API-k (kivéve a .NET API) használatakor adja meg a Data Factory-entitások a JSON formátumban.  Az adatok másolása az Azure Blob Storage-tárolók/használt Data Factory-entitások JSON-definíciói minták, lásd: [JSON példák](#json-examples-for-copying-data-to-and-from-blob-storage  ) című szakaszát.

A következő szakaszok az Azure Blob Storage adott Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
Használhatja az Azure Storage összekapcsolása egy Azure data factoryt, társított szolgáltatásokat két típusa van. Ezek a következők: **AzureStorage** társított szolgáltatást, és **AzureStorageSas** társított szolgáltatást. Az Azure Storage társított szolgáltatás az adat-előállító globális hozzáférést biztosít az Azure Storage. Mivel az Azure Storage SAS (közös hozzáférésű Jogosultságkód) társított szolgáltatás az adat-előállító korlátozott/időhöz kötött hozzáféréssel rendelkező biztosítja az Azure Storage. Nincs más két társított szolgáltatás közötti különbségek vannak. Válassza ki az igényeinek megfelelő társított szolgáltatás. A következő szakaszok további részletekkel szolgálnak ezek két társított szolgáltatást.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Adjon meg egy adatkészletet, amely egy Azure Blob Storage bemeneti vagy kimeneti adatokat jelöli, állítsa be a type tulajdonság, az adatkészlet: **AzureBlob**. Állítsa be a **linkedServiceName** tulajdonságot az adatkészlet neve lesz az Azure Storage vagy az Azure Storage SAS-beli társított szolgáltatást.  A az adatkészlet tulajdonságait adja meg a **blobtároló** és a **mappa** a blob storage-ban.

JSON-szakaszok & adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például a szerkezetet, rendelkezésre állást és szabályzatát adatkészlet JSON szakaszok hasonlóak az összes adatkészlet esetében (az Azure SQL, az Azure blob-, az Azure table-, stb.).

A Data factory olvasási séma adatforrásokhoz, például az Azure blob "struktúra" írja be az adatokat, amelyek biztosítják a következő CLS-kompatibilis .NET-alapú típusú értékeket támogatja: Int16, Int32, Int64, egyetlen, Double, Decimal, Byte [], Bool, String, Guid, Datetime, Datetimeoffset, időtartam. A Data Factory automatikusan típuskonverziók hajt végre, amikor, amely adatokat helyez át egy forrásadattárból egy fogadó adattárba.

A **typeProperties** szakasz eltérő az egyes adatkészletet, és információkat nyújt a hely formátumra stb, az adatok az adattárban. A typeProperties szakasz típusú adatkészlet **AzureBlob** adatkészlet a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| folderPath |A tároló és a blob Storage-mappa elérési útja. Példa: myblobcontainer\myblobfolder\ |Igen |
| fileName |A blob nevével. a fájlnév paraméter nem kötelező, és a kis-és nagybetűket.<br/><br/>Ha megadja a FileName paramétert, a Blobra a (beleértve a másolási) tevékenység működik.<br/><br/>Ha a fájlnév nincs megadva, példány összes BLOB bemeneti adatkészlet a folderPath tartalmazza.<br/><br/>Amikor **fileName** nincs megadva a kimeneti adatkészlet és **preserveHierarchy** nincs megadva a tevékenység fogadó, a létrehozott fájl neve a következő lenne ebben a formátumban: Adatok. <Guid>.txt (például:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nem |
| partitionedBy |partitionedBy tulajdonság megadása nem kötelező. Használhatja a dinamikus folderPath és fájlnevét, idősorozat-adatok megadása. Ha például folderPath rendelkeznek az adatok minden óra. Tekintse meg a [partitionedBy tulajdonság szakaszának használatával](#using-partitionedBy-property) a részletek és példák. |Nem |
| Formátum | A következő formátumtípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt formátumot az alábbi értékek egyikére. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquetformátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszokat. <br><br> Ha azt szeretné, hogy **, a fájlok másolása a-rendszer** közötti fájlalapú tárolók (bináris másolat), hagyja ki a format szakaszban mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem |
| A tömörítés | Adja meg a típus és az adatok tömörítési szintje. Támogatott típusok a következők: **A GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. Támogatott szintek a következők: **Optimális** és **leggyorsabb**. További információkért lásd: [fájl- és tömörítési formátumok az Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |

### <a name="using-partitionedby-property"></a>PartitionedBy tulajdonság használatával
Az előző szakaszban ismertetett módon, megadhat egy dinamikus folderPath és fájlnevét, idősorozat-adatokat a **partitionedBy** tulajdonság, [Data Factory-függvények, és a rendszerváltozók](data-factory-functions-variables.md).

A time series adatkészleteket, az ütemezés és a szeletek további információkért lásd: [adatkészletek létrehozása](data-factory-create-datasets.md) és [ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) cikkeket.

#### <a name="sample-1"></a>1. példa

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Ebben a példában {szelet} helyére a változó értékét, a Data Factory rendszer SliceStart (YYYYMMDDHH) formátumban megadva. Indítsa el a szelet időpontja a SliceStart hivatkozik. A folderPath eltér az egyes szeletekhez. Például: wikidatagateway/wikisampledataout/2014100103 vagy wikidatagateway/wikisampledataout/2014100104

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

Ebben a példában év, hónap, nap és SliceStart idején kinyert folderPath és a fileName tulajdonság által használt külön változókba.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & definiálását tevékenységek tulajdonságainak teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Tulajdonságok, mint például a nevét, leírását, bemeneti és kimeneti adatkészleteket és szabályzatokat minden típusú tevékenységek érhetők el. Mivel a tulajdonságok érhetők el a **typeProperties** a tevékenység szakaszban tevékenységek minden típusának számától függ. A másolási tevékenységhez azok változhat a forrásként és fogadóként típusú is. Egy Azure Blob Storage-ból helyez át adatokat, ha a forrás típusaként állítsa be a másolási tevékenység **BlobSource**. Ehhez hasonlóan ha egy Azure Blob Storage helyez át adatokat, állítsa be a fogadó típusa a másolási tevékenység **BlobSink**. Ez a szakasz a BlobSource és BlobSink által támogatott tulajdonságok listáját tartalmazza.

**BlobSource** támogatja a következő tulajdonságok a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| a rekurzív |Azt jelzi, hogy az adatok olvasható rekurzív módon az almappákban vagy csak a megadott mappába. |(Alapértelmezett érték), true a False |Nem |

**BlobSink** támogatja a következő tulajdonságok **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| a copyBehavior |A másolási viselkedés határozza meg, ha a forrás BlobSource vagy fájlrendszer. |<b>PreserveHierarchy</b>: megőrzi a hierarchiája a célmappában. A forrásmappa forrás-fájl elérési útja megegyezik a célmappában a célfájl elérési útja.<br/><br/><b>FlattenHierarchy</b>: a forrásmappából a fájlok a célmappában az első szintjét is. A cél fájlok automatikusan létrehozott nevet adni. <br/><br/><b>MergeFiles</b>: egyesíti a forrásmappából egy fájl összes fájlt. Ha a fájl/Blob neve van megadva, az egyesített fájl neve lesz a megadott név; Ellenkező esetben lenne automatikusan létrehozott fájl nevét. |Nem |

**BlobSource** is támogatja a két tulajdonság a visszamenőleges kompatibilitás érdekében.

* **treatEmptyAsNull**: Megadja, hogy kezelni a nullértékű vagy üres karakterlánc értéke null.
* **skipHeaderLineCount** – Itt adhatja meg, hogy hány sort kell hagyni. Ez akkor alkalmazható, csak amikor bemeneti adatkészletet használ TextFormat.

Ehhez hasonlóan **BlobSink** támogatja a következő tulajdonságot a visszamenőleges kompatibilitás érdekében.

* **blobWriterAddHeader**: Megadja, hogy a magyarázat az oszlopokhoz fejléc hozzáadása egy kimeneti adatkészletet írása közben.

Az adatkészletek most már támogatja a következő tulajdonságok ugyanazokat a funkciókat megvalósító: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

Az alábbi táblázat az új adatkészlet tulajdonságai helyett a blob-forrás/fogadó tulajdonságok használatával nyújt útmutatást.

| Másolási tevékenység tulajdonság | Adatkészlet tulajdonság |
|:--- |:--- |
| skipHeaderLineCount on BlobSource |skipLineCount és a firstRowAsHeader. A rendszer először kihagyja sorokat, és majd beolvassa az első sort fejlécnek. |
| a BlobSource treatEmptyAsNull |a bemeneti adatkészlet treatEmptyAsNull |
| a BlobSink blobWriterAddHeader |a kimeneti adatkészlet firstRowAsHeader |

Lásd: [TextFormat megadása](data-factory-supported-file-and-compression-formats.md#text-format) ezeket a tulajdonságokat a részletes információt a következő szakaszban.

### <a name="recursive-and-copybehavior-examples"></a>a rekurzív és copyBehavior példák
Ez a szakasz ismerteti az eredményül kapott viselkedéstől a másolási művelet rekurzív és copyBehavior értékek különböző kombinációihoz.

| a rekurzív | a copyBehavior | Eredményül kapott viselkedés |
| --- | --- | --- |
| true |preserveHierarchy |Forrás mappa mappa1 az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célmappában mappa1 szerkezete ugyanaz, mint a forrás jön létre<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |Forrás mappa mappa1 az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File5 |
| true |mergeFiles |Forrás mappa mappa1 az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + fájl3 + File4 + 5 fájl tartalmát egy fájlba a fájl automatikusan létrehozott névvel egyesítésekor |
| false |preserveHierarchy |Forrás mappa mappa1 az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Az alábbi struktúra használatával jön létre a célmappában mappa1<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>Fájl3, File4 és File5 Subfolder1 a rendszer nem követi. |
| false |flattenHierarchy |Forrás mappa mappa1 az alábbi struktúra használatával:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Az alábbi struktúra használatával jön létre a célmappában mappa1<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/><br/><br/>Fájl3, File4 és File5 Subfolder1 a rendszer nem követi. |
| false |mergeFiles |Forrás mappa mappa1 az alábbi struktúra használatával:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Az alábbi struktúra használatával jön létre a célmappában mappa1<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + 2. fájl tartalmát egy fájlba a fájl automatikusan létrehozott névvel összefésülése megtörténjen. Automatikusan létrehozott nevet a file1 kiszolgálón<br/><br/>Fájl3, File4 és File5 Subfolder1 a rendszer nem követi. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Forgatókönyv: A másolás varázsló használatával másolja ki az adatok Blob Storage
Tekintsük át gyorsan másolása az adatok Azure blob storage. Ez az útmutató a forrás- és cél adattárak típusa: Azure Blob Storage. Ebben az útmutatóban a folyamat adatokat másol egy mappát ugyanazon blob-tárolóban egy másik mappába. Ez a forgatókönyv nem szándékosan egyszerű mutatni, beállítások vagy a tulajdonságok a forrás vagy a fogadó Blob tároló használata esetén.

### <a name="prerequisites"></a>Előfeltételek
1. Hozzon létre egy általános célú **Azure Storage-fiók** Ha még nincs ilyen. A blob storage-használunk használja **forrás** és **cél** adatokat tárolni ebben az útmutatóban. Ha még nem rendelkezik Azure Storage-fiókkal, a létrehozás folyamatáért lásd a [tárfiók létrehozását](../../storage/common/storage-quickstart-create-account.md) ismertető cikket.
2. Hozzon létre egy blobtárolót **adfblobconnector** a storage-fiókban.
4. Hozza létre a **bemeneti** a a **adfblobconnector** tároló.
5. Hozzon létre egy fájlt **emp.txt** az az alábbi tartalmat, és töltse fel a **bemeneti** mappa-eszközökkel például [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```

### <a name="create-the-data-factory"></a>Az adat-előállító létrehozása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **erőforrás létrehozása** a bal felső sarokban, kattintson a **intelligencia és elemzés**, és kattintson a **adat-előállító**.
3. Az a **új adat-előállító** panelen:  
    1. Adja meg **ADFBlobConnectorDF** számára a **neve**. Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a hibaüzenetet kapja: `*Data factory name “ADFBlobConnectorDF” is not available`, módosítsa a nevet az adat-előállító (például yournameADFBlobConnectorDF), és próbálkozzon újra a létrehozással. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.
    2. Jelölje ki az Azure-**előfizetést**.
    3. Válassza ki az erőforráscsoportban **meglévő használata** válasszon ki egy meglévő erőforráscsoportot (vagy) válasszon **új létrehozása** erőforráscsoport nevének megadását.
    4. Válassza ki a Data Factory **helyét**.
    5. A panel alján jelölje be a **Pin to dashboard** (Rögzítés az irányítópulton) jelölőnégyzetet.
    6. Kattintson a **Create** (Létrehozás) gombra.
3. A létrehozás befejezése után a **Data Factory** panel a következő képen látható módon jelenik meg:  ![Data factory kezdőlap](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Másolás varázsló
1. A Data Factory kezdőlapján kattintson a **adatmásolás** csempére kattintva indítsa el a **másolás varázsló** külön lapon.  
    
    > [!NOTE]
    > Ha látja, hogy a böngésző "Authorizing..." megakadt, tiltsa le vagy törölje a jelet **külső cookie-k blokkolását és a helyadatok** beállítása (vagy) engedélyezve legyen, és hozzon létre kivételt a **login.microsoftonline.com**, majd próbálja ismét a varázsló elindítását.
2. A **Properties** (Tulajdonságok) oldalon:
    1. Adja meg **CopyPipeline** a **feladatnév**. A feladat neve az a név a data Factory-folyamatot.
    2. Adjon meg egy **leírás** (nem kötelező) a feladathoz.
    3. A **feladat kiadása ütemben történik, vagy a feladat ütemezés**, tartsa a **rendszeres ütemezés szerint fut** lehetőséget. Ha azt szeretné, ezt a feladatot csak egyszer helyett a Futtatás ismétlődő ütemezés szerint futtatni, válassza ki a **már egyszer futtatni**. Ha kiválasztja, **már egyszer futtatni** beállítást, egy [egyszeri folyamat](data-factory-create-pipelines.md#onetime-pipeline) jön létre.
    4. A megadott beállításokat tartsa **ismétlődő minta**. Ez a feladat fut, naponta, a következő lépésben adja meg a kezdési és befejezési idejének között.
    5. Módosítsa a **kezdő dátum/idő** való **2017-04/21**.
    6. Módosítsa a **záró dátum és idő** való **2017-04/25-ös**. Írja be a dátum a naptár böngészés helyett érdemes.
    8. Kattintson a **tovább**.
        ![Másolja ki az eszköz – Tulajdonságok lap](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png)
3. A **Source data store** (Forrásadattár) oldalon kattintson az **Azure Blob Storage** csempére. Az oldal használatával megadhatja a forrásadattárat a másolási feladathoz. Használhatja egy meglévő adattár társított szolgáltatását, vagy megadhat egy új adattárat. Egy meglévő társított szolgáltatás használatához kiválasztott **a meglévő társított szolgáltatások** , és válassza ki a megfelelő társított szolgáltatást.
    ![Másolja ki az eszköz - forrás adattároló lap](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. A **Specify the Azure Blob storage account** (Az Azure Blob Storage-fiók megadása) oldalon:
    1. Az automatikusan létrehozott nevet, a **kapcsolatnevet**. A kapcsolat neve a következő típusú társított szolgáltatás neve: Azure Storage.
    2. Győződjön meg arról, hogy az **Account selection method** (Fiókválasztási módszer) mezőben a **From Azure subscriptions** (Azure-előfizetésekből) lehetőség van kiválasztva.
    3. Válassza ki az Azure-előfizetését, vagy hagyja **válassza ki az összes** a **Azure-előfizetés**.
    4. A kiválasztott előfizetéshez elérhető Azure Storage-fiókok listájából válasszon ki egy **Azure Storage-fiókot**. Azt is beállíthatja a kiválasztásával manuálisan adja meg a tárfiók-beállítások **adja meg manuálisan** első számú megoldás a **kijelöléséről fiók**.
    5. Kattintson a **tovább**.  
        ![Másolja ki az eszköz – az Azure Blob storage-fiók megadása](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. A **Choose the input file or folder** (A bemeneti fájl vagy mappa kiválasztása) oldalon:
    1. Kattintson duplán a **adfblobcontainer**.
    2. Válassza ki **bemeneti**, és kattintson a **válasszon**. Ebben az útmutatóban válassza ki a bemeneti mappában. Kiválaszthatja az emp.txt fájlt a mappába helyette.
        ![Másolja ki az eszköz – a bemeneti fájl vagy mappa kiválasztása](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. Az a **a bemeneti fájl vagy mappa kiválasztása** oldalon:
    1. Ellenőrizze, hogy a **fájl vagy mappa** értékre van állítva **adfblobconnector/input**. Ha a fájlok almappákban levő, írja be például 2017/04/01, 2017/04/02, és így tovább, adfblobconnector/input / {year} / {month} / {day} fájlra vagy mappára vonatkozóan. Amikor lenyomja a TAB billentyűt a szövegmezőben jelölje ki, látható három legördülő listán válassza ki a formátumok év (éééé), (MM) hónap és nap (nn).
    2. Nincs beállítva **fájl rekurzív módon másolja**. Válassza ezt a beállítást a célhelyre kell másolni a fájlokat a rekurzív módon bejárás mappák között.
    3. Nem a **bináris másolat** lehetőséget. Válassza ki ezt a beállítást, hajtsa végre a célhelyre forrásfájl bináris másolatát. Ne válassza a forgatókönyv, hogy további beállításokat a következő lapokon megjelenik.
    4. Ellenőrizze, hogy a **tömörítési típus** értékre van állítva **None**. Ez a beállítás értékét akkor válassza, ha a forrásfájlok tömörített a támogatott formátumok valamelyikében.
    5. Kattintson a **tovább**.
    ![Másolja ki az eszköz – a bemeneti fájl vagy mappa kiválasztása](./media/data-factory-azure-blob-connector/chose-input-file-folder.png)
7. A **File format settings** (Fájlformátum beállításai) oldalon a fájl elemzése során a varázsló által automatikusan észlelt elválasztó karakterek és séma láthatók.
    1. Erősítse meg a következő beállításokat:  
        a. A **fájlformátum** értékre van állítva **szövegformátum**. Láthatja, hogy a támogatott formátumok a legördülő listában. Példa: JSON-t, Parquet, Avro és ORC.
       b. A **oszlophatároló** értékre van állítva `Comma (,)`. A legördülő listából válassza ki a Data Factory által támogatott más oszlophatárolókat látható. Megadhat egy egyéni elválasztó karakter is.
       c. A **Sorelválasztó** értékre van állítva `Carriage Return + Line feed (\r\n)`. Láthatja, hogy az a legördülő listából válassza ki a Data Factory által támogatott más sorok határolókaraktereit. Megadhat egy egyéni elválasztó karakter is.
       d. A **hagyja ki a sor száma** értékre van állítva **0**. Ha azt szeretné, hogy néhány sort a fájl elején hagyni, adja meg az ide.
       e. A **adatok első sora tartalmazza az oszlopnevek** nincs beállítva. Ha a forrásfájlok az első sorban oszlopneveket tartalmaz, akkor válassza ezt a lehetőséget.
       f. A **kezelni üres oszlop értékének null** beállítás.
    2. Bontsa ki a **speciális beállítások** megjelenítéséhez a speciális beállítás érhető el.
    3. A lap alján jelennek meg a **előzetes** adatok az emp.txt fájlban találhatók.
    4. Kattintson a **SÉMA** lap alján, a séma, amely a másolás varázsló következtetni megnézzük a forrás-fájlban lévő adatokat.
    5. Az elválasztó karakterek megtekintése és adatok előzetes megtekintése után kattintson a **Next** (Tovább) gombra.
    ![Másolja ki az eszköz - fájlformátum beállításai](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)
8. Az a **céladattár lap**válassza **Azure Blob Storage**, és kattintson a **tovább**. Használja az Azure Blob Storage, a forrás és cél adattárolók ebben az útmutatóban.  
    ![Másolja ki az eszköz - válassza célként megadott adattárba](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. A **adja meg az Azure Blob storage-fiók** oldalon:  
    1. Adja meg **AzureStorageLinkedService** számára a **kapcsolatnevet** mező.
    2. Győződjön meg arról, hogy az **Account selection method** (Fiókválasztási módszer) mezőben a **From Azure subscriptions** (Azure-előfizetésekből) lehetőség van kiválasztva.
    3. Jelölje ki az Azure-**előfizetést**.
    4. Válassza ki az Azure storage-fiókjában.
    5. Kattintson a **tovább**.
10. Az a **a kimeneti fájl vagy mappa kiválasztása** oldalon:  
    1. Adja meg **mappa elérési útja** , **adfblobconnector/output / {year} / {month} / {day}**. Adja meg **lapon**.
    1. Az a **év**válassza **éééé**.
    1. Az a **hónap**, győződjön meg arról, hogy van-e állítva **MM**.
    1. Az a **nap**, győződjön meg arról, hogy van-e állítva **nn**.
    1. Ellenőrizze, hogy a **tömörítési típus** értékre van állítva **None**.
    1. Ellenőrizze, hogy a **viselkedés másolása** értékre van állítva **fájlok egyesítése**. Ha a kimeneti fájl ezzel a névvel már létezik, az új tartalmakról ugyanazt a fájlt a végén.
    1. Kattintson a **tovább**.
       ![Másolja ki az eszköz – a kimeneti fájl vagy mappa kiválasztása](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. Az a **fájlformátum beállításai** lapon tekintse át a beállításokat, és kattintson a **tovább**. A további beállítások egyik egy fejléc hozzáadását a kimeneti fájlt. Ha ezt a lehetőséget választja, egy fejléc sorra kerül az oszlopok neveit, a forrás sémája. Az alapértelmezett oszlopnevek nevezheti át a forrás sémája megtekintésekor. Az első oszlop, például utónév és Vezetéknév második oszlopnak sikerült módosítani. Ezt követően a kimeneti fájl jön létre az ezekkel a nevekkel fejléc oszlopnevekként.
    ![Másolja ki az eszköz - cél fájlformátum beállításai](media/data-factory-azure-blob-connector/file-format-destination.png)
12. A a **teljesítménybeállításokat** lapon, ellenőrizze, hogy **felhőbeli egység** és **másolatok párhuzamos** vannak beállítva, hogy **automatikus**, és kattintson a Tovább gombra. Ezekkel a beállításokkal kapcsolatos részletekért lásd: [másolási tevékenységek teljesítményéhez és finomhangolási útmutató](data-factory-copy-activity-performance.md#parallel-copy).
    ![Eszköz - teljesítmény beállítások másolása](media/data-factory-azure-blob-connector/copy-performance-settings.png)
14. Az a **összefoglalás** lapon tekintse át a beállításokat (feladat tulajdonságai, a forrás és cél beállításai és beállítások), és kattintson a **tovább**.
    ![Másolja ki az eszköz - összegző lapja](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Tekintse át a **Summary** (Összegzés) oldalon szereplő információkat, majd kattintson a **Finish** (Befejezés) gombra. A varázsló létrehoz két társított szolgáltatást, két adathalmazt (bemeneti és kimeneti), valamint egy folyamatot a data factoryban (ahonnét elindította a Másolás varázslót).
    ![Másolja ki az eszköz – üzembe helyezés lap](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>A folyamat (a másolási tevékenység) figyelése

1. Kattintson a hivatkozásra `Click here to monitor copy pipeline` a a **üzembe helyezési** lapot.
2. Megtekintheti a **figyelése és felügyelete az alkalmazás** külön lapon.  ![Figyelheti és kezelheti az alkalmazás](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Változás a **start** idő a legfelül `04/19/2017` és **záró** időt `04/27/2017`, és kattintson a **alkalmaz**.
4. Az öt tevékenységablakok kell megjelennie a **TEVÉKENYSÉGABLAKOK** lista. A **WindowStart** alkalommal le kell fednie minden nap a folyamat kezdő folyamatfuttatások záró időponttal.
5. Kattintson a **frissítése** gombot a **TEVÉKENYSÉGABLAKOK** néhányszor, amíg megjelenik az állapotát a tevékenységablakok lista kész értékre van állítva.
6. Most győződjön meg arról, hogy a kimeneti fájlok jönnek létre adfblobconnector tároló kimeneti mappában. A kimeneti mappában a következő mappastruktúra kell megjelennie:
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25
    ```
   Figyelése és kezelése az adat-előállítók kapcsolatos részletes információkért lásd: [figyelése és kezelése a Data Factory-folyamatot](data-factory-monitor-manage-app.md) cikk.

### <a name="data-factory-entities"></a>Data Factory-entitások
Most váltson vissza a lap a Data Factory kezdőlapján. Figyelje meg, hogy jelenleg két társított szolgáltatást, két adatkészletet és egy folyamatot az adat-előállítóban.

![Data Factory kezdőlapjának az entitásokkal](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Kattintson a **készítése és üzembe helyezése** , indítsa el a Data Factory Editor.

![A Data Factory szerkesztője](media/data-factory-azure-blob-connector/data-factory-editor.png)

A data factory a következő Data Factory-entitások kell megjelennie:

- Két társított szolgáltatást. Egyet a forrás- és a másik egy, a cél. A társított szolgáltatások tekintse meg az azonos Azure Storage-fiók ebben az útmutatóban.
- Két adatkészletet. A bemeneti adatkészletek és a egy kimeneti adatkészletet. Ez az útmutató is ugyanazt a blob-tárolót használja, de különböző mappák (bemeneti és kimeneti) hivatkozik.
- Egy folyamat. A folyamat egy másolási tevékenység adatokat másol egy Azure blob-helyre máshová az Azure blob egy blob forrás és a egy blob fogadó használó tartalmazza.

A következő szakaszok ezeket az entitásokat további információt.

#### <a name="linked-services"></a>Társított szolgáltatások
Két társított szolgáltatást kell megjelennie. Egyet a forrás- és a másik egy, a cél. Ez az útmutató mindkét definíciók néznek ki, kivéve a neveket. A **típus** társított szolgáltatás beállítása **AzureStorage**. A társított szolgáltatás definíciójában legfontosabb tulajdonsága a **connectionString**, amelyekkel az adat-előállító való csatlakozáshoz futásidőben Azure Storage-fiókját. Hagyja figyelmen kívül a hubName tulajdonság a definícióban.

##### <a name="source-blob-storage-linked-service"></a>Forrás blob storage-beli társított szolgáltatás
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

##### <a name="destination-blob-storage-linked-service"></a>Cél blob storage-beli társított szolgáltatás

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

Azure Storage társított szolgáltatás kapcsolatos további információkért lásd: [társított szolgáltatások tulajdonságai](#linked-service-properties) szakaszban.

#### <a name="datasets"></a>Adathalmazok
Nincsenek a két adatkészletet: a bemeneti adatkészletek és a egy kimeneti adatkészletet. Az adatkészlet típusa értékre van állítva **AzureBlob** esetében is.

A bemeneti adatkészlet mutat a **bemeneti** mappában található a **adfblobconnector** blob-tárolóba. A **külső** tulajdonsága **igaz** ehhez az adatkészlethez, a másolási tevékenység, amely bemenetként az adatkészlet a folyamat nem hozzák az adatokat.

A kimeneti adatkészlet mutat a **kimeneti** ugyanazt a blob-tárolót, mappát. A kimeneti adatkészletet is használ, az év, hónap és napja a **SliceStart** rendszerváltozó dinamikusan értékelheti ki a kimeneti fájl elérési útját. Funkciók és a Data Factory által támogatott rendszerváltozók listáját lásd: [Data Factory-függvények és rendszerváltozók](data-factory-functions-variables.md). A **külső** tulajdonsága **hamis** (alapértelmezett érték), mert ez az adatkészlet a folyamat állítja elő.

Támogatja az Azure Blob-adatkészlet-tulajdonságokkal kapcsolatos további információkért lásd: [adatkészlet tulajdonságai](#dataset-properties) szakaszban.

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
A folyamat egyetlen tevékenységet tartalmaz. A **típus** a tevékenység értékre van állítva **másolási**. A tevékenység típusa tulajdonságai között nincsenek két szakasz, egyet a forrás és a többi fogadó. A forrástípus értéke **BlobSource** , a tevékenység adatokat másol egy blob storage-ból. A fogadó típusa **BlobSink** a tevékenység adatokat másol egy blob Storage-t. A másolási tevékenység bemeneti InputDataset-z4y vesz igénybe, és OutputDataset-z4y a kimenetként.

BlobSource és BlobSink által támogatott tulajdonságokkal kapcsolatos további információkért lásd: [másolási tevékenység tulajdonságai](#copy-activity-properties) szakaszban.

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

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>Adatok másolása Blob Storage szolgáltatásba vagy onnan a JSON-példák
Az alábbi példák megadják példa JSON-definíciók, amelyek segítségével létrehoz egy folyamatot használatával [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell-lel](data-factory-copy-activity-tutorial-using-powershell.md). Adatok másolása az Azure Blob Storage és az Azure SQL Database mutatnak. Azonban az adatok átmásolhatók **közvetlenül** bármelyik források a conditions stated above fogadóként valamelyik [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenységgel az Azure Data Factoryban.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>JSON-példa: Adatok másolása Blob Storage-ból SQL-adatbázis
Az alábbi mintában látható:

1. A társított szolgáltatás típusa [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](#linked-service-properties).
3. Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](#dataset-properties).
4. Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. A [folyamat](data-factory-create-pipelines.md) egy másolási tevékenységgel, amely használja [BlobSource](#copy-activity-properties) és [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties).

A minta másolatokat idősorozat-adatokat egy Azure-blobból egy Azure SQL table óránként történik. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

**Az Azure SQL társított szolgáltatást:**

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
**Az Azure Storage társított szolgáltatást:**

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
Az Azure Data Factory az Azure Storage-beli társított szolgáltatások két típusát támogatja: **AzureStorage** és **AzureStorageSas**. Az első címtárra, adja meg a kapcsolati karakterláncot, amely tartalmazza a kulcsát, és az újabb már, adja meg a közös hozzáférésű Jogosultságkód (SAS) URI-t. Lásd: [társított szolgáltatások](#linked-service-properties) című szakasz részletezi.

**Azure blobbemeneti adatkészlet:**

Adatok felülettől új blob minden órában (frequency: óra, időköz: 1). A mappa elérési útját és nevét a BLOB dinamikusan a feldolgozás alatt álló szelet kezdő időpontja alapján értékeli ki. A mappa elérési útjának év, hónap és nap részét a kezdési időpont és fájlnevet a kezdő időpontja óra részét használja. "external": "true" beállítással, hogy a tábla a data factory a külső, és nem egy adat-előállító tevékenység által előállított tájékoztatja a Data Factory.

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
**Az Azure SQL kimeneti adatkészlete:**

A minta adatokat másol egy táblához "MyTable" nevű egy Azure SQL database-ben. A tábla létrehozása az Azure SQL database, az azonos számú oszlopot az a Blob CSV-fájl tartalmazza a várt módon. Új sorok hozzáadódnak a tábla minden órában.

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
**Fogadó-Blob-forrás- és SQL rendelkező folyamatot egy másolási tevékenységgel:**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **BlobSource** és **fogadó** típusa **SqlSink**.

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
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>JSON-példa: Adatok másolása az Azure SQL az Azure Blob
Az alábbi mintában látható:

1. A társított szolgáltatás típusa [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](#linked-service-properties).
3. Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](#dataset-properties).
5. A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) és [BlobSink](#copy-activity-properties).

A minta idősorozat-adatokat másol egy Azure SQL-táblát az Azure-blobba óránként. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

**Az Azure SQL társított szolgáltatást:**

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
**Az Azure Storage társított szolgáltatást:**

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
Az Azure Data Factory az Azure Storage-beli társított szolgáltatások két típusát támogatja: **AzureStorage** és **AzureStorageSas**. Az első címtárra, adja meg a kapcsolati karakterláncot, amely tartalmazza a kulcsát, és az újabb már, adja meg a közös hozzáférésű Jogosultságkód (SAS) URI-t. Lásd: [társított szolgáltatások](#linked-service-properties) című szakasz részletezi.

**Az Azure SQL bemeneti adatkészlet:**

A minta azt feltételezi, létrehozott egy táblát "MyTable" az Azure SQL-ben és a egy idősorozat-adatok a "timestampcolumn" nevű oszlopot tartalmaz.

Beállítás az "external": "true" tájékoztatja a Data Factory szolgáltatásban, hogy a tábla a data factory a külső, és nem hozzák az adat-előállító adott tevékenységéhez.

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

**Azure blobkimeneti adatkészlet:**

Adatokat írt egy új blob minden órában (frequency: óra, időköz: 1). A mappa elérési útját a BLOB a feldolgozás alatt álló szelet kezdő időpontja alapján dinamikusan kiértékeli. A mappa elérési útját használja, év, hónap, nap és óra részei a kezdési időpontot.

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
          "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
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

**Az SQL-forrás és fogadó Blob egy folyamatot egy másolási tevékenységgel:**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **SqlSource** és **fogadó** típusa **BlobSink**. A megadott SQL-lekérdezést a **SqlReaderQuery** tulajdonság kiválasztja az adatokat másolni az elmúlt órában.

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
> Fogadó-adatkészlet az oszlopok a forrásadatkészlet oszlopok leképezésére, lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Lásd: [másolási tevékenységek teljesítményéhez és teljesítményhangolási útmutatóból](data-factory-copy-activity-performance.md) megismerheti a kulcsfontosságú szerepet játszik az adatáthelyezés (másolási tevékenység) az Azure Data Factory és a különféle módokon optimalizálhatja azt, hogy hatással lehet a teljesítményre.

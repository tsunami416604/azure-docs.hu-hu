---
title: Adatok másolása az Azure Blob Storage-ba/onnan
description: 'Ismerje meg, hogyan másolhatja a blobadatokat az Azure Data Factoryban. Használja a minta: Hogyan másolhatja az adatokat, és az Azure Blob Storage és az Azure SQL Database.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: eab332f102b9e39981e2d8ed6e84f73fada87a1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282132"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Adatok másolása az Azure Blob Storage-ba vagy onnan az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-azure-blob-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-azure-blob-storage.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [Azure Blob Storage-összekötő a V2-ben című témakört.](../connector-azure-blob-storage.md)


Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása az Azure Blob Storage-ba és onnan. Az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkre épül, amely általános áttekintést nyújt az adatmozgásról a másolási tevékenységgel.

## <a name="overview"></a>Áttekintés
Bármely támogatott forrásadattárból adatokat másolhat az Azure Blob Storage-ba vagy az Azure Blob Storage bármely támogatott fogadó adattárba. Az alábbi táblázat a másolási tevékenység által forrásként vagy fogadóként támogatott adattárolók listáját tartalmazza. Például áthelyezheti **from** az adatokat egy SQL Server-adatbázisból vagy egy Azure SQL-adatbázisból egy Azure blob **storage-ba.** Az Azure blob **storage-ból** adatokat másolhat **egy** Azure SQL Data Warehouse vagy egy Azure Cosmos DB-gyűjteménybe.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Támogatott esetek
Az Azure **Blob Storage-ból** adatokat másolhat a következő adattárakba:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

A következő adattárakból másolhat adatokat **az Azure Blob Storage-ba:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!IMPORTANT]
> A Másolási tevékenység támogatja az adatok másolását/át másolását az általános célú Azure Storage-fiókokból és a Hot/Cool Blob storage-ba. A tevékenység támogatja a **blokk- és hozzáfűző vagy lapblobok olvasását,** de csak **a blokkblobok írását.** Az Azure Premium Storage nem támogatott fogadóként, mert lapblobok támogatják.
>
> A Másolási tevékenység nem törli az adatokat a forrásból, miután az adatokat sikeresen átmásolta a célhelyre. Ha egy sikeres másolás után törölnie kell a forrásadatokat, hozzon létre egy [egyéni tevékenységet](data-factory-use-custom-activities.md) az adatok törléséhez és a folyamatban lévő tevékenység használatához. Például tekintse meg a [Blob vagy mappa törlése mintát a GitHubon.](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity)

## <a name="get-started"></a>Bevezetés
Létrehozhat egy folyamatot egy másolási tevékenységgel, amely az adatokat áthelyezi/onnan egy Azure Blob Storage-ba különböző eszközök/API-k használatával.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló**használata. Ez a cikk egy [forgatókönyv et](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) hoz létre egy folyamat ot az Azure Blob Storage-helyről egy másik Azure Blob Storage-helyre. Az Azure Blob Storage-ból az Azure SQL Database-be adatok másolására szolgáló folyamat létrehozásáról az [Oktatóanyag: Folyamat létrehozása a Másolás varázslóval](data-factory-copy-data-wizard-tutorial.md)című témakörben található.

A következő eszközökkel is létrehozhat egy folyamatot: **Visual Studio,** **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**és REST **API.** Lásd: [Tevékenység-oktatóanyag másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című témakörben részletes útmutatást talál egy másolási tevékenységgel rendelkező folyamat létrehozásához.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépéseket hajthatja végre egy olyan folyamat létrehozásához, amely adatokat helyezi át a forrásadattárból a fogadó adattárába:

1. Hozzon létre egy **adat-előállító**. Az adat-előállító egy vagy több folyamatot tartalmazhat.
2. **Összekapcsolt szolgáltatások** létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására. Ha például adatokat másol egy Azure blob storage-ból egy Azure SQL-adatbázisba, két összekapcsolt szolgáltatást hoz létre az Azure storage-fiók és az Azure SQL-adatbázis és az adat-előállító összekapcsolására. Az Azure Blob Storage-ra jellemző kapcsolt szolgáltatástulajdonságokról lásd: [csatolt szolgáltatástulajdonságok](#linked-service-properties) szakasz.
2. **Adatkészletek** létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására. Az utolsó lépésben említett példában hozzon létre egy adatkészletet a blob tároló és a bemeneti adatokat tartalmazó mappa megadásához. És hozzon létre egy másik adatkészletet az SQL-tábla megadásához az Azure SQL-adatbázisban, amely a blob storage-ból másolt adatokat tartalmazza. Az Azure Blob Storage-ra jellemző adatkészlet-tulajdonságokat lásd: [adatkészlet tulajdonságai](#dataset-properties) szakasz.
3. Hozzon létre egy **folyamatot** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, egy adatkészletet pedig kimenetként vesz fel. A korábban említett példában a BlobSource-ot forrásként, az SqlSink-et pedig a másolási tevékenység fogadójaként használja. Hasonlóképpen, ha az Azure SQL Database-ből az Azure Blob Storage-ba másolja, az SqlSource és a BlobSink a másolási tevékenység. Az Azure Blob Storage-ra jellemző másolási tevékenységtulajdonságokról a [Másolási tevékenység tulajdonságai](#copy-activity-properties) című szakaszban található. Az adattár forrásként vagy fogadóként való használatáról az adattár előző szakaszában található hivatkozásra kattintva.

A varázsló használatakor a Data Factory entitásokhoz (csatolt szolgáltatások, adatkészletek és a folyamat) json-definíciók automatikusan létrejönnek. Eszközök/API-k használatakor (a .NET API kivételével) ezeket a Data Factory entitásokat a JSON formátum használatával definiálhatja.  Az Azure Blob Storage-ba vagy onnan történő adatok másolásához használt Data Factory-entitások JSON-definícióival rendelkező mintákat lásd: A cikk [JSON-példái](#json-examples-for-copying-data-to-and-from-blob-storage  ) című részében.

A következő szakaszok részletesen ismertetik a JSON-tulajdonságokat, amelyek az Azure Blob Storage-ra jellemző Data Factory entitások definiálására szolgálnak.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai
Az Azure Storage-t kétféle összekapcsolt szolgáltatással kapcsolhatja össze egy Azure-adat-előállítóval. Ezek a következők: **AzureStorage-kapcsolt** szolgáltatás és **AzureStorageSas** kapcsolt szolgáltatás. Az Azure Storage-hoz kapcsolódó szolgáltatás biztosítja az adatgyár globális hozzáférést biztosít az Azure Storage-hoz. Mivel az Azure Storage SAS (megosztott hozzáférésű aláírás) összekapcsolt szolgáltatás biztosítja az adatgyár korlátozott/időhöz kötött hozzáférést az Azure Storage-hoz. Nincs más különbség a két összekapcsolt szolgáltatás között. Válassza ki az igényeinek megfelelő összekapcsolt szolgáltatást. A következő szakaszok további részleteket tartalmaznak a két összekapcsolt szolgáltatásról.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Ha egy Azure Blob Storage-ban bemeneti vagy kimeneti adatokat jelölő adatkészletet szeretne megadni, állítsa be az adatkészlet típustulajdonságát a következőre: **AzureBlob**. Állítsa be az adatkészlet **linkedServiceName** tulajdonságát az Azure Storage vagy az Azure Storage SAS csatolt szolgáltatás nevére.  Az adatkészlet típustulajdonságai határozzák meg a **blobtárolót** és a **blobstorage-tárolóban lévő mappát.**

A JSON-szakaszok teljes listáját & adatkészletek definiálására elérhető tulajdonságokat az [Adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben. A json-i adatkészletek például a struktúra, a rendelkezésre állás és a szabályzat hasonlóak az összes adatkészlettípushoz (Azure SQL, Azure blob, Azure table stb.).

A Data factory a következő CLS-kompatibilis .NET alapú típusértékeket támogatja a séma olvasásra készült adatforrások , például az Azure blob típusadatainak megadásához: Int16, Int32, Int64, Single, Double, Decimal, Byte[], Bool, String, Guid, Datetime, Datetimeoffset, Timespan. A Data Factory automatikusan szövegkonverziókat hajt végre, amikor adatokat helyez át a forrásadattárból a fogadó adattárba.

A **typeProperties** szakasz az adatkészlet egyes típusainál eltérő, és tájékoztatást nyújt az adattárban lévő adatok helyéről, formátumáról stb. **Az AzureBlob** típusú adatkészlet typeProperties szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| folderPath |A blob storage-ban lévő tároló és mappa elérési útja. Példa: myblobcontainer\myblobfolder\ |Igen |
| fileName |A blob neve. a fájlnév megadása nem kötelező, és a kis- és nagybetűket is ki nem szokja.<br/><br/>Ha megad egy fájlnevet, a tevékenység (beleértve a másolást is) az adott Blobon működik.<br/><br/>Ha a fájlnév nincs megadva, a Másolás a folderPath a bemeneti adatkészlethez tartalmazza az összes blobot.<br/><br/>Ha a **fájlnév** nincs megadva egy kimeneti adatkészlethez, és a **preserveHierarchy** nincs megadva a tevékenységfogadóban, a létrehozott fájl neve a következő formátumban lesz: `Data.<Guid>.txt` (például: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nem |
| particionáltby |partitionedBy egy választható tulajdonság. Segítségével dinamikus mappátadhat megPath és fájlnév az idősorozat-adatokhoz. Például a folderPath paraméterezhető az adatok minden órájára. A részleteket és példákat a [PartitionedBy tulajdonság használata című szakaszban](#using-partitionedby-property) találja. |Nem |
| Formátum | A következő formátumtípusok támogatottak: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa a formátum alatti **típustulajdonságot** ezen értékek egyikére. További információt a [Szövegformátum,](data-factory-supported-file-and-compression-formats.md#text-format) [a Json formátum,](data-factory-supported-file-and-compression-formats.md#json-format) [az Avro formátum,](data-factory-supported-file-and-compression-formats.md#avro-format) [az Orc Formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és a [Parkettaformátum](data-factory-supported-file-and-compression-formats.md#parquet-format) című szakaszban talál. <br><br> Ha a fájlokat fájlalapú tárolók között (bináris másolat) szeretné **másolni,** hagyja ki a formátum szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is. |Nem |
| tömörítés | Adja meg az adatok tömörítésének típusát és szintjét. A támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**és **ZipDeflate**. Támogatott szintek: **Optimális** és **leggyorsabb**. További információt a [Fájl- és tömörítési formátumok az Azure Data Factoryban című témakörben talál.](data-factory-supported-file-and-compression-formats.md#compression-support) |Nem |

### <a name="using-partitionedby-property"></a>PartitionedBy tulajdonság használata
Ahogy az előző szakaszban is említettük, dinamikus folderPath és fájlnév adható meg az idősorozat-adatokhoz a **partitionedBy** tulajdonsággal, a [Data Factory függvényekkel és a rendszerváltozókkal.](data-factory-functions-variables.md)

Az idősorozat-adatkészletekről, az ütemezésről és a szeletekről az [Adatkészletek létrehozása](data-factory-create-datasets.md) és [& végrehajtási](data-factory-scheduling-and-execution.md) cikkek című témakörben talál további információt.

#### <a name="sample-1"></a>1. példa

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Ebben a példában a ({Slice} ) a megadott formátumban (YYYYMMDDHH) lecseréli a SliceStart Data Factory rendszerváltozó értékére. A SliceStart a szelet kezdési időpontjára utal. A folderPath minden szeletesetében más. Például: wikidatagateway/wikisampledataout/2014100103 vagy wikidatagateway/wikisampledataout/2014100104

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

Ebben a példában a SliceStart évét, hónapját, napját és idejét a folderPath és a fileName tulajdonságai által használt különálló változókba bontják ki.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálására rendelkezésre álló szakaszok & tulajdonságok teljes listáját a [Folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben olvashat. Tulajdonságok, például név, leírás, bemeneti és kimeneti adatkészletek és házirendek állnak rendelkezésre minden típusú tevékenységek. Mivel a tevékenység **typeProperties** szakaszában elérhető tulajdonságok az egyes tevékenységtípusoktól függően változnak. Másolási tevékenység esetén a források és a fogadók típusától függően változnak. Ha adatokat helyez át egy Azure Blob Storage-ból, a másolási tevékenység forrástípusát **a BlobSource**mezőre állítja be. Hasonlóképpen, ha adatokat helyez át egy Azure Blob Storage-ba, a copy tevékenység fogadótípusát **blobsink-re állítja**be. Ez a szakasz a BlobSource és a BlobSink által támogatott tulajdonságok listáját tartalmazza.

**A BlobSource** a következő tulajdonságokat támogatja a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| Rekurzív |Azt jelzi, hogy az adatok olvasása rekurzív an- vagy csak a megadott mappából történik. |Igaz (alapértelmezett érték), Hamis |Nem |

**A BlobSink** a következő **tulajdonságokat támogatjaTulajdonságok** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| copyBehavior (Másként) |A másolási viselkedést határozza meg, ha a forrás BlobSource vagy FileSystem. |<b>Megőrzés:</b>megőrzi a fájlhierarchiát a célmappában. A forrásfájl forrásmappához viszonyított elérési útja megegyezik a célfájl célmappához való relatív elérési útjával.<br/><br/><b>Összeolvasztás:</b>a forrásmappából származó összes fájl a célmappa első szintjén található. A célfájlok automatikusan létrehozott névvel rendelkeznek. <br/><br/><b>MergeFiles</b>: a forrásmappából származó összes fájlt egyetlen fájlba egyesíti. Ha a Fájl/Blob név meg van adva, az egyesített fájlnév lesz a megadott név; ellenkező esetben lenne automatikusan generált fájlnevét. |Nem |

**A BlobSource** ezt a két tulajdonságot is támogatja a visszamenőleges kompatibilitás érdekében.

* **treatEmptyAsNull**: Megadja, hogy a null vagy az üres karakterláncot null értékként kezelje-e.
* **skipHeaderLineCount** - Itt adhatja meg, hogy hány sort kell kihagyni. Csak akkor alkalmazható, ha a bemeneti adatkészlet a TextFormat-ot használja.

Hasonlóképpen **a BlobSink** támogatja a következő tulajdonságot a visszamenőleges kompatibilitás érdekében.

* **blobWriterAddHeader**: Megadja, hogy a kimeneti adatkészletbe írás közben hozzáadjon-e oszlopdefiníciók fejlécét.

Az adatkészletek mostantól a következő tulajdonságokat támogatják, amelyek ugyanazt a funkciót valósítják meg: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

Az alábbi táblázat útmutatást nyújt az új adatkészlet-tulajdonságok használatával kapcsolatban ezek a blob forrás/fogadó tulajdonságok.

| Tevékenység másolása tulajdonság | Adatkészlet tulajdonság |
|:--- |:--- |
| skipHeaderLineCount a BlobSource-on |skipLineCount és firstRowAsHeader. A sorokat először kihagyja, majd az első sort fejlécként olvassa fel. |
| treatEmptyAsNull a BlobSource-on |treatEmptyAsNull beviteli adatkészleten |
| blobWriterAddHeader a BlobSink en |firstRowAsHeader a kimeneti adatkészleten |

Ezekről a tulajdonságokról a [Szövegformátum megadása](data-factory-supported-file-and-compression-formats.md#text-format) című szakaszban talál részletes információt.

### <a name="recursive-and-copybehavior-examples"></a>rekurzív és copyBehavior példák
Ez a szakasz a Copy művelet viselkedését ismerteti a rekurzív és copyBehavior értékek különböző kombinációihoz.

| Rekurzív | copyBehavior (Másként) | Eredményül kapott viselkedés |
| --- | --- | --- |
| igaz |megőrzési hierarchia |A Mappa1 forrásmappa a következő struktúrával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5<br/><br/>a mappamappa1 ugyanazzal a struktúrával jön létre, mint a forrás<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5. |
| igaz |összeolvasztási hierarchia |A Mappa1 forrásmappa a következő struktúrával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5<br/><br/>a célmappa1 a következő struktúrával jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl automatikusan létrehozott neve1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott név a File2 fájlhoz<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl 3 automatikusan létrehozott neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl4 automatikusan létrehozott neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott név a File5 fájlhoz |
| igaz |mergeFiles |A Mappa1 forrásmappa a következő struktúrával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5<br/><br/>a célmappa1 a következő struktúrával jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 tartalmát egyesítik egy fájlautomatikusan generált fájl neve |
| hamis |megőrzési hierarchia |A Mappa1 forrásmappa a következő struktúrával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5<br/><br/>a mappamappa1 a következő struktúrával jön létre<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/><br/><br/>A File3, File4 és File5 almappát a rendszer nem veszi fel. |
| hamis |összeolvasztási hierarchia |A Mappa1 forrásmappa a következő struktúrával:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5<br/><br/>a mappamappa1 a következő struktúrával jön létre<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl automatikusan létrehozott neve1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott név a File2 fájlhoz<br/><br/><br/>A File3, File4 és File5 almappát a rendszer nem veszi fel. |
| hamis |mergeFiles |A Mappa1 forrásmappa a következő struktúrával:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5<br/><br/>a mappamappa1 a következő struktúrával jön létre<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;A Fájl1 + File2 tartalom egyetlen, automatikusan generált fájlnévvel rendelkező fájlba egyesül. fájl automatikusan létrehozott neve1<br/><br/>A File3, File4 és File5 almappát a rendszer nem veszi fel. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Forgatókönyv: Adatok másolása a Blob Storage-ba/blobtárolóból a Másolás varázslóval
Nézzük meg, hogyan lehet gyorsan másolni az adatokat, vagy egy Azure blob storage. Ebben a forgatókönyvben az Azure Blob Storage típusú forrás- és céladat-tárolók is. A forgatókönyvben lévő folyamat adatokat másol egy mappából egy másik mappába ugyanabban a blobtárolóban. Ez a forgatókönyv szándékosan egyszerű a beállítások vagy tulajdonságok megjelenítése a Blob Storage forrásként vagy fogadóként való használatakor.

### <a name="prerequisites"></a>Előfeltételek
1. Hozzon létre egy általános célú **Azure Storage-fiókot,** ha még nem rendelkezik ilyen. Ebben a forgatókönyvben a blobstorage-t **forrás-** és **céladattárként** is használhatja. ha nem rendelkezik Azure-tárfiókkal, tekintse meg a [tárfiók létrehozása](../../storage/common/storage-account-create.md) cikket a létrehozási lépéseket.
2. Hozzon létre egy **adfblobconnector** nevű blobtárolót a tárfiókban.
4. Hozzon létre egy **bemenetnevű** mappát az **adfblobconnector** tárolóban.
5. Hozzon létre egy **emp.txt** nevű fájlt a következő tartalommal, és töltse fel a **bemeneti** mappába olyan eszközökkel, mint az [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```

### <a name="create-the-data-factory"></a>Az adat-előállító létrehozása
1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Kattintson az **Erőforrás létrehozása** a bal felső sarokban, majd az **Intelligencia + elemzés**elemre, majd a Data **Factory**parancsra.
3. Az **Új adatgyár** ablaktáblában:  
    1. Adja meg az **ADFBlobConnectorDF** nevet a **nevéhez.** Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő `*Data factory name “ADFBlobConnectorDF” is not available`hibaüzenet jelenik meg: , módosítsa az adatgyár nevét (például a yournameADFBlobConnectorDF), és próbálkozzon újra a létrehozással. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.
    2. Válassza ki **Azure-előfizetését.**
    3. Az Erőforráscsoport csoportban válassza a **Meglévő használata** lehetőséget egy meglévő erőforráscsoport kiválasztásához (vagy) válassza az Új **létrehozása lehetőséget** az erőforráscsoport nevének megadásához.
    4. Válassza ki a Data Factory **helyét**.
    5. A panel alján jelölje be a **Pin to dashboard** (Rögzítés az irányítópulton) jelölőnégyzetet.
    6. Kattintson **a Létrehozás gombra.**
3. A létrehozás befejezése után megjelenik a **Data Factory** panel ![az alábbi képen látható módon: Data factory home page](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Másolás varázsló
1. A Data Factory kezdőlapján kattintson az **Adatmásolás** csempére az **Adatok másolása varázsló** külön lapon való elindításához.  

    > [!NOTE]
    > Ha úgy látja, hogy a webböngésző beragadt a "Engedélyezés..." beállításnál, tiltsa le/törölje a jelet **a Harmadik féltől származó cookie-k és a webhelyadat-beállítás blokkolása** (vagy) engedélyezve van, és hozzon létre egy kivételt **login.microsoftonline.com,** majd próbálja meg újra elindítani a varázslót.
2. A **Properties** (Tulajdonságok) oldalon:
    1. Adja meg a **Folyamatmásolat** nevet a **Feladat nevéhez.** A feladat neve az adat-előállító folyamatneve.
    2. Adja meg a feladat **leírását** (nem kötelező).
    3. A **Tevékenység lépésszám vagy a Tevékenység ütemezése**esetén tartsa meg a **Futtatás ütemezés szerint** beállítást. Ha ezt a feladatot csak egyszer szeretné futtatni, nem pedig többször futtatni az ütemezés szerint, válassza **a Futtatás most**lehetőséget. Ha a Futtatás most lehetőséget **választja,** létrejön egy [egyszeri folyamat.](data-factory-create-pipelines.md#onetime-pipeline)
    4. Tartsa meg az **Ismétlődő minta beállításait.** Ez a feladat naponta fut a következő lépésben megadott kezdési és befejezési időpontok között.
    5. Módosítsa a **kezdési dátum időpontját** **2017.04.21-re.**
    6. Módosítsa a **befejezési dátum időpontját** **2017.04.25-re.** Előfordulhat, hogy a naptár böngészése helyett be szeretné írni a dátumot.
    8. Kattintson a **Tovább** gombra.
        ![Copy (Másolás) eszköz – Properties (Tulajdonságok) oldal](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png)
3. A **Source data store** (Forrásadattár) oldalon kattintson az **Azure Blob Storage** csempére. Az oldal használatával megadhatja a forrásadattárat a másolási feladathoz. Használhatja egy meglévő adattár társított szolgáltatását, vagy megadhat egy új adattárat. Meglévő csatolt szolgáltatás használatához válassza ki **a MEGLÉVŐ CSATOLT SZOLGÁLTATÁSOKBÓL** lehetőséget, és válassza ki a megfelelő csatolt szolgáltatást.
    ![Copy (Másolás) eszköz – Source data store (Forrásadattár) oldal](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. A **Specify the Azure Blob storage account** (Az Azure Blob Storage-fiók megadása) oldalon:
    1. Tartsa meg a kapcsolat **nevének**automatikusan létrehozott nevét. A kapcsolat neve a kapcsolódó szolgáltatás neve: Azure Storage.
    2. Győződjön meg arról, hogy az **Account selection method** (Fiókválasztási módszer) mezőben a **From Azure subscriptions** (Azure-előfizetésekből) lehetőség van kiválasztva.
    3. Válassza ki Azure-előfizetését, vagy tartsa meg az **Összes** **kiválasztása Azure-előfizetéshez**lehetőséget.
    4. A kiválasztott előfizetéshez elérhető Azure Storage-fiókok listájából válasszon ki egy **Azure Storage-fiókot**. A fiókkijelölési módszer **Kézi bevitele** lehetőség kiválasztásával manuálisan is megadhatja a **tárfiók beállításait.**
    5. Kattintson a **Tovább** gombra.  
        ![Copy (Másolás) eszköz – Specify the Azure Blob storage account (Az Azure Blob Storage-fiók megadása) oldal](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. A **Choose the input file or folder** (A bemeneti fájl vagy mappa kiválasztása) oldalon:
    1. Kattintson duplán **az adfblobcontainer ikonra.**
    2. Válassza a **bemeneti**lehetőséget, majd kattintson **a Választás gombra.** Ebben a forgatókönyvben kijelölheti a beviteli mappát. Az emp.txt fájlt is kijelölheti a mappában.
        ![Copy (Másolás) eszköz – Choose the input file or folder (A bemeneti fájl vagy mappa kiválasztása) oldal](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. A **Beviteli fájl vagy mappa kiválasztása** lapon:
    1. Ellenőrizze, hogy a fájl vagy mappa **adfblobconnector/input .confirm**that **the file or folder** is set to adfblobconnector/input . Ha a fájlok almappákban találhatók, például 2017/04/01, 2017/04/02 és így tovább, adja meg az adfblobconnector/input/{year}/{month}/{day} parancsot a fájlhoz vagy mappához. Amikor kinyomja a TAB billentyűt a szövegdobozból, három legördülő lista jelenik meg az év (ééééé), hónap (MM) és nap (dd) formátumainak kiválasztásához.
    2. Ne állítsa be **rekurzívmódon a Fájl másolása parancsot**. Akkor válassza ezt a lehetőséget, ha rekurzívan be szeretné kelni a mappákat a célhelyre másolandó fájlokhoz.
    3. Ne a **bináris másolási opciót.** Akkor válassza ezt a lehetőséget, ha a forrásfájl bináris másolatát szeretné végrehajtani a célhelyre. Ne jelölje ki ezt a forgatókönyvet, hogy a következő oldalakon további beállítások jelenjenek meg.
    4. Ellenőrizze, hogy a **Tömörítés típus** nincs.Confirm that the Compression type is set to **None**. Ha a forrásfájlok tömörítése a támogatott formátumok egyikében történik, válasszon ehhez a beállításhoz egy értéket.
    5. Kattintson a **Tovább** gombra.
    ![Copy (Másolás) eszköz – Choose the input file or folder (A bemeneti fájl vagy mappa kiválasztása) oldal](./media/data-factory-azure-blob-connector/chose-input-file-folder.png)
7. A **File format settings** (Fájlformátum beállításai) oldalon a fájl elemzése során a varázsló által automatikusan észlelt elválasztó karakterek és séma láthatók.
    1. Erősítse meg a következő beállításokat:  
        a. A **fájlformátum** **Szöveg formátumra**van állítva. A legördülő listában az összes támogatott formátum látható. Például: JSON, Avro, ORC, Parquet.
       b. Az **oszlophatároló** beállítása `Comma (,)`. A Legördülő listában a Data Factory által támogatott többi oszlophatároló látható. Egyéni határolószert is megadhat.
       c. A **sorhatároló** beállítása `Carriage Return + Line feed (\r\n)`. A Legördülő listában a Data Factory által támogatott másik sorhatároló látható. Egyéni határolószert is megadhat.
       d. Az **átugrási sorok száma** **0-ra**van állítva. Ha azt szeretné, hogy néhány sor kimaradjon a fájl tetejére, itt adja meg a számot.
       e. Az **első adatsor oszlopneveket tartalmaz,** nincs beállítva. Ha a forrásfájlok az első sorban oszlopneveket tartalmaznak, jelölje be ezt a beállítást.
       f. Az **üres oszlopérték null beállításként való kezelése** be van állítva.
    2. A **Speciális beállítások** elemre bontva megtekintheti a speciális beállításlehetőséget.
    3. A lap alján láthatja az emp.txt fájlból származó adatok **előnézetét.**
    4. Kattintson a **SCHEMA** fülre az alján a másoló varázsló által a forrásfájlban lévő adatok megtekintésével kikövetkeztetett séma megtekintéséhez.
    5. Az elválasztó karakterek megtekintése és adatok előzetes megtekintése után kattintson a **Next** (Tovább) gombra.
    ![Copy (Másolás eszköz) – File format settings (Fájlformátum beállításai) oldal](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)
8. A **Cél adattár lapon**válassza az **Azure Blob Storage**lehetőséget, majd kattintson a **Tovább**gombra. Ebben a forgatókönyvben az Azure Blob Storage-t használja forrás- és céladattárolóként is.  
    ![Copy Tool – céladattár kiválasztása](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. Az **Azure Blob tárfióklapjának megadása:**  
    1. Adja meg az **AzureStorageLinkedService szolgáltatást** a **Kapcsolat név** mezőjéhez.
    2. Győződjön meg arról, hogy az **Account selection method** (Fiókválasztási módszer) mezőben a **From Azure subscriptions** (Azure-előfizetésekből) lehetőség van kiválasztva.
    3. Válassza ki **Azure-előfizetését.**
    4. Válassza ki az Azure storage-fiókját.
    5. Kattintson a **Tovább** gombra.
10. A **Kimeneti fájl vagy mappa kiválasztása** lapon:  
    1. adja meg **a mappa elérési útját** **adfblobconnector/output/{year}/{month}/{day}** néven. Írja be **a TAB billentyűt**.
    1. Az **év**során válassza a **yyyy**lehetőséget.
    1. A **hónapban**ellenőrizze , hogy **mm-re van-e**állítva .
    1. A **nap**, ellenőrizze, hogy van beállítva , hogy **dd**.
    1. Ellenőrizze, hogy a **tömörítés típusa** **Nincs**.
    1. Ellenőrizze, hogy a **másolási viselkedés** a **Fájlok egyesítése**beállítására van-e állítva. Ha az azonos nevű kimeneti fájl már létezik, az új tartalom a végén ugyanahhoz a fájlhoz kerül.
    1. Kattintson a **Tovább** gombra.
       ![Copy Tool - Kimeneti fájl vagy mappa kiválasztása](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. A **Fájlformátum beállításai** lapon tekintse át a beállításokat, majd kattintson a **Tovább**gombra. Az egyik további lehetőség itt az, hogy egy fejlécet ad a kimeneti fájlhoz. Ha ezt a beállítást választja, a forrás sémájából származó oszlopok neveit tartalmazó fejlécsor kerül hozzáadásra. A forrás sémájának megtekintésekor átnevezheti az alapértelmezett oszlopneveket. Az első oszlopot például utónévre, a másodikoszlopot pedig Vezetéknév re módosíthatja. Ezután a kimeneti fájl egy fejléccel jön létre, amelynek ezeket a neveket oszlopnevekként kell létrehoznia.
    ![Copy Tool - Fájlformátum-beállítások a célhoz](media/data-factory-azure-blob-connector/file-format-destination.png)
12. A **Teljesítménybeállítások** lapon ellenőrizze, hogy a **felhőegységek** és **a párhuzamos másolatok** **automatikusra**vannak-e állítva, majd kattintson a Tovább gombra. Ezekről a beállításokról a [Tevékenység teljesítményének másolása és finomhangolása című témakörben olvashat részletesen.](data-factory-copy-activity-performance.md#parallel-copy)
    ![Copy Tool – Teljesítménybeállítások](media/data-factory-azure-blob-connector/copy-performance-settings.png)
14. Az **Összegzés** lapon tekintse át az összes beállítást (feladattulajdonságok, a forrás- és célbeállítások, valamint a másolási beállítások), majd kattintson a **Tovább**gombra.
    ![Vágóeszköz másolása – Összefoglaló lap](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Tekintse át a **Summary** (Összegzés) oldalon szereplő információkat, majd kattintson a **Finish** (Befejezés) gombra. A varázsló létrehoz két társított szolgáltatást, két adathalmazt (bemeneti és kimeneti), valamint egy folyamatot a data factoryban (ahonnét elindította a Másolás varázslót).
    ![Másolóeszköz – Telepítési lap](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>A folyamat figyelése (feladat másolása)

1. Kattintson `Click here to monitor copy pipeline` a **telepítési** lap hivatkozására.
2. A Figyelő és a **Kezelés alkalmazásnak** külön lapon kell lennie.  ![Alkalmazás figyelése és kezelése](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Módosítsa a **kezdő** időpontot `04/19/2017` a felülés a **befejezési** időpont ban, `04/27/2017`majd kattintson az Alkalmaz **gombra.**
4. A TEVÉKENYSÉG WINDOWS listában öt tevékenységablaknak kell **szerepelnie.** A **WindowStart-időknek** a folyamat kezdetétől a folyamat befejezési időszakaiig minden napot le kell fednie.
5. Kattintson a **Frissítés** gombra a **TEVÉKENYSÉG WINDOWS-listában** néhányszor, amíg meg nem jelenik, hogy az összes tevékenységablak állapota Készen áll.
6. Most ellenőrizze, hogy a kimeneti fájlok az adfblobconnector tároló kimeneti mappájában jönnek-e létre. A kimeneti mappában a következő mappastruktúrának kell láthatónak lennie:
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25
    ```
   Az adatgyárak figyeléséről és kezeléséről a [Data Factory folyamatcikkének figyelése és kezelése](data-factory-monitor-manage-app.md) című témakörben olvashat részletesen.

### <a name="data-factory-entities"></a>Adatfeldolgozó entitások
Most váltson vissza a Data Factory kezdőlapjával rendelkező lapra. Figyelje meg, hogy két összekapcsolt szolgáltatás, két adatkészlet és egy folyamat van az adat-előállítóban.

![A Data Factory kezdőlapja entitásokkal](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Kattintson **a Szerző és üzembe helyezés** elemre a Data Factory Editor elindításához.

![A Data Factory szerkesztője](media/data-factory-azure-blob-connector/data-factory-editor.png)

Az adat-előállítódódásban a következő Data Factory-entitásokat kell látnia:

- Két összekapcsolt szolgáltatás. Az egyik a forrásnak, a másik a célállomásnak. Mindkét csatolt szolgáltatások hivatkoznak az azure storage-fiók ebben a forgatókönyvben.
- Két adatkészlet. Bemeneti adatkészlet és egy kimeneti adatkészlet. Ebben a forgatókönyvben mindkettő ugyanazt a blob-tárolót használja, de különböző mappákra (bemenetre és kimenetre) hivatkozik.
- Egy csővezeték. A folyamat tartalmaz egy másolási tevékenységet, amely egy blobforrást és egy blob-fogadót használ az Azure blob helyéről egy másik Azure blobhelyről egy másik Azure blobhelyre történő másolásához.

A következő szakaszok további információt nyújtanak ezekről az entitásokról.

#### <a name="linked-services"></a>Társított szolgáltatások
Meg kell jelennie két összekapcsolt szolgáltatások. Az egyik a forrásnak, a másik a célállomásnak. Ebben a forgatókönyvben mindkét definíció ugyanúgy néz ki, kivéve a neveket. A csatolt szolgáltatás **típusa** **az AzureStorage lesz**állítva. A csatolt szolgáltatásdefiníció legfontosabb tulajdonsága a **connectionString,** amelyet a Data Factory használ az Azure Storage-fiókhoz való futásidőben való csatlakozáshoz. Hagyja figyelmen kívül a hubName tulajdonságot a definícióban.

##### <a name="source-blob-storage-linked-service"></a>Forrásblob-tárolóhoz csatolt szolgáltatás
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

##### <a name="destination-blob-storage-linked-service"></a>Célblob-tárolóhoz csatolt szolgáltatás

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

Az Azure Storage-hoz csatolt szolgáltatásról további információt a [Csatolt szolgáltatás tulajdonságai](#linked-service-properties) szakaszban talál.

#### <a name="datasets"></a>Adathalmazok
Két adatkészlet létezik: egy bemeneti adatkészlet és egy kimeneti adatkészlet. Az adatkészlet típusa **azureblob** mindkét beállítással van beállítva.

A bemeneti adatkészlet az **adfblobconnector blobtároló** **bemeneti** mappájára mutat. A **külső** tulajdonság **értéke igaz** az adatkészlet, mivel az adatok nem a folyamat által létrehozott a másolási tevékenység, amely ezt az adatkészletet bemenetként veszi.

A kimeneti adatkészlet ugyanarra a blobtároló **kimeneti** mappájára mutat. A kimeneti adatkészlet a **SliceStart** rendszerváltozó évét, hónapját és napját is használja a kimeneti fájl elérési útjának dinamikus kiértékeléséhez. A Data Factory által támogatott függvények és rendszerváltozók listáját a [Data Factory függvényei és rendszerváltozói című témakörben található.](data-factory-functions-variables.md) A **külső** tulajdonság **értéke hamis** (alapértelmezett érték), mert ezt az adatkészletet a folyamat hozza létre.

Az Azure Blob-adatkészlet által támogatott tulajdonságokról az [Adatkészlet tulajdonságai](#dataset-properties) című szakaszban talál további információt.

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
A folyamat nak csak egy tevékenysége van. A tevékenység **típusa** **Másolás.** A tevékenység típustulajdonságaiban két szakasz található, az egyik a forrás, a másik a fogadó. A forrástípus **blobsource** beállítással rendelkezik, mivel a tevékenység adatokat másol egy blobstorage-ból. A fogadó típusa van beállítva **BlobSink,** mint a tevékenység adatok másolása egy blob storage.The sink type is set to BlobSink as the activity copying data to a blob storage. A másolási tevékenység az InputDataset-z4y-t bemeneti és OutputDataset-z4y bemeneti bemenetként veszi fel kimenetként.

A BlobSource és a BlobSink által támogatott tulajdonságokról a [Tevékenység tulajdonságainak másolása](#copy-activity-properties) című szakaszban talál további információt.

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

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>JSON-példák a Blob Storage-ba és a Blob Storage-ból történő adatmásoláshoz
Az alábbi példák minta JSON-definíciókat tartalmaznak, amelyek segítségével folyamatot hozhat létre a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy az [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával. Bemutatják, hogyan másolhat adatokat az Azure Blob Storage-ba és az Azure SQL Database-ből. Azonban az adatok **közvetlenül** másolhatók bármelyik forrásból bármelyik fogadók [az itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott az Azure Data Factory másolási tevékenység használatával.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>JSON-példa: Adatok másolása a Blob Storage-ból az SQL-adatbázisba
A következő minta a következőket mutatja:

1. [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties)típusú csatolt szolgáltatás.
2. [AzureStorage](#linked-service-properties)típusú kapcsolt szolgáltatás.
3. [AzureBlob](#dataset-properties)típusú bemeneti [adatkészlet.](data-factory-create-datasets.md)
4. [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
5. [BlobSource](#copy-activity-properties) és [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties)programot használó másolási tevékenységgel rendelkező [folyamat.](data-factory-create-pipelines.md)

A minta idősorozat-adatokat másol egy Azure blobból egy Azure SQL-táblába óránként. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

**Azure SQL-hez csatolt szolgáltatás:**

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
**Azure Storage-hoz kapcsolódó szolgáltatás:**

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
Az Azure Data Factory kétféle Azure Storage-kapcsolt szolgáltatást támogat: **az AzureStorage-t** és **az AzureStorageSas-t.** Az elsőhöz adja meg a fiókkulcsot tartalmazó kapcsolati karakterláncot, a későbbihez pedig a Megosztott hozzáférésű aláírás (SAS) Uri-t. A részleteket lásd a [Csatolt szolgáltatások](#linked-service-properties) című szakaszban.

**Azure Blob bemeneti adatkészlet:**

Az adatokat óránként veszi fel egy új blob (gyakoriság: óra, időköz: 1). A blob mappaelérési útja és fájlneve dinamikusan kiértékelésre kerül a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési időpont év, hónap és nap részét, a fájlnév pedig a kezdési időpont órarészét használja. "külső": az "igaz" beállítás tájékoztatja a Data Factory-t, hogy a tábla az adat-előállítón kívül található, és nem az adat-előállító tevékenység állítja elő.

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
**Azure SQL kimeneti adatkészlet:**

A minta adatokat másol egy "MyTable" nevű táblába egy Azure SQL-adatbázisban. Hozza létre a táblát az Azure SQL-adatbázisban ugyanannyi oszlopot, mint ablob CSV-fájl tartalmaz. A rendszer óránként új sorokat ad a táblához.

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
**Másolási tevékenység blobforrással és SQL-fogadóval rendelkező folyamatban:**

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránként i. A folyamat JSON-definíciójában a **forrástípus** **BlobSource** lesz állítva, **a fogadó** típusa pedig **SqlSink**.

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
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>JSON-példa: Adatok másolása az Azure SQL-ből az Azure Blobba
A következő minta a következőket mutatja:

1. [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties)típusú csatolt szolgáltatás.
2. [AzureStorage](#linked-service-properties)típusú kapcsolt szolgáltatás.
3. [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties)típusú bemeneti [adatkészlet.](data-factory-create-datasets.md)
4. [AzureBlob](#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
5. [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) és [BlobSink](#copy-activity-properties)programot használó másolási tevékenységgel rendelkező [folyamat.](data-factory-create-pipelines.md)

A minta idősorozat-adatokat másol egy Azure SQL-táblából egy Azure blobóránként. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

**Azure SQL-hez csatolt szolgáltatás:**

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
**Azure Storage-hoz kapcsolódó szolgáltatás:**

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
Az Azure Data Factory kétféle Azure Storage-kapcsolt szolgáltatást támogat: **az AzureStorage-t** és **az AzureStorageSas-t.** Az elsőhöz adja meg a fiókkulcsot tartalmazó kapcsolati karakterláncot, a későbbihez pedig a Megosztott hozzáférésű aláírás (SAS) Uri-t. A részleteket lásd a [Csatolt szolgáltatások](#linked-service-properties) című szakaszban.

**Azure SQL bemeneti adatkészlet:**

A minta feltételezi, hogy létrehozott egy "MyTable" táblát az Azure SQL-ben, és tartalmaz egy "timestampcolumn" nevű oszlopot az idősorozat-adatokhoz.

A "külső": "true" beállítás tájékoztatja a Data Factory szolgáltatást, hogy a tábla az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított.

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

**Azure Blob kimeneti adatkészlet:**

Az adatok óránként egy új blobba (gyakoriság: óra, időköz: 1) kerül beírásra. A blob mappaelérési útja dinamikusan kiértékelve a feldolgozás alatt álló szelet kezdési időpontja alapján történik. A mappa elérési útja a kezdési időpont év-, hónap-, nap- és órarészeit használja.

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

**SQL-forrással és Blob-fogadóval rendelkező folyamat másolási tevékenysége:**

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránként i. A folyamat JSON-definíciójában a **forrástípus** **sqlsource-ra** van állítva, **a fogadó** típusa pedig **BlobSink**. Az **SqlReaderQuery** tulajdonsághoz megadott SQL-lekérdezés kiválasztja a másolni kívánt adatokat az elmúlt órában.

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
> Ha oszlopokat szeretne leképezni a forrásadatkészletről a fogadó adatkészletoszlopaira, olvassa el [az Adatkészletoszlopok leképezése az Azure Data Factoryban című témakört.](data-factory-map-columns.md)

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [Tevékenység teljesítményének másolása & hangolási útmutatóban](data-factory-copy-activity-performance.md) megismerést talál az adatok (másolási tevékenység) azure Data Factory ban az adatmozgatás (másolási tevékenység) teljesítményét befolyásoló legfontosabb tényezőkről, valamint az optimalizálás különböző módjairól.

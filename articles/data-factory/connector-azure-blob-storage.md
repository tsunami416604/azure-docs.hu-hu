---
title: Másolja a adatok vagy az Azure Blob storage használatával a Data Factory |} Microsoft Docs
description: Ismerje meg az adatok másolása az Azure Blob storage támogatott forrás adattárolókhoz, vagy a Blob-tároló támogatott fogadó adattárolókhoz, adat-előállító használatával.
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 04/27/2018
ms.author: jingwang
ms.openlocfilehash: 1c214dc34361bea49ad00cb5dfab71a7e6855996
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Másolja a adatok vagy az Azure Blob storage Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-azure-blob-connector.md)
> * [2. verzió – Előzetes verzió](connector-azure-blob-storage.md)

Ez a cikk ismerteti, hogyan Azure Data Factory másolási tevékenység segítségével másolja az adatokat, és az Azure Blob Storage tárolóban. Buildekről nyújtanak a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory, amely általában a rendelkezésre álló, 1 verzióját használja [Blob storage-összekötőt a 1-es verziójú](v1/data-factory-azure-blob-connector.md).


## <a name="supported-capabilities"></a>Támogatott képességei

Bármely támogatott forrás adattár adatok átmásolhatja Blob Storage. Is másolhat adatokat a Blob storage bármely támogatott fogadó adattárolóhoz. A másolási tevékenység által támogatott adatforrások vagy mosdók adattárolókhoz listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md) tábla.

Pontosabban a Blob storage összekötő támogatja:

- Blobok másolása, és az általános célú Azure storage-fiókok és közbeni/ritkán blob Storage tárolóban. 
- BLOB másolása fiókkulcs és a szolgáltatás használatával megosztott hozzáférési aláírást hitelesítések.
- Blokkot, a másolási blobok hozzáfűzni, vagy a lapblobokat és adat másolása az csak a blokkblobokat. Prémium szintű Storage, a fogadó nem támogatott, mert a lapblobokat ezt támogatja.
- A BLOB másolása, vagy elemzés vagy generálása blobok [támogatott formátumok és a tömörítési kodek](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják a Blob storage Data Factory tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

### <a name="use-an-account-key"></a>Fiók-kulcsot használ

A fiók kulcs használatával létrehozhat egy tárolás társított szolgáltatása. Storage globális hozzáférést biztosít az adat-előállítóban. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot meg kell **AzureStorage**. |Igen |
| connectionString | Adja meg a connectionString tulajdonság tárolási való kapcsolódáshoz szükséges adatokat. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). |Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhat Azure integrációs futásidejű vagy Self-hosted integrációs futásidejű (ha az adattároló egy magánhálózathoz). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

**Példa**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-service-shared-access-signature-authentication"></a>Szolgáltatás megosztott hozzáférési aláírást hitelesítés használata

A tárolás társított szolgáltatásának a közös hozzáférésű jogosultságkód használatával hozhat létre. Az adat-előállítóban minden/specifikus erőforrások (blobtárolóban /) a tárolóban lévő korlátozott/időhöz kötött hozzáférést biztosít.

A közös hozzáférésű jogosultságkód a tárfiókban lévő erőforrások delegált hozzáférést biztosít. A közös hozzáférésű jogosultságkód segítségével engedélyezheti a ügyfél korlátozott engedélyekkel a tárfiókban lévő objektumok egy meghatározott ideig. A tárelérési kulcsok megosztásához nem rendelkezik. A közös hozzáférésű jogosultságkódot URI, amely a lekérdezési paraméterek magában foglalja a hitelesített hozzáférést a tároló egyik erőforrásához szükséges összes adatot. A közös hozzáférésű jogosultságkódot tárolási erőforrások eléréséhez az ügyfél csak a megfelelő konstruktort vagy metódust átadása az a közös hozzáférésű jogosultságkódot van szüksége. Megosztott hozzáférési aláírásokkal kapcsolatos további információkért lásd: [Shared access signatures: megérteni a megosztott hozzáférési aláírást modell](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!IMPORTANT]
> Adat-előállító mostantól támogatja a csak megosztott hozzáférési aláírásokkal, de nem a fiók megosztott hozzáférési aláírásokkal. További információ a kétféle típusú, és hogyan készítse őket: [közös hozzáférésű jogosultságkód típusú](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures). A közös hozzáférésű az Azure portálon vagy az Azure Tártallózó előállított aláírás URL-cím egy fiók közös hozzáférésű jogosultságkódot, amely nem támogatott.

> [!TIP]
> A szolgáltatás közös hozzáférésű jogosultságkód a tárfiók létrehozásához a következő PowerShell-parancsokat hajthat végre. Cserélje le a helyőrzőket, és adja meg a szükséges engedéllyel.
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Szolgáltatás megosztott hozzáférési aláírást hitelesítés használatához a következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot meg kell **AzureStorage**. |Igen |
| sasUri | Adja meg a közös hozzáférésű jogosultságkódot URI a tárolási erőforrások, például blob, -tároló vagy tábla. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). |Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhatja az Azure integrációs futásidejű vagy a Self-hosted integrációs futásidejű (ha az adattároló egy magánhálózaton található). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

**Példa**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Amikor létrehoz egy közös hozzáférésű jogosultságkódot URI, vegye figyelembe a következő szempontokat:

- A társított szolgáltatás (olvasási, írási, olvasás/írás) az adat-előállítóban felhasznált alapján objektumok megfelelő olvasási/írási engedélyeket.
- Állítsa be **lejárati idejének** megfelelően. Győződjön meg arról, hogy a nem az adatcsatorna aktív időszakára jár le a tárolási objektum elérésére.
- Az URI a igények alapján megfelelő tárolót vagy blobot, vagy tábla szinten kell létrehozni. A közös hozzáférésű jogosultságkód URI blob lehetővé teszi, hogy a Data Factory, hogy a blob eléréséhez. A közös hozzáférésű jogosultságkód URI egy Blob tároló lehetővé teszi, hogy a Data Factoryben az adott tárolóban lévő blobok iterációt. Hozzáférést biztosít több vagy kevesebb objektumok később, vagy a közös hozzáférésű jogosultságkódot URI, ne felejtse el frissíteni a társított szolgáltatás új URI-azonosítójú.

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a rész felsorolja a Blob-tároló adatkészlet által támogatott tulajdonságokról.

Adatok másolása a Blob storage érkező vagy oda irányuló, az adatkészlet típus tulajdonságának beállítása **AzureBlob**. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani **AzureBlob**. |Igen |
| folderPath | A tároló és a blob-tároló mappa elérési útja. Példa: myblobcontainer/myblobfolder /. |Igen |
| fileName | **Név vagy helyettesítő karakter szűrő** az alatt a megadott "folderPath" blob(s). Ha nem adja meg egy értéket ehhez a tulajdonsághoz a DataSet adatkészlet mutat, a mappában található összes BLOB. <br/><br/>Szűrő, az engedélyezett a helyettesítő karaktereket: `*` (több karaktert) és `?` (egyetlen karakter).<br/>-1. példa: `"fileName": "*.csv"`<br/>– 2. példa: `"fileName": "???20180427.txt"`<br/><br/>Ha nincs megadva fájlnév egy kimeneti adatkészlet és **preserveHierarchy** nincs meghatározva a tevékenység a fogadó, a másolási tevékenység során automatikusan létrehozza a blob neve a következő mintát: `Data.[activity run id GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]`. Példa: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". |Nem |
| Formátumban | Ha át kívánja másolni a fájlokat, a fájlalapú tárolók (bináris másolás) között van, hagyja ki a Formátum szakasz mind a bemeneti és kimeneti adatkészlet-definíciók.<br/><br/>Szeretne elemezni, vagy egy adott formátumú fájlok létrehozása, ha a következő fájl formátuma típusok támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**, **OrcFormat**, és **ParquetFormat**. Állítsa be a **típus** tulajdonság alapján **formátum** számára a következő értékek egyike. További információkért lásd: a [szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), [JSON formátumban](supported-file-formats-and-compression-codecs.md#json-format), [az Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátum](supported-file-formats-and-compression-codecs.md#orc-format), és [Parquet formátum](supported-file-formats-and-compression-codecs.md#parquet-format) szakaszok. |Nem (csak a bináris másolásának esetéhez) |
| Tömörítés | Adja meg a típus és az adatok tömörítése szintjét. További információkért lásd: [támogatott formátumok és a tömörítési kodek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Támogatott típusok a következők **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**.<br/>Támogatott szintek a következők **Optimal** és **leggyorsabb**. |Nem |

>[!TIP]
>Másolja az összes BLOB egy mappában, adja meg a **folderPath** csak.<br>Adja meg a megadott nevű egyetlen blob másolásához **folderPath** mappa megadó és **Fájlnév** fájlnévvel.<br>Másolja át egy blobot egy mappában részhalmazát, adja meg a **folderPath** mappa megadó és **Fájlnév** helyettesítő szűrővel. 

**Példa**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName": "myfile.csv.gz",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a rész felsorolja a Blob storage-forrás és a fogadó által támogatott tulajdonságokról.

### <a name="blob-storage-as-a-source-type"></a>A BLOB storage egy forrás típusa

Adatok másolása Blob-tároló, állítsa be a forrás típusa a másolási tevékenység **BlobSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani **BlobSource**. |Igen |
| Rekurzív | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappákat, illetve csak a megadott mappát. Ne feledje, hogy ha rekurzív értéke true, és a fogadó fájlalapú tároló, egy üres mappát vagy almappát nem másolta, vagy létrehozott, a fogadó.<br/>Két érték engedélyezett **igaz** (alapértelmezett) és **hamis**. | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="blob-storage-as-a-sink-type"></a>BLOB Storage tárolót a fogadó típusa

Adatok másolása Blob-tároló, állítsa be a fogadó típusa a másolási tevékenység **BlobSink**. A következő tulajdonságok támogatottak a **fogadó** szakasz.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó type tulajdonsága értékre kell állítani **BlobSink**. |Igen |
| copyBehavior | Meghatározza a másolási viselkedését, ha az adatforrás fájlok fájlalapú adattárolóból.<br/><br/>Engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: őrzi meg a fájl hierarchia a célmappában. A következő forrásfájl forrásmappához relatív elérési a relatív elérési út a cél-fájlját és a célmappa megegyezik.<br/><b>-FlattenHierarchy</b>: a forrásmappából a fájlok a célmappában első szintjén is. A fájlok céljaként automatikusan generált nevek rendelkezik. <br/><b>-Mergefiles típusú</b>: egy fájl összes fájlt a forrásmappából egyesíti. Ha a fájl vagy a blob neve meg van adva, az egyesített fájlnév a megadott név. Ellenkező esetben egy automatikusan létrehozott nevét. | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="some-recursive-and-copybehavior-examples"></a>Rekurzív és copyBehavior példák

Ez a szakasz ismerteti az eredményül kapott viselkedéstől rekurzív és copyBehavior kombinációk a másolási művelet.

| Rekurzív | copyBehavior | Forrás mappaszerkezet | Eredményül kapott cél |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappa mappa1 forrásaként azonos struktúrájú jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a cél az alábbi szerkezettel mappa1 jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File5 |
| true |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a cél az alábbi szerkezettel mappa1 jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + fájl3 + File4 + File5 tartalmát egy fájlba fájl automatikusan létrehozott névvel egyesítve lesznek. |
| false |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a tároló mappa mappa1 jön létre az alábbi szerkezettel: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Fájl3, File4 és File5 Subfolder1 nem felvételre. |
| false |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a tároló mappa mappa1 jön létre az alábbi szerkezettel: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/><br/>Fájl3, File4 és File5 Subfolder1 nem felvételre. |
| false |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappa mappa1 jön létre a következő struktúra<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Egy fájl egy automatikusan létrehozott fájlnévvel egyesített file1 + File2 tartalma. automatikusan létrehozott nevet a file1 kiszolgálón<br/><br/>Fájl3, File4 és File5 Subfolder1 nem felvételre. |

## <a name="next-steps"></a>További lépések
Források és mosdók adat-előállítóban másolási tevékenység által támogatott adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md##supported-data-stores-and-formats).

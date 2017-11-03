---
title: "Adatok másolása az Azure Blob Storage használatával a Data Factory |} Microsoft Docs"
description: "Tudnivalók a támogatott forráshierarchiából adatokat tároló Azure Blob Storage (vagy) a Blob Storage-adatok másolása Data Factory használatával támogatott fogadó adattárolókhoz."
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 10/13/2017
ms.author: jingwang
ms.openlocfilehash: c0fd6db570a91eeb1ddcc8a0dcade009b8179f2c
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2017
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Másolja a adatok vagy az Azure Blob Storage Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-azure-blob-connector.md)
> * [2. verzió – Előzetes verzió](connector-azure-blob-storage.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység az Azure Data Factory és az Azure Blob Storage-adatok másolása. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [Azure Blog tárolási connnector a V1](v1/data-factory-azure-blob-connector.md).


## <a name="supported-scenarios"></a>Támogatott esetek

Adatok másolása az egyetlen támogatott forrás adattár az Azure Blob Storage vagy adatok másolása az Azure Blob Storage bármely támogatott fogadó adattárolóhoz. Adattároló forrásként támogatott listája / vagy mosdók által a másolási tevékenység során, tekintse meg a [adattárolókhoz támogatott](copy-activity-overview.md) tábla.

Pontosabban az Azure Blob-összekötő támogatja:

- Másolás és a általános célú Azure Storage-fiókok és a gyakran használt adatok/ritkán a Blob storage BLOB. 
- Blobok használatával is másolása **fiókkulcs** és **szolgáltatás SAS** (közös hozzáférésű Jogosultságkód) hitelesítés.
- Blobok másolása **a blokkot, hozzáfűzése, vagy a lapblobokat**, és az adatok másolásának **számára csak a blokkblobokat**. Prémium szintű Storage nem támogatott, a fogadó, mert a lapblobokat ezt támogatja.
- Másolás, blobok-, vagy a blobok elemzése/létrehozásakor a [támogatott formátumok és a tömörítési kodek](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Bevezetés
A másolási tevékenység során .NET SDK, a Python SDK, az Azure PowerShell, a REST API-t vagy a Azure Resource Manager-sablon használatával hozhat létre egy folyamatot. Lásd: [másolási tevékenység oktatóanyag](quickstart-create-data-factory-dot-net.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját. 

A következő szakaszok részletesen bemutatják adat-előállító entitások adott Azure Blob Storage meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

### <a name="using-account-key"></a>Fiók kulcsával.

Az Azure tárolás társított szolgáltatásának a fiókkulcs, amely az adat-előállítóban globális hozzáférést biztosít az Azure Storage használatával hozhat létre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **AzureStorage** |Igen |
| connectionString | Adja meg a connectionString tulajdonság az Azure storage való kapcsolódáshoz szükséges adatokat. Ez a mező megjelölése a SecureString. |Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhat Azure integrációs futásidejű vagy Self-hosted integrációs futásidejű (amennyiben az adattároló magánhálózaton található). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

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

### <a name="using-service-sas-authentication"></a>Szolgáltatás SAS-hitelesítéssel

Az Azure tárolás társított szolgáltatásának is létrehozhat egy közös hozzáférésű Jogosultságkód (SAS), amely minden/specifikus erőforrások (blobtárolóban /) a tárolóban lévő korlátozott/időhöz kötött hozzáférést biztosít a data factory használatával.

Egy közös hozzáférésű Jogosultságkód (SAS) delegált hozzáférést biztosít azokhoz a tárfiókban lévő erőforrások. Az SAS segítségével biztosíthat a ügyfél csak korlátozott a tárfiókban lévő objektumokra vonatkozó engedélyeken megadott időn belül, anélkül, hogy a fiók hozzáférési kulcsait megosztásához. A SAS URI, amely a lekérdezési paraméterek magában foglalja a hitelesített hozzáférést a tároló egyik erőforrásához szükséges összes adatot. Az SA-kat a tárolási erőforrások eléréséhez az ügyfélnek csak kell átadni a SAS a megfelelő konstruktort vagy metódust. Részletes információ a SAS: [megosztott hozzáférési aláírásokkal: az SAS-modell ismertetése](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!IMPORTANT]
> Az Azure Data Factory most csak támogatja **szolgáltatás SAS** , de nem fiók SAS. Lásd: [típusok a megosztott hozzáférési aláírásokkal](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) kétféle típusú és létrehozására vonatkozó további információért. Az SAS URL-cím által létrehozott Azure-portálon vagy a Tártallózó egy fiók SAS, ami nem támogatott.
>

Szolgáltatás SAS-hitelesítés használatához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **AzureStorage** |Igen |
| sasUri | Adja meg a megosztott hozzáférési aláírást URI az Azure Storage-erőforrások, például a blob, -tároló vagy tábla. Ez a mező megjelölése a SecureString. |Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhat Azure integrációs futásidejű vagy Self-hosted integrációs futásidejű (amennyiben az adattároló magánhálózaton található). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

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

Amikor hoz létre egy **SAS URI**, figyelembe véve a következő szempontokat:

- Állítsa be a megfelelő olvasási/írási **engedélyek** alapján a társított szolgáltatás (olvasási, írási, olvasás/írás) az adat-előállítóban felhasznált objektumokon.
- Állítsa be **lejárati idejének** megfelelően. Győződjön meg arról, hogy az Azure Storage-objektumokhoz való hozzáférést nem jár le az adatcsatorna aktív időszakára.
- URI létre kell hozni a megfelelő tárolót vagy blobot vagy a tábla szintjén a igények alapján. A SAS Uri-t az Azure blob lehetővé teszi, hogy a Data Factory szolgáltatásnak, hogy a blob eléréséhez. A SAS Uri-t az Azure blob-tároló lehetővé teszi, hogy az adott tárolóban lévő blobok iterációt a Data Factory szolgáltatásnak. Ha szeretne hozzáférést biztosítson nagyobb vagy kisebb értékre objektumok később, vagy frissítse a SAS URI-t, ne felejtse el a társított szolgáltatás frissítése új URI-azonosítójú.

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a témakör az Azure Blob-adathalmazra által támogatott tulajdonságokról.

Adatok másolása az Azure Blob, állítsa be a type tulajdonságot az adathalmaz **AzureBlob**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **AzureBlob** |Igen |
| folderPath | A tároló és a blob-tároló mappa elérési útja. Példa: myblobcontainer/myblobfolder / |Igen |
| fileName | A blob nevének megadása a **folderPath** Ha át kívánja másolni az egy adott blob. Ha nem ad meg ehhez a tulajdonsághoz értéket, a DataSet adatkészlet mutat a mappában található összes BLOB.<br/><br/>Ha nincs megadva fájlnév egy kimeneti adatkészlet és **preserveHierarchy** nincs megadva tevékenység fogadó, a másolási tevékenység során automatikusan létrehozza a blob neve a következő formátumban: `Data.[activity run id GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]`. Például: `Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz`. |Nem |
| Formátumban | Ha azt szeretné, hogy **másolja a fájlokat-van** közötti fájlalapú tárolók (bináris másolhatja azokat), hagyja ki a Formátum szakasz mindkét bemeneti és kimeneti adatkészlet-definíciókban.<br/><br/>Szeretne elemezni, vagy egy adott formátumú fájlok létrehozása, ha a következő fájl formátuma típusok támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság a formátuma a következő értékek egyikét. További információkért lásd: [szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), [Json formátumban](supported-file-formats-and-compression-codecs.md#json-format), [az Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátum](supported-file-formats-and-compression-codecs.md#orc-format), és [Parquet formátum](supported-file-formats-and-compression-codecs.md#parquet-format) szakaszok. |Nem (csak a bináris másolásának esetéhez) |
| Tömörítés | Adja meg a típus és az adatok tömörítése szintjét. További információkért lásd: [támogatott formátumok és a tömörítési kodek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**.<br/>Támogatott szintek a következők: **Optimal** és **leggyorsabb**. |Nem |

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

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör az Azure Blob-forrás és a fogadó által támogatott tulajdonságokról.

### <a name="azure-blob-as-source"></a>Az Azure Blob forrásaként

Adatok másolása az Azure Blob, állítsa be a forrás típusa a másolási tevékenység **BlobSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **BlobSource** |Igen |
| Rekurzív | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappát.<br/>Két érték engedélyezett: **igaz** (alapértelmezett), **hamis** | Nem |

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

### <a name="azure-blob-as-sink"></a>Az Azure Blob, a fogadó

Adatok másolása az Azure Blob, állítsa be a fogadó típusa a másolási tevékenység **BlobSink**. A következő tulajdonságok támogatottak a **fogadó** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó type tulajdonsága értékre kell állítani: **BlobSink** |Igen |
| copyBehavior | Meghatározza a másolási viselkedését, ha az adatforrás fájlok fájlalapú adattárolóból.<br/><br/>Engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: őrzi meg a fájl hierarchia a célmappában. A következő forrásfájl forrásmappához relatív elérési a relatív elérési út a cél-fájlját és a célmappa megegyezik.<br/><b>-FlattenHierarchy</b>: a forrásmappából a fájlok a célmappában első szintjét is. A fájlok céljaként automatikusan létrehozott nevet adni. <br/><b>-Mergefiles típusú</b>: egy fájl összes fájlt a forrásmappából egyesíti. Ha a fájl/Blob neve meg van adva, az egyesített neve legyen a megadott név; Ellenkező esetben lenne automatikusan létrehozott fájl nevét. | Nem |

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

### <a name="recursive-and-copybehavior-examples"></a>rekurzív és copyBehavior példák

Ez a szakasz ismerteti az eredményül kapott viselkedéstől rekurzív és copyBehavior kombinációk a másolási művelet.

| Rekurzív | copyBehavior | Forrás mappaszerkezet | Eredményül kapott cél |
|:--- |:--- |:--- |:--- |
| Igaz |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappa mappa1 forrásaként azonos struktúrájú jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| Igaz |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a cél az alábbi szerkezettel mappa1 jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File5 |
| Igaz |mergefiles típusú | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a cél az alábbi szerkezettel mappa1 jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + fájl3 + File4 + 5 fájl tartalmát egy fájl automatikusan létrehozott fájlnévvel egyesülnek |
| hamis |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappa mappa1 jön létre a következő struktúra<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Fájl3, File4 és File5 Subfolder1 nem átveszik. |
| hamis |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappa mappa1 jön létre a következő struktúra<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/><br/>Fájl3, File4 és File5 Subfolder1 nem átveszik. |
| hamis |mergefiles típusú | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappa mappa1 jön létre a következő struktúra<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Egy fájl automatikusan létrehozott fájlnévvel egyesített file1 + File2 tartalma. automatikusan létrehozott nevet a file1 kiszolgálón<br/><br/>Fájl3, File4 és File5 Subfolder1 nem átveszik. |

## <a name="next-steps"></a>Következő lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md##supported-data-stores-and-formats).
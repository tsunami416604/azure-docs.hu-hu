---
title: Adatok másolása a Google Cloud Storage-ból a Azure Data Factory használatával
description: További információ a Google Cloud Storage-ból származó adatok másolásáról a Azure Data Factory használatával támogatott fogadó adattárakra.
services: data-factory
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: jingwang
ms.openlocfilehash: f6b488fc4817e360ed512441f67d2e00356e6134
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758605"
---
# <a name="copy-data-from-google-cloud-storage-using-azure-data-factory"></a>Adatok másolása a Google Cloud Storage-ból a Azure Data Factory használatával
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk az adatok Google Cloud Storage-ból (GCS) történő másolását ismerteti. A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Google Cloud Storage-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)

Ez a Google Cloud Storage-összekötő támogatja a fájlok másolását, illetve a [támogatott fájlformátumokat és tömörítési kodekeket](supported-file-formats-and-compression-codecs.md)tartalmazó fájlok elemzését. A GCS S3-kompatibilis együttműködési képességet használ.

## <a name="prerequisites"></a>Előfeltételek

A következő beállításra van szükség a Google Cloud Storage-fiókjában:

1. A Google Cloud Storage-fiók együttműködési képességének engedélyezése
2. Állítsa be az alapértelmezett projektet, amely a cél GCS gyűjtőből másolni kívánt adatokból áll
3. Hozzon létre egy szolgáltatásfiókot, és határozza meg a megfelelő szintű engedélyeket a Cloud IAM használatával a GCP 
4. A szolgáltatásfiók hozzáférési kulcsainak létrehozása

![A Google Cloud Storage elérési kulcsának beolvasása](media/connector-google-cloud-storage/google-storage-cloud-settings.png)

## <a name="required-permissions"></a>Szükséges engedélyek

A Google Cloud Storage-ból származó adatok másolásához győződjön meg arról, hogy megadta a szükséges engedélyeket. A szolgáltatási fiókban definiált engedélyek tartalmazhatják `storage.buckets.get` az `storage.buckets.list` `storage.objects.get` objektum műveleteit.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek a Google Cloud Storage-hoz kapcsolódó Data Factory-entitások definiálására szolgálnak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Google Cloud Storage társított szolgáltatása a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot **GoogleCloudStorage**értékre kell beállítani. | Igen |
| accessKeyId | A titkos elérési kulcs azonosítója. A hozzáférési kulcs és a titok megkereséséhez tekintse meg az [Előfeltételek](#prerequisites)című témakört. |Igen |
| secretAccessKey | Maga a titkos elérési kulcs. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). |Igen |
| serviceUrl | Határozza meg az egyéni GCS-végpontot **`https://storage.googleapis.com`** . | Igen |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . Használhat Azure Integration Runtime vagy saját üzemeltetésű Integration Runtime (ha az adattár a magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |Nem |

Például:

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "GoogleCloudStorage",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "serviceUrl": "https://storage.googleapis.com"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A Google Cloud Storage a következő tulajdonságokat támogatja a `location` Format-alapú adatkészlet beállításai alatt:

| Tulajdonság   | Leírás                                                  | Kötelező |
| ---------- | ------------------------------------------------------------ | -------- |
| típus       | Az `location` adatkészletben található Type tulajdonságot **GoogleCloudStorageLocation**értékre kell állítani. | Igen      |
| bucketName | A GCS-gyűjtő neve.                                          | Igen      |
| folderPath | Az adott gyűjtőhöz tartozó mappa elérési útja. Ha a mappa szűréséhez helyettesítő karaktert szeretne használni, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításai között válassza a lehetőséget. | Nem       |
| fileName   | A fájl neve az adott gyűjtő + folderPath. Ha helyettesítő karaktereket szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításai között válassza a lehetőséget. | Nem       |

**Például**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Google Cloud Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "GoogleCloudStorageLocation",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Google Cloud Storage-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="google-cloud-storage-as-source"></a>Google Cloud Storage forrásként

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A Google Cloud Storage a következő tulajdonságokat támogatja a `storeSettings` Format-alapú másolási forrás beállításai alatt:

| Tulajdonság                 | Leírás                                                  | Kötelező                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| típus                     | A Type tulajdonságot a `storeSettings` **GoogleCloudStorageReadSettings**értékre kell állítani. | Igen                                                         |
| ***Keresse meg a másolandó fájlokat:*** |  |  |
| 1. lehetőség: statikus elérési út<br> | Másolja az adatkészletben megadott gyűjtő vagy mappa vagy fájl elérési útját. Ha az összes fájlt egy gyűjtőből vagy mappából kívánja átmásolni, a következőképpen adhatja meg: `wildcardFileName` `*` . |  |
| 2. lehetőség: GCS-előtag<br>-előtag | A GCS-kulcs nevének előtagja az adatkészletben konfigurált megadott gyűjtőben a forrás GCS-fájlok szűréséhez. Azok a GCS-kulcsok, amelyek nevével kezdődik, `bucket_in_dataset/this_prefix` ki vannak választva. Ez a GCS olyan szolgáltatás-oldalsó szűrőt használ, amely jobb teljesítményt nyújt, mint a helyettesítő karakteres szűrő. | Nem |
| 3. lehetőség: helyettesítő karakter<br>- wildcardFolderPath | A mappa elérési útja a forrás mappák szűréséhez az adatkészletben konfigurált megadott gyűjtőben helyettesítő karakterekkel. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egy karakter egyezése) `^` <br>További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Nem                                            |
| 3. lehetőség: helyettesítő karakter<br>- wildcardFileName | A forrásfájl szűréséhez a megadott gyűjtő + folderPath/wildcardFolderPath helyettesítő karaktereket tartalmazó fájlnév. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egy karakter egyezése) `^`  További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Igen |
| 3. lehetőség: a fájlok listája<br>- fileListPath | Egy adott fájl másolását jelzi. Mutasson egy szövegfájlra, amely tartalmazza a másolni kívánt fájlok listáját, soronként egy fájlt, amely az adatkészletben konfigurált útvonal relatív elérési útja.<br/>Ha ezt a beállítást használja, ne adja meg a fájl nevét az adatkészletben. További példákat a [fájllista példákban](#file-list-examples)talál. |Nem |
| ***További beállítások:*** |  | |
| rekurzív | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. Vegye figyelembe, hogy ha a rekurzív értéke TRUE (igaz), a fogadó pedig egy fájl alapú tároló, a fogadó nem másolja vagy hozza létre az üres mappát vagy almappát. <br>Az engedélyezett értékek: **true** (alapértelmezett) és **false (hamis**).<br>Ez a tulajdonság nem érvényes a konfiguráláskor `fileListPath` . |Nem |
| modifiedDatetimeStart    | A fájlok szűrése a következő attribútum alapján: utoljára módosítva. <br>A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a és a közötti időtartományon belül van `modifiedDatetimeStart` `modifiedDatetimeEnd` . Az idő az UTC-időzónára vonatkozik "2018-12-01T05:00:00Z" formátumban. <br> A tulajdonságok értéke lehet NULL, ami azt jelenti, hogy nem lesz alkalmazva a file Attribute szűrő az adatkészletre.  Ha `modifiedDatetimeStart` a dátum datetime értékkel rendelkezik `modifiedDatetimeEnd` , de null értékű, az azt jelenti, hogy azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha `modifiedDatetimeEnd` a dátum datetime értékkel rendelkezik `modifiedDatetimeStart` , de null értékű, az azt jelenti, hogy azok a fájlok, amelyek utolsó módosítási attribútuma kisebb, mint a DateTime érték, ki lesz választva.<br/>Ez a tulajdonság nem érvényes a konfiguráláskor `fileListPath` . | Nem                                            |
| modifiedDatetimeEnd      | Ugyanaz, mint a fenti.                                               | Nem                                                          |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem                                                          |

**Például**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "GoogleCloudStorageReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Példák a mappák és a fájlok szűrésére

Ez a szakasz a mappa elérési útjának és fájlnevének a helyettesítő karakteres szűrőkkel való viselkedését írja le.

| gyűjtő | key | rekurzív | A forrás mappa szerkezete és a szűrő eredménye (a félkövérrel szedett fájlok beolvasása)|
|:--- |:--- |:--- |:--- |
| gyűjtő | `Folder*/*` | hamis | gyűjtő<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| gyűjtő | `Folder*/*` | igaz | gyűjtő<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| gyűjtő | `Folder*/*.csv` | hamis | gyűjtő<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| gyűjtő | `Folder*/*.csv` | igaz | gyűjtő<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="file-list-examples"></a>Példák a fájllista

Ez a szakasz a fájllista elérési útjának a másolási tevékenység forrásában való használatának eredményét írja le.

Feltételezve, hogy rendelkezik a következő forrás-mappa struktúrájával, és félkövéren szeretné átmásolni a fájlokat:

| Példa a forrás struktúrájára                                      | Tartalom a FileListToCopy. txt fájlban                             | ADF-konfiguráció                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| gyűjtő<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metaadatok<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy. txt | File1. csv<br>Subfolder1/fájl3. csv<br>Subfolder1/File5. csv | **Az adatkészletben:**<br>Gyűjtő`bucket`<br>-Mappa elérési útja:`FolderA`<br><br>**A másolási tevékenység forrása:**<br>-Fájllista elérési útja:`bucket/Metadata/FileListToCopy.txt` <br><br>A fájl-lista elérési útja ugyanazon az adattárban található szövegfájlra mutat, amely tartalmazza a másolni kívánt fájlok listáját, és soronként egy fájlt az adatkészletben konfigurált útvonal relatív elérési útjával. |

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [GetMetaData tevékenységet](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Tevékenység tulajdonságainak törlése

A tulajdonságok részleteinek megismeréséhez tekintse meg a [tevékenység törlése](delete-activity.md) lehetőséget.

## <a name="legacy-models"></a>Örökölt modellek

Ha az Amazon S3-összekötőt használta a Google Cloud Storage-ból származó adatok másolásához, akkor továbbra is támogatott a visszamenőleges kompatibilitás érdekében. Azt javasoljuk, hogy használja a fenti szakaszban említett új modellt, és az ADF authoring felhasználói felülete átvált az új modell generálására.

## <a name="next-steps"></a>További lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listájáért lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

---
title: Adatok másolása az Amazon Simple Storage Service (S3) szolgáltatásból Azure Data Factory használatával | Microsoft Docs
description: Ismerje meg, hogyan másolhat adatok az Amazon Simple Storage Service (S3) szolgáltatásból a Azure Data Factory használatával támogatott fogadó adattárakba.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 3aa1284212ce12db2e94d8e753d3d75cf8a44ba8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931183"
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Adatok másolása az Amazon Simple Storage szolgáltatásból Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
>
> * [1-es verzió](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Aktuális verzió](connector-amazon-simple-storage-service.md)

Ez a cikk az Amazon Simple Storage Service (Amazon S3) adatainak másolását ismerteti. A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

>[!TIP]
>Az Amazon S3-ból az Azure Storage-ba irányuló adatáttelepítési forgatókönyv esetén további információ a [használat Azure Data Factory az Amazon S3-ból az Azure Storage-ba való Migrálás](data-migration-guidance-s3-azure-storage.md)során.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Amazon S3-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)

Pontosabban, ez az Amazon S3-összekötő támogatja a fájlok másolását vagy elemzését a [támogatott fájlformátumokkal és tömörítési kodekekkel](supported-file-formats-and-compression-codecs.md). Az [AWS-aláírás 4-es verzióját](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) használja az S3-ra irányuló kérések hitelesítéséhez.

>[!TIP]
>Ezt az Amazon S3-összekötőt használhatja **bármely S3-kompatibilis Storage-szolgáltatóról** , például a [Google Cloud Storage](connector-google-cloud-storage.md)-ból származó adatok másolásához. A társított szolgáltatás konfigurációjában határozza meg a kapcsolódó szolgáltatás URL-címét.

## <a name="required-permissions"></a>Szükséges engedélyek

Az Amazon S3-ból származó adatok másolásához győződjön meg arról, hogy a következő engedélyeket kapta:

- **Másolási tevékenység végrehajtásához:** : `s3:GetObject` és `s3:GetObjectVersion` az Amazon S3 Object műveletekhez.
- **Data Factory grafikus felhasználói felület létrehozásához**: `s3:ListAllMyBuckets` és `s3:ListBucket`/`s3:GetBucketLocation` az Amazon S3-gyűjtőhöz szükséges műveletekhez, például a kapcsolatok teszteléséhez és a fájlok elérési útjának tallózásához/böngészéséhez. Ha nem szeretné megadni ezeket az engedélyeket, hagyja ki a kapcsolat tesztelése a társított szolgáltatás létrehozása lapon, és adja meg az elérési utat közvetlenül az adatkészlet beállításai között.

Az Amazon S3-engedélyek teljes listájáról az [engedélyek megadása házirendben](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html)című témakörben olvashat bővebben.

## <a name="getting-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

A következő szakaszokban részletesen ismertetjük az Amazon S3-ra jellemző Data Factory-entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Amazon S3 társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **AmazonS3**értékre kell beállítani. | Igen |
| accessKeyId | A titkos elérési kulcs azonosítója. |Igen |
| secretAccessKey | Maga a titkos elérési kulcs. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). |Igen |
| serviceUrl | Az egyéni S3-végpontot akkor válassza, ha a hivatalos Amazon S3-szolgáltatástól eltérő, S3-kompatibilis tárolási szolgáltatóktól másol Adatmásolást. A Google Cloud Storage-ból származó adatok másolásához például a következőt kell megadnia: `https://storage.googleapis.com`. | Nem |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . Használhat Azure Integration Runtime vagy saját üzemeltetésű Integration Runtime (ha az adattár a magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |Nem |

>[!TIP]
>Az egyéni S3 szolgáltatás URL-címének megadása, ha az adatok másolása a hivatalos Amazon S3-szolgáltatástól eltérő S3-kompatibilis tárolóból történik.

>[!NOTE]
>Ehhez az összekötőhöz hozzáférési kulcsok szükségesek a IAM-fiókhoz az Amazon S3-ból való adatok másolásához. Az [ideiglenes biztonsági hitelesítő adatok](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) nem támogatottak.
>

Például:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok támogatottak az Amazon S3 esetében `location` beállításokban a Format-alapú adatkészletben:

| Tulajdonság   | Leírás                                                  | Szükséges |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Az adatkészletben található `location` típus tulajdonságot **AmazonS3Location**értékre kell állítani. | Igen      |
| bucketName | Az S3-gyűjtő neve.                                          | Igen      |
| folderPath | Az adott gyűjtőhöz tartozó mappa elérési útja. Ha a mappa szűréséhez helyettesítő karaktert szeretne használni, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításai között válassza a lehetőséget. | Nem       |
| fileName   | A fájl neve az adott gyűjtő + folderPath. Ha helyettesítő karaktereket szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításai között válassza a lehetőséget. | Nem       |
| version | Az S3 objektum verziója, ha az S3 Verziószámozás engedélyezve van. Ha nincs megadva, a rendszer beolvassa a legújabb verziót. |Nem |

**Példa**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
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

### <a name="legacy-dataset-model"></a>Örökölt adatkészlet-modell

>[!NOTE]
>A következő adatkészlet-modell továbbra is támogatott, ha visszafelé kompatibilis. Azt javasoljuk, hogy használja a fentiekben említett új modellt, és az ADF authoring felhasználói felülete átvált az új modell generálására.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **AmazonS3Object** |Igen |
| bucketName | Az S3-gyűjtő neve. A helyettesítő karakteres szűrő nem támogatott. |Igen a másolási/keresési tevékenységhez, nem a GetMetadata tevékenységhez |
| kulcs | A megadott gyűjtőben az S3-objektum kulcsának **neve vagy helyettesítő szűrője** . Csak akkor érvényes, ha nincs megadva az "előtag" tulajdonság. <br/><br/>A helyettesítő karakteres szűrő a mappa és a fájlnév résznél is támogatott. Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egy karakternek felel meg).<br/>-1. példa: `"key": "rootfolder/subfolder/*.csv"`<br/>-2. példa: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>További példa a [mappa-és fájlszűrő-példákban](#folder-and-file-filter-examples). A `^` segítségével elkerülheti, ha a tényleges mappája/fájlneve helyettesítő karakterrel vagy a menekülési karakterrel rendelkezik. |Nem |
| előtag | Az S3-objektum kulcsának előtagja. Azok az objektumok, amelyek esetében ezzel az előtaggal kezdődnek a kulcsok. Csak akkor érvényes, ha a "Key" tulajdonság nincs megadva. |Nem |
| version | Az S3 objektum verziója, ha az S3 Verziószámozás engedélyezve van. Ha nincs megadva, a rendszer beolvassa a legújabb verziót. |Nem |
| modifiedDatetimeStart | A fájlok szűrése a következő attribútum alapján: utoljára módosítva. A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a `modifiedDatetimeStart` és `modifiedDatetimeEnd`közötti időtartományon belül van. Az idő az UTC-időzónára vonatkozik "2018-12-01T05:00:00Z" formátumban. <br/><br/> Ügyeljen arra, hogy az adatáthelyezés általános teljesítményét a beállítás engedélyezésével befolyásolja, ha nagy mennyiségű fájlból szeretne szűrni a fájlmegosztást. <br/><br/> A tulajdonságok értéke lehet NULL, ami azt jelenti, hogy nem lesz alkalmazva a file Attribute szűrő az adatkészletre.  Ha `modifiedDatetimeStart` dátum datetime értékkel rendelkezik, de a `modifiedDatetimeEnd` NULL értékű, akkor azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik, de a `modifiedDatetimeStart` NULL értékű, akkor azokat a fájlokat jelenti, amelyek utolsó módosítási attribútuma kisebb, mint a DateTime érték.| Nem |
| modifiedDatetimeEnd | A fájlok szűrése a következő attribútum alapján: utoljára módosítva. A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a `modifiedDatetimeStart` és `modifiedDatetimeEnd`közötti időtartományon belül van. Az idő az UTC-időzónára vonatkozik "2018-12-01T05:00:00Z" formátumban. <br/><br/> Ügyeljen arra, hogy az adatáthelyezés általános teljesítményét a beállítás engedélyezésével befolyásolja, ha nagy mennyiségű fájlból szeretne szűrni a fájlmegosztást. <br/><br/> A tulajdonságok értéke lehet NULL, ami azt jelenti, hogy nem lesz alkalmazva a file Attribute szűrő az adatkészletre.  Ha `modifiedDatetimeStart` dátum datetime értékkel rendelkezik, de a `modifiedDatetimeEnd` NULL értékű, akkor azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik, de a `modifiedDatetimeStart` NULL értékű, akkor azokat a fájlokat jelenti, amelyek utolsó módosítási attribútuma kisebb, mint a DateTime érték.| Nem |
| formátumban | Ha **fájlokat szeretne másolni** a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt mind a bemeneti, mind a kimeneti adatkészlet-definíciókban.<br/><br/>Ha a fájlokat egy adott formátummal szeretné elemezni vagy előállítani, a következő fájlformátum-típusok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a Format értékre a következő értékek egyikére. További információkért lásd: [Szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), JSON- [Formátum](supported-file-formats-and-compression-codecs.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [ork-formátum](supported-file-formats-and-compression-codecs.md#orc-format)és a [parketta formátuma](supported-file-formats-and-compression-codecs.md#parquet-format) című rész. |Nem (csak bináris másolási forgatókönyv esetén) |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. További információ: [támogatott fájlformátumok és tömörítési kodekek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>A támogatott típusok a következők: **gzip**, **deflate**, **BZip2**és **ZipDeflate**.<br/>A támogatott szintek a következők: **optimális** és **leggyorsabb**. |Nem |

>[!TIP]
>Ha egy mappa összes fájlját át szeretné másolni, akkor a **bucketName** megadásával adhatja meg a mappa részét a gyűjtőhöz és az **előtaghoz** .<br>Egy adott névvel rendelkező egyetlen fájl másolásához adja meg a **bucketName** a mappa részhez, **valamint a fájl** nevét.<br>Ha egy mappában lévő fájlok egy részhalmazát szeretné átmásolni, akkor a **bucketName** és a **kulcs** létrehozása a mappa részéhez, valamint a helyettesítő karakteres szűréshez.

**Példa: előtag használata**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
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

**Példa: kulcs és verzió használata (nem kötelező)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
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

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz az Amazon S3 forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="amazon-s3-as-source"></a>Amazon S3 forrásként

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok támogatottak az Amazon S3 esetében `storeSettings` beállításokban a Format-alapú másolási forrásban:

| Tulajdonság                 | Leírás                                                  | Szükséges                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | A `storeSettings` alatti Type tulajdonságot **AmazonS3ReadSetting**értékre kell beállítani. | Igen                                                         |
| rekurzív                | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. Vegye figyelembe, hogy ha a rekurzív értéke TRUE (igaz), a fogadó pedig egy fájl alapú tároló, a fogadó nem másolja vagy hozza létre az üres mappát vagy almappát. Az engedélyezett értékek: **true** (alapértelmezett) és **false (hamis**). | Nem                                                          |
| előtag                   | A forrásoldali objektumok szűréséhez az adatkészletben konfigurált megadott gyűjtő alatt található S3-objektumhoz tartozó előtag. Azok az objektumok, amelyek esetében ezzel az előtaggal kezdődnek a kulcsok. <br>Csak akkor érvényes, ha a `wildcardFolderPath` és a `wildcardFileName` tulajdonság nincs megadva. | Nem                                                          |
| wildcardFolderPath       | A mappa elérési útja a forrás mappák szűréséhez az adatkészletben konfigurált megadott gyűjtőben helyettesítő karakterekkel. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egyetlen karakternek felel meg); a `^` használatával elkerülheti, hogy a tényleges mappanév helyettesítő karakterrel vagy a menekülési karakterrel rendelkezik-e a belsejében. <br>További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Nem                                                          |
| wildcardFileName         | A forrásfájl szűréséhez a megadott gyűjtő + folderPath/wildcardFolderPath helyettesítő karaktereket tartalmazó fájlnév. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egyetlen karakternek felel meg); a `^` használatával elkerülheti, hogy a tényleges mappanév helyettesítő karakterrel vagy a menekülési karakterrel rendelkezik-e a belsejében.  További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Igen, ha `fileName` az adatkészletben, és a `prefix` nincs megadva |
| modifiedDatetimeStart    | A fájlok szűrése a következő attribútum alapján: utoljára módosítva. A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a `modifiedDatetimeStart` és `modifiedDatetimeEnd`közötti időtartományon belül van. Az idő az UTC-időzónára vonatkozik "2018-12-01T05:00:00Z" formátumban. <br> A tulajdonságok értéke lehet NULL, ami azt jelenti, hogy nem lesz alkalmazva a file Attribute szűrő az adatkészletre.  Ha `modifiedDatetimeStart` dátum datetime értékkel rendelkezik, de a `modifiedDatetimeEnd` NULL értékű, akkor azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik, de a `modifiedDatetimeStart` NULL értékű, akkor azokat a fájlokat jelenti, amelyek utolsó módosítási attribútuma kisebb, mint a DateTime érték. | Nem                                                          |
| modifiedDatetimeEnd      | Ugyanaz, mint a fenti.                                               | Nem                                                          |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem                                                          |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
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
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AmazonS3ReadSetting",
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

#### <a name="legacy-source-model"></a>Örökölt forrás modell

>[!NOTE]
>A következő másolási forrás modell továbbra is támogatott, ha visszafelé kompatibilis. Azt javasoljuk, hogy használja a fentiekben említett új modellt, és az ADF authoring felhasználói felülete átvált az új modell generálására.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **FileSystemSource** |Igen |
| rekurzív | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az alárendelt mappákból, vagy csak a megadott mappából. Vegye figyelembe, hogy ha a rekurzív értéke TRUE (igaz), a fogadó pedig a fájl alapú tároló, akkor a rendszer nem másolja/hozza létre az üres mappát/almappát a fogadóban.<br/>Az engedélyezett értékek: **true** (alapértelmezett), **false** | Nem |
| maxConcurrentConnections | Az adattárhoz való kapcsolódáshoz szükséges kapcsolatok száma. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
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

| gyűjtő | kulcs | rekurzív | A forrás mappa szerkezete és a szűrő eredménye (a félkövérrel szedett fájlok beolvasása)|
|:--- |:--- |:--- |:--- |
| gyűjtő | `Folder*/*` | hamis | gyűjtő<br/>&nbsp;&nbsp;&nbsp;&nbsp;mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**fájl2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| gyűjtő | `Folder*/*` | igaz | gyűjtő<br/>&nbsp;&nbsp;&nbsp;&nbsp;mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**fájl2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| gyűjtő | `Folder*/*.csv` | hamis | gyűjtő<br/>&nbsp;&nbsp;&nbsp;&nbsp;mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| gyűjtő | `Folder*/*.csv` | igaz | gyűjtő<br/>&nbsp;&nbsp;&nbsp;&nbsp;mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [GetMetaData tevékenységet](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Tevékenység tulajdonságainak törlése

A tulajdonságok részleteinek megismeréséhez tekintse meg a [tevékenység törlése](delete-activity.md) lehetőséget.

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listájáért lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).

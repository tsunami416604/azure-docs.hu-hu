---
title: Adatok másolása az Azure Data Factory segítségével a Google Cloud Storage |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat a Google Cloud Storage támogatott fogadó adattárakba az Azure Data Factory használatával kapcsolatban.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 16f917701d23ae9c363efbe2b3637b9d9b9d16b8
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64876726"
---
# <a name="copy-data-from-google-cloud-storage-using-azure-data-factory"></a>Adatok másolása a Google Cloud Storage, Azure Data Factory használatával

Ez a cikk ismerteti, hogyan másolhat adatokat a Google Cloud Storage-ból. Azure Data Factory szolgáltatásról, olvassa el a [bevezető cikk](introduction.md).

## <a name="supported-capabilities"></a>Támogatott képességek

A Google Cloud Storage-összekötő támogatott a következő tevékenységeket:

- [Másolási tevékenység](copy-activity-overview.md) a [támogatott forrás/fogadó mátrix](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

Pontosabban, a Google Cloud Storage-összekötő támogatja a fájlok másolása,-elemzési a fájlokat, vagy a [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>Az adatok másolása a Google Cloud Storage kihasználja a [Amazon S3-összekötő](connector-amazon-simple-storage-service.md) megfelelő egyéni S3 végponttal, mint a Google Cloud Storage lehetővé teszi a S3-kompatibilis érdekében.

## <a name="required-permissions"></a>Szükséges engedélyek

Adatok másolása a Google Cloud Storage-ból, győződjön meg arról, kapott a következő engedélyeket:

- **A másolási tevékenység-végrehajtási:**: `s3:GetObject` és `s3:GetObjectVersion` objektum műveletekhez.
- **A Data Factory grafikus felhasználói felületének létrehozásához**: `s3:ListAllMyBuckets` és `s3:ListBucket` / `s3:GetBucketLocation` gyűjtőbe műveletek engedélyek szükségesek emellett a műveletek, például a kapcsolat tesztelése, és tallózással keresse meg és nyissa meg a fájlok elérési útjainak. Ha nem szeretné ezeket az engedélyeket, hagyja ki a kapcsolat tesztelése a társított szolgáltatás létrehozása oldal, és adja meg az elérési út közvetlenül az adatkészlet beállításai.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások Google Cloud Storage-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Google Cloud Storage társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **AmazonS3**. | Igen |
| accessKeyId | A titkos hozzáférési kulcs azonosítója. A hozzáférési kulcs és titkos kulcs találja, **Google Cloud Storage** > **beállítások** > **együttműködési**. |Igen |
| secretAccessKey | A titkos hívóbetűje magát. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
| serviceUrl | Az egyéni S3-végpontokat, **`https://storage.googleapis.com`**. | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Használhatja az Azure integrációs modul vagy a helyi integrációs modul (ha az adattár magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

Például:

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "AmazonS3",
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

- A **Parquet és tagolt szövegformátum**, tekintse meg [Parquet és elválasztójellel tagolt szöveges formátumban adatkészlet](#parquet-and-delimited-text-format-dataset) szakaszban.
- Más formátumú hasonló **ORC/Avro/JSON/bináris formátum**, tekintse meg [más formátumú adatkészlet](#other-format-dataset) szakaszban.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet és elválasztójellel tagolt szöveges formátum adatkészlet

Adatok másolása a Google Cloud Storage **Parquet vagy tagolt szövegformátum**, tekintse meg [Parquet formátum](format-parquet.md) és [tagolt szövegformátum](format-delimited-text.md) cikk formátum-alapú adatkészlet és támogatott beállítások. A következő tulajdonságok támogatottak a Google Cloud Storage alatt `location` formátum-alapú adatkészlet beállításai:

| Tulajdonság   | Leírás                                                  | Szükséges |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A type tulajdonság alatt `location` adatkészlet értékre kell állítani **AmazonS3Location**. | Igen      |
| bucketName | Az S3 gyűjtő neve.                                          | Igen      |
| folderPath | A megadott időszak alatt mappa elérési útját. Ha azt szeretné, helyettesítő karaktert tartalmazó szűrő mappába használandó, hagyja ki ezt a beállítást, és adja meg a tevékenység – forrásbeállítások. | Nem       |
| fileName   | A fájl neve, az adott gyűjtőbe + folderPath alatt. Ha szeretné használni a helyettesítő karaktert tartalmazó fájlok szűrésére, hagyja ki ezt a beállítást, és adja meg a tevékenység – forrásbeállítások. | Nem       |

> [!NOTE]
> **AmazonS3Object** továbbra is támogatja a Parquet vagy szöveges formátum a következő szakaszban említett adatkészlet típusa – a Másolás/keresési/GetMetadata tevékenység a visszamenőleges kompatibilitás érdekében. A jövőben az új modell használata javasolt, és ezek a típusok létrehozása a felhasználói felület szerzői ADF változott.

**Példa**

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

### <a name="other-format-dataset"></a>Más formátumú adatkészlet

Adatok másolása a Google Cloud Storage **ORC/Avro/JSON/bináris formátum**, a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **AmazonS3Object** |Igen |
| bucketName | Az S3 gyűjtő neve. Helyettesítő karaktert tartalmazó szűrő nem támogatott. |Igen, a Másolás/keresési tevékenység, GetMetadata tevékenység esetében: nem |
| kulcs | A **nevét vagy helyettesítő karaktert tartalmazó szűrő** S3 objektum kulcs alatt a megadott gyűjtőjét. Érvényes, csak ha "előtag" tulajdonság nincs megadva. <br/><br/>A helyettesítő karaktert tartalmazó szűrő támogatott egyaránt mappa és fájl nevét részét. A helyettesítő karakterek engedélyezettek: `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter).<br/>-1. példa: `"key": "rootfolder/subfolder/*.csv"`<br/>– 2. példa: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>További példák a [példák a mappához és fájlhoz szűrők](#folder-and-file-filter-examples). Használat `^` elkerülésére, ha a fájl tényleges mappa neve helyettesítő elemet vagy a escape karaktere belül. |Nem |
| előtag | Az S3-objektum kulcs előtag. Ezzel az előtaggal start amelynek kulcsok objektum van kijelölve. Csak akkor, ha a "key" tulajdonság nincs megadva érvényes. |Nem |
| version | Az S3-objektum, ha engedélyezve van a S3 versioning verziója. |Nem |
| modifiedDatetimeStart | Az attribútum alapján fájlok szűrés: Utolsó módosítás. A fájlok lesz kiválasztva, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br/><br/> A Tulajdonságok lehet null értékű, ami jelenti azt, hogy nincs fájlszűrő attribútum alkalmazandó az adatkészletet.  Amikor `modifiedDatetimeStart` dátum és idő értékkel rendelkezik, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a dátum és idő értékkel lesz kiválasztva.  Amikor `modifiedDatetimeEnd` dátum és idő értékkel rendelkezik, de `modifiedDatetimeStart` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke kisebb, mint a dátum/idő értéket fog jelölni.| Nem |
| modifiedDatetimeEnd | Az attribútum alapján fájlok szűrés: Utolsó módosítás. A fájlok lesz kiválasztva, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br/><br/> A Tulajdonságok lehet null értékű, ami jelenti azt, hogy nincs fájlszűrő attribútum alkalmazandó az adatkészletet.  Amikor `modifiedDatetimeStart` dátum és idő értékkel rendelkezik, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a dátum és idő értékkel lesz kiválasztva.  Amikor `modifiedDatetimeEnd` dátum és idő értékkel rendelkezik, de `modifiedDatetimeStart` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke kisebb, mint a dátum/idő értéket fog jelölni.| Nem |
| Formátum | Ha azt szeretné, hogy **, a fájlok másolása a-rendszer** közötti fájlalapú tárolók (bináris másolat), hagyja ki a format szakaszban mindkét bemeneti és kimeneti adatkészlet-definíciókban.<br/><br/>Ha szeretné elemezni, vagy hozzon létre egy adott formátumú fájlok formátuma a következő fájltípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt formátumot az alábbi értékek egyikére. További információkért lásd: [szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), [Json formátumban](supported-file-formats-and-compression-codecs.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátum](supported-file-formats-and-compression-codecs.md#orc-format), és [Parquetformátum](supported-file-formats-and-compression-codecs.md#parquet-format) szakaszokat. |Nem (csak a bináris másolás esetén) |
| A tömörítés | Adja meg a típus és az adatok tömörítési szintje. További információkért lásd: [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Támogatott típusok a következők: **A GZip**, **Deflate**, **BZip2**, és **ZipDeflate**.<br/>Támogatott szintek a következők: **Optimális** és **leggyorsabb**. |Nem |

>[!TIP]
>Másolja egy mappában található összes fájlt, adja meg a **bucketName** gyűjtők és **előtag** a mappára vonatkozó részt.<br>Adja meg a megadott nevű egyetlen fájl másolásához **bucketName** gyűjtők és **kulcs** mappa része és a fájl neve.<br>Másolja a fájlokat egy mappában egy részét, adja meg a **bucketName** gyűjtők és **kulcs** mappa része és a helyettesítő karaktert tartalmazó szűrő.

**Példa: előtag használatával**

```json
{
    "name": "GoogleCloudStorageDataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<linked service name>",
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

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Google Cloud Storage forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="google-cloud-storage-as-source"></a>Google Cloud Storage forrásként

- A Másolás **Parquet és tagolt szövegformátum**, tekintse meg [elválasztójellel tagolt szöveges formátum forrás- és Parquet](#parquet-and-delimited-text-format-source) szakaszban.
- A más formátumú, például a Másolás **ORC/Avro/JSON/bináris formátum**, tekintse meg [más formátumú forrás](#other-format-source) szakaszban.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet és elválasztójellel tagolt szöveges formátum forrása

Adatok másolása a Google Cloud Storage **Parquet vagy tagolt szövegformátum**, tekintse meg [Parquet formátum](format-parquet.md) és [tagolt szövegformátum](format-delimited-text.md) cikk formátum-alapú másolási tevékenység forrás- és támogatott beállítás. A következő tulajdonságok támogatottak a Google Cloud Storage alatt `storeSettings` formátum-alapú másolási forrásaként beállításait:

| Tulajdonság                 | Leírás                                                  | Szükséges                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | A type tulajdonság alatt `storeSettings` értékre kell állítani **AmazonS3ReadSetting**. | Igen                                                         |
| a rekurzív                | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. Vegye figyelembe, hogy ha a rekurzív értéke igaz, és a fogadó a fájlalapú tároló, egy üres mappát vagy almappát nem másolja vagy létrehozott, a fogadó. Engedélyezett értékek a következők **igaz** (alapértelmezett), és **hamis**. | Nem                                                          |
| előtag                   | Az S3 objektum kulcs alatt a konfigurált adatforrás-objektumok szűrése az adatkészletben megadott gyűjtőjét előtagot. Ezzel az előtaggal start amelynek kulcsok objektum van kijelölve. Csak akkor, ha érvényes `wildcardFolderPath` és `wildcardFileName` tulajdonságai nincsenek megadva. |                                                             |
| wildcardFolderPath       | Mappa elérési útja a helyettesítő karaktereket a megadott gyűjtőjét konfigurált adatforrás mappákba szűrő adatkészlet alapján. <br>Helyettesítő karakterek engedélyezettek: `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter); használata `^` elkerülésére, ha a tényleges mappanevet helyettesítő elemet vagy a escape karaktere belül. <br>További példák a [példák a mappához és fájlhoz szűrők](#folder-and-file-filter-examples). | Nem                                                          |
| wildcardFileName         | A helyettesítő karakterek az adott gyűjtőbe + folderPath/wildcardFolderPath szűrő forrásfájlok a fájl neve. <br>Helyettesítő karakterek engedélyezettek: `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter); használata `^` elkerülésére, ha a tényleges mappanevet helyettesítő elemet vagy a escape karaktere belül.  További példák a [példák a mappához és fájlhoz szűrők](#folder-and-file-filter-examples). | Igen, ha `fileName` adatkészlet és `prefix` nincs megadva |
| modifiedDatetimeStart    | Az attribútum alapján fájlok szűrés: Utolsó módosítás. A fájlok lesz kiválasztva, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br> A Tulajdonságok lehet null értékű, ami jelenti azt, hogy nincs fájlszűrő attribútum alkalmazandó az adatkészletet.  Amikor `modifiedDatetimeStart` dátum és idő értékkel rendelkezik, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a dátum és idő értékkel lesz kiválasztva.  Amikor `modifiedDatetimeEnd` dátum és idő értékkel rendelkezik, de `modifiedDatetimeStart` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke kisebb, mint a dátum/idő értéket fog jelölni. | Nem                                                          |
| modifiedDatetimeEnd      | Ugyanaz, mint a fenti.                                               | Nem                                                          |
| maxConcurrentConnections | A szeretne csatlakozni a storage-tároló egyidejű kapcsolatok száma. Adja meg, csak akkor, ha szeretné korlátozni a egyidejű kapcsolat az adattárba. | Nem                                                          |

> [!NOTE]
> A Parquet vagy tagolt szövegformátum **FileSystemSource** továbbra is támogatott a következő szakaszban említett típusa másolási tevékenység forrása-visszamenőleges kompatibilitásra szolgál. A jövőben az új modell használata javasolt, és ezek a típusok létrehozása a felhasználói felület szerzői ADF változott.

**Példa**

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

#### <a name="other-format-source"></a>Más formátumú forrás

Adatok másolása a Google Cloud Storage **ORC/Avro/JSON/bináris formátum**, a következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **FileSystemSource** |Igen |
| a rekurzív | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappákban vagy csak a megadott mappába. Megjegyzés: Ha a rekurzív értéke igaz, és a fogadó fájlalapú tároló, üres mappa/alárendelt-folder nem lesz másolva vagy hozható létre, a fogadó.<br/>Engedélyezett értékek a következők: **igaz** (alapértelmezett), **false (hamis)** | Nem |
| maxConcurrentConnections | A szeretne csatlakozni a storage-tároló egyidejű kapcsolatok száma. Adja meg, csak akkor, ha szeretné korlátozni a egyidejű kapcsolat az adattárba. | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
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

### <a name="folder-and-file-filter-examples"></a>Mappa és fájl szűrő példák

Ez a szakasz ismerteti a mappa elérési útját és nevét, az eredményül kapott viselkedéstől helyettesítő szűrőket.

| gyűjtő | key | a rekurzív | Forrás-mappa szerkezete és szűrő eredmény (a fájlok a félkövérrel szedett blobnevet)|
|:--- |:--- |:--- |:--- |
| gyűjtő | `Folder*/*` | false | gyűjtő<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| gyűjtő | `Folder*/*` | true | gyűjtő<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| gyűjtő | `Folder*/*.csv` | false | gyűjtő<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| gyűjtő | `Folder*/*.csv` | true | gyűjtő<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).

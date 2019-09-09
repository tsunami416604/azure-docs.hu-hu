---
title: Adatok másolása a Google Cloud Storage-ból a Azure Data Factory használatával | Microsoft Docs
description: További információ a Google Cloud Storage-ból származó adatok másolásáról a Azure Data Factory használatával támogatott fogadó adattárakra.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: fcfdf7de3d121030e0ceb345829b153235a52703
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813522"
---
# <a name="copy-data-from-google-cloud-storage-using-azure-data-factory"></a>Adatok másolása a Google Cloud Storage-ból a Azure Data Factory használatával

Ez a cikk az adatok Google Cloud Storage-ból való másolásának lépéseit ismerteti. Azure Data Factory szolgáltatásról, olvassa el a [bevezető cikk](introduction.md).

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Google Cloud Storage-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

Ez a Google Cloud Storage-összekötő támogatja a fájlok másolását, illetve a [támogatott fájlformátumokat és tömörítési kodekeket](supported-file-formats-and-compression-codecs.md)tartalmazó fájlok elemzését.

>[!NOTE]
>A Google Cloud Storage-ból származó adatok másolása az [Amazon S3-összekötőt](connector-amazon-simple-storage-service.md) használja a megfelelő egyéni S3-végponttal, mivel a Google Cloud Storage az S3-kompatibilis együttműködési képességet is biztosítja.

## <a name="required-permissions"></a>Szükséges engedélyek

A Google Cloud Storage-ból származó adatok másolásához győződjön meg arról, hogy a következő engedélyek vannak megadva:

- **Másolási tevékenység végrehajtásához:** `s3:GetObject` : `s3:GetObjectVersion` és objektum-műveletekhez.
- **Data Factory grafikus felhasználói felület létrehozásához**: `s3:ListAllMyBuckets` és `s3:ListBucket` / `s3:GetBucketLocation` a gyűjtői műveletek engedélyeihez szükség van az olyan műveletekhez, mint a kapcsolatok tesztelése és a fájlok elérési útjának tallózása vagy navigálása. Ha nem szeretné megadni ezeket az engedélyeket, hagyja ki a kapcsolat tesztelése a társított szolgáltatás létrehozása lapon, és adja meg az elérési utat közvetlenül az adatkészlet beállításai között.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek a Google Cloud Storage-hoz kapcsolódó Data Factory-entitások definiálására szolgálnak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Google Cloud Storage társított szolgáltatása a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **AmazonS3**. | Igen |
| accessKeyId | A titkos hozzáférési kulcs azonosítója. A hozzáférési kulcs és a titkos kód megkereséséhez lépjen a **Google Cloud Storage** > -**Beállítások** > **együttműködési képességére**. |Igen |
| secretAccessKey | A titkos hívóbetűje magát. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
| serviceUrl | Határozza meg az egyéni S3- **`https://storage.googleapis.com`** végpontot. | Igen |
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

- A **Parquet, a tagolt szöveget, a JSON-t, a Avro és a bináris formátumot**a [parketta, a tagolt szöveg, a JSON, a Avro és a bináris formátum adatkészlet](#format-based-dataset) szakaszban találja.
- Más formátumok, például az **ork formátum**esetében tekintse meg a [más formátumú adatkészlet](#other-format-dataset) szakaszt.

### <a name="format-based-dataset"></a>Parketta, tagolt szöveg, JSON, Avro és bináris formátumú adatkészlet

Ha a **parketta, a tagolt szöveg, a JSON, a Avro és a bináris formátum**adatait szeretné másolni, tekintse meg a [parketta formátumát](format-parquet.md), a [tagolt szöveg formátumát](format-delimited-text.md), a [Avro formátumát](format-avro.md) és a [bináris formátumú](format-binary.md) cikket a Format-alapú adatkészletek és a támogatott beállítások A Google Cloud Storage a következő tulajdonságokat támogatja a Format `location` -alapú adatkészlet beállításai alatt:

| Tulajdonság   | Leírás                                                  | Szükséges |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Az adatkészletben `location` található Type tulajdonságot **AmazonS3Location**értékre kell állítani. | Igen      |
| bucketName | Az S3 gyűjtő neve.                                          | Igen      |
| folderPath | Az adott gyűjtőhöz tartozó mappa elérési útja. Ha a mappa szűréséhez helyettesítő karaktert szeretne használni, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításai között válassza a lehetőséget. | Nem       |
| fileName   | A fájl neve az adott gyűjtő + folderPath. Ha helyettesítő karaktereket szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításai között válassza a lehetőséget. | Nem       |

> [!NOTE]
> A következő szakaszban említett, a Parquet/Text formátumot tartalmazó **AmazonS3Object** -típus adatkészlete továbbra is támogatott, ha a visszamenőleges kompatibilitás érdekében a másolási, a keresési vagy a GetMetaData tevékenység van. Azt javasoljuk, hogy ezt az új modellt fogja használni, és az ADF szerzői felhasználói felülete átváltott az új típusok létrehozásához.

**Példa:**

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

### <a name="other-format-dataset"></a>Egyéb formátumú adatkészlet

Az adatok Google Cloud Storage-ból az **ork formátumba**való másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **AmazonS3Object** |Igen |
| bucketName | Az S3 gyűjtő neve. Helyettesítő karaktert tartalmazó szűrő nem támogatott. |Igen, a Másolás/keresési tevékenység, GetMetadata tevékenység esetében: nem |
| key | A **nevét vagy helyettesítő karaktert tartalmazó szűrő** S3 objektum kulcs alatt a megadott gyűjtőjét. Érvényes, csak ha "előtag" tulajdonság nincs megadva. <br/><br/>A helyettesítő karakteres szűrő a mappa és a fájlnév résznél is támogatott. A helyettesítő karakterek engedélyezettek: `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter).<br/>-1. példa: `"key": "rootfolder/subfolder/*.csv"`<br/>– 2. példa: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. A `^` (z) használatával elkerülheti a mappát, ha a tényleges mappája/fájlneve helyettesítő karakterrel rendelkezik, vagy a menekülési karakter a belsejében |Nem |
| prefix | Az S3-objektum kulcs előtag. Ezzel az előtaggal start amelynek kulcsok objektum van kijelölve. Csak akkor, ha a "key" tulajdonság nincs megadva érvényes. |Nem |
| version | Az S3-objektum, ha engedélyezve van a S3 versioning verziója. |Nem |
| modifiedDatetimeStart | A fájlok szűrése az attribútum alapján: Utolsó módosítás. A fájlok lesz kiválasztva, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br/><br/> A Tulajdonságok lehet null értékű, ami jelenti azt, hogy nincs fájlszűrő attribútum alkalmazandó az adatkészletet.  Amikor `modifiedDatetimeStart` dátum és idő értékkel rendelkezik, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a dátum és idő értékkel lesz kiválasztva.  Amikor `modifiedDatetimeEnd` dátum és idő értékkel rendelkezik, de `modifiedDatetimeStart` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke kisebb, mint a dátum/idő értéket fog jelölni.| Nem |
| modifiedDatetimeEnd | A fájlok szűrése az attribútum alapján: Utolsó módosítás. A fájlok lesz kiválasztva, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br/><br/> A Tulajdonságok lehet null értékű, ami jelenti azt, hogy nincs fájlszűrő attribútum alkalmazandó az adatkészletet.  Amikor `modifiedDatetimeStart` dátum és idő értékkel rendelkezik, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a dátum és idő értékkel lesz kiválasztva.  Amikor `modifiedDatetimeEnd` dátum és idő értékkel rendelkezik, de `modifiedDatetimeStart` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke kisebb, mint a dátum/idő értéket fog jelölni.| Nem |
| format | Ha azt szeretné, hogy **, a fájlok másolása a-rendszer** közötti fájlalapú tárolók (bináris másolat), hagyja ki a format szakaszban mindkét bemeneti és kimeneti adatkészlet-definíciókban.<br/><br/>Ha a fájlokat egy adott formátummal szeretné elemezni vagy előállítani, a következő fájlformátum-típusok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt formátumot az alábbi értékek egyikére. További információkért lásd: [szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), [Json formátumban](supported-file-formats-and-compression-codecs.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátum](supported-file-formats-and-compression-codecs.md#orc-format), és [Parquetformátum](supported-file-formats-and-compression-codecs.md#parquet-format) szakaszokat. |Nem (csak a bináris másolás esetén) |
| compression | Adja meg a típus és az adatok tömörítési szintje. További információkért lásd: [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>A támogatott típusok a következők: **Gzip**, **deflate**, **BZip2**és **ZipDeflate**.<br/>A támogatott szintek a következők: **Optimális** és **leggyorsabb**. |Nem |

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

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Google Cloud Storage-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="google-cloud-storage-as-source"></a>Google Cloud Storage forrásként

- A parketta, a **tagolt szöveg, a JSON, a Avro és a bináris formátum**másolásához tekintse meg a [parketta, a tagolt szöveg, a JSON, a Avro és a bináris formátum forrás](#format-based-source) szakaszát.
- Más formátumokból, például az **ork formátumból**való másoláshoz tekintse meg a [más formátumú forrás](#other-format-source) szakaszt.

#### <a name="format-based-source"></a>Parketta, tagolt szöveg, JSON, Avro és bináris formátum forrása

Ha a **parketta, a tagolt szöveg, a JSON, a Avro és a bináris formátum**adatait szeretné átmásolni, tekintse meg a [parketta formátumát](format-parquet.md), a [tagolt szöveg formátumát](format-delimited-text.md), a [Avro formátumát](format-avro.md) és a [bináris formátumú](format-binary.md) cikket a Format-alapú másolási tevékenység forrásáról beállítások. A Google Cloud Storage a következő tulajdonságokat támogatja a Format `storeSettings` -alapú másolási forrás beállításai alatt:

| Tulajdonság                 | Leírás                                                  | Szükséges                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | A Type tulajdonságot `storeSettings` a **AmazonS3ReadSetting**értékre kell állítani. | Igen                                                         |
| recursive                | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. Vegye figyelembe, hogy ha a rekurzív értéke igaz, és a fogadó a fájlalapú tároló, egy üres mappát vagy almappát nem másolja vagy létrehozott, a fogadó. Engedélyezett értékek a következők **igaz** (alapértelmezett), és **hamis**. | Nem                                                          |
| prefix                   | A forrásoldali objektumok szűréséhez az adatkészletben konfigurált megadott gyűjtő alatt található S3-objektumhoz tartozó előtag. Ezzel az előtaggal start amelynek kulcsok objektum van kijelölve. Csak akkor érvényes `wildcardFolderPath` , `wildcardFileName` ha a tulajdonságok nincsenek megadva. |                                                             |
| wildcardFolderPath       | A mappa elérési útja a forrás mappák szűréséhez az adatkészletben konfigurált megadott gyűjtőben helyettesítő karakterekkel. <br>Az engedélyezett helyettesítő karakterek a `*` következők: (nulla vagy több karakternek `?` felel meg) és (a nulla vagy `^` egy karakter egyezése) <br>További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Nem                                                          |
| wildcardFileName         | A forrásfájl szűréséhez a megadott gyűjtő + folderPath/wildcardFolderPath helyettesítő karaktereket tartalmazó fájlnév. <br>Az engedélyezett helyettesítő karakterek a `*` következők: (nulla vagy több karakternek `?` felel meg) és (a nulla vagy `^` egy karakter egyezése)  További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Igen, `fileName` ha az adatkészletben, és `prefix` nincsenek megadva |
| modifiedDatetimeStart    | A fájlok szűrése az attribútum alapján: Utolsó módosítás. A fájlok lesz kiválasztva, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br> A Tulajdonságok lehet null értékű, ami jelenti azt, hogy nincs fájlszűrő attribútum alkalmazandó az adatkészletet.  Amikor `modifiedDatetimeStart` dátum és idő értékkel rendelkezik, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a dátum és idő értékkel lesz kiválasztva.  Amikor `modifiedDatetimeEnd` dátum és idő értékkel rendelkezik, de `modifiedDatetimeStart` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke kisebb, mint a dátum/idő értéket fog jelölni. | Nem                                                          |
| modifiedDatetimeEnd      | Ugyanaz, mint a fenti.                                               | Nem                                                          |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem                                                          |

> [!NOTE]
> A Parquet/tagolt szöveg formátuma esetén a következő szakaszban említett, **FileSystemSource** típusú másolási tevékenység továbbra is támogatott a visszafelé kompatibilitás érdekében. Azt javasoljuk, hogy ezt az új modellt fogja használni, és az ADF szerzői felhasználói felülete átváltott az új típusok létrehozásához.

**Példa:**

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

#### <a name="other-format-source"></a>Egyéb formátum forrása

Az adatok Google Cloud Storage-ból az **ork formátumban**való másolásához a következő tulajdonságok támogatottak a másolási tevékenység **forrása** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **FileSystemSource** |Igen |
| recursive | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappákban vagy csak a megadott mappába. Megjegyzés: Ha a rekurzív értéke igaz, és a fogadó fájlalapú tároló, üres mappa/alárendelt-folder nem lesz másolva vagy hozható létre, a fogadó.<br/>Engedélyezett értékek a következők: **igaz** (alapértelmezett), **false (hamis)** | Nem |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem |

**Példa:**

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

### <a name="folder-and-file-filter-examples"></a>Példák a mappák és a fájlok szűrésére

Ez a szakasz a mappa elérési útjának és fájlnevének a helyettesítő karakteres szűrőkkel való viselkedését írja le.

| bucket | key | recursive | A forrás mappa szerkezete és a szűrő eredménye (a félkövérrel szedett fájlok beolvasása)|
|:--- |:--- |:--- |:--- |
| bucket | `Folder*/*` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="next-steps"></a>További lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listájáért lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).

---
title: Adatok másolása az Azure File Storageba a Azure Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokból az Azure File Storageról a támogatott forrás-adattárakból származó, az Azure-ba File Storage támogatott fogadó adattárba (vagy) Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: d20f6c3239ce5976391eb6ae77637017e59cd9a4
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931142"
---
# <a name="copy-data-from-or-to-azure-file-storage-by-using-azure-data-factory"></a>Adatok másolása az Azure File Storageba vagy az Azure Data Factory használatával

Ez a cikk azt ismerteti, hogyan másolhatók az adatok az Azure File Storageba és onnan. A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure File Storage-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)

Ez az Azure File Storage-összekötő támogatja a fájlok másolását, illetve a [támogatott fájlformátumokat és tömörítési kodekeket](supported-file-formats-and-compression-codecs.md)tartalmazó fájlok elemzését vagy generálását.

## <a name="getting-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek az Azure-File Storagera jellemző Data Factory-entitások definiálására szolgálnak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Azure File Storage társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **AzureFileStorage**. | Igen |
| gazdagép | Az Azure File Storage végpontot adja meg a következőképpen: <br/>-Felhasználói felület használata: `\\<storage name>.file.core.windows.net\<file service name>` megadását<br/>-JSON használata: `"host": "\\\\<storage name>.file.core.windows.net\\<file service name>"`. | Igen |
| userid | Az Azure File Storage eléréséhez a következőt kell megadnia: <br/>-Felhasználói felület használata: `AZURE\<storage name>` megadását<br/>-JSON használata: `"userid": "AZURE\\<storage name>"`. | Igen |
| jelszó | A tárolási hozzáférési kulcs meghatározása. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . Használhat Azure Integration Runtime vagy saját üzemeltetésű Integration Runtime (ha az adattár a magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |Nem, forrás, igen, fogadó |

>[!IMPORTANT]
> - Az Azure File Storageba Azure Integration Runtime használatával történő adatmásoláshoz explicit módon [hozzon létre egy Azure IRt](create-azure-integration-runtime.md#create-azure-ir) a file Storage helyével, és társítsa a társított szolgáltatáshoz a következő példaként.
> - Ha az Azure Integration Runtime-on kívülről az Azure-File Storage szeretné másolni az adatait az Azure-on kívülről, ne feledje, hogy a helyi hálózaton a 445-as kimenő TCP-portot nyitja meg.

>[!TIP]
>Ha az ADF felhasználói felületét használja a szerzői műveletekhez, a társított szolgáltatás létrehozásához tartozó "Azure File Storage" adott bejegyzést megtalálja, amely alatt a Type `FileServer` objektum generálása szerepel.

**Példa**

```json
{
    "name": "AzureFileStorageLinkedService",
    "properties": {
        "type": "AzureFileStorage",
        "typeProperties": {
            "host": "\\\\<storage name>.file.core.windows.net\\<file service name>",
            "userid": "AZURE\\<storage name>",
            "password": {
                "type": "SecureString",
                "value": "<storage access key>"
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

A következő tulajdonságok támogatottak az Azure File Storage számára a Format-alapú adatkészlet `location` beállításai területen:

| Tulajdonság   | Leírás                                                  | Szükséges |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Az adatkészletben található `location` típus tulajdonságot **FileServerLocation**értékre kell állítani. | Igen      |
| folderPath | A mappa elérési útja. Ha a mappa szűréséhez helyettesítő karaktert szeretne használni, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításai között válassza a lehetőséget. | Nem       |
| fileName   | A fájlnév a megadott folderPath alatt. Ha helyettesítő karaktereket szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításai között válassza a lehetőséget. | Nem       |

**Példa**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure File Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "FileServerLocation",
                "folderPath": "root/folder/subfolder"
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
>A következő adatkészlet-modell továbbra is támogatott, ha visszafelé kompatibilis. Javasoljuk, hogy használja a fenti szakaszban említett új modellt, és az ADF authoring felhasználói felülete átvált az új modell generálására.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **fájlmegosztás** |Igen |
| folderPath | A mappa elérési útja. <br/><br/>A helyettesítő karakteres szűrő támogatott, az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egy karakternek felel meg); a `^` használatával elkerülheti, hogy a tényleges mappanév helyettesítő karakterrel vagy a menekülési karakterrel rendelkezik-e a belsejében. <br/><br/>Példák: gyökérmappa/almappa/, további példák a [mappák és a fájlok szűrése példákban](#folder-and-file-filter-examples). |Igen |
| fileName | A fájl (ok) **neve vagy helyettesítő szűrője** a megadott "folderPath". Ha nem ad meg értéket ehhez a tulajdonsághoz, az adatkészlet a mappában található összes fájlra mutat. <br/><br/>A Filter (szűrő) esetében az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egy karakternek felel meg).<br/>-1. példa: `"fileName": "*.csv"`<br/>-2. példa: `"fileName": "???20180427.txt"`<br/>A `^` használatával elkerülheti, ha a tényleges fájlnév helyettesítő karakterrel vagy a menekülési karakterrel rendelkezik.<br/><br/>Ha nincs megadva fájlnév a kimeneti adatkészlethez, és a **preserveHierarchy** nincs megadva a tevékenység fogadójában, a másolási tevékenység automatikusan létrehozza a fájlnevet a következő mintával: "*adat. [ tevékenység futtatási azonosítója GUID]. [GUID if FlattenHierarchy]. [formátum, ha konfigurálva]. [tömörítés, ha be van állítva]* ", például" adatfájl. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz "; Ha táblázatos forrásból másol a lekérdezés helyett táblanév használatával, a név minta a következő: " *[Table Name]. [ Format]. [tömörítés, ha konfigurálva]* ", például" sajáttábla. csv ". |Nem |
| modifiedDatetimeStart | A fájlok szűrése a következő attribútum alapján: utoljára módosítva. A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a `modifiedDatetimeStart` és `modifiedDatetimeEnd`közötti időtartományon belül van. Az idő az UTC-időzónára vonatkozik "2018-12-01T05:00:00Z" formátumban. <br/><br/> Ügyeljen arra, hogy az adatáthelyezés általános teljesítményét a beállítás engedélyezésével befolyásolja, ha nagy mennyiségű fájlból szeretne szűrni a fájlmegosztást. <br/><br/> A tulajdonságok értéke NULL lehet, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha `modifiedDatetimeStart` dátum datetime értékkel rendelkezik, de a `modifiedDatetimeEnd` NULL értékű, akkor azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik, de a `modifiedDatetimeStart` NULL értékű, akkor azokat a fájlokat jelenti, amelyek utolsó módosítási attribútuma kisebb, mint a DateTime érték.| Nem |
| modifiedDatetimeEnd | A fájlok szűrése a következő attribútum alapján: utoljára módosítva. A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a `modifiedDatetimeStart` és `modifiedDatetimeEnd`közötti időtartományon belül van. Az idő az UTC-időzónára vonatkozik "2018-12-01T05:00:00Z" formátumban. <br/><br/> Ügyeljen arra, hogy az adatáthelyezés általános teljesítményét a beállítás engedélyezésével befolyásolja, ha nagy mennyiségű fájlból szeretne szűrni a fájlmegosztást. <br/><br/> A tulajdonságok értéke NULL lehet, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha `modifiedDatetimeStart` dátum datetime értékkel rendelkezik, de a `modifiedDatetimeEnd` NULL értékű, akkor azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik, de a `modifiedDatetimeStart` NULL értékű, akkor azokat a fájlokat jelenti, amelyek utolsó módosítási attribútuma kisebb, mint a DateTime érték.| Nem |
| formátumban | Ha **fájlokat szeretne másolni** a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt mind a bemeneti, mind a kimeneti adatkészlet-definíciókban.<br/><br/>Ha a fájlokat egy adott formátummal szeretné elemezni vagy előállítani, a következő fájlformátum-típusok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a Format értékre a következő értékek egyikére. További információkért lásd: [Szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), JSON- [Formátum](supported-file-formats-and-compression-codecs.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [ork-formátum](supported-file-formats-and-compression-codecs.md#orc-format)és a [parketta formátuma](supported-file-formats-and-compression-codecs.md#parquet-format) című rész. |Nem (csak bináris másolási forgatókönyv esetén) |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. További információ: [támogatott fájlformátumok és tömörítési kodekek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>A támogatott típusok a következők: **gzip**, **deflate**, **BZip2**és **ZipDeflate**.<br/>A támogatott szintek a következők: **optimális** és **leggyorsabb**. |Nem |

>[!TIP]
>Egy mappa összes fájljának másolásához csak a **folderPath** kell megadni.<br>Egy adott névvel rendelkező egyetlen fájl másolásához adja meg a **folderPath** és a fájlnév nevű **fájlnevet** .<br>Ha egy mappában lévő fájlok egy részhalmazát szeretné másolni, akkor a **folderPath** és a **filename** paramétert a helyettesítő karakteres szűrővel.

>[!NOTE]
>Ha a "fileFilter" tulajdonságot használta a fájl szűrőhöz, a rendszer továbbra is támogatja a-t, míg a rendszer a "fileName" kifejezéshez hozzáadott új szűrő funkció használatát javasolja.

**Példa**

```json
{
    "name": "AzureFileStorageDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<Azure File Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
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

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz az Azure File Storage forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-file-storage-as-source"></a>Azure-File Storage forrásként

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok támogatottak az Azure-File Storage a Format-alapú másolási forrás `storeSettings` beállításai alatt:

| Tulajdonság                 | Leírás                                                  | Szükséges                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A `storeSettings` alatti Type tulajdonságot **FileServerReadSetting**értékre kell beállítani. | Igen                                           |
| rekurzív                | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. Vegye figyelembe, hogy ha a rekurzív értéke TRUE (igaz), a fogadó pedig egy fájl alapú tároló, a fogadó nem másolja vagy hozza létre az üres mappát vagy almappát. Az engedélyezett értékek: **true** (alapértelmezett) és **false (hamis**). | Nem                                            |
| wildcardFolderPath       | A mappa elérési útja helyettesítő karakterekkel a forrás mappák szűréséhez. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egyetlen karakternek felel meg); a `^` használatával elkerülheti, hogy a tényleges mappanév helyettesítő karakterrel vagy a menekülési karakterrel rendelkezik-e a belsejében. <br>További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Nem                                            |
| wildcardFileName         | A forrásfájl szűréséhez a megadott folderPath/wildcardFolderPath helyettesítő karaktereket tartalmazó fájlnév. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egyetlen karakternek felel meg); a `^` használatával elkerülheti, hogy a tényleges mappanév helyettesítő karakterrel vagy a menekülési karakterrel rendelkezik-e a belsejében.  További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Igen, ha a `fileName` nincs megadva az adatkészletben |
| modifiedDatetimeStart    | A fájlok szűrése a következő attribútum alapján: utoljára módosítva. A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a `modifiedDatetimeStart` és `modifiedDatetimeEnd`közötti időtartományon belül van. Az idő az UTC-időzónára vonatkozik "2018-12-01T05:00:00Z" formátumban. <br> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha `modifiedDatetimeStart` dátum datetime értékkel rendelkezik, de a `modifiedDatetimeEnd` NULL értékű, akkor azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik, de a `modifiedDatetimeStart` NULL értékű, akkor azokat a fájlokat jelenti, amelyek utolsó módosítási attribútuma kisebb, mint a DateTime érték. | Nem                                            |
| modifiedDatetimeEnd      | Ugyanaz, mint a fenti.                                               | Nem                                            |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem                                            |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromAzureFileStorage",
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
                    "type": "FileServerReadSetting",
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
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure File Storage input dataset name>",
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

### <a name="azure-file-storage-as-sink"></a>Azure-File Storage fogadóként

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok támogatottak az Azure-File Storage számára a Format-alapú másolási fogadó `storeSettings` beállításainál:

| Tulajdonság                 | Leírás                                                  | Szükséges |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A `storeSettings` alatti Type tulajdonságot **FileServerWriteSetting**értékre kell beállítani. | Igen      |
| copyBehavior             | Meghatározza a másolási viselkedést, ha a forrás fájl-alapú adattárból származó fájlok.<br/><br/>Az engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: megőrzi a fájl-hierarchiát a célmappában. A forrásfájl a forrás mappájához relatív elérési útja megegyezik a célfájl relatív elérési útjával.<br/><b>-FlattenHierarchy</b>: a forrás mappából származó összes fájl a célmappa első szintjén van. A célként megadott fájlok automatikusan generált névvel rendelkeznek. <br/><b>-MergeFiles</b>: az összes fájlt egyesíti a forrás mappájából egy fájlba. Ha meg van adva a fájl neve, az egyesített fájl neve a megadott név. Ellenkező esetben ez egy automatikusan létrehozott fájl neve. | Nem       |
| maxConcurrentConnections | Az adattárhoz való kapcsolódáshoz szükséges kapcsolatok száma. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem       |

**Példa**

```json
"activities":[
    {
        "name": "CopyToAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "FileServerWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="legacy-sink-model"></a>Örökölt fogadó modell

>[!NOTE]
>A következő másolási fogadó modell továbbra is támogatott a visszamenőleges kompatibilitás érdekében. Azt javasoljuk, hogy használja a fentiekben említett új modellt, és az ADF authoring felhasználói felülete átvált az új modell generálására.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának Type tulajdonságát a következőre kell beállítani: **FileSystemSink** |Igen |
| copyBehavior | Meghatározza a másolási viselkedést, ha a forrás fájl-alapú adattárból származó fájlok.<br/><br/>Az engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: megőrzi a fájl-hierarchiát a célmappában. A forrásfájl a forrás mappájához relatív elérési útja megegyezik a célfájl relatív elérési útjával.<br/><b>-FlattenHierarchy</b>: a forrás mappából származó összes fájl a célmappa első szintjén van. A célfájl automatikusan generált névvel rendelkezik. <br/><b>-MergeFiles</b>: az összes fájlt egyesíti a forrás mappájából egy fájlba. Ha a fájl neve meg van adva, az egyesített fájlnév a megadott név lesz. Ellenkező esetben az automatikusan létrehozott fájlnév lenne. | Nem |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyToAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure File Storage output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "FileSystemSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Példák a mappák és a fájlok szűrésére

Ez a szakasz a mappa elérési útjának és fájlnevének a helyettesítő karakteres szűrőkkel való viselkedését írja le.

| folderPath | fileName | rekurzív | A forrás mappa szerkezete és a szűrő eredménye (a **félkövérrel szedett** fájlok beolvasása)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (üres, alapértelmezett használata) | hamis | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**fájl2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | (üres, alapértelmezett használata) | igaz | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**fájl2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | hamis | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | igaz | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="recursive-and-copybehavior-examples"></a>rekurzív és copyBehavior példák

Ez a szakasz a rekurzív és copyBehavior értékek különböző kombinációinak másolási műveletének eredményét írja le.

| rekurzív | copyBehavior | Forrás mappa szerkezete | Eredményül kapott cél |
|:--- |:--- |:--- |:--- |
| igaz |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célmappa Mappa1 ugyanazzal a struktúrával jön létre, mint a forrás:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| igaz |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célként megadott Mappa1 a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a File5 |
| igaz |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célként megadott Mappa1 a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1 + Fájl2 + fájl3 + File4 + file 5 tartalom egyetlen fájlba van egyesítve, automatikusan létrehozott fájlnévvel |
| hamis |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célmappa Mappa1 a következő szerkezettel jön létre<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem vesznek fel. |
| hamis |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célmappa Mappa1 a következő szerkezettel jön létre<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a Fájl2<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem vesznek fel. |
| hamis |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célmappa Mappa1 a következő szerkezettel jön létre<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1 + Fájl2 tartalma egyetlen fájlba van egyesítve, amely automatikusan létrehozott fájlnevet tartalmaz. a file1 automatikusan generált neve<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem vesznek fel. |

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [GetMetaData tevékenységet](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Tevékenység tulajdonságainak törlése

A tulajdonságok részleteinek megismeréséhez tekintse meg a [tevékenység törlése](delete-activity.md) lehetőséget.

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).

---
title: Adatok másolása a fájlrendszerből a Azure Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan másolhat a fájlrendszerből származó adatokból támogatott fogadó adattárakba (vagy) a támogatott forrás-adattárakból a fájlrendszerbe Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: b22242ac617f8dca95941a489c38658f5721851a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387289"
---
# <a name="copy-data-to-or-from-a-file-system-by-using-azure-data-factory"></a>Adatok másolása fájlrendszerből vagy rendszerbe a Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-onprem-file-system-connector.md)
> * [Aktuális verzió](connector-file-system.md)

Ez a cikk azt ismerteti, hogyan másolhatók az adatok a fájlrendszerbe. A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a fájlrendszer-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)

A fájlrendszer-összekötő különösen a következőket támogatja:

- Fájlok másolása helyi gépre vagy hálózati fájlmegosztásba. Linux-fájlmegosztás használatához telepítse a [Samba](https://www.samba.org/) -t a Linux-kiszolgálóra.
- Fájlok másolása **Windows** -hitelesítéssel.
- Fájlok másolása a-ként vagy a fájlok elemzése/létrehozása a [támogatott fájlformátumokkal és tömörítési kodekekkel](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek a fájlrendszerre vonatkozó Data Factory-entitások definiálására szolgálnak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A fájlrendszerhez társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **fileserver**. | Igen |
| gazdagép | Megadja a másolni kívánt mappa gyökerének elérési útját. Használja a "\"" Escape-karaktert a karakterlánc speciális karaktereinek számára. Példákat a következő témakörben talál: példa [társított szolgáltatás és adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) . | Igen |
| userid | Itt adhatja meg annak a felhasználónak az AZONOSÍTÓját, aki hozzáfér a kiszolgálóhoz. | Igen |
| jelszó | A felhasználó jelszavának megadása (userid). Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |Nem |

### <a name="sample-linked-service-and-dataset-definitions"></a>Példa társított szolgáltatás és adatkészlet-definíciók

| Alkalmazási helyzet | "host" a társított szolgáltatás definíciójában | "folderPath" az adatkészlet definíciójában |
|:--- |:--- |:--- |
| Helyi mappa Integration Runtime gépen: <br/><br/>Példák: D: \\ @ no__t-1 vagy D:\folder\subfolder @ no__t-2 @ no__t-3 |JSON-ban: `D:\\`<br/>Felhasználói felületen: `D:\` |JSON-ban: `.\\` vagy `folder\\subfolder`<br>Felhasználói felületen: `.\` vagy `folder\subfolder` |
| Távoli megosztott mappa: <br/><br/>Példák: \\ @ no__t-1myserver @ no__t-2share @ no__t-3 @ no__t-4 vagy \\ @ no__t-6myserver @ no__t-7share @ no__t-8folder @ no__t-9subfolder @ no__t-10 @ no__t-11 |JSON-ban: `\\\\myserver\\share`<br/>Felhasználói felületen: `\\myserver\share` |JSON-ban: `.\\` vagy `folder\\subfolder`<br/>Felhasználói felületen: `.\` vagy `folder\subfolder` |

>[!NOTE]
>Felhasználói felületen keresztüli szerzői műveletek esetén nem kell megadnia a dupla fordított perjelet (`\\`) a JSON-on keresztüli Escape-művelethez, adjon meg egy fordított perjelet.

**Példa**

```json
{
    "name": "FileLinkedService",
    "properties": {
        "type": "FileServer",
        "typeProperties": {
            "host": "<host>",
            "userid": "<domain>\\<user>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

- A **Parquet, a tagolt szöveget, a JSON-t, a Avro és a bináris formátumot**a [parketta, a tagolt szöveg, a JSON, a Avro és a bináris formátum adatkészlet](#format-based-dataset) szakaszban találja.
- Más formátumok, például az **ork formátum**esetében tekintse meg a [más formátumú adatkészlet](#other-format-dataset) szakaszt.

### <a name="format-based-dataset"></a>Parketta, tagolt szöveg, JSON, Avro és bináris formátumú adatkészlet

Ha a **parketta, a tagolt szöveg, a JSON, a Avro és a bináris formátum**adatait szeretné másolni, tekintse meg a [parketta formátumát](format-parquet.md), a [tagolt szöveg formátumát](format-delimited-text.md), a [Avro formátumát](format-avro.md) és a [bináris formátumú](format-binary.md) cikket a Format-alapú adatkészlet és a támogatott beállítások alapján. . A fájlrendszerben a következő tulajdonságok támogatottak `location` beállításokban a Format-alapú adatkészletben:

| Tulajdonság   | Leírás                                                  | Szükséges |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Az adatkészletben található `location` típus tulajdonságot **FileServerLocation**értékre kell állítani. | Igen      |
| folderPath | A mappa elérési útja. Ha a mappa szűréséhez helyettesítő karaktert szeretne használni, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításai között válassza a lehetőséget. | Nem       |
| fileName   | A fájlnév a megadott folderPath alatt. Ha helyettesítő karaktereket szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításai között válassza a lehetőséget. | Nem       |

> [!NOTE]
> A következő szakaszban említett, a Parquet/Text formátumot tartalmazó **fájlmegosztás** -típus adatkészlete továbbra is támogatott a visszamenőleges kompatibilitás érdekében a másolási/keresési/GetMetaData tevékenységek esetében, de nem működik a leképezési adatfolyammal. Azt javasoljuk, hogy ezt az új modellt fogja használni, és az ADF szerzői felhasználói felülete átváltott az új típusok létrehozásához.

**Példa**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<File system linked service name>",
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

### <a name="other-format-dataset"></a>Egyéb formátumú adatkészlet

Az adatoknak az **ork formátumba**és a fájlrendszerbe való másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **fájlmegosztás** |Igen |
| folderPath | A mappa elérési útja. A helyettesítő karakteres szűrő támogatott, az engedélyezett helyettesítő karakterek a következők: `*` (a nulla vagy több karakternek felel meg) és a `?` (a nulla vagy egy karakternek felel meg); a `^` paranccsal elkerülheti, ha a tényleges mappanév helyettesítő karakterrel vagy a menekülési karakterrel rendelkezik. <br/><br/>Példák: gyökérmappa/almappa/, további példák a példaként szolgáló [társított szolgáltatás és adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) , valamint a [mappa-és fájlszűrő-példák](#folder-and-file-filter-examples)elemre. |Nem |
| fileName | A fájl (ok) **neve vagy helyettesítő szűrője** a megadott "folderPath". Ha nem ad meg értéket ehhez a tulajdonsághoz, az adatkészlet a mappában található összes fájlra mutat. <br/><br/>A Filter (szűrő) esetében az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egy karakternek felel meg).<br/>-1. példa: `"fileName": "*.csv"`<br/>-2. példa: `"fileName": "???20180427.txt"`<br/>Ha a tényleges fájlnév helyettesítő karakterrel rendelkezik, vagy a menekülési karakter szerepel a következőben, használja a `^` karaktert.<br/><br/>Ha nincs megadva fájlnév a kimeneti adatkészlethez, és a **preserveHierarchy** nincs megadva a tevékenység fogadójában, a másolási tevékenység automatikusan létrehozza a fájlnevet a következő mintával: "*adat. [ tevékenység futtatási azonosítója GUID]. [GUID if FlattenHierarchy]. [formátum, ha konfigurálva]. [tömörítés, ha be van állítva]* ", például" adatfájl. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz "; Ha táblázatos forrásból másol a lekérdezés helyett táblanév használatával, a név minta a következő: " *[Table Name]. [ Format]. [tömörítés, ha konfigurálva]* ", például" sajáttábla. csv ". |Nem |
| modifiedDatetimeStart | A fájlok szűrése a következő attribútum alapján: utoljára módosítva. A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja @no__t – 0 és `modifiedDatetimeEnd` közötti időtartományon belül van. Az idő az UTC-időzónára vonatkozik "2018-12-01T05:00:00Z" formátumban. <br/><br/> Ügyeljen arra, hogy az adatáthelyezés általános teljesítményét a beállítás engedélyezésével befolyásolja, ha nagy mennyiségű fájlból szeretne szűrni a fájlmegosztást. <br/><br/> A tulajdonságok értéke NULL lehet, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha `modifiedDatetimeStart` dátum datetime értékkel rendelkezik, de a `modifiedDatetimeEnd` NULL értékű, akkor azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik, de a `modifiedDatetimeStart` NULL értékű, akkor azokat a fájlokat jelenti, amelyek utolsó módosítási attribútuma kisebb, mint a DateTime érték.| Nem |
| modifiedDatetimeEnd | A fájlok szűrése a következő attribútum alapján: utoljára módosítva. A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja @no__t – 0 és `modifiedDatetimeEnd` közötti időtartományon belül van. Az idő az UTC-időzónára vonatkozik "2018-12-01T05:00:00Z" formátumban. <br/><br/> Ügyeljen arra, hogy az adatáthelyezés általános teljesítményét a beállítás engedélyezésével befolyásolja, ha nagy mennyiségű fájlból szeretne szűrni a fájlmegosztást. <br/><br/> A tulajdonságok értéke NULL lehet, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha `modifiedDatetimeStart` dátum datetime értékkel rendelkezik, de a `modifiedDatetimeEnd` NULL értékű, akkor azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik, de a `modifiedDatetimeStart` NULL értékű, akkor azokat a fájlokat jelenti, amelyek utolsó módosítási attribútuma kisebb, mint a DateTime érték.| Nem |
| formátumban | Ha **fájlokat szeretne másolni** a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt mind a bemeneti, mind a kimeneti adatkészlet-definíciókban.<br/><br/>Ha a fájlokat egy adott formátummal szeretné elemezni vagy előállítani, a következő fájlformátum-típusok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a Format értékre a következő értékek egyikére. További információkért lásd: [Szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), JSON- [Formátum](supported-file-formats-and-compression-codecs.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [ork-formátum](supported-file-formats-and-compression-codecs.md#orc-format)és a [parketta formátuma](supported-file-formats-and-compression-codecs.md#parquet-format) című rész. |Nem (csak bináris másolási forgatókönyv esetén) |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. További információ: [támogatott fájlformátumok és tömörítési kodekek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>A támogatott típusok a következők: **gzip**, **deflate**, **BZip2**és **ZipDeflate**.<br/>A támogatott szintek a következők: **optimális** és **leggyorsabb**. |Nem |

>[!TIP]
>Egy mappa összes fájljának másolásához csak a **folderPath** kell megadni.<br>Egy adott névvel rendelkező egyetlen fájl másolásához adja meg a **folderPath** és a fájlnév nevű **fájlnevet** .<br>Ha egy mappában lévő fájlok egy részhalmazát szeretné másolni, akkor a **folderPath** és a **filename** paramétert a helyettesítő karakteres szűrővel.

>[!NOTE]
>Ha a "fileFilter" tulajdonságot használta a fájl szűrőhöz, a rendszer továbbra is támogatja a-t, míg a rendszer a "fileName" kifejezéshez hozzáadott új szűrő funkció használatát javasolja.

**Példa**

```json
{
    "name": "FileSystemDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<file system linked service name>",
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

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a fájlrendszer forrása és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="file-system-as-source"></a>Fájlrendszer forrásként

- A parketta, a **tagolt szöveg, a JSON, a Avro és a bináris formátum**másolásához tekintse meg a [parketta, a tagolt szöveg, a JSON, a Avro és a bináris formátum forrás](#format-based-source) szakaszát.
- Más formátumokból, például az **ork formátumból**való másoláshoz tekintse meg a [más formátumú forrás](#other-format-source) szakaszt.

#### <a name="format-based-source"></a>Parketta, tagolt szöveg, JSON, Avro és bináris formátum forrása

Ha a **parketta, a tagolt szöveg, a JSON, a Avro és a bináris formátum**adatait szeretné átmásolni, tekintse meg a [parketta formátumát](format-parquet.md), a [tagolt szöveg formátumát](format-delimited-text.md), a [Avro formátumát](format-avro.md) és a [bináris formátumú](format-binary.md) cikket a Format-alapú másolási tevékenység forrásáról beállítások. A fájlrendszer a következő tulajdonságokat támogatja a `storeSettings` beállításokban a Format-alapú másolási forrásban:

| Tulajdonság                 | Leírás                                                  | Szükséges                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A `storeSettings` alatti Type tulajdonságot **FileServerReadSetting**értékre kell beállítani. | Igen                                           |
| rekurzív                | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. Vegye figyelembe, hogy ha a rekurzív értéke TRUE (igaz), a fogadó pedig egy fájl alapú tároló, a fogadó nem másolja vagy hozza létre az üres mappát vagy almappát. Az engedélyezett értékek: **true** (alapértelmezett) és **false (hamis**). | Nem                                            |
| wildcardFolderPath       | A mappa elérési útja helyettesítő karakterekkel a forrás mappák szűréséhez. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egy karakternek felel meg); a `^` paranccsal elkerülheti, ha a tényleges mappanév helyettesítő karakterrel vagy a menekülési karakterrel rendelkezik. <br>További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Nem                                            |
| wildcardFileName         | A forrásfájl szűréséhez a megadott folderPath/wildcardFolderPath helyettesítő karaktereket tartalmazó fájlnév. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egy karakternek felel meg); a `^` paranccsal elkerülheti, ha a tényleges mappanév helyettesítő karakterrel vagy a menekülési karakterrel rendelkezik.  További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Igen, ha a `fileName` nincs megadva az adatkészletben |
| modifiedDatetimeStart    | A fájlok szűrése a következő attribútum alapján: utoljára módosítva. A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja @no__t – 0 és `modifiedDatetimeEnd` közötti időtartományon belül van. Az idő az UTC-időzónára vonatkozik "2018-12-01T05:00:00Z" formátumban. <br> A tulajdonságok értéke lehet NULL, ami azt jelenti, hogy nem lesz alkalmazva a file Attribute szűrő az adatkészletre.  Ha `modifiedDatetimeStart` dátum datetime értékkel rendelkezik, de a `modifiedDatetimeEnd` NULL értékű, akkor azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik, de a `modifiedDatetimeStart` NULL értékű, akkor azokat a fájlokat jelenti, amelyek utolsó módosítási attribútuma kisebb, mint a DateTime érték. | Nem                                            |
| modifiedDatetimeEnd      | Ugyanaz, mint a fenti.                                               | Nem                                            |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem                                            |

> [!NOTE]
> A Parquet/tagolt szöveg formátuma esetén a következő szakaszban említett, **FileSystemSource** típusú másolási tevékenység továbbra is támogatott a visszafelé kompatibilitás érdekében. Azt javasoljuk, hogy ezt az új modellt fogja használni, és az ADF szerzői felhasználói felülete átváltott az új típusok létrehozásához.

**Példa**

```json
"activities":[
    {
        "name": "CopyFromFileSystem",
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

#### <a name="other-format-source"></a>Egyéb formátum forrása

Ha az adatok a fájlrendszerből az **ork formátumban**másolhatók, a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **FileSystemSource** |Igen |
| rekurzív | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az alárendelt mappákból, vagy csak a megadott mappából. Vegye figyelembe, hogy ha a rekurzív értéke TRUE (igaz), a fogadó pedig a fájl alapú tároló, akkor a rendszer nem másolja/hozza létre az üres mappát/almappát a fogadóban.<br/>Az engedélyezett értékek: **true** (alapértelmezett), **false** | Nem |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<file system input dataset name>",
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

### <a name="file-system-as-sink"></a>Fájlrendszer fogadóként

- A parketta, a **tagolt szöveg, a JSON, a Avro és a bináris formátum**másolásához tekintse meg a [Parquet, a tagolt szöveget, a JSON-t, a Avro és a bináris formátumú mosogató](#format-based-sink) szakaszt.
- Más formátumokba, például az **ork formátumba**való másoláshoz tekintse meg a [más Format mosogató](#other-format-sink) szakaszt.

#### <a name="format-based-sink"></a>Parketta, tagolt szöveg, JSON, Avro és bináris formátumú fogadó

Az adatgyűjtés a **parketta, a tagolt szöveg, a JSON, a Avro és a bináris formátum**között, a [parketta formátuma](format-parquet.md), a [tagolt szöveg formátuma](format-delimited-text.md), a [Avro formátum](format-avro.md) és a [bináris formátum](format-binary.md) cikk a Format-alapú másolási tevékenység fogadóján és a támogatott beállítások. A fájlrendszer a következő tulajdonságokat támogatja a `storeSettings` beállításokban a Format-alapú másolási fogadóban:

| Tulajdonság                 | Leírás                                                  | Szükséges |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A `storeSettings` alatti Type tulajdonságot **FileServerWriteSetting**értékre kell beállítani. | Igen      |
| copyBehavior             | Meghatározza a másolási viselkedést, ha a forrás fájl-alapú adattárból származó fájlok.<br/><br/>Az engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: megőrzi a fájl-hierarchiát a célmappában. A forrásfájl a forrás mappájához relatív elérési útja megegyezik a célfájl relatív elérési útjával.<br/><b>-FlattenHierarchy</b>: a forrás mappából származó összes fájl a célmappa első szintjén van. A célként megadott fájlok automatikusan generált névvel rendelkeznek. <br/><b>-MergeFiles</b>: az összes fájlt egyesíti a forrás mappájából egy fájlba. Ha meg van adva a fájl neve, az egyesített fájl neve a megadott név. Ellenkező esetben ez egy automatikusan létrehozott fájl neve. | Nem       |
| maxConcurrentConnections | Az adattárhoz való kapcsolódáshoz szükséges kapcsolatok száma. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem       |

> [!NOTE]
> A Parquet/tagolt szöveges formátum esetében a következő szakaszban említett, **FileSystemSink** típusú másolási tevékenység fogadója továbbra is támogatott a visszamenőleges kompatibilitás érdekében. Azt javasoljuk, hogy ezt az új modellt fogja használni, és az ADF szerzői felhasználói felülete átváltott az új típusok létrehozásához.

**Példa**

```json
"activities":[
    {
        "name": "CopyToFileSystem",
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

#### <a name="other-format-sink"></a>Egyéb formátumú fogadó

Az adatfájlnak az **ork formátumba**való másolásához a **fogadó szakaszban a** következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának Type tulajdonságát a következőre kell beállítani: **FileSystemSink** |Igen |
| copyBehavior | Meghatározza a másolási viselkedést, ha a forrás fájl-alapú adattárból származó fájlok.<br/><br/>Az engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: megőrzi a fájl-hierarchiát a célmappában. A forrásfájl a forrás mappájához relatív elérési útja megegyezik a célfájl relatív elérési útjával.<br/><b>-FlattenHierarchy</b>: a forrás mappából származó összes fájl a célmappa első szintjén van. A célfájl automatikusan generált névvel rendelkezik. <br/><b>-MergeFiles</b>: az összes fájlt egyesíti a forrás mappájából egy fájlba. Ha a fájl neve meg van adva, az egyesített fájlnév a megadott név lesz. Ellenkező esetben az automatikusan generált fájlnév lenne. | Nem |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyToFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<file system output dataset name>",
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
| `Folder*` | (üres, alapértelmezett használata) | hamis | Mappa<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3**file1. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3**fájl2. JSON**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File3.csv<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File4.json<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp; @ no__t-1 @ no__t-2&nbsp;File6.csv |
| `Folder*` | (üres, alapértelmezett használata) | igaz | Mappa<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3**file1. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3**fájl2. JSON**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7**fájl3. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7**File4. JSON**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7**File5. csv**<br/>AnotherFolderB<br/>&nbsp; @ no__t-1 @ no__t-2&nbsp;File6.csv |
| `Folder*` | `*.csv` | hamis | Mappa<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3**file1. csv**<br/>&nbsp; @ no__t-1 @ no__t-2&nbsp;File2.json<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File3.csv<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File4.json<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp; @ no__t-1 @ no__t-2&nbsp;File6.csv |
| `Folder*` | `*.csv` | igaz | Mappa<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3**file1. csv**<br/>&nbsp; @ no__t-1 @ no__t-2&nbsp;File2.json<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7**fájl3. csv**<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6&nbsp;File4.json<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7**File5. csv**<br/>AnotherFolderB<br/>&nbsp; @ no__t-1 @ no__t-2&nbsp;File6.csv |

### <a name="recursive-and-copybehavior-examples"></a>rekurzív és copyBehavior példák

Ez a szakasz a rekurzív és copyBehavior értékek különböző kombinációinak másolási műveletének eredményét írja le.

| rekurzív | copyBehavior | Forrás mappa szerkezete | Eredményül kapott cél |
|:--- |:--- |:--- |:--- |
| igaz |preserveHierarchy | Mappa1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File2<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File3<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File4<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File5 | A célmappa Mappa1 ugyanazzal a struktúrával jön létre, mint a forrás:<br/><br/>Mappa1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File2<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File3<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File4<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File5. |
| igaz |flattenHierarchy | Mappa1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File2<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File3<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File4<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File5 | A célként megadott Mappa1 a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3auto-generált név a file1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3auto-generált név a Fájl2<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3auto-generált név a fájl3<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3auto-generált név a File4<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3auto-generált név a File5 |
| igaz |mergeFiles | Mappa1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File2<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File3<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File4<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File5 | A célként megadott Mappa1 a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1 + Fájl2 + fájl3 + File4 + file 5 tartalom egyesítve egy fájlba automatikusan létrehozott fájlnévvel |
| hamis |preserveHierarchy | Mappa1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File2<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File3<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File4<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File5 | A célmappa Mappa1 a következő szerkezettel jön létre<br/><br/>Mappa1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File2<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem vesznek fel. |
| hamis |flattenHierarchy | Mappa1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File2<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File3<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File4<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File5 | A célmappa Mappa1 a következő szerkezettel jön létre<br/><br/>Mappa1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3auto-generált név a file1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3auto-generált név a Fájl2<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem vesznek fel. |
| hamis |mergeFiles | Mappa1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File2<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3Subfolder1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File3<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File4<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7File5 | A célmappa Mappa1 a következő szerkezettel jön létre<br/><br/>Mappa1<br/>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3File1 + Fájl2 tartalma egyetlen fájlba van egyesítve automatikusan generált fájlnévvel. a file1 automatikusan generált neve<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem vesznek fel. |

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [GetMetaData tevékenységet](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Tevékenység tulajdonságainak törlése

A tulajdonságok részleteinek megismeréséhez tekintse meg a [tevékenység törlése](delete-activity.md) lehetőséget.

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).

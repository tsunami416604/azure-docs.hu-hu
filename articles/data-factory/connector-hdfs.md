---
title: Adatok másolása a HDFS a Azure Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan másolhat adatok egy felhőből vagy helyszíni HDFS-forrásból a fogadó adattárakba egy Azure Data Factory folyamat másolási tevékenységének használatával.
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
ms.openlocfilehash: 0a695f08f00b99fcd0bc634d12e30c0f3cfbd312
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813087"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Adatok másolása a HDFS a Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-hdfs-connector.md)
> * [Aktuális verzió](connector-hdfs.md)

Ez a cikk az adatok HDFS-kiszolgálóról történő másolását ismerteti. Azure Data Factory szolgáltatásról, olvassa el a [bevezető cikk](introduction.md).

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a HDFS-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Ez a HDFS-összekötő a következőket támogatja:

- Fájlok másolása **Windows** (Kerberos) vagy **Névtelen** hitelesítés használatával.
- Fájlok másolása a **webhdfs** protokollal vagy **a beépített DistCp-** támogatással.
- Másolja a fájlokat,-fájlok az elemzés/létrehozása, vagy a [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Győződjön meg arról, hogy a Integration Runtime hozzáférhet az **összes** [név csomópont-kiszolgálóhoz]: [name Node port] és [adatcsomópont-kiszolgálók]: [adatcsomópont-port] a Hadoop-fürthöz. Az alapértelmezett [name Node port] a 50070, és az alapértelmezett [adatcsomópont-port] a 50075.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek a HDFS-re vonatkozó Data Factory-entitások definiálására szolgálnak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A HDFS társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **Hdfs**. | Igen |
| url |A HDFS URL-címe |Igen |
| authenticationType | Engedélyezett értékek a következők: **Névtelen**vagy **Windows**. <br><br> Ha **Kerberos-hitelesítést** kíván használni a HDFS-összekötőhöz, tekintse meg [ezt a szakaszt](#use-kerberos-authentication-for-hdfs-connector) , és ennek megfelelően állítsa be a helyszíni környezetet. |Igen |
| userName |Felhasználónév a Windows-hitelesítéshez. Kerberos-hitelesítés esetén válassza `<username>@<domain>.com`a következőt:. |Igen (Windows-hitelesítéshez) |
| password |Jelszó a Windows-hitelesítéshez. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen (Windows-hitelesítéshez) |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

**Példa: Névtelen hitelesítés használata**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: Windows-hitelesítés használata**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. 

- A **Parquet, a tagolt szöveget, a JSON-t, a Avro és a bináris formátumot**a [parketta, a tagolt szöveg, a JSON, a Avro és a bináris formátum adatkészlet](#format-based-dataset) szakaszban találja.
- Más formátumok, például az **ork formátum**esetében tekintse meg a [más formátumú adatkészlet](#other-format-dataset) szakaszt.

### <a name="format-based-dataset"></a>Parketta, tagolt szöveg, JSON, Avro és bináris formátumú adatkészlet

Ha a **parketta, a tagolt szöveg, a JSON, a Avro és a bináris formátum**adatait szeretné másolni, tekintse meg a [parketta formátumát](format-parquet.md), a [tagolt szöveg formátumát](format-delimited-text.md), a [Avro formátumát](format-avro.md) és a [bináris formátumú](format-binary.md) cikket a Format-alapú adatkészletek és a támogatott beállítások A HDFS a következő tulajdonságokat támogatja a Format `location` -alapú adatkészlet beállításai alatt:

| Tulajdonság   | Leírás                                                  | Szükséges |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Az adatkészletben `location` található Type tulajdonságot **HdfsLocation**értékre kell állítani. | Igen      |
| folderPath | A mappa elérési útja. Ha a mappa szűréséhez helyettesítő karaktert szeretne használni, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításai között válassza a lehetőséget. | Nem       |
| fileName   | A fájlnév a megadott folderPath alatt. Ha helyettesítő karaktereket szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításai között válassza a lehetőséget. | Nem       |

> [!NOTE]
> A következő szakaszban említett, a Parquet/Text formátumot tartalmazó **fájlmegosztás** -típus adatkészlete továbbra is támogatott a visszamenőleges kompatibilitás érdekében a másolási/keresési tevékenységekhez. Azt javasoljuk, hogy ezt az új modellt fogja használni, és az ADF szerzői felhasználói felülete átváltott az új típusok létrehozásához.

**Példa:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
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

Az adatok az HDFS-ből az **ork formátumba**való másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **FileShare** |Igen |
| folderPath | A mappa elérési útját. A helyettesítő karakteres szűrő támogatott, az engedélyezett helyettesítő `*` karakterek a következők: (nulla vagy több `?` karakternek felel meg) és (a nulla `^` vagy az egyetlen karakternek felel meg); Ha a tényleges fájlnév helyettesítő karakter vagy a escape-karakter szerepel a rendszerben, használja a Escape karaktert. <br/><br/>Példák: gyökérmappa/almappa/, további példák a [mappák és a fájlok szűrése példákban](#folder-and-file-filter-examples). |Igen |
| fileName |  **Név vagy helyettesítő karaktert tartalmazó szűrő** az fájl(ok) a megadott "folderPath" alatt. Ez a tulajdonság értékét nem adja meg, ha az adatkészlet mutat a mappában lévő összes fájlt. <br/><br/>Szűrő esetén engedélyezett a helyettesítő karaktereket: `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter).<br/>-1. példa: `"fileName": "*.csv"`<br/>– 2. példa: `"fileName": "???20180427.txt"`<br/>A `^` (z) használatával elkerülheti a mappát, ha az aktuális mappanév helyettesítő karakterrel rendelkezik, vagy a menekülési karakter szerepel a |Nem |
| modifiedDatetimeStart | A fájlok szűrése az attribútum alapján: Utolsó módosítás. A fájlok lesz kiválasztva, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br/><br/> Ügyeljen arra, hogy az adatáthelyezés általános teljesítményét a beállítás engedélyezésével befolyásolja, ha nagy mennyiségű fájlból szeretne szűrni a fájlmegosztást. <br/><br/> A tulajdonságok értéke NULL lehet, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Amikor `modifiedDatetimeStart` dátum és idő értékkel rendelkezik, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a dátum és idő értékkel lesz kiválasztva.  Amikor `modifiedDatetimeEnd` dátum és idő értékkel rendelkezik, de `modifiedDatetimeStart` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke kisebb, mint a dátum/idő értéket fog jelölni.| Nem |
| modifiedDatetimeEnd | A fájlok szűrése az attribútum alapján: Utolsó módosítás. A fájlok lesz kiválasztva, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br/><br/> Ügyeljen arra, hogy az adatáthelyezés általános teljesítményét a beállítás engedélyezésével befolyásolja, ha nagy mennyiségű fájlból szeretne szűrni a fájlmegosztást. <br/><br/> A tulajdonságok értéke NULL lehet, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Amikor `modifiedDatetimeStart` dátum és idő értékkel rendelkezik, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a dátum és idő értékkel lesz kiválasztva.  Amikor `modifiedDatetimeEnd` dátum és idő értékkel rendelkezik, de `modifiedDatetimeStart` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke kisebb, mint a dátum/idő értéket fog jelölni.| Nem |
| format | Ha azt szeretné, hogy **, a fájlok másolása a-rendszer** közötti fájlalapú tárolók (bináris másolat), hagyja ki a format szakaszban mindkét bemeneti és kimeneti adatkészlet-definíciókban.<br/><br/>Ha a fájlokat egy adott formátummal szeretné elemezni, a következő fájlformátum-típusok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt formátumot az alábbi értékek egyikére. További információkért lásd: [szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), [Json formátumban](supported-file-formats-and-compression-codecs.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátum](supported-file-formats-and-compression-codecs.md#orc-format), és [Parquetformátum](supported-file-formats-and-compression-codecs.md#parquet-format) szakaszokat. |Nem (csak a bináris másolás esetén) |
| compression | Adja meg a típus és az adatok tömörítési szintje. További információkért lásd: [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>A támogatott típusok a következők: **Gzip**, **deflate**, **BZip2**és **ZipDeflate**.<br/>A támogatott szintek a következők: **Optimális** és **leggyorsabb**. |Nem |

>[!TIP]
>Másolja egy mappában található összes fájlt, adja meg a **folderPath** csak.<br>Adja meg a megadott nevű egyetlen fájl másolásához **folderPath** mappára vonatkozó részt a és **fileName** nevére.<br>Másolja a fájlokat egy mappában egy részét, adja meg a **folderPath** mappára vonatkozó részt a és **fileName** helyettesítő szűrővel.

**Példa**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
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

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a HDFS forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="hdfs-as-source"></a>HDFS forrásként

- A parketta, a **tagolt szöveg, a JSON, a Avro és a bináris formátum**másolásához tekintse meg a [parketta, a tagolt szöveg, a JSON, a Avro és a bináris formátum forrás](#format-based-source) szakaszát.
- Más formátumokból, például az **ork formátumból**való másoláshoz tekintse meg a [más formátumú forrás](#other-format-source) szakaszt.

#### <a name="format-based-source"></a>Parketta, tagolt szöveg, JSON, Avro és bináris formátum forrása

Ha a **parketta, a tagolt szöveg, a JSON, a Avro és a bináris formátum**adatait szeretné átmásolni, tekintse meg a [parketta formátumát](format-parquet.md), a [tagolt szöveg formátumát](format-delimited-text.md), a [Avro formátumát](format-avro.md) és a [bináris formátumú](format-binary.md) cikket a Format-alapú másolási tevékenység forrásáról beállítások. A következő tulajdonságok támogatottak a HDFS a `storeSettings` Format-alapú másolási forrás beállításai alatt:

| Tulajdonság                 | Leírás                                                  | Szükséges                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A Type tulajdonságot `storeSettings` a **HdfsReadSetting**értékre kell állítani. | Igen                                           |
| recursive                | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. Vegye figyelembe, hogy ha a rekurzív értéke igaz, és a fogadó a fájlalapú tároló, egy üres mappát vagy almappát nem másolja vagy létrehozott, a fogadó. Engedélyezett értékek a következők **igaz** (alapértelmezett), és **hamis**. | Nem                                            |
| wildcardFolderPath       | A mappa elérési útja helyettesítő karakterekkel a forrás mappák szűréséhez. <br>Az engedélyezett helyettesítő karakterek a `*` következők: (nulla vagy több karakternek `?` felel meg) és (a nulla vagy `^` egy karakter egyezése) <br>További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Nem                                            |
| wildcardFileName         | A forrásfájl szűréséhez a megadott folderPath/wildcardFolderPath helyettesítő karaktereket tartalmazó fájlnév. <br>Az engedélyezett helyettesítő karakterek a `*` következők: (nulla vagy több karakternek `?` felel meg) és (a nulla vagy `^` egy karakter egyezése)  További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Igen, `fileName` ha nincs megadva az adatkészletben |
| modifiedDatetimeStart    | A fájlok szűrése az attribútum alapján: Utolsó módosítás. A fájlok lesz kiválasztva, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br> A Tulajdonságok lehet null értékű, ami jelenti azt, hogy nincs fájlszűrő attribútum alkalmazandó az adatkészletet.  Amikor `modifiedDatetimeStart` dátum és idő értékkel rendelkezik, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a dátum és idő értékkel lesz kiválasztva.  Amikor `modifiedDatetimeEnd` dátum és idő értékkel rendelkezik, de `modifiedDatetimeStart` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke kisebb, mint a dátum/idő értéket fog jelölni. | Nem                                            |
| modifiedDatetimeEnd      | Ugyanaz, mint a fenti.                                               | Nem                                            |
| distcpSettings | Tulajdonság a HDFS DistCp használatakor. | Nem |
| resourceManagerEndpoint | A fonál Resource Manager-végpont | Igen, ha DistCp használ |
| tempScriptPath | A temp DistCp parancs parancsfájljának tárolására szolgáló mappa elérési útja. A parancsfájlt Data Factory hozza létre, és a másolási feladatok befejezése után el lesz távolítva. | Igen, ha DistCp használ |
| distcpOptions | A DistCp parancshoz megadott további beállítások. | Nem |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem                                            |

> [!NOTE]
> A Parquet/tagolt szöveg formátuma esetén a következő szakaszban említett, **FileSystemSource** típusú másolási tevékenység továbbra is támogatott a visszafelé kompatibilitás érdekében. Azt javasoljuk, hogy ezt az új modellt fogja használni, és az ADF szerzői felhasználói felülete átváltott az új típusok létrehozásához.

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
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
                    "type": "HdfsReadSetting",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
                    }
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

Az adatok az HDFS-ből az **ork formátumból**történő másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **HdfsSource** |Igen |
| recursive | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappákban vagy csak a megadott mappába. Megjegyzés: Ha a rekurzív értéke igaz, és a fogadó fájlalapú tároló, üres mappa/alárendelt-folder nem lesz másolva vagy hozható létre, a fogadó.<br/>Engedélyezett értékek a következők: **igaz** (alapértelmezett), **false (hamis)** | Nem |
| distcpSettings | Tulajdonság a HDFS DistCp használatakor. | Nem |
| resourceManagerEndpoint | A fonál Resource Manager-végpont | Igen, ha DistCp használ |
| tempScriptPath | A temp DistCp parancs parancsfájljának tárolására szolgáló mappa elérési útja. A parancsfájlt Data Factory hozza létre, és a másolási feladatok befejezése után el lesz távolítva. | Igen, ha DistCp használ |
| distcpOptions | A DistCp parancshoz megadott további beállítások. | Nem |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem |

**Példa: HDFS forrása a másolási tevékenységben a DistCp használatával**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

További információ arról, hogyan használható a DistCp az adatok hatékony HDFS való másolásához a következő szakaszból.

### <a name="folder-and-file-filter-examples"></a>Példák a mappák és a fájlok szűrésére

Ez a szakasz a mappa elérési útjának és fájlnevének a helyettesítő karakteres szűrőkkel való viselkedését írja le.

| folderPath | fileName             | recursive | A forrás mappa szerkezete és a szűrő eredménye (a **félkövérrel szedett** fájlok beolvasása) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (üres, alapértelmezett használata) | false     | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | (üres, alapértelmezett használata) | true      | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | false     | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | true      | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Adatok másolása a HDFS-ből a DistCp használatával

A [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) egy Hadoop natív parancssori eszköz, amellyel elosztott másolatot készíthet egy Hadoop-fürtön. Distcp parancs futtatásakor először az összes másolandó fájlt listázza, több leképezési feladatot hoz létre a Hadoop-fürtben, és minden térképi feladat binárisan másolja a forrásról a fogadóba.

A másolási tevékenység támogatása a DistCp használatával a fájlok másolása az Azure Blobba (beleértve a [szakaszos másolást](copy-activity-performance.md)) vagy a Azure Data Lake Store, amely esetben teljes mértékben kihasználhatja a fürt erejét a saját üzemeltetésű Integration Runtime futtatása helyett. Jobb másolási sebességet biztosít, különösen akkor, ha a fürt nagyon hatékony. A Azure Data Factory konfigurációján alapuló másolási tevékenység automatikusan létrehoz egy distcp-parancsot, elküldi a Hadoop-fürtnek, és figyeli a másolási állapotot.

### <a name="prerequisites"></a>Előfeltételek

Ha a DistCp-et szeretné használni a HDFS-ből az Azure-Blobba (beleértve a szakaszos másolást) vagy a Azure Data Lake Store, akkor győződjön meg arról, hogy a Hadoop-fürt megfelel az alábbi követelményeknek:

1. A MapReduce és a fonalas szolgáltatások engedélyezve vannak.
2. A fonal verziója 2,5 vagy újabb.
3. A HDFS-kiszolgáló integrálva van a célként megadott adattárral – Azure Blob vagy Azure Data Lake Store:

    - Az Azure Blob-fájlrendszer natív módon támogatott a Hadoop 2,7 óta. Csak a jar elérési utat kell megadnia a Hadoop env-konfigurációban.
    - Azure Data Lake Store fájlrendszer a Hadoop 3.0.0-alfa1-től kezdve van csomagolva. Ha a Hadoop-fürt alacsonyabb ennél a verziónál, manuálisan kell importálnia a ADLS kapcsolódó jar-csomagokat (Azure-datalake-Store. jar) a fürtbe [innen, és](https://hadoop.apache.org/releases.html)a jar elérési utat kell megadnia a Hadoop env konfigurációban.

4. Készítse elő a Temp mappát a HDFS-ben. Ez a temp mappa a DistCp-rendszerhéj parancsfájljának tárolására szolgál, így KB szintű helyet foglal le.
5. Győződjön meg arról, hogy a HDFS társított szolgáltatásban megadott felhasználói fiók rendelkezik engedéllyel a () alkalmazás beküldéséhez a Fonalban; b) engedéllyel rendelkezik az almappa létrehozásához, a fájlok olvasásához/írásához a fenti Temp mappában.

### <a name="configurations"></a>Konfigurációk

Lásd: DistCp kapcsolódó konfigurációk és példák a [HDFS forrás](#hdfs-as-source) szakasza.

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Kerberos-hitelesítés használata a HDFS-összekötőhöz

Két lehetőség áll rendelkezésre a helyszíni környezet beállítására úgy, hogy a Kerberos-hitelesítést használják a HDFS-összekötőben. Kiválaszthatja, hogy melyik illik jobban az esethez.
* 1\. lehetőség: [Saját üzemeltetésű Integration Runtime gép csatlakoztatása Kerberos-tartományban](#kerberos-join-realm)
* 2\. lehetőség: [Kölcsönös megbízhatóság engedélyezése a Windows-tartomány és a Kerberos tartomány között](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>1. lehetőség: Saját üzemeltetésű Integration Runtime gép csatlakoztatása Kerberos-tartományban

#### <a name="requirements"></a>Követelmények

* A saját üzemeltetésű Integration Runtime számítógépnek csatlakoznia kell a Kerberos-tartományhoz, és nem csatlakozhat Windows-tartományhoz.

#### <a name="how-to-configure"></a>Konfigurálás

**Önkiszolgáló Integration Runtime gépen:**

1.  Futtassa a **Ksetup** segédprogramot a Kerberos KDC-kiszolgáló és-tartomány konfigurálásához.

    A gépet egy munkacsoport tagjaként kell konfigurálni, mert egy Kerberos-tartomány eltér a Windows-tartománytól. Ezt a Kerberos-tartomány beállításával és a KDC-kiszolgáló a következőképpen való hozzáadásával lehet megvalósítani. Szükség szerint cserélje le a *REALM.com* -t a saját megfelelő tartománynevére.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Indítsa újra** a gépet a 2 parancs végrehajtása után.

2.  Ellenőrizze a konfigurációt a **Ksetup** paranccsal. A kimenetnek az alábbihoz hasonlónak kell lennie:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Azure Data Factory:**

* Konfigurálja a HDFS-összekötőt a **Windows-hitelesítéssel** együtt a Kerberos egyszerű felhasználónevével és jelszavával a HDFS-adatforráshoz való csatlakozáshoz. A konfiguráció részleteinél keresse meg a [HDFS társított szolgáltatás tulajdonságai](#linked-service-properties) szakaszt.

### <a name="kerberos-mutual-trust"></a>2. lehetőség: Kölcsönös megbízhatóság engedélyezése a Windows-tartomány és a Kerberos tartomány között

#### <a name="requirements"></a>Követelmények

*   A saját üzemeltetésű Integration Runtime számítógépnek csatlakoznia kell egy Windows-tartományhoz.
*   Engedéllyel kell rendelkeznie a tartományvezérlő beállításainak frissítéséhez.

#### <a name="how-to-configure"></a>Konfigurálás

> [!NOTE]
> Cserélje le a REALM.COM és a AD.COM-t a következő oktatóanyagban a saját megfelelő tartományára és tartományvezérlőre szükség szerint.

**KDC-kiszolgálón:**

1. Szerkessze a KDC konfigurációját a **krb5. conf** fájlban, hogy a KDC megbízzon a Windows-tartományon a következő konfigurációs sablonra hivatkozva. Alapértelmezés szerint a konfiguráció a következő helyen található: **/etc/krb5.conf állományt**.

           [logging]
            default = FILE:/var/log/krb5libs.log
            kdc = FILE:/var/log/krb5kdc.log
            admin_server = FILE:/var/log/kadmind.log
            
           [libdefaults]
            default_realm = REALM.COM
            dns_lookup_realm = false
            dns_lookup_kdc = false
            ticket_lifetime = 24h
            renew_lifetime = 7d
            forwardable = true
            
           [realms]
            REALM.COM = {
             kdc = node.REALM.COM
             admin_server = node.REALM.COM
            }
           AD.COM = {
            kdc = windc.ad.com
            admin_server = windc.ad.com
           }
            
           [domain_realm]
            .REALM.COM = REALM.COM
            REALM.COM = REALM.COM
            .ad.com = AD.COM
            ad.com = AD.COM
            
           [capaths]
            AD.COM = {
             REALM.COM = .
            }

   A konfiguráció után **indítsa újra** a KDC szolgáltatást.

2. Készítse elő a **krbtgt/REALM. com\@ad.com** nevű rendszerbiztonsági tag a KDC-kiszolgálón a következő paranccsal:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. A **Hadoop. Security. auth_to_local** HDFS szolgáltatás konfigurációs fájljában adja `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`hozzá a t.

**Tartományvezérlőn:**

1.  A következő **Ksetup** parancsok futtatásával vegyen fel egy tartományi bejegyzést:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Megbízhatósági kapcsolat létrehozása a Windows-tartományból a Kerberos tartományba. a [password] a fő **krbtgt/REALM.\@com ad.com**tartozó jelszó.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Válassza ki a Kerberosban használt titkosítási algoritmust.

    1. Lépjen a Kiszolgálókezelő > Csoportházirend felügyeleti > tartomány > Csoportházirend objektumok > alapértelmezett vagy aktív tartományi házirend elemre, és szerkessze a következőt:.

    2. A **csoportházirend-felügyeleti szerkesztő** előugró ablakban lépjen a számítógép konfigurációja > házirendek > Windows-beállítások > biztonsági beállítások > Helyi házirendek > biztonsági beállítások elemre, **és konfigurálja a hálózati biztonságot: Konfigurálhatja a Kerberos**számára engedélyezett titkosítási típusokat.

    3. Válassza ki a KDC-hoz való csatlakozáskor használni kívánt titkosítási algoritmust. Általában egyszerűen kiválaszthatja az összes beállítást.

        ![A Kerberos konfigurációjának titkosítási típusai](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. A **Ksetup** parancs használatával megadhatja az adott tartományban használandó titkosítási algoritmust.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Hozzon létre egy leképezést a tartományi fiók és a Kerberos-tag között a Kerberos-rendszerbiztonsági tag Windows-tartományban való használatához.

    1. Indítsa el a felügyeleti eszközöket > **Active Directory felhasználókat és számítógépeket**.

    2. A speciális szolgáltatások konfigurálásához kattintson a**speciális szolgáltatások** **megtekintése** > elemre.

    3. Keresse meg azt a fiókot, amelyhez leképezéseket szeretne létrehozni, majd kattintson a jobb gombbal a **név-hozzárendelések** megtekintéséhez > kattintson a **Kerberos-nevek** fülre.

    4. Adjon hozzá egy rendszerbiztonsági tag a tartományhoz.

        ![Térkép biztonsági identitása](media/connector-hdfs/map-security-identity.png)

**Önkiszolgáló Integration Runtime gépen:**

* A következő **Ksetup** parancsok futtatásával vegyen fel egy tartományi bejegyzést.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Azure Data Factory:**

* Konfigurálja a HDFS-összekötőt a **Windows-hitelesítéssel** együtt a tartományi fiókkal vagy a Kerberos-rendszerbiztonsági tag használatával a HDFS-adatforráshoz való csatlakozáshoz. A konfiguráció részleteinél keresse meg a [HDFS társított szolgáltatás tulajdonságai](#linked-service-properties) szakaszt.


## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

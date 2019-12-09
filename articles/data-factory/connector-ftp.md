---
title: Adatok másolása FTP-kiszolgálóról Azure Data Factory használatával
description: Megtudhatja, hogyan másolhat adatokat egy FTP-kiszolgálóról egy támogatott fogadó adattárba egy Azure Data Factory folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: bc426c889eb634f3dc4d69f73f4163b575268dc7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929491"
---
# <a name="copy-data-from-ftp-server-by-using-azure-data-factory"></a>Adatok másolása az FTP-kiszolgálóról Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
>
> * [1-es verzió](v1/data-factory-ftp-connector.md)
> * [Aktuális verzió](connector-ftp.md)

Ez a cikk az adatok FTP-kiszolgálóról való másolásának lépéseit ismerteti. A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az FTP-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)

Ez az FTP-összekötő a következőket támogatja:

- Fájlok másolása **Alapszintű** vagy **Névtelen** hitelesítés használatával.
- Fájlok másolása a-ként vagy a fájlok elemzése a [támogatott fájlformátumokkal és tömörítési kodekekkel](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Az első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük a Data Factory az FTP-hez kapcsolódó entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az FTP-hez társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **FTP**. | Igen |
| gazdagép | Adja meg az FTP-kiszolgáló nevét vagy IP-címét. | Igen |
| port | Itt adhatja meg azt a portot, amelyen az FTP-kiszolgáló figyel.<br/>Az engedélyezett értékek: Integer, az alapértelmezett érték **21**. | Nem |
| enableSsl | Adja meg, hogy az FTP-t SSL/TLS-csatornán keresztül kívánja-e használni.<br/>Az engedélyezett értékek: **true** (alapértelmezett), **false**. | Nem |
| enableServerCertificateValidation | Adja meg, hogy engedélyezi-e a kiszolgáló SSL-tanúsítványának érvényesítését az SSL/TLS-csatornán keresztüli FTP használata esetén.<br/>Az engedélyezett értékek: **true** (alapértelmezett), **false**. | Nem |
| authenticationType | Adja meg a hitelesítési típust.<br/>Az engedélyezett értékek a következők: **Alapszintű**, **Névtelen** | Igen |
| userName (Felhasználónév) | Válassza ki azt a felhasználót, aki hozzáfér az FTP-kiszolgálóhoz. | Nem |
| jelszó | A felhasználó (userName) jelszavának megadása. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Nem |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |Nem |

>[!NOTE]
>Az FTP-összekötő támogatja az FTP-kiszolgáló titkosítás nélküli vagy explicit SSL/TLS titkosítással való elérését. nem támogatja az implicit SSL/TLS-titkosítást.

**1. példa: Névtelen hitelesítés használata**

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "<ftp server>",
            "port": 21,
            "enableSsl": true,
            "enableServerCertificateValidation": true,
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**2. példa: egyszerű hitelesítés használata**

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "<ftp server>",
            "port": 21,
            "enableSsl": true,
            "enableServerCertificateValidation": true,
            "authenticationType": "Basic",
            "userName": "<username>",
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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok támogatottak az FTP esetében `location` beállítások a Format-alapú adatkészletben:

| Tulajdonság   | Leírás                                                  | Szükséges |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Az adatkészlet `location` területén található Type tulajdonságot **FtpServerLocation**értékre kell állítani. | Igen      |
| folderPath | A mappa elérési útja. Ha a mappa szűréséhez helyettesítő karaktert szeretne használni, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításai között válassza a lehetőséget. | Nem       |
| fileName   | A fájlnév a megadott folderPath alatt. Ha helyettesítő karaktereket szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításai között válassza a lehetőséget. | Nem       |

**Példa**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<FTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "FtpServerLocation",
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
| folderPath | A mappa elérési útja. A helyettesítő karakteres szűrő támogatott, az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egy karakternek felel meg); a `^` használatával elkerülheti, hogy a tényleges mappanév helyettesítő karakterrel vagy a menekülési karakterrel rendelkezik-e a belsejében. <br/><br/>Példák: gyökérmappa/almappa/, további példák a [mappák és a fájlok szűrése példákban](#folder-and-file-filter-examples). |Igen |
| fileName | A fájl (ok) **neve vagy helyettesítő szűrője** a megadott "folderPath". Ha nem ad meg értéket ehhez a tulajdonsághoz, az adatkészlet a mappában található összes fájlra mutat. <br/><br/>A Filter (szűrő) esetében az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (nulla vagy egyetlen karakternek felel meg).<br/>– 1. példa: `"fileName": "*.csv"`<br/>– 2. példa: `"fileName": "???20180427.txt"`<br/>A `^` használatával elkerülheti, ha a tényleges fájlnév helyettesítő karakterrel vagy a menekülési karakterrel rendelkezik. |Nem |
| formátum | Ha **fájlokat szeretne másolni** a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt mind a bemeneti, mind a kimeneti adatkészlet-definíciókban.<br/><br/>Ha a fájlokat egy adott formátummal szeretné elemezni, a következő fájlformátum-típusok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a Format értékre a következő értékek egyikére. További információkért lásd: [Szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), JSON- [Formátum](supported-file-formats-and-compression-codecs.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [ork-formátum](supported-file-formats-and-compression-codecs.md#orc-format)és a [parketta formátuma](supported-file-formats-and-compression-codecs.md#parquet-format) című rész. |Nem (csak bináris másolási forgatókönyv esetén) |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. További információ: [támogatott fájlformátumok és tömörítési kodekek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>A támogatott típusok a következők: **gzip**, **deflate**, **BZip2**és **ZipDeflate**.<br/>A támogatott szintek a következők: **optimális** és **leggyorsabb**. |Nem |
| useBinaryTransfer | Adja meg, hogy a bináris átviteli módot kívánja-e használni. Az értékek a bináris üzemmód (alapértelmezett) és a False for ASCII esetében igazak. |Nem |

>[!TIP]
>Egy mappa összes fájljának másolásához csak a **folderPath** kell megadni.<br>Egy adott névvel rendelkező egyetlen fájl másolásához adja meg a **folderPath** és a fájlnév nevű **fájlnevet** .<br>Ha egy mappában lévő fájlok egy részhalmazát szeretné másolni, akkor a **folderPath** és a **filename** paramétert a helyettesítő karakteres szűrővel.

>[!NOTE]
>Ha a "fileFilter" tulajdonságot használta a fájl szűrőhöz, a rendszer továbbra is támogatja a-t, míg a rendszer a "fileName" kifejezéshez hozzáadott új szűrő funkció használatát javasolja.

**Példa**

```json
{
    "name": "FTPDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<FTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
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

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz az FTP-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="ftp-as-source"></a>FTP forrásként

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok támogatottak az FTP esetében `storeSettings` beállítások a Format-alapú másolási forrásban:

| Tulajdonság                 | Leírás                                                  | Szükséges                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A `storeSettings` alatti Type tulajdonságot **FtpReadSetting**értékre kell állítani. | Igen                                           |
| rekurzív                | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. Vegye figyelembe, hogy ha a rekurzív értéke TRUE (igaz), a fogadó pedig egy fájl alapú tároló, a fogadó nem másolja vagy hozza létre az üres mappát vagy almappát. Az engedélyezett értékek: **true** (alapértelmezett) és **false (hamis**). | Nem                                            |
| wildcardFolderPath       | A mappa elérési útja helyettesítő karakterekkel a forrás mappák szűréséhez. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egyetlen karakternek felel meg); a `^` használatával elkerülheti, hogy a tényleges mappanév helyettesítő karakterrel vagy a menekülési karakterrel rendelkezik-e a belsejében. <br>További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Nem                                            |
| wildcardFileName         | A forrásfájl szűréséhez a megadott folderPath/wildcardFolderPath helyettesítő karaktereket tartalmazó fájlnév. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egyetlen karakternek felel meg); a `^` használatával elkerülheti, hogy a tényleges mappanév helyettesítő karakterrel vagy a menekülési karakterrel rendelkezik-e a belsejében.  További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Igen, ha `fileName` nincs megadva az adatkészletben |
| useBinaryTransfer | Adja meg, hogy a bináris átviteli módot kívánja-e használni. Az értékek a bináris üzemmód (alapértelmezett) és a False for ASCII esetében igazak. |Nem |
| maxConcurrentConnections | Az adattárhoz való kapcsolódáshoz szükséges kapcsolatok száma. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromFTP",
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
                    "type": "FtpReadSetting",
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
        "name": "CopyFromFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<FTP input dataset name>",
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

| folderPath | fileName | rekurzív | A forrás mappa szerkezete és a szűrő eredménye (a **félkövérrel szedett** fájlok beolvasása)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (üres, alapértelmezett használata) | hamis | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**fájl2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | (üres, alapértelmezett használata) | igaz | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**fájl2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | hamis | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | igaz | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [GetMetaData tevékenységet](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Tevékenység tulajdonságainak törlése

A tulajdonságok részleteinek megismeréséhez tekintse meg a [tevékenység törlése](delete-activity.md) lehetőséget.

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).

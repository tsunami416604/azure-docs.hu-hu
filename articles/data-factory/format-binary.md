---
title: Bináris formátum a Azure Data Factoryban
description: Ez a témakör azt ismerteti, hogyan kezelhető a bináris formátum a Azure Data Factoryban.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: jingwang
ms.openlocfilehash: 781d88f1862ac20a1212676f29dc022157b3601c
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2020
ms.locfileid: "90061563"
---
# <a name="binary-format-in-azure-data-factory"></a>Bináris formátum a Azure Data Factoryban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az alábbi összekötők támogatják a bináris formátumot: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure file Storage](connector-azure-file-storage.md), [fájlrendszer](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)és [SFTP](connector-sftp.md).

A bináris adatkészletet a [másolási tevékenység](copy-activity-overview.md), a [GetMetaData tevékenység](control-flow-get-metadata-activity.md)vagy a [Törlés tevékenység](delete-activity.md)használatával lehet használni. Ha bináris adatkészletet használ, az ADF nem elemzi a fájl tartalmát, de nem kezeli azt. 

>[!NOTE]
>Ha bináris adatkészletet használ a másolási tevékenységben, csak bináris adatkészletből másolhatja a bináris adatkészletet.

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a bináris adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Kötelező |
| ---------------- | ------------------------------------------------------------ | -------- |
| típus             | Az adatkészlet Type tulajdonságát **bináris**értékre kell beállítani. | Yes      |
| location         | A fájl (ok) helyének beállításai. Minden fájl alapú összekötőhöz tartozik a saját hely típusa és a támogatott tulajdonságai `location` . **Tekintse meg a részleteket az összekötő cikk-> adatkészlet tulajdonságai szakaszban**. | Yes      |
| tömörítés | A fájltömörítés konfigurálására szolgáló tulajdonságok csoportja. Akkor konfigurálja ezt a szakaszt, ha a tevékenység végrehajtása során tömörítést vagy kibontást szeretne végezni. | No |
| típus | A bináris fájlok olvasásához/írásához használt tömörítési kodek. <br>Az engedélyezett értékek a **bzip2**, a **gzip**, a **deflate**, a **ZipDeflate**vagy a **TarGzip**. <br>**Megjegyzés** : Ha a másolási tevékenység használatával kibontja a **ZipDeflate** / **TarGzip** -fájl (oka) t, és a fájl alapú fogadó adattárba ír, a fájlok alapértelmezés szerint a mappába kerülnek, a `<path specified in dataset>/<folder named as source compressed file>/` `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` [másolási tevékenység forrásaként](#binary-as-source) pedig megadhatja, hogy a rendszer a zip-fájl nevét a mappa szerkezeteként őrizze-e meg.| No       |
| szint | A tömörítési arány. Akkor alkalmazza, ha az adatkészletet a másolási tevékenység fogadójában használják.<br>Az engedélyezett értékek az **optimálisak** vagy a **leggyorsabbek**.<br>- **Leggyorsabb:** A tömörítési műveletnek a lehető leggyorsabban kell elvégeznie, még akkor is, ha az eredményül kapott fájl nem tömöríthető optimálisan.<br>- **Optimális**: a tömörítési műveletet optimálisan kell tömöríteni, még akkor is, ha a művelet végrehajtása hosszú időt vesz igénybe. További információ: [tömörítési szint](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) témakör. | No       |

Az alábbi példa az Azure Blob Storage bináris adatkészletét mutatja be:

```json
{
    "name": "BinaryDataset",
    "properties": {
        "type": "Binary",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a bináris forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

>[!NOTE]
>Ha bináris adatkészletet használ a másolási tevékenységben, csak bináris adatkészletből másolhatja a bináris adatkészletet.

### <a name="binary-as-source"></a>Bináris forrásként

A másolási tevékenység *** \* forrása \* *** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| típus          | A másolási tevékenység forrásának Type tulajdonságát **BinarySource**értékre kell állítani. | Yes      |
| formatSettings | Tulajdonságok csoportja. Tekintse meg az alábbi **bináris olvasási beállítások** táblázatot. | No       |
| storeSettings | Az adattárakból származó adatok beolvasására szolgáló tulajdonságok csoportja. A fájl alapú összekötők a saját támogatott olvasási beállításaival rendelkeznek `storeSettings` . **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | No       |

Támogatott **bináris olvasási beállítások** a következő alatt `formatSettings` :

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| típus          | A formatSettings típusát **BinaryReadSettings**értékre kell beállítani. | Yes      |
| compressionProperties | Egy adott tömörítési kodekhez tartozó adatok kibontására szolgáló tulajdonságok csoportja. | No       |
| preserveZipFileNameAsFolder<br>(*a `compressionProperties` -ben -> ) `type` `ZipDeflateReadSettings` * | Akkor érvényes, ha a bemeneti adatkészlet **ZipDeflate** tömörítéssel van konfigurálva. Azt jelzi, hogy a forrás zip-fájlnevet a másolás során a mappa szerkezeteként kell-e megőrizni.<br>– Ha **true (alapértelmezett)** értékre van állítva, Data Factory a kibontott fájlokat ír a következőre: `<path specified in dataset>/<folder named as source zip file>/` .<br>– Ha **hamis**értékre van állítva, Data Factory a kibontott fájlokat közvetlenül a következőre írja: `<path specified in dataset>` . A versenyzés vagy a váratlan viselkedés elkerülése érdekében ügyeljen arra, hogy ne legyenek duplikált fájlnevek különböző zip-fájlokban.  | No |
| preserveCompressionFileNameAsFolder<br>(*a `compressionProperties` -ben -> ) `type` `TarGZipReadSettings` * | Akkor érvényes, ha a bemeneti adatkészlet **TarGzip** tömörítéssel van konfigurálva. Azt jelzi, hogy a forrás tömörített fájlnevet a másolás során a mappa szerkezeteként kell-e megőrizni.<br>– Ha **true (alapértelmezett)** értékre van állítva, Data Factory a kibontott fájlokat ír a következőre: `<path specified in dataset>/<folder named as source compressed file>/` . <br>-Ha **hamis**értékre van állítva, Data Factory a kibontott fájlokat közvetlenül a következőre írja: `<path specified in dataset>` . A versenyzés vagy a váratlan viselkedés elkerülése érdekében ügyeljen arra, hogy ne legyenek duplikált fájlnevek a különböző forrásfájlok között. | No |

```json
"activities": [
    {
        "name": "CopyFromBinary",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true,
                    "deleteFilesAfterCompletion": true
                },
                "formatSettings": {
                    "type": "BinaryReadSettings",
                    "compressionProperties": {
                        "type": "ZipDeflateReadSettings",
                        "preserveZipFileNameAsFolder": false
                    }
                }
            },
            ...
        }
        ...
    }
]
```

### <a name="binary-as-sink"></a>Bináris fogadóként

A másolási *** \* \* tevékenység*** fogadója szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| típus          | A másolási tevékenység forrásának Type tulajdonságát **BinarySink**értékre kell állítani. | Yes      |
| storeSettings | Az adattárakba való adatíráshoz szükséges tulajdonságok csoportja. A fájl alapú összekötők a saját támogatott írási beállításaival rendelkeznek `storeSettings` . **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | Nem       |

## <a name="next-steps"></a>Következő lépések

- [Másolási tevékenység áttekintése](copy-activity-overview.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)

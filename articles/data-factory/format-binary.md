---
title: Bináris formátum a Azure Data Factoryban
description: Ez a témakör azt ismerteti, hogyan kezelhető a bináris formátum a Azure Data Factoryban.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: be4338942057cf52bf26b7fd1e15f77e724d49f8
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348908"
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
| típus             | Az adatkészlet Type tulajdonságát **bináris** értékre kell beállítani. | Igen      |
| location         | A fájl (ok) helyének beállításai. Minden fájl alapú összekötőhöz tartozik a saját hely típusa és a támogatott tulajdonságai `location` . **Tekintse meg a részleteket az összekötő cikk-> adatkészlet tulajdonságai szakaszban**. | Igen      |
| tömörítés | A fájltömörítés konfigurálására szolgáló tulajdonságok csoportja. Akkor konfigurálja ezt a szakaszt, ha a tevékenység végrehajtása során tömörítést vagy kibontást szeretne végezni. | Nem |
| típus | A bináris fájlok olvasásához/írásához használt tömörítési kodek. <br>Az engedélyezett értékek a **bzip2**, a **gzip**, a **deflate**, a **ZipDeflate**, a **tar** vagy a **TarGzip**. <br>**Megjegyzés** : Ha a másolási tevékenységgel kibontja a **ZipDeflate** / **TarGzip** / **tar** -fájl (oka) t, és az írás a fájl alapú fogadó adattárba történik, az alapértelmezett fájlok a mappába kerülnek, a `<path specified in dataset>/<folder named as source compressed file>/` `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` [másolási tevékenység forrásaként](#binary-as-source) pedig megadhatja, hogy meg kell-e őrizni a tömörített fájl (ok) nevét a mappa szerkezeteként.| Nem       |
| szint | A tömörítési arány. Akkor alkalmazza, ha az adatkészletet a másolási tevékenység fogadójában használják.<br>Az engedélyezett értékek az **optimálisak** vagy a **leggyorsabbek**.<br>- **Leggyorsabb:** A tömörítési műveletnek a lehető leggyorsabban kell elvégeznie, még akkor is, ha az eredményül kapott fájl nem tömöríthető optimálisan.<br>- **Optimális**: a tömörítési műveletet optimálisan kell tömöríteni, még akkor is, ha a művelet végrehajtása hosszú időt vesz igénybe. További információ: [tömörítési szint](/dotnet/api/system.io.compression.compressionlevel) témakör. | Nem       |

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

A másolási tevékenység **_ \_ forrás \**** szakaszában a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| típus          | A másolási tevékenység forrásának Type tulajdonságát **BinarySource** értékre kell állítani. | Igen      |
| formatSettings | Tulajdonságok csoportja. Tekintse meg az alábbi **bináris olvasási beállítások** táblázatot. | Nem       |
| storeSettings | Az adattárakból származó adatok beolvasására szolgáló tulajdonságok csoportja. A fájl alapú összekötők a saját támogatott olvasási beállításaival rendelkeznek `storeSettings` . **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | Nem       |

Támogatott **bináris olvasási beállítások** a következő alatt `formatSettings` :

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| típus          | A formatSettings típusát **BinaryReadSettings** értékre kell beállítani. | Igen      |
| compressionProperties | Egy adott tömörítési kodekhez tartozó adatok kibontására szolgáló tulajdonságok csoportja. | Nem       |
| preserveZipFileNameAsFolder<br>(*a `compressionProperties` -ben -> ) `type` `ZipDeflateReadSettings`* | Akkor érvényes, ha a bemeneti adatkészlet **ZipDeflate** tömörítéssel van konfigurálva. Azt jelzi, hogy a forrás zip-fájlnevet a másolás során a mappa szerkezeteként kell-e megőrizni.<br>– Ha **true (alapértelmezett)** értékre van állítva, Data Factory a kibontott fájlokat ír a következőre: `<path specified in dataset>/<folder named as source zip file>/` .<br>– Ha **hamis** értékre van állítva, Data Factory a kibontott fájlokat közvetlenül a következőre írja: `<path specified in dataset>` . A versenyzés vagy a váratlan viselkedés elkerülése érdekében ügyeljen arra, hogy ne legyenek duplikált fájlnevek különböző zip-fájlokban.  | Nem |
| preserveCompressionFileNameAsFolder<br>(*a `compressionProperties` -> `type` `TarGZipReadSettings` -ban `TarReadSettings` vagy a*-ben) | Akkor érvényes, ha a bemeneti adatkészlet **TarGzip** / **tar** tömörítéssel van konfigurálva. Azt jelzi, hogy a forrás tömörített fájlnevet a másolás során a mappa szerkezeteként kell-e megőrizni.<br>– Ha **true (alapértelmezett)** értékre van állítva, Data Factory a kibontott fájlokat ír a következőre: `<path specified in dataset>/<folder named as source compressed file>/` . <br>-Ha **hamis** értékre van állítva, Data Factory a kibontott fájlokat közvetlenül a következőre írja: `<path specified in dataset>` . A versenyzés vagy a váratlan viselkedés elkerülése érdekében ügyeljen arra, hogy ne legyenek duplikált fájlnevek a különböző forrásfájlok között. | Nem |

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

A másolási tevékenység **_ \_ \*** fogadó * szakasza a következő tulajdonságokat támogatja.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| típus          | A másolási tevékenység forrásának Type tulajdonságát **BinarySink** értékre kell állítani. | Igen      |
| storeSettings | Az adattárakba való adatíráshoz szükséges tulajdonságok csoportja. A fájl alapú összekötők a saját támogatott írási beállításaival rendelkeznek `storeSettings` . **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | Nem       |

## <a name="next-steps"></a>Következő lépések

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)
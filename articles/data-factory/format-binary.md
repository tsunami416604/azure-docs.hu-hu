---
title: Bináris formátum a Azure Data Factoryban
description: Ez a témakör azt ismerteti, hogyan kezelhető a bináris formátum a Azure Data Factoryban.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: jingwang
ms.openlocfilehash: 8ebb4f0d1a06a7bf29dc46cd696b6acfd2527095
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927398"
---
# <a name="binary-format-in-azure-data-factory"></a>Bináris formátum a Azure Data Factoryban

Az alábbi összekötők támogatják a bináris formátumot: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure file Storage](connector-azure-file-storage.md), [fájlrendszer](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)és [SFTP](connector-sftp.md).

A bináris adatkészletet a [másolási tevékenység](copy-activity-overview.md), a [GetMetaData tevékenység](control-flow-get-metadata-activity.md)vagy a [Törlés tevékenység](delete-activity.md)használatával lehet használni. Ha bináris adatkészletet használ, az ADF nem elemzi a fájl tartalmát, de nem kezeli azt. 

>[!NOTE]
>Ha bináris adatkészletet használ a másolási tevékenységben, csak bináris adatkészletből másolhatja a bináris adatkészletet.

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a bináris adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Szükséges |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Az adatkészlet Type tulajdonságát **bináris**értékre kell beállítani. | Igen      |
| location         | A fájl (ok) helyének beállításai. Minden fájl alapú összekötőhöz a saját hely típusa és a `location`alatt támogatott tulajdonságok tartozik. **Tekintse meg a részleteket az összekötő cikk-> adatkészlet tulajdonságai szakaszban**. | Igen      |
| tömörítés | A fájltömörítés konfigurálására szolgáló tulajdonságok csoportja. Akkor konfigurálja ezt a szakaszt, ha a tevékenység végrehajtása során tömörítést vagy kibontást szeretne végezni. | Nem |
| type | A bináris fájlok olvasásához/írásához használt tömörítési kodek. <br>Az engedélyezett értékek a **bzip2**, a **gzip**, a **deflate**és a **ZipDeflate**. a fájl mentésekor használatos.<br>Vegye figyelembe, hogy ha másolási tevékenységet használ a ZipDeflate fájl (ok) kibontásához és a fájl alapú fogadó adattárba való íráshoz, a fájlok a következő mappába lesznek kibontva: `<path specified in dataset>/<folder named as source zip file>/`. | Nem       |
| szint | A tömörítési arány. Akkor alkalmazza, ha az adatkészletet a másolási tevékenység fogadójában használják.<br>Az engedélyezett értékek az **optimálisak** vagy a **leggyorsabbek**.<br>- **leggyorsabb:** a tömörítési műveletnek a lehető leggyorsabbnak kell lennie, még akkor is, ha az eredményül kapott fájl nem tömöríthető optimálisan.<br>- **optimális**: a tömörítési műveletet optimálisan kell tömöríteni, még akkor is, ha a művelet végrehajtása hosszú időt vesz igénybe. További információ: [tömörítési szint](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) témakör. | Nem       |

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

A másolási tevékenység ***\*forrás\**** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Szükséges |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenység forrásának Type tulajdonságát **BinarySource**értékre kell állítani. | Igen      |
| storeSettings | Az adattárakból származó adatok beolvasására szolgáló tulajdonságok csoportja. Minden fájl alapú összekötő rendelkezik a saját támogatott olvasási beállításokkal a `storeSettings`alatt. **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | Nem       |

### <a name="binary-as-sink"></a>Bináris fogadóként

A másolási tevékenység\*a fogadó ***\**** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Szükséges |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenység forrásának Type tulajdonságát **BinarySink**értékre kell állítani. | Igen      |
| storeSettings | Az adattárakba való adatíráshoz szükséges tulajdonságok csoportja. Minden fájl alapú összekötő rendelkezik a saját támogatott írási beállításaival a `storeSettings`alatt. **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | Nem       |

## <a name="next-steps"></a>Következő lépések

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)

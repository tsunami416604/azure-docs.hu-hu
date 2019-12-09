---
title: Avro formátuma Azure Data Factory
description: Ez a témakör azt ismerteti, hogyan kezelhető a Avro formátuma Azure Data Factoryban.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 9e962a0e76cdc0d51a87df3c33927c34db991fc7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927413"
---
# <a name="avro-format-in-azure-data-factory"></a>Avro formátuma Azure Data Factory

Kövesse ezt a cikket, ha **elemezni szeretné a Avro-fájlokat, vagy Avro formátumban kell írnia azokat**. 

A Avro formátum a következő összekötők esetében támogatott: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure file Storage](connector-azure-file-storage.md), [fájlrendszer](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)és [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a Avro adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Szükséges |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Az adatkészlet Type tulajdonságát **Avro**értékre kell állítani. | Igen      |
| location         | A fájl (ok) helyének beállításai. Minden fájl alapú összekötőhöz a saját hely típusa és a `location`alatt támogatott tulajdonságok tartozik. **Tekintse meg a részleteket az összekötő cikk-> adatkészlet tulajdonságai szakaszban**. | Igen      |
| avroCompressionCodec | A Avro-fájlok írásakor használandó tömörítési kodek. A Avro-fájlokból való olvasáskor Data Factory automatikusan meghatározza a tömörítési kodeket a fájl metaadatai alapján.<br>A támogatott típusok a következők: "**none**" (alapértelmezett), "**deflate**", "**Snappy**". | Nem       |

> [!NOTE]
> A Avro-fájlok nem támogatják az oszlopnév üres területét.

Alább látható egy példa az Azure Blob Storage Avro adatkészletére:

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Avro forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="avro-as-source"></a>Avro forrásként

A másolási tevékenység ***\*forrás\**** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Szükséges |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenység forrásának Type tulajdonságát **AvroSource**értékre kell állítani. | Igen      |
| storeSettings | Az adattárakból származó adatok beolvasására szolgáló tulajdonságok csoportja. Minden fájl alapú összekötő rendelkezik a saját támogatott olvasási beállításokkal a `storeSettings`alatt. **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | Nem       |

### <a name="avro-as-sink"></a>Avro fogadóként

A másolási tevékenység\*a fogadó ***\**** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Szükséges |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenység forrásának Type tulajdonságát **AvroSink**értékre kell állítani. | Igen      |
| storeSettings | Az adattárakba való adatíráshoz szükséges tulajdonságok csoportja. Minden fájl alapú összekötő rendelkezik a saját támogatott írási beállításaival a `storeSettings`alatt. **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | Nem       |

## <a name="data-type-support"></a>Adattípusok támogatása

Az Avro [összetett adattípusok](https://avro.apache.org/docs/current/spec.html#schema_complex) nem támogatottak (rekordok, enumerálások, tömbök, térképek, szakszervezetek és rögzített).

## <a name="next-steps"></a>Következő lépések

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

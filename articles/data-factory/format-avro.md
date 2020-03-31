---
title: Avro formátum az Azure Data Factoryban
description: Ez a témakör ismerteti, hogyan kell kezelni az Avro formátumot az Azure Data Factoryban.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jingwang
ms.openlocfilehash: 1717969aeb24a153f986c70ef60db1aac5c840fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78267790"
---
# <a name="avro-format-in-azure-data-factory"></a>Avro formátum az Azure Data Factoryban

Kövesse ezt a cikket, ha elemezni szeretné **az Avro fájlokat, vagy az adatokat Avro formátumba írni.** 

Az Avro formátum a következő összekötők esetében támogatott: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), File [System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)és [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek](concepts-datasets-linked-services.md) című cikkben olvashatja. Ez a szakasz az Avro-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Kötelező |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Az adatkészlet típustulajdonságát **Avro**-ra kell állítani. | Igen      |
| location         | A fájl(ok) helybeállításai. Minden fájlalapú összekötő saját helytípussal és `location`támogatott tulajdonságokkal rendelkezik a csoportban. **További részletek az összekötő -> adatkészlet tulajdonságai szakaszban.** | Igen      |
| avroCompression Codec | Az Avro fájlokíráshoz használt tömörítési kodek. Amikor Avro fájlokból olvas, a Data Factory automatikusan meghatározza a tömörítési kodeket a fájl metaadatai alapján.<br>A támogatott típusok a következők: "**none**" (alapértelmezett), "**deflate**", "**lendületes**". Megjegyzés: Jelenleg másolási tevékenység nem támogatja a Snappy olvasása / írása Avro fájlokat. | Nem       |

> [!NOTE]
> Az Avro-fájlok nem támogatják az oszlopnévben lévő szóközt.

Az alábbiakban egy példa az Avro adatkészlet az Azure Blob Storage:

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

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz az Avro-forrás és -fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="avro-as-source"></a>Avro forrásként

A következő tulajdonságokat a másolási tevékenység *** \*forrása\* *** szakasz támogatja.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenységforrás típustulajdonságát **AvroSource (AvroSource )** tulajdonságra kell állítani. | Igen      |
| storeSettings (tárolóbeállítások) | Tulajdonságok csoportja az adattárból történő adatok olvasásáról. Minden fájlalapú összekötő saját támogatott olvasási beállításokkal rendelkezik a területen. `storeSettings` **További részletek az összekötő cikkében -> a tevékenység tulajdonságainak másolása szakaszban.** | Nem       |

### <a name="avro-as-sink"></a>Avro mint mosogató

A következő tulajdonságokat a *** \*\* *** másolási tevékenység fogadó szakasza támogatja.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenységforrás típustulajdonságának **AvroSink (AvroSink ) típusú tulajdonságát AvroSink (AvroSink**) tulajdonságra kell állítani. | Igen      |
| storeSettings (tárolóbeállítások) | Tulajdonságok csoportja az adatok adattárba való írásának módjáról. Minden fájlalapú összekötő saját támogatott írási beállításokkal rendelkezik a területen. `storeSettings` **További részletek az összekötő cikkében -> a tevékenység tulajdonságainak másolása szakaszban.** | Nem       |

## <a name="data-type-support"></a>Adattípus-támogatás

### <a name="copy-activity"></a>Másolási tevékenység
Az Avro [összetett adattípusai](https://avro.apache.org/docs/current/spec.html#schema_complex) nem támogatottak (rekordok, felsorakatok, tömbök, térképek, tagoltak és rögzítettek) a Másolási tevékenységben.

### <a name="data-flows"></a>Adatfolyamok
Amikor az Avro-fájlokkal dolgozik az adatfolyamokban, összetett adattípusokat olvashat és írhat, de először törölje a fizikai sémát az adatkészletből. Az adatfolyamokban beállíthatja a logikai vetületet, és összetett struktúrákat eredményező oszlopokat hozhat létre, majd automatikusan leképezheti ezeket a mezőket egy Avro-fájlhoz.

## <a name="next-steps"></a>További lépések

- [Tevékenység másolása – áttekintés](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

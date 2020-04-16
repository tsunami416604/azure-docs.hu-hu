---
title: Bináris formátum az Azure Data Factoryban
description: Ez a témakör ismerteti, hogyan kell kezelni a bináris formátum az Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: jingwang
ms.openlocfilehash: 4560560b3677030a66e277e96eb552d39f5c82c1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416325"
---
# <a name="binary-format-in-azure-data-factory"></a>Bináris formátum az Azure Data Factoryban
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A bináris formátum a következő összekötők esetében támogatott: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), Azure Data Lake [Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), Google [Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)és [SFTP](connector-sftp.md).

A bináris adatkészletet használhatja a [Másolás tevékenységben](copy-activity-overview.md), a [GetMetadata tevékenységben](control-flow-get-metadata-activity.md)vagy a [Törlés tevékenységben.](delete-activity.md) Bináris adatkészlet használata esetén az ADF nem elemzi a fájltartalmat, hanem úgy kezeli, ahogy van. 

>[!NOTE]
>Bináris adatkészlet másolási tevékenységben való használataesetén csak bináris adatkészletből másolhat bináris adatkészletet.

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek](concepts-datasets-linked-services.md) című cikkben olvashatja. Ez a szakasz a bináris adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Kötelező |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Az adatkészlet típustulajdonságának Bináris tulajdonságra kell **állítania.** | Igen      |
| location         | A fájl(ok) helybeállításai. Minden fájlalapú összekötő saját helytípussal és `location`támogatott tulajdonságokkal rendelkezik a csoportban. **További részletek az összekötő -> adatkészlet tulajdonságai szakaszban.** | Igen      |
| tömörítés | Tulajdonságok csoportja a fájltömörítés konfigurálásához. Akkor konfigurálja ezt a szakaszt, ha a tevékenység végrehajtása során tömörítést/kicsomagolást szeretne végezni. | Nem |
| type | A bináris fájlok olvasására/írására használt tömörítési kodek. <br>Megengedett **értékek: bzip2,** **gzip,** **deflate,** **ZipDeflate**. a fájl mentésekor.<br>Megjegyzés: Ha másolási tevékenységet használ a ZipDeflate fájl(ok) kibontásához és a fájlalapú `<path specified in dataset>/<folder named as source zip file>/`fogadó adattárba való íráshoz, a fájlok kibontása a következő mappába kerül: . | Nem       |
| szint | A tömörítési arány. Alkalmazza, ha az adatkészlet et használja a Másolás tevékenység fogadó.<br>Az engedélyezett értékek **az Optimális** vagy **a Leggyorsabb**.<br>- **Leggyorsabb:** A tömörítési műveletnek a lehető leggyorsabban el kell végeznie, még akkor is, ha az eredményül kapott fájl nincs optimálisan tömörítve.<br>- **Optimális**: A tömörítési műveletet optimálisan kell tömöríteni, még akkor is, ha a művelet hosszabb időt vesz igénybe. További információt a [Tömörítési szint témakörben](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) talál. | Nem       |

Az alábbiakban egy példa a bináris adatkészlet az Azure Blob Storage:

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

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a bináris forrás és fogadó által támogatott tulajdonságok listáját tartalmazza.

>[!NOTE]
>Bináris adatkészlet másolási tevékenységben való használataesetén csak bináris adatkészletből másolhat bináris adatkészletet.

### <a name="binary-as-source"></a>Bináris forrásként

A következő tulajdonságokat a másolási tevékenység *** \*forrása\* *** szakasz támogatja.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenységforrás típustulajdonságának **BinarySource (BinarySource**) típusú tulajdonságát kell állítani. | Igen      |
| storeSettings (tárolóbeállítások) | Tulajdonságok csoportja az adattárból történő adatok olvasásáról. Minden fájlalapú összekötő saját támogatott olvasási beállításokkal rendelkezik a területen. `storeSettings` **További részletek az összekötő cikkében -> a tevékenység tulajdonságainak másolása szakaszban.** | Nem       |

### <a name="binary-as-sink"></a>Bináris mint fogadó

A következő tulajdonságokat a *** \*\* *** másolási tevékenység fogadó szakasza támogatja.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenységforrás típustulajdonságának **BinarySink (BinarySink ) (BinarySink**) típusú tulajdonságának kell lennie. | Igen      |
| storeSettings (tárolóbeállítások) | Tulajdonságok csoportja az adatok adattárba való írásának módjáról. Minden fájlalapú összekötő saját támogatott írási beállításokkal rendelkezik a területen. `storeSettings` **További részletek az összekötő cikkében -> a tevékenység tulajdonságainak másolása szakaszban.** | Nem       |

## <a name="next-steps"></a>További lépések

- [Tevékenység másolása – áttekintés](copy-activity-overview.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)

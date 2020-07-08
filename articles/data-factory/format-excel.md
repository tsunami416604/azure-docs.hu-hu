---
title: Excel-formátum Azure Data Factory
description: Ez a témakör a Azure Data Factory Excel-formátumának kezelését ismerteti.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: jingwang
ms.openlocfilehash: 8b4876377501209e19ac496d605d228208d2323d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84670921"
---
# <a name="excel-format-in-azure-data-factory"></a>Excel-formátum Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ha szeretné **elemezni az Excel-fájlokat**, kövesse ezt a cikket. A Azure Data Factory támogatja a ". xls" és az ". xlsx" is.

Az Excel formátum a következő összekötők esetében támogatott: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure file Storage](connector-azure-file-storage.md), [fájlrendszer](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)és [SFTP](connector-sftp.md). Forrásként, de nem fogadóként támogatott.

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz az Excel-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Kötelező |
| ---------------- | ------------------------------------------------------------ | -------- |
| típus             | Az adatkészlet Type tulajdonságát az **Excel**értékre kell beállítani.   | Yes      |
| location         | A fájl (ok) helyének beállításai. Minden fájl alapú összekötőhöz tartozik a saját hely típusa és a támogatott tulajdonságai `location` . | Yes      |
| SheetName tulajdonságbeállító        | Az Excel-munkalap neve az adatolvasáshoz.                       | Yes      |
| tartomány            | Az adott munkalapon lévő cellatartomány a szelektív adatok (például az a3 és a H5 közötti tábla) megkereséséhez (egy, az `A3:H5` `A3` a3 cellától kezdődő tábla), `A3:A3` (egyetlen cella). Ha nincs megadva, az ADF a teljes munkalapról táblázatként olvas. | No       |
| firstRowAsHeader | Meghatározza, hogy az adott munkalap vagy tartomány első sorát fejlécként kell-e kezelni az oszlopok neveivel.<br>Az engedélyezett értékek: **true** és **false** (alapértelmezett). | No       |
| nullValue        | Megadja a null értékű karakterlánc-ábrázolást. <br>Az alapértelmezett érték **üres karakterlánc**. | No       |
| compressionCodec | Az Excel-fájlok olvasásához használt tömörítési kodek. <br>Az engedélyezett értékek a **bzip2**, a **gzip**, a **deflate**, a **ZipDeflate**, a **Snappy**vagy a **lz4**. Az alapértelmezett érték nincs tömörítve. <br>**Megjegyzés:** a másolási tevékenység nem támogatja a "snappy" & "lz4", és a leképezési adatfolyam nem támogatja a "ZipDeflate". <br>**Vegye figyelembe** , hogy ha másolási tevékenységet használ a **ZipDeflate** -fájl (ok) kibontásához és a fájl alapú fogadó adattárba való íráshoz, a fájlok kikerülnek a mappába: `<path specified in dataset>/<folder named as source zip file>/` . | No       |
| compressionLevel | A tömörítési arány. <br>Az engedélyezett értékek az **optimálisak** vagy a **leggyorsabbek**.<br>- **Leggyorsabb:** A tömörítési műveletnek a lehető leggyorsabban kell elvégeznie, még akkor is, ha az eredményül kapott fájl nem tömöríthető optimálisan.<br>- **Optimális**: a tömörítési műveletet optimálisan kell tömöríteni, még akkor is, ha a művelet végrehajtása hosszú időt vesz igénybe. További információ: [tömörítési szint](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) témakör. | No       |

Az alábbi példa az Azure Blob Storage Excel-adatkészletet mutatja be:

```json
{
    "name": "ExcelDataset",
    "properties": {
        "type": "Excel",
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
            "sheetName": "MyWorksheet",
            "range": "A3:H5",
            "firstRowAsHeader": true
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz az Excel-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="excel-as-source"></a>Excel forrásként 

A másolási tevékenység *** \* forrása \* *** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| típus          | A másolási tevékenység forrásának Type tulajdonságát **ExcelSource**értékre kell állítani. | Yes      |
| storeSettings | Az adattárakból származó adatok beolvasására szolgáló tulajdonságok csoportja. A fájl alapú összekötők a saját támogatott olvasási beállításaival rendelkeznek `storeSettings` . | Nem       |

```json
"activities": [
    {
        "name": "CopyFromExcel",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "ExcelSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            ...
        }
        ...
    }
]
```

## <a name="next-steps"></a>Következő lépések

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

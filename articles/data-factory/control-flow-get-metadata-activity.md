---
title: Metaadatok beolvasása tevékenység a Azure Data Factoryban | Microsoft Docs
description: Megtudhatja, hogyan használhatja a GetMetadata tevékenységet egy Data Factory folyamat során.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: b819a990b9f607aaf70bf2e16a5857de3f7306cc
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827493"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Metaadatok beolvasása tevékenység Azure Data Factory

A GetMetadata tevékenység a Azure Data Factoryban található adatok **metaadatainak** lekérésére használható. Ez a tevékenység a következő esetekben használható:

- Bármely adat metaadat-adatainak ellenőrzése
- Folyamat elindítása, ha az adatkész/elérhető

A következő funkciók érhetők el a vezérlési folyamatban:

- A GetMetadata tevékenység kimenete feltételes kifejezésekben használható az érvényesítés végrehajtásához.
- Egy folyamat aktiválható, ha a feltétel teljesül

## <a name="supported-capabilities"></a>Támogatott képességek

A GetMetaData tevékenység kötelező bemenetként fogad egy adatkészletet, és a tevékenység kimenete elérhető metaadat-információkat jeleníti meg. Jelenleg a következő, megfelelő lekéréses metaadatokkal rendelkező összekötők támogatottak, a metaadatok maximális mérete pedig 1 **MB**.

>[!NOTE]
>Ha saját üzemeltetésű Integration Runtime futtat GetMetadata tevékenységet, a legújabb funkció a 3,6-es vagy újabb verzióban támogatott. 

### <a name="supported-connectors"></a>Támogatott összekötők

**File Storage:**

| Összekötő/metaadatok | itemName<br>(fájl/mappa) | itemType<br>(fájl/mappa) | size<br>fájl | létrehozva<br>(fájl/mappa) | lastModified<br>(fájl/mappa) |childItems<br>mappa |contentMD5<br>fájl | structure<br/>fájl | columnCount<br>fájl | létezik<br>(fájl/mappa) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Azure Blob](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure File Storage](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Fájlrendszer](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |

- Az Amazon S3 és a Google Cloud Storage esetében `lastModified` az a a gyűjtőre és a kulcsra, de nem a `exists` virtuális mappára vonatkozik; a és a nem az előtagra, sem a virtuális mappára vonatkozik.
- Az Azure Blob esetében az `lastModified` a tárolóra és a blobra vonatkozik, de a virtuális mappára nem.

**Viszonyítási adatbázis:**

| Összekötő/metaadatok | structure | columnCount | létezik |
|:--- |:--- |:--- |:--- |
| [Azure SQL Database](connector-azure-sql-database.md) | √ | √ | √ |
| [Az Azure SQL Database felügyelt példány](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Metaadatok beállításai

A következő metaadatok adhatók meg a GetMetadata tevékenység mezőinek listájában a lekéréshez:

| Metaadat típusa | Leírás |
|:--- |:--- |
| itemName | A fájl vagy mappa neve. |
| itemType | A fájl vagy mappa típusa. A kimeneti érték `File` a `Folder`vagy a. |
| size | A fájl mérete bájtban. Csak a fájlra vonatkozik. |
| létrehozva | A fájl vagy mappa dátum és idő (datetime) létrehozása. |
| lastModified | A fájl vagy mappa utolsó módosításának datetime értéke. |
| childItems | A megadott mappában található almappák és fájlok listája. Csak a mappára érvényes. A kimeneti érték az egyes alárendelt elemek nevének és típusának listája. |
| contentMD5 | A fájl MD5-je. Csak a fájlra vonatkozik. |
| structure | Adatstruktúra a fájl vagy a viszonyítási adatbázis tábláján belül. A kimeneti érték az oszlopnév és az oszlop típusának listája. |
| columnCount | A fájl vagy a rokon tábla oszlopainak száma. |
| létezik| Azt jelzi, hogy létezik-e fájl, mappa vagy tábla. Vegye figyelembe, hogy ha a "létezik" érték van megadva a GetaMetadata mezőben, a tevékenység nem fog működni, még akkor sem, ha az elem (fájl/mappa/tábla) nem létezik. Ehelyett visszaadja `exists: false` a kimenetet. |

>[!TIP]
>Ha szeretné ellenőrizni, hogy egy fájl/mappa vagy tábla létezik vagy sem, adja meg `exists` a GetMetaData tevékenység mezőinek listáját, majd ellenőrizze a `exists: true/false` tevékenység kimenetének eredményét. Ha `exists` nincs konfigurálva a mezőlista, a GetMetaData tevékenység sikertelen lesz, ha az objektum nem található.

>[!NOTE]
>Ha a fájl tárolja a metaadatokat, és `modifiedDatetimeStart` konfigurálja a és `modifiedDatetimeEnd`/vagy `childItems` a értéket, a kimenet csak a megadott elérési úton lévő fájlokat adja vissza a tartomány közötti utolsó módosítás időpontig, de nem tartalmaz almappákat.

## <a name="syntax"></a>Szintaxis

**GetMetadata tevékenység:**

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```

**Adatkészlet**

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "filename": "file.json",
            "format":{
                "type":"JsonFormat"
            }
        }
    }
}
```

## <a name="type-properties"></a>Típus tulajdonságai

A GetMetadata tevékenység jelenleg a következő típusú metaadatokat tudja beolvasni.

Tulajdonság | Leírás | Kötelező
-------- | ----------- | --------
Mezőlista | Felsorolja a szükséges metaadat-információk típusait. A támogatott metaadatokat a [metaadatok beállításai](#metadata-options) című szakaszban találja. | Igen 
adatkészlet | Az a hivatkozási adatkészlet, amelynek a metaadat-tevékenységét a GetMetadata tevékenység kéri le. Tekintse meg a támogatott összekötők témakör [támogatott képességek](#supported-capabilities) szakaszát, és tekintse meg az összekötő témakört az adatkészlet szintaxisának részleteiről. | Igen
formatSettings | A Format Type adatkészlet (Parque, DelimitedText) használatakor érvényes. | Nem
storeSettings | A Format Type adatkészlet (Parque, DelimitedText) használatakor érvényes. | Nem

## <a name="sample-output"></a>Példa kimenet

A GetMetadata eredménye a tevékenység kimenetében látható. Az alábbi két minta a mezőlista hivatkozásként való kiválasztásával. Ha a következő tevékenység eredményét szeretné használni, használja a mintázatát `@{activity('MyGetMetadataActivity').output.itemName}`.

### <a name="get-a-files-metadata"></a>Fájl metaadatainak beolvasása

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>Mappa metaadatainak beolvasása

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>További lépések
Tekintse meg a Data Factory által támogatott egyéb vezérlési folyamatokat: 

- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden tevékenységhez](control-flow-for-each-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)

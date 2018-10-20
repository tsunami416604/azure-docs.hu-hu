---
title: Metaadatok beolvasása tevékenység az Azure Data Factoryban |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az SQL Server tárolt eljárási tevékenység egy tárolt eljárást az Azure SQL Database vagy Azure SQL Data Warehouse a Data Factory-folyamatok meghívásához.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: shlo
ms.openlocfilehash: f61399a3a6cb5c67343e28e4364d8d796ffbc066
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457066"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Metaadatok beolvasása tevékenység az Azure Data Factoryban
GetMetadata tevékenység is lehet lekérni **metaadatok** minden adat, az Azure Data Factoryban. Ez a tevékenység a következő esetekben használhatók:

- Metaadat-információkat az adatok ellenőrzése
- Folyamat aktiválása, ha az adatok készen / érhető el

Az alábbi funkciókat átvitelvezérlés érhető el:

- A feltételes kifejezések érvényesítésekhez GetMetadata tevékenység kimenete is használható.
- Egy folyamat indítható el, ha a feltétel teljesül-keresztül Do-Until ciklusos

## <a name="supported-capabilities"></a>Támogatott képességek

A GetMetadata tevékenység egy kötelező bemeneti adatkészlet szükséges, és kiírja a tevékenység kimeneti elérhető metaadat-információkat. Jelenleg az alábbi csatlakozók a megfelelő lekérhető meatadata támogatottak, és a maximális támogatott metaadatok mérete legfeljebb **1MB**.

>[!NOTE]
>Ha egy helyi Integration Runtime GetMetadata tevékenység futtatja, a legújabb funkció támogatott 3.6-os verzióját vagy újabb. 

### <a name="supported-connectors"></a>Támogatott összekötők

**Fájltároló:**

| Összekötő/metaadatok | Elemnév<br>(fájlok/mappák) | ItemType<br>(fájlok/mappák) | méret<br>(fájl) | létrehozva<br>(fájlok/mappák) | módosítás dátuma<br>(fájlok/mappák) |childItems<br>(mappa) |contentMD5<br>(fájl) | struktúra<br/>(fájl) | Oszlopszám<br>(fájl) | Létezik<br>(fájlok/mappák) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Amazon S3 | √/√ | √/√ | √ | x/x | √ / √ * | √ | x | √ | √ | √ / √ * |
| Azure-blob | √/√ | √/√ | √ | x/x | √ / √ * | √ | √ | √ | √ | √/√ |
| Azure Data Lake Store | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| Azure File Storage | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| Fájlrendszer | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| SFTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| FTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |

- Az Amazon S3 a `lastModified` bucket és a kulcs, de nem virtuális mappában; vonatkozik; és a `exists` bucket és a kulcsot, de nem előtag vagy virtuális mappa vonatkozik.
- Az Azure Blob a `lastModified` tároló és a blob, de nem virtuális mappa vonatkozik.

**Relációs adatbázis:**

| Összekötő/metaadatok | struktúra | Oszlopszám | Létezik |
|:--- |:--- |:--- |:--- |
| Azure SQL Database | √ | √ | √ |
| Azure SQL Database felügyelt példány | √ | √ | √ |
| Azure SQL Data Warehouse | √ | √ | √ |
| SQL Server | √ | √ | √ |

### <a name="metadata-options"></a>Metaadat-beállítások

A következő metaadat-típusok a GetMetadata tevékenység lekérdezni a mezőlistában adható meg:

| Metaadat típusa | Leírás |
|:--- |:--- |
| Elemnév | A fájl vagy mappa neve. |
| ItemType | A fájl vagy mappa típusú. Kimeneti érték `File` vagy `Folder`. |
| méret | Fájl mérete a bájtban. Csak fájl alkalmazható. |
| létrehozva | A fájl vagy mappa létrehozott datetime. |
| módosítás dátuma | Utolsó módosítás a fájl vagy mappa datetime. |
| childItems | Almappák és az adott mappában lévő fájlok listája. Csak a mappára érvényes. Kimeneti érték neve és típusa, egyes alárendelt elemek listáját. |
| contentMD5 | A fájl MD5-tel. Csak fájl alkalmazható. |
| struktúra | A fájl vagy a relációs adatbázis-táblában lévő adatok szerkezetét. Kimeneti oszlop neve és típusú oszlop értéke. |
| Oszlopszám | A fájl vagy a relációs tábla található oszlopok számát. |
| Létezik| Egy fájl/mappa/table megtalálható-e vagy sem. Megjegyzés: Ha "létezik" van megadva a GetaMetadata mezők listájában, a tevékenység sikertelen nem, akkor is, ha az elem (fájl/mappa/tábla) nem létezik; Ehelyett adja vissza `exists: false` a kimenetben. |

>[!TIP]
>Amikor ellenőrizheti, hogy egy fájl/mappa/tábla létezik, vagy nem szeretné, adja meg a `exists` a GetMetadata tevékenység mezők listájában, majd ellenőrizheti a `exists: true/false` a tevékenység kimeneti eredménye. Ha `exists` nem történik meg a mezők listájában, a GetMetadata tevékenység sikertelen lesz, amikor az objektum nem található.

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

**Adatkészlet:**

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

## <a name="type-properties"></a>Tulajdonságok

Jelenleg a GetMetadata tevékenység lehet beolvasni a következő típusú metaadat-információkat.

Tulajdonság | Leírás | Szükséges
-------- | ----------- | --------
Mezőlista | Felsorolja a szükséges metaadatokat adatokat. A részleteket a [metaadatok beállítások](#metadata-options) a támogatott metaadatok szakasz. | Igen 
Adatkészlet | A referencia-adatkészletnek amelynek metaadatok tevékenységet, GetMetadata tevékenység által lekérni. Lásd: [Supported capabilities](#supported-capabilities) támogatott összekötők a szakaszt, és hivatkozik adatkészlet szintaxis részleteinek összekötő témakör. | Igen

## <a name="sample-output"></a>Példa kimenet

Tevékenység kimenete a GetMetadata eredménye látható. Az alábbiakban két mintát a teljes körű metaadatok beállítás van kiválasztva, a mező listában, hivatkozásként van listázva. Az eredmény a soron következő tevékenysége használatához használja a következő mintát, `@{activity('MyGetMetadataActivity').output.itemName}`.

### <a name="get-a-files-metadata"></a>A fájl metaadatainak beolvasása

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

### <a name="get-a-folders-metadata"></a>Egy mappametaadatok beolvasása

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
Tekintse meg a többi Data Factory által támogatott átvitelvezérlési tevékenységek: 

- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)

---
title: Azure Data Factory beolvasása a metaadatok tevékenység |} Microsoft Docs
description: Ismerje meg, hogyan használhatja az SQL Server tárolt eljárási tevékenység meghívni a Data Factory-folyamat az az Azure SQL Database vagy az Azure SQL Data Warehouse tárolt eljárást.
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
ms.topic: article
ms.date: 05/10/2018
ms.author: shlo
ms.openlocfilehash: 4698f2e4c75456de7387ee7fe3bfa9b2ab4dd406
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Az Azure Data Factory metaadatok tevékenység beolvasása
GetMetadata tevékenység használható beolvasása **metaadatok** az Azure Data Factory adatok. Ez a tevékenység csak 2-es verzióját az adat-előállítók esetén támogatott. A következő esetekben használhatók:

- A metaadatok adatok ellenőrzése
- Egy folyamat indul el, ha adatok készen / érhető el

A következő funkciókat a vezérlési folyamatában érhető el:

- A GetMetadata tevékenység kimenete ellenőrzéshez feltételes kifejezéseket is használható.
- Egy folyamat is elindítható, amikor keresztül tegye feltétel teljesül-e-amíg ismétlési

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [Data Factory V1 dokumentáció](v1/data-factory-introduction.md).

## <a name="supported-capabilities"></a>Támogatott képességei

A GetMetadata tevékenység egy kötelező bemeneti adatkészlet szükséges, és kiírja a tevékenység kimeneti elérhető metaadat-információt. Jelenleg az alábbi csatlakozók a megfelelő lekérhető meatadata támogatottak:

>[!NOTE]
>Ha egy Self-hosted integrációs futásidejű GetMetadata tevékenység futtatja, a legújabb funkció támogatott 3.6 verzió vagy újabb verzióját. 

### <a name="supported-connectors"></a>Támogatott összekötők

**Fájltároló:**

| Összekötő/metaadatok | itemName<br>(fájl vagy mappa) | ItemType<br>(fájl vagy mappa) | méret<br>(fájl) | létrehozva<br>(fájl vagy mappa) | Módosítás dátuma<br>(fájl vagy mappa) |childItems<br>(mappa) |contentMD5<br>(fájl) | struktúra<br/>(fájl) | Oszlopszám<br>(fájl) | létezik-e<br>(fájl vagy mappa) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure-blob | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| Azure Data Lake Store | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| Azure File Storage | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| Fájlrendszer | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| SFTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| FTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |

**Relációs adatbázis:**

| Összekötő/metaadatok | struktúra | Oszlopszám | létezik-e |
|:--- |:--- |:--- |:--- |
| Azure SQL Database | √ | √ | √ |
| Azure SQL Data Warehouse | √ | √ | √ |
| SQL Server | √ | √ | √ |

### <a name="metadata-options"></a>Metaadat-beállítások

A következő metaadat-típusok beolvasása a GetMetadata tevékenység mezőlistán adható meg:

| Metaadatok típusa | Leírás |
|:--- |:--- |
| itemName | A fájl vagy mappa neve. |
| ItemType | A fájl vagy mappa típusú. Kimeneti érték `File` vagy `Folder`. |
| méret | A fájl bájtos mérete. A következő fájl csak alkalmazható. |
| létrehozva | A fájl vagy mappa létrehozott datetime. |
| Módosítás dátuma | Utolsó módosítás dátum és idő, a fájl vagy mappa. |
| childItems | Almappák és a megadott mappában lévő fájlok listájának. Csak a mappára érvényes. Kimeneti értéke nevét és típusát az egyes alárendelt elemek listáját. |
| contentMD5 | A fájl a MD5-kivonata. A következő fájl csak alkalmazható. |
| struktúra | A fájl vagy a relációs adatbázis tábla belül adatstruktúra. Kimeneti értéke oszlopnév és oszloptípus listáját. |
| Oszlopszám | A fájl vagy a relációs tábla belül oszlopok száma. |
| létezik-e| E egy fájl vagy mappa vagy a tábla létezik-e vagy sem. Vegye figyelembe, ha "létezik-e" a GetaMetadata mezőlistán van megadva, a tevékenység nem sikertelen, akkor is, ha a cikk (fájl/mappa vagy tábla) nem létezik; Ehelyett adja vissza `exists: false` kimenet. |

>[!TIP]
>Ha azt szeretné, ellenőrizze, hogy a fájl vagy mappa vagy a tábla létezik-e vagy sem, adja meg a `exists` a GetMetadata tevékenység mezőlistán, majd ellenőrizheti a `exists: true/false` eredménye a tevékenység kimenetét. Ha `exists` nem szerepel a listán, a GetMetadata tevékenység sikertelen lesz, amikor az objektum nem található.

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

## <a name="type-properties"></a>A típus tulajdonságai

Jelenleg a GetMetadata tevékenység is fetch metaadat-információkat a következő típusú.

Tulajdonság | Leírás | Szükséges
-------- | ----------- | --------
Mezőlista | Felsorolja a szükséges metaadatok. A részleteket a [metaadatok](#metadata-options) támogatott metaadatok szakaszt. | Igen 
Adatkészlet | A referencia-adatkészletnek amelynek metaadatok tevékenysége a GetMetadata tevékenység által kérhető. Lásd: [támogatott képességek](#supported-capabilities) támogatott összekötők a szakaszt, és tekintse meg a dataset szintaxis részletek összekötő témakör. | Igen

## <a name="sample-output"></a>Példa kimenet

A GetMetadata eredmény megjelenik az tevékenység kimenetét. Az alábbiakban a két minta hivatkozásként mező listában kijelölt teljes körű metaadatok beállításokkal. Az eredmény a soron következő tevékenységek használatához a mintát `@{activity('MyGetMetadataActivity').output.itemName}`.

### <a name="get-a-files-metadata"></a>A fájl metaadatot beszerezni

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

### <a name="get-a-folders-metadata"></a>A mappa metaadatot beszerezni

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
Tekintse meg a többi adat-előállító által támogatott vezérlésfolyam-tevékenységek: 

- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
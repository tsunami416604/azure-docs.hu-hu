---
title: "Azure Data Factory beolvasása a metaadatok tevékenység |} Microsoft Docs"
description: "Ismerje meg, hogyan használhatja az SQL Server tárolt eljárási tevékenység meghívni a Data Factory-folyamat az az Azure SQL Database vagy az Azure SQL Data Warehouse tárolt eljárást."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 20f3d4bb876a46b67385dd4435296e149641149e
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Az Azure Data Factory metaadatok tevékenység beolvasása
A metaadatok beolvasása tevékenység segítségével az Azure Data Factory bármely metaadatát lekérheti. Ez a tevékenység csak 2-es verzióját az adat-előállítók esetén támogatott. A következő esetekben használhatók:

- A metaadatok adatok ellenőrzése
- Egy folyamat indul el, ha adatok készen / érhető el

A következő funkciókat a vezérlési folyamatában érhető el:
- A GetMetadata tevékenység kimenete ellenőrzéshez feltételes kifejezéseket is használható.
- Egy folyamat is elindítható, amikor keresztül tegye feltétel teljesül-e-amíg ismétlési

Kimeneti adatkészlet szükséges bemeneti és kimeneti metaadatait érhető el, a GetMetadata tevékenység vesz. Jelenleg csak az Azure blob-adathalmazra használata támogatott. A támogatott metaadatmezőket mérete, felépítését és lastModified idő.  

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [Data Factory V1 dokumentáció](v1/data-factory-introduction.md).


## <a name="syntax"></a>Szintaxis

### <a name="get-metadata-activity-definition"></a>Metaadatok tevékenység definíciójának lekérdezése:
A következő példában a GetMetadata tevékenység a MyDataset által képviselt adatokkal kapcsolatos metaadatokat adja vissza. 

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
### <a name="dataset-definition"></a>Adatkészlet-definícióban:

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
            "Filename": "file.json",
            "format":{
                "type":"JsonFormat"
                "nestedSeperator": ","
            }
        }
    }
}
```

### <a name="output"></a>Kimenet
```json
{
    "size": 1024,
    "structure": [
        {
            "name": "id",
            "type": "Int64"
        }, 
    ],
    "lastModified": "2016-07-12T00:00:00Z"
}
```

## <a name="type-properties"></a>A típus tulajdonságai
Jelenleg GetMetadata tevékenység metaadat-információkat a következő típusú lehet beolvasni az Azure storage adatkészlethez.

Tulajdonság | Leírás | Megengedett értékek | Szükséges
-------- | ----------- | -------------- | --------
fieldList | Felsorolja a szükséges metaadatok.  | <ul><li>méret</li><li>struktúra</li><li>Módosítás dátuma</li></ul> |    Nem<br/>Ha üres, a tevékenység minden 3 támogatott metaadat-információkat ad vissza. 
Adatkészlet | A referencia-adatkészletnek amelynek metaadatok tevékenysége a GetMetadata tevékenység által kérhető. <br/><br/>A dataset jelenleg támogatott típus Azure Blob. Két sub tulajdonságai a következők: <ul><li><b>Hivatkozásnév</b>: egy meglévő Azure Blob-adathalmazra mutató hivatkozás</li><li><b>típus</b>: mivel az adatkészlet hivatkoznak, célszerű, "DatasetReference" típusú</li></ul> |    <ul><li>Karakterlánc</li><li>DatasetReference</li></ul> | Igen

## <a name="next-steps"></a>További lépések
Tekintse meg a többi adat-előállító által támogatott vezérlésfolyam-tevékenységek: 

- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
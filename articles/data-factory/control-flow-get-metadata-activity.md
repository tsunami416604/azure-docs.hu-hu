---
title: Metaadatok beolvasása tevékenység Azure Data Factory
description: Megtudhatja, hogyan használhatja a metaadatok beolvasása tevékenységet egy Data Factory folyamaton.
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
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: e891f6675920e7bb90d2a6d007676cdd65f19917
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679892"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Metaadatok beolvasása tevékenység Azure Data Factory

A metaadatok beolvasása tevékenység segítségével lekérheti a Azure Data Factoryban található adatok metaadatait. Ezt a tevékenységet a következő esetekben használhatja:

- Ellenőrizze az adatok metaadatait.
- Folyamat elindítása, ha az adatgyűjtés kész/elérhető.

A következő funkciók érhetők el a vezérlési folyamatban:

- Az érvényesítés végrehajtásához használhatja a metaadatok beolvasása tevékenységből a feltételes kifejezésekben szereplő kimenetet.
- A folyamat akkor aktiválható, ha a feltételt a "Do" utasításon keresztül, a hurok nélkül kell megtenni.

## <a name="capabilities"></a>Funkciók

A metaadatok beolvasása tevékenység bemenetként fogadja az adatkészletet, és a metaadatok adatait adja vissza kimenetként. Jelenleg a következő összekötők és a megfelelő lekérdezhető metaadatok támogatottak. A visszaadott metaadatok maximális mérete 1 MB.

>[!NOTE]
>Ha a metaadatok lekérése tevékenységet egy saját üzemeltetésű integrációs modulon futtatja, a legújabb funkciók a 3,6-es vagy újabb verziókban támogatottak.

### <a name="supported-connectors"></a>Támogatott összekötők

**File Storage**

| Összekötő/metaadatok | ItemName<br>(fájl/mappa) | ItemType<br>(fájl/mappa) | méret<br>fájl | létrehozott<br>(fájl/mappa) | lastModified<br>(fájl/mappa) |childItems<br>mappa |contentMD5<br>fájl | szerkezet<br/>fájl | columnCount<br>fájl | létezik<br>(fájl/mappa) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√ * | √ | x | √ | √ | √/√ * |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√ * | √ | x | √ | √ | √/√ * |
| [Azure Blob Storage](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√ * | √ | √ | √ | √ | √/√ |
| [1. generációs Azure Data Lake Storage](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Files](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Fájlrendszer](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |

- Az Amazon S3 és a Google Cloud Storage esetében `lastModified` a gyűjtőre és a kulcsra, de nem a virtuális mappára vonatkozik, és `exists` a gyűjtőre és a kulcsra, de nem az előtagra vagy a virtuális mappára vonatkozik.
- Az Azure Blob Storage esetében a `lastModified` a tárolóra és a blobra vonatkozik, de a virtuális mappára nem.

**Viszonyítási adatbázis**

| Összekötő/metaadatok | szerkezet | columnCount | létezik |
|:--- |:--- |:--- |:--- |
| [Azure SQL Database](connector-azure-sql-database.md) | √ | √ | √ |
| [Felügyelt Azure SQL Database-példány](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Metaadatok beállításai

A következő metaadatokat adhatja meg a metaadatok beolvasása tevékenység mezőinek listájában a megfelelő információk lekéréséhez:

| Metaadat típusa | Leírás |
|:--- |:--- |
| ItemName | A fájl vagy mappa neve. |
| ItemType | A fájl vagy mappa típusa. A visszaadott érték `File` vagy `Folder`. |
| méret | A fájl mérete bájtban megadva. Csak a fájlokra érvényes. |
| létrehozott | A fájl vagy mappa dátum és idő (datetime) létrehozása. |
| lastModified | A fájl vagy mappa utolsó módosításának datetime értéke. |
| childItems | A megadott mappában található almappák és fájlok listája. Csak a mappákra érvényes. A visszaadott érték az egyes alárendelt elemek nevének és típusának listája. |
| contentMD5 | A fájl MD5-je. Csak a fájlokra érvényes. |
| szerkezet | A fájl vagy a viszonyítási adatbázis táblázatának adatstruktúrája. A visszaadott érték az oszlopnevek és az oszlopok típusának listája. |
| columnCount | A fájl vagy a rokon tábla oszlopainak száma. |
| létezik| Azt határozza meg, hogy létezik-e fájl, mappa vagy tábla. Vegye figyelembe, hogy ha `exists` van megadva a metaadatok beolvasása mezők listájában, akkor a tevékenység nem fog működni, még akkor sem, ha a fájl, mappa vagy tábla nem létezik. Ehelyett a rendszer visszaadja a `exists: false` a kimenetben. |

>[!TIP]
>Ha szeretné ellenőrizni, hogy egy fájl, mappa vagy tábla létezik-e, a metaadatok beolvasása tevékenység mezők listájában válassza a `exists` lehetőséget. Ezt követően a tevékenység kimenetének `exists: true/false` eredményét is megtekintheti. Ha `exists` nincs megadva a mezőlista, a metaadatok beolvasása tevékenység sikertelen lesz, ha az objektum nem található.

>[!NOTE]
>Amikor metaadatokat kap a tárakból, és konfigurálja `modifiedDatetimeStart` vagy `modifiedDatetimeEnd`, a kimenetben lévő `childItems` csak a megadott tartományon belüli utolsó módosítási idővel rendelkező fájlokat fogja tartalmazni. A nem tartalmazza az almappákban található elemeket.

## <a name="syntax"></a>Szintaxis

**Metaadatok beolvasása tevékenység**

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

A metaadatok beolvasása tevékenység jelenleg a következő típusú metaadatokat tudja visszaadni:

Tulajdonság | Leírás | Kötelező
-------- | ----------- | --------
Mezőlista | A metaadatokhoz szükséges információk típusai. A támogatott metaadatokkal kapcsolatos részletekért tekintse meg a jelen cikk [metaadat-beállítások](#metadata-options) című szakaszát. | Igen 
Adatkészlet | A metaadatok beolvasása tevékenység által a metaadatokat lekérő hivatkozási adatkészlet. A támogatott összekötők információit a [képességek](#capabilities) című szakaszban találja. Az adatkészlet szintaxisával kapcsolatos részletekért tekintse meg az összekötőhöz kapcsolódó témaköröket. | Igen
formatSettings | Alkalmazza a Format Type adatkészlet használatakor. | Nem
storeSettings | Alkalmazza a Format Type adatkészlet használatakor. | Nem

## <a name="sample-output"></a>Példa kimenet

A metaadatok beolvasása eredmények a tevékenység kimenetében jelennek meg. A következő két minta kiterjedt metaadat-beállításokat jelenít meg. Ha az eredményeket egy későbbi tevékenységben szeretné használni, használja a következő mintát: `@{activity('MyGetMetadataActivity').output.itemName}`.

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
További információ a Data Factory által támogatott egyéb irányítási folyamatokról:

- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [ForEach tevékenység](control-flow-for-each-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)

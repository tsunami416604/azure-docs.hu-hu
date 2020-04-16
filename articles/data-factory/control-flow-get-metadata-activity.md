---
title: Metaadat-tevékenység beszereznie az Azure Data Factoryban
description: Ismerje meg, hogyan használhatja a Metaadat-folyamat beszereznie tevékenységet egy Data Factory-folyamatban.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jingwang
ms.openlocfilehash: 344ad8e106c119c1de59570d1ec4e3df5e1cc8af
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417109"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Metaadat-tevékenység beszereznie az Azure Data Factoryban
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Metaadat-szolgáltatás beolvasása tevékenység segítségével lekérheti az Azure Data Factory bármely adatmetaadatait. Ezt a tevékenységet a következő esetekben használhatja:

- Bármely adat metaadatainak ellenőrzése.
- Folyamat ot indít, ha az adatok készen állnak/elérhetők.

A vezérlőfolyamat a következő funkciókat elérhető:

- A Metaadat-műveletek becsatornázása feltételes kifejezésekben az ellenőrzés végrehajtásához használhatja a Metaadat-tevékenység beírása kimenetét.
- Akkor indíthat el egy folyamatot, ha egy feltétel teljesül a Do Until hurkolásig keresztül.

## <a name="capabilities"></a>Funkciók

A Metaadat beszerezése tevékenység egy adatkészletet vesz fel bemenetként, és metaadat-információt ad vissza kimenetként. Jelenleg a következő összekötők és a megfelelő leolvasható metaadatok támogatottak. A visszaadott metaadatok maximális mérete 2 MB.

>[!NOTE]
>Ha a Metaadatok beolvasása tevékenységet saját üzemeltetésű integrációs futásidőben futtatja, a legújabb funkciók at 3.6-os vagy újabb verzió támogatja.

### <a name="supported-connectors"></a>Támogatott összekötők

**Fájltárolás**

| Összekötő/metaadatok | itemName elem<br>(fájl/mappa) | Itemtype<br>(fájl/mappa) | size<br>(fájl) | Létrehozott<br>(fájl/mappa) | lastModified (utolsó módosítás: )<br>(fájl/mappa) |childItems (gyermekelemek)<br>(mappa) |contentMD5<br>(fájl) | Szerkezet<br/>(fájl) | oszlopszáma<br>(fájl) | Létezik<br>(fájl/mappa) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Azure Blob Storage](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [1. generációs Azure Data Lake Storage](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [2. generációs Azure Data Lake Storage](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Files](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Fájlrendszer](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

- Ha metaadat-tevékenység beszedése van egy mappában, győződjön meg arról, hogy list/EXECUTE engedéllyel rendelkezik az adott mappához.
- Az Amazon S3 és `lastModified` a Google Cloud Storage, vonatkozik a vödröt és a kulcsot, de nem a virtuális mappát, és `exists` vonatkozik a vödör és a kulcs, de nem az előtag vagy virtuális mappát.
- Az Azure Blob `lastModified` storage, vonatkozik a tároló és a blob, de nem a virtuális mappát.
- `lastModified`a szűrő jelenleg a gyermekelemek szűrésére vonatkozik, de magára a mappára/fájlra nem.
- A mappákon/fájlokon lévő helyettesítő karakter szűrő nem támogatott a Metaadat-tevékenység bekésése esetén.

**Relációs adatbázis**

| Összekötő/metaadatok | Szerkezet | oszlopszáma | Létezik |
|:--- |:--- |:--- |:--- |
| [Azure SQL Database](connector-azure-sql-database.md) | √ | √ | √ |
| [Azure SQL Database felügyelt példánya](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [Azure SQL adattárház](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Metaadatok beállításai

A metaadatok beolvasása mezőben a következő metaadat-típusokat adhatja meg a megfelelő információk beolvasásához:

| Metaadat-típus | Leírás |
|:--- |:--- |
| itemName elem | A fájl vagy mappa neve. |
| Itemtype | A fájl vagy mappa típusa. A visszaadott `Folder`érték vagy `File` . |
| size | A fájl mérete bájtban. Csak fájlokra alkalmazható. |
| Létrehozott | A fájl vagy mappa dátumának létrehozása. |
| lastModified (utolsó módosítás: ) | A fájl vagy mappa utolsó módosításdátuma. |
| childItems (gyermekelemek) | Az adott mappában lévő almappák és fájlok listája. Csak mappákra alkalmazható. A visszaadott érték az egyes gyermekelemek nevének és típusának listája. |
| contentMD5 | A fájl MD5-ös részében. Csak fájlokra alkalmazható. |
| Szerkezet | A fájl- vagy relációs adatbázistábla adatszerkezete. A visszaadott érték az oszlopnevek és az oszloptípusok listája. |
| oszlopszáma | A fájl- vagy relációs tábla oszlopainak száma. |
| Létezik| Azt jelzi, hogy létezik-e fájl, mappa vagy tábla. Ne feledje, hogy ha `exists` meg van adva a Metaadatok bekerülési adatai nak listájában, a tevékenység akkor sem fog sikertelen, ha a fájl, mappa vagy tábla nem létezik. Ehelyett `exists: false` a kimenetben ad vissza. |

>[!TIP]
>Ha ellenőrizni szeretné, hogy létezik-e fájl, `exists` mappa vagy tábla, adja meg a Metaadatok bekerülése mezőlistában. Ezután ellenőrizheti `exists: true/false` az eredményt a tevékenység kimenetében. Ha `exists` nincs megadva a mezőlistában, a Metaadat bekerülési adatai nak bekerülési értéke sikertelen lesz, ha az objektum nem található.

>[!NOTE]
>Amikor metaadatokat kap a `modifiedDatetimeStart` fájltárolókból, és konfigurálja a vagy `modifiedDatetimeEnd`a kimenetet, akkor csak azok a `childItems` fájlok lesznek az adott elérési úton, amelyek utolsó módosítási ideje a megadott tartományon belül van. Az in nem tartalmazza az almappákban lévő elemeket.

## <a name="syntax"></a>Szintaxis

**Metaadat-tevékenység beszereznie**

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

Jelenleg a Metaadat beküldése tevékenység a következő metaadat-adatokat adja vissza:

Tulajdonság | Leírás | Kötelező
-------- | ----------- | --------
fieldList | A szükséges metaadat-adatok típusai. A támogatott metaadatokról a cikk [Metaadatok beállításai](#metadata-options) című szakaszában olvashat részletesen. | Igen 
Adatkészlet | Az a referencia-adatkészlet, amelynek metaadatait a Metaadat beolvasása tevékenység nek kell beolvasnia. A támogatott [összekötőkről](#capabilities) a Képességek című szakaszban talál tájékoztatást. Tekintse meg az adatkészlet szintaxisának részleteit az összekötő konkrét témakörei. | Igen
formatSettings (Beállítások) | Alkalmazás formátumtípus-adatkészlet használataesetén. | Nem
storeSettings (tárolóbeállítások) | Alkalmazás formátumtípus-adatkészlet használataesetén. | Nem

## <a name="sample-output"></a>Példa kimenet

A Metaadat-eredmények bekésése a tevékenység kimenetében jelenik meg. Az alábbiakban két minta látható kiterjedt metaadat-beállításokat. Ha az eredményeket egy későbbi tevékenységben `@{activity('MyGetMetadataActivity').output.itemName}`szeretné használni, használja ezt a mintát: .

### <a name="get-a-files-metadata"></a>Fájl metaadatainak beszereznie

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

### <a name="get-a-folders-metadata"></a>Mappa metaadatainak beszereznie

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
További információ a Data Factory által támogatott egyéb vezérlési folyamattevékenységekről:

- [Folyamattevékenység végrehajtása](control-flow-execute-pipeline-activity.md)
- [ForEach tevékenység](control-flow-for-each-activity.md)
- [Keress tevékenységet](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)

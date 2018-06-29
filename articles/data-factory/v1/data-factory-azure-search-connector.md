---
title: Adatok leküldése Search-index használatával a Data Factory |} Microsoft Docs
description: További tudnivalók az adatok Azure Data Factory használatával küldje le az Azure Search-Index.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f68e1077ebc26245b25eae3b0310db74b6d1357e
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046445"
---
# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>Elküldik az adatokat az Azure Search-index Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verziójával](data-factory-azure-search-connector.md)
> * [(Az aktuális verzió) 2-es verzió](../connector-azure-search.md)

> [!NOTE]
> Ez a cikk a Data Factory 1 verziójára vonatkozik. A Data Factory szolgáltatásnak aktuális verziójának használatakor lásd [Azure Search-összekötőt, a V2](../connector-azure-search.md).

A cikkből megtudhatja, hogyan használható a másolási tevékenység során elküldik az adatokat egy támogatott forráshierarchiából adattárból az Azure Search-index. A forrás oszlop a felsorolt támogatott forrás adattárolókhoz a [támogatott források és mosdók](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. Ez a cikk épít, a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, amely adatmozgás általános áttekintést során másolási tevékenység és a támogatott adatokat tároló kombinációja.

## <a name="enabling-connectivity"></a>Kapcsolat engedélyezése
Ahhoz, hogy a Data Factory service csatlakozás helyszíni adattárolóihoz, telepítse az adatkezelési átjáró a helyszíni környezetben. Átjáró telepíthető ugyanazon a számítógépen a forrásadatok állomások tároló vagy egy külön számítógépen erőforrás esetén az adattár versengő elkerülése érdekében.

Az adatkezelési átjáró csatlakozik a helyszíni adatforrások felhőszolgáltatások biztonságának és kezelésének módja. Lásd: [helyezze át az adatokat a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) szóló cikkben olvashat az adatkezelési átjáró.

## <a name="getting-started"></a>Első lépések
A másolási tevékenység során, az leküldi adatok egy forrás adattárból Azure Search-index különböző eszközök/API-k használatával létrehozhat egy folyamatot.

Hozzon létre egy folyamatot a legegyszerűbb módja használatára a **másolása varázsló**. Lásd: [oktatóanyag: hozzon létre egy folyamatot, másolása varázslóval](data-factory-copy-data-wizard-tutorial.md) létrehozásával egy folyamatot, az adatok másolása varázsló segítségével gyorsan útmutatást.

Az alábbi eszközöket használhatja a folyamatokat létrehozni: **Azure-portálon**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-sablon** , **.NET API**, és **REST API-t**. Lásd: [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját. 

Akár az eszközök vagy API-k, hajtsa végre a következő lépésekkel hozza létre egy folyamatot, amely mozgatja az adatokat a forrás-tárolóban a fogadó tárolóban: 

1. Hozzon létre **összekapcsolt szolgáltatások** bemeneti és kimeneti adatok csatolásához tárolja a a data factory.
2. Hozzon létre **adatkészletek** a másolási művelet bemeneti és kimeneti adatok. 
3. Hozzon létre egy **csővezeték** , amely fogad egy bemeneti adatkészlet és egy kimeneti adatkészletet másolási tevékenységgel. 

A varázsló használatakor a Data Factory entitások (összekapcsolt szolgáltatások adatkészletek és a feldolgozási sor) JSON-definíciók automatikusan létrejönnek. Eszközök/API-k (kivéve a .NET API-t) használata esetén adja meg a Data Factory entitások a JSON formátum használatával.  Adatok másolása az Azure Search-index használt adat-előállító entitások JSON-definíciók minta, lásd: [JSON-példa: adatok másolása az helyszíni SQL Server az Azure Search-index](#json-example-copy-data-from-on-premises-sql-server-to-azure-search-index) című szakaszát. 

A következő szakaszok részletesen bemutatják, amely segítségével az Azure Search-Index megadása a Data Factory tartozó entitások JSON-tulajdonságok:

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

A következő táblázat ismerteti, amelyek a kapcsolódó Azure Search szolgáltatásra vonatkozó JSON-elemek szerepelnek.

| Tulajdonság | Leírás | Szükséges |
| -------- | ----------- | -------- |
| type | A type tulajdonságot kell beállítani: **AzureSearch**. | Igen |
| url | Az Azure Search szolgáltatás URL-címe. | Igen |
| kulcs | Az Azure Search szolgáltatás adminisztrációs kulcsot. | Igen |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Illetve meghatározásához adatkészletek rendelkezésre álló tulajdonságok teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például struktúra, a rendelkezésre állás és a házirend a DataSet adatkészlet JSON hasonlítanak minden adatkészlet esetében. A **typeProperties** szakaszban nem egyezik az adatkészlet egyes típusú. A typeProperties szakasz egy adatkészlet típusú **AzureSearchIndex** tulajdonságai a következők:

| Tulajdonság | Leírás | Szükséges |
| -------- | ----------- | -------- |
| type | A type tulajdonságot meg kell **AzureSearchIndex**.| Igen |
| indexName | Az Azure Search-index neve. Adat-előállító nem hoz létre az indexet. Az index az Azure Search léteznie kell. | Igen |


## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok és tevékenységek meghatározásához rendelkezésre álló tulajdonságok teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Például a nevét, leírását, bemeneti és kimeneti tábláinak és különböző házirendek tulajdonságok minden típusú tevékenységek érhetők el. Mivel a typeProperties szakaszban rendelkezésre álló tulajdonságok tevékenységek minden típusának függenek. A másolási tevékenység során két érték források és mosdók típusától függően.

A másolási tevékenység, ha a fogadó típusa nem **AzureSearchIndexSink**, a következő tulajdonságok érhetők el typeProperties szakaszában:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| -------- | ----------- | -------------- | -------- |
| writeBehavior | Megadja, hogy egyesíteni vagy cserélje le, ha az index már létezik egy dokumentumot. Tekintse meg a [WriteBehavior tulajdonság](#writebehavior-property).| Egyesítés (alapértelmezett)<br/>Feltöltés| Nem |
| WriteBatchSize | Amikor a puffer mérete eléri writeBatchSize feltölti az adatok be az Azure Search-index. Tekintse meg a [WriteBatchSize tulajdonság](#writebatchsize-property) részleteiről. | 1-1 000. Alapértelmezett érték 1000. | Nem |

### <a name="writebehavior-property"></a>WriteBehavior tulajdonság
AzureSearchSink upserts adatok írásakor. Ez azt jelenti történő írásakor egy dokumentumot, ha a dokumentum kulcs már létezik az Azure Search-index, az Azure Search frissíti a meglévő dokumentumról, hanem egy ütközés Kivétel kiváltása.

A AzureSearchSink az alábbi két upsert viselkedésmódok biztosít a (AzureSearch SDK használatával):

- **Egyesítési**: kombinálhatja az összes oszlop az új dokumentum a meglévővel. Az oszlop, null értéket ad meg az új dokumentum a meglévő ütemezés értékét megőrződik.
- **Töltse fel**: az új dokumentum lecseréli a meglévő fájlt. Nincs megadva az új dokumentum oszlopokhoz a beállítás értéke be NULL értékre van-e egy nem null értéket a meglévő dokumentum vagy sem.

Az alapértelmezett viselkedés **egyesítése**.

### <a name="writebatchsize-property"></a>WriteBatchSize tulajdonság
Az Azure Search szolgáltatás egy kötegelt dokumentumok írása támogatja. A kötegelt 1-1 000 műveletek is tartalmazhat. Egy műveletet a feltöltési/egyesítési művelet egy dokumentum kezeli.

### <a name="data-type-support"></a>Adattípus-támogatás
Az alábbi táblázat felsorolja, hogy az Azure Search adattípus támogatott-e, vagy nem.

| Az Azure Search-adattípus | Az Azure Search fogadó támogatott |
| ---------------------- | ------------------------------ |
| Sztring | I |
| Int32 | I |
| Int64 | I |
| Dupla | I |
| Logikai | I |
| DataTimeOffset | I |
| Karakterlánc-tömbben | N |
| GeographyPoint | N |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>JSON-példa: adatok másolása az helyszíni SQL Server az Azure Search-index

A következő példában:

1.  A társított szolgáltatás típusa [AzureSearch](#linked-service-properties).
2.  A társított szolgáltatás típusa [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3.  Bemeneti [dataset](data-factory-create-datasets.md) típusú [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4.  Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureSearchIndex](#dataset-properties).
4.  A [csővezeték](data-factory-create-pipelines.md) , a másolási tevékenység által használt [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) és [AzureSearchIndexSink](#copy-activity-properties).

A minta másol idősorozat adatokat a helyszíni SQL Server adatbázis az Azure Search-index óránként. Ez a minta használt JSON-tulajdonságok a mintákat a következő szakaszok ismertetik.

Első lépésként a telepítő az adatkezelési átjáró a helyi számítógépen. Az utasítások szerepelnek a [adatokat a helyszíni helyek és a felhő közötti áthelyezése](data-factory-move-data-between-onprem-and-cloud.md) cikk.

**Az Azure Search kapcsolódó szolgáltatás:**

```JSON
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

**Kapcsolódó SQL Server szolgáltatás**

```JSON
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```

**SQL Server bemeneti adatkészlet**

A minta azt feltételezi, hogy létrehozott egy tábla "MyTable" SQL Server és a "timestampcolumn" nevű adatsorozat időadatok oszlopot tartalmaz. Lekérheti az egyetlen adatkészlet ugyanazon adatbázis több tábla keresztül, de egyetlen tábla a dataset tableName typeProperty kell használni.

"External" beállítása: "true" tájékoztatja Data Factory szolgáltatásnak, hogy az adatkészlet data factoryval való külső, és egy tevékenység adat-előállító nem hozzák.

```JSON
{
  "name": "SqlServerDataset",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Az Azure Search kimeneti adatkészlet:**

A minta másolja az adatokat az Azure Search-index nevű **termékek**. Adat-előállító nem hoz létre az indexet. A minta teszteléséhez index létrehozása ezen a néven. Hozzon létre az Azure Search-index a azonos számú oszlopot, mint a bemeneti adatkészletet. Új bejegyzések kerülnek az Azure Search-index óránként.

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
   }
}
```

**Másolási tevékenység során a folyamat az SQL-forrás és fogadó Azure Search-Index:**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **SqlSource** és **fogadó** típusúra **AzureSearchIndexSink**. A megadott SQL-lekérdezést a **SqlReaderQuery** tulajdonság kiválasztása az adatok másolása az elmúlt órában.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
     ]
   }
}
```

Ha a másolt adatok egy felhőalapú adattárból Azure Search szolgáltatásba történő `executionLocation` tulajdonság megadása kötelező. Az alábbi JSON kódrészletben láthatja a másolási tevékenység során a szükséges módosítást `typeProperties` példaként. Ellenőrizze [felhőalapú adattároló közötti másolásához](data-factory-data-movement-activities.md#global) szakaszban a támogatott értékek és a további részleteket.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```


## <a name="copy-from-a-cloud-source"></a>A felhő forrás másolása
Ha a másolt adatok egy felhőalapú adattárból Azure Search szolgáltatásba történő `executionLocation` tulajdonság megadása kötelező. Az alábbi JSON kódrészletben láthatja a másolási tevékenység során a szükséges módosítást `typeProperties` példaként. Ellenőrizze [felhőalapú adattároló közötti másolásához](data-factory-data-movement-activities.md#global) szakaszban a támogatott értékek és a további részleteket.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

A másolási tevékenység definíciójának fogadó adatkészletből oszlopok forrás adatkészletből oszlopokat is leképezheti. További információkért lásd: [Azure Data Factory dataset oszlopai leképezési](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás  
Tekintse meg a [másolási tevékenység teljesítmény- és hangolási útmutató](data-factory-copy-activity-performance.md) adatátvitelt jelölik a (másolási tevékenység) és a különböző módokon azt optimalizálása hatása teljesítmény kulcsfontosságú szerepet játszik tájékozódhat.

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

* [Másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) való a másolási tevékenység során a folyamat létrehozásának lépéseit.

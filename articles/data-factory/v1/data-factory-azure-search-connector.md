---
title: Adatok leküldése a Search-index Data Factory használatával |} A Microsoft Docs
description: Ismerje meg az Azure Search-Index adatok elküldése az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4d3c67974bc1dd0e52d3de457071d550a6379e36
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54023087"
---
# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>Adatok leküldése az Azure Search-index az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-azure-search-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-azure-search.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [Azure Search-összekötő a v2-ben](../connector-azure-search.md).

Ez a cikk ismerteti, hogyan használható a másolási tevékenység az adatok leküldéséhez forrásadattárból egy támogatott az Azure Search-indexbe. A forrás oszlopban található felsorolt támogatott forrás adattárak a [forrásként és fogadóként támogatott](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. Ez a cikk épül, amely a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) cikk, amelyen az adatok áthelyezését általános áttekintése a másolási tevékenység és a támogatott data store kombinációk.

## <a name="enabling-connectivity"></a>Kapcsolat engedélyezése
Ahhoz, hogy a Data Factory szolgáltatás kapcsolódni egy helyszíni adattárolóban, telepítse az adatkezelési átjáró a helyszíni környezetben. Átjáró telepíthető ugyanarra a gépre, amely a forrásadatok gazdagépek tárolása vagy egy külön számítógépen az adattár erőforrások versengő elkerülése érdekében.

Adatkezelési átjáró felhőszolgáltatások olyan biztonságos és felügyelt módon a helyszíni adatforrásokhoz kapcsolódik. Lásd: [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) adatkezelési átjáró részleteit ismertető cikket.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel az, hogy az adatokat egy forrásadattárból Azure Search-indexbe más eszközök/API-k használatával.

A folyamat létrehozásának legegyszerűbb módja az, hogy használja a **másolása varázsló**. Lásd: [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md) gyors bemutató létrehozása egy folyamatot az adatok másolása varázsló használatával.

A következő eszközök használatával hozzon létre egy folyamatot: **Az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, **Azure Resource Manager-sablon**, **.NET API**, és  **REST API-val**. Lásd: [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját. 

Az eszközök vagy az API-kat használja, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani: 

1. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához.
2. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli. 
3. Hozzon létre egy **folyamat** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként. 

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) JSON-definíciói az Ön számára. Eszközök/API-k (kivéve a .NET API) használatakor adja meg a Data Factory-entitások a JSON formátumban.  A minta az adatok másolása az Azure Search-index használt Data Factory-entitások JSON-definíciói: [JSON-példa: Adatok másolása helyszíni SQL Serverről az Azure Search-index](#json-example-copy-data-from-on-premises-sql-server-to-azure-search-index) című szakaszát. 

Az alábbi szakaszok nyújtanak az Azure Search-Index adott Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részletekért:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az alábbi táblázat ismerteti a társított Azure Search szolgáltatás adott JSON-elemek.

| Tulajdonság | Leírás | Szükséges |
| -------- | ----------- | -------- |
| type | A type tulajdonságot kell beállítani: **Az AzureSearch**. | Igen |
| url | Az Azure Search szolgáltatás URL-címe. | Igen |
| kulcs | Az Azure Search szolgáltatás rendszergazdai kulcsa. | Igen |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és adatkészletek definiálását tulajdonságok teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például a szerkezetet, rendelkezésre állást és szabályzatát adatkészlet JSON szakaszok hasonlóak az összes adatkészlet esetében. A **typeProperties** szakasz eltér az egyes adatkészlet. A typeProperties szakasz egy adatkészlet típusú **AzureSearchIndex** tulajdonságai a következők:

| Tulajdonság | Leírás | Szükséges |
| -------- | ----------- | -------- |
| type | A type tulajdonságot állítsa **AzureSearchIndex**.| Igen |
| indexName | Az Azure Search-index neve. Adat-előállító nem hoz létre az indexet. Az index léteznie kell az Azure Search szolgáltatásban. | Igen |


## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok és a tevékenységek definiálását tulajdonságok teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Tulajdonságok, mint például a nevét, leírását, bemeneti és kimeneti táblák és a különféle szabályzatok minden típusú tevékenységek érhetők el. Mivel a typeProperties szakasz tulajdonságai tevékenységek minden típusának számától függ. A másolási tevékenységhez azok változhat a forrásként és fogadóként típusú is.

A másolási tevékenység, ha a fogadó típusa nem **AzureSearchIndexSink**, typeProperties szakasz érhető el az alábbi tulajdonságokat:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Megadja, hogy egyesítéséhez, vagy cserélje le, amikor a dokumentum az indexben már létezik. Tekintse meg a [WriteBehavior tulajdonság](#writebehavior-property).| Egyesítés (alapértelmezett)<br/>Feltöltés| Nem |
| WriteBatchSize | Amikor a puffer mérete eléri a writeBatchSize feltölti az adatokat az Azure Search-indexbe. Tekintse meg a [WriteBatchSize tulajdonság](#writebatchsize-property) részleteiről. | az 1000 1. Alapértelmezett értéke 1000. | Nem |

### <a name="writebehavior-property"></a>WriteBehavior tulajdonság
Az adatok írásakor AzureSearchSink upserts. Más szóval ha ír egy dokumentumot, ha az Azure Search-index már létezik a dokumentum kulcsaként, Azure Search frissíti egy ütközést kivétel értesítő helyett a meglévő dokumentumot.

A AzureSearchSink biztosítja az alábbi két upsert viselkedések (SDK-val AzureSearch):

- **Egyesítse**: összes oszlop az új dokumentum a egyesítése. Az új dokumentum null értékű oszlopokhoz a rendszer megőrzi az értéket a meglévőt.
- **Töltse fel**: Az új dokumentum felülírja a meglévőt. Nincs megadva az új dokumentum oszlopokhoz a értéke van-e egy nem null értéket a meglévő dokumentum vagy nem null értékű.

Alapértelmezés szerint **egyesítése**.

### <a name="writebatchsize-property"></a>WriteBatchSize tulajdonság
Az Azure Search szolgáltatás támogatja a kötegelt dokumentumok írása. A batch 1-1 000 műveletek is tartalmazhat. Egy műveletet kezeli egy dokumentumot a feltöltési/merge művelet végrehajtásához.

### <a name="data-type-support"></a>Adattípus-támogatás
Az alábbi tábla meghatározza, hogy egy Azure Search adattípus támogatott-e, vagy nem.

| Az Azure Search-adattípus | Az Azure Search fogadó támogatott |
| ---------------------- | ------------------------------ |
| Karakterlánc | I |
| Int32 | I |
| Int64 | I |
| Dupla | I |
| Logikai | I |
| DataTimeOffset | I |
| Karakterlánc-tömbben | N |
| GeographyPoint | N |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>JSON-példa: Adatok másolása helyszíni SQL Serverről az Azure Search-indexbe

Az alábbi mintában látható:

1.  A társított szolgáltatás típusa [AzureSearch](#linked-service-properties).
2.  A társított szolgáltatás típusa [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3.  Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4.  Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureSearchIndex](#dataset-properties).
4.  A [folyamat](data-factory-create-pipelines.md) egy másolási tevékenységgel, amely használja [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) és [AzureSearchIndexSink](#copy-activity-properties).

A minta idősorozat-adatokat másol egy helyszíni SQL Server-adatbázisból az Azure Search-index óránként. Ebben a példában használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

Első lépésként a telepítő az adatkezelési átjárót a helyszíni gépen. A rendszer az utasításokat a [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) cikk.

**Az Azure Search társított szolgáltatás:**

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

**Az SQL Server-alapú társított szolgáltatás**

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

**Az SQL Server bemeneti adatkészlet**

A minta azt feltételezi, létrehozott egy táblát "MyTable" az SQL Server és a egy idősorozat-adatok a "timestampcolumn" nevű oszlopot tartalmaz. Egyetlen adatkészlet használata ugyanabban az adatbázisban több tábla alatt lekérdezheti, de az adatkészlet tableName typeProperty egyetlen táblában kell használni.

Beállítás az "external": "true" tájékoztatja a Data Factory szolgáltatásban, hogy az adatkészletet a data factory a külső, és nem hozzák az adat-előállító adott tevékenységéhez.

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

A minta másolnak adatokat az Azure Search-index nevű **termékek**. Adat-előállító nem hoz létre az indexet. A minta teszteléséhez index létrehozása ezen a néven. Az azonos számú oszlopot, ahogy a bemeneti adatkészlet létrehozása az Azure Search-index. Új bejegyzések kerülnek az Azure Search-index óránként.

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

**Az SQL-forrás és fogadó Azure Search-Index a folyamat másolási tevékenysége:**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **SqlSource** és **fogadó** típusa **AzureSearchIndexSink**. A megadott SQL-lekérdezést a **SqlReaderQuery** tulajdonság kiválasztja az adatokat másolni az elmúlt órában.

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

Ha adatokat másolhat egy felhőalapú adattárból az Azure searchbe `executionLocation` tulajdonságot kötelező megadni. A következő JSON-kódrészletben látható a másolási tevékenység a szükséges módosítást `typeProperties` példaként. Ellenőrizze [adatmásolás felhőalapú adattár közötti adatáthelyezéshez](data-factory-data-movement-activities.md#global) támogatott értékei, és további részleteket a következő szakaszban.

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


## <a name="copy-from-a-cloud-source"></a>A felhőbeli forrásból
Ha adatokat másolhat egy felhőalapú adattárból az Azure searchbe `executionLocation` tulajdonságot kötelező megadni. A következő JSON-kódrészletben látható a másolási tevékenység a szükséges módosítást `typeProperties` példaként. Ellenőrizze [adatmásolás felhőalapú adattár közötti adatáthelyezéshez](data-factory-data-movement-activities.md#global) támogatott értékei, és további részleteket a következő szakaszban.

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

A másolási tevékenységhez tartozó definíció a fogadó-adatkészlet-oszlop a forrásadatkészlet oszlopok is leképezheti. További információkért lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás  
Tekintse meg a [másolási tevékenységek teljesítményéről és finomhangolásáról szóló útmutató](data-factory-copy-activity-performance.md) megismerheti a legfontosabb tényezők adatáthelyezés (másolási tevékenységgel) és a különféle módokon optimalizálhatja azt, hogy hatással lehet a teljesítményre.

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

* [Másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) létrehoz egy folyamatot egy másolási tevékenységgel rendelkező kapcsolatos lépésenkénti útmutatót.

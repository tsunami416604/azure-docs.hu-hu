---
title: Az adatküldés a keresési indexbe Data Factory használatával
description: Útmutató az adatküldéshez Azure Search indexhez Azure Data Factory használatával.
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
ms.openlocfilehash: 09b891ba753291511bb1f203b7ac4437e6b2c542
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683112"
---
# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>Az adatküldés Azure Search indexbe Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-azure-search-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-azure-search.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg az Azure Search Connector v2-ben](../connector-azure-search.md)című témakört.

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység az adatok egy támogatott forrás adattárból való leküldéséhez Azure Search indexbe. A támogatott forrás adattárakat a [támogatott források és a mosogatók](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla forrás oszlopában találja. Ez a cikk az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikket ismerteti, amely általános áttekintést nyújt az adatáthelyezésről másolási tevékenységgel és a támogatott adattár-kombinációkkal.

## <a name="enabling-connectivity"></a>Kapcsolat engedélyezése
Ahhoz, hogy Data Factory szolgáltatás egy helyszíni adattárhoz kapcsolódjon, adatkezelés átjárót kell telepítenie a helyszíni környezetbe. Az átjárót ugyanarra a gépre telepítheti, amely a forrás-vagy egy különálló gépen található, hogy elkerülje az adattárolóval való konkurens erőforrásokat.

Adatkezelés átjáró biztonságos és felügyelt módon csatlakoztatja a helyszíni adatforrásokat a Cloud Serviceshez. Az adatkezelés átjáróval kapcsolatos további információkért lásd: [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) .

## <a name="getting-started"></a>Bevezetés
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely egy forrás-adattárból küldi el az adatok Azure Search indexét különböző eszközök/API-k használatával.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló**használata. Tekintse meg az [oktatóanyag: folyamat létrehozása a másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakört, amely gyors áttekintést nyújt a folyamat létrehozásáról az adatmásolási varázsló használatával.

A következő eszközöket is használhatja a folyamat létrehozásához: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API**és **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) talál.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépések végrehajtásával hozhat létre egy folyamatot, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához.
2. Hozzon létre **adatkészleteket** a másolási művelet bemeneti és kimeneti adatok ábrázolásához.
3. Hozzon **létre egy másolási tevékenységgel rendelkező folyamatot** , amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások (társított szolgáltatások, adatkészletek és a folyamat) JSON-definícióit. Ha eszközöket/API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia.  Az adatok Azure Search indexbe másolásához használt Data Factory JSON-definíciókkal rendelkező minta esetében lásd a [JSON-példa: adatok másolása helyszíni SQL Serverról Azure Search indexre](#json-example-copy-data-from-on-premises-sql-server-to-azure-search-index) című szakaszt a jelen cikkben.

A következő szakaszokban részletesen ismertetjük azokat a JSON-tulajdonságokat, amelyek Data Factory Azure Search indexhez tartozó entitások definiálására használhatók:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A következő táblázat a Azure Search társított szolgáltatáshoz tartozó JSON-elemek leírásait tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| -------- | ----------- | -------- |
| type | A Type tulajdonságot a következőre kell beállítani: **AzureSearch**. | Igen |
| url | A Azure Search szolgáltatás URL-címe. | Igen |
| kulcs | A Azure Search szolgáltatáshoz tartozó rendszergazdai kulcs. | Igen |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához elérhető csoportok és tulajdonságok teljes listáját az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben találja. Az adatkészletek JSON-típusai, például a struktúra, a rendelkezésre állás és a szabályzat, az összes adatkészlet esetében hasonlóak. A **typeProperties** szakasz eltérő az egyes adatkészletek esetében. A **AzureSearchIndex** típusú adatkészlet typeProperties szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| -------- | ----------- | -------- |
| type | A Type tulajdonságot **AzureSearchIndex**értékre kell beállítani.| Igen |
| indexName | A Azure Search index neve. Data Factory nem hozza létre az indexet. Az indexnek léteznie kell a Azure Searchban. | Igen |


## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben találja. A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti táblák, valamint a különböző szabályzatok minden típusú tevékenységhez elérhetők. Míg a typeProperties szakaszban elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. Másolási tevékenység esetén a források és a nyelők típusaitól függően változnak.

Másolási tevékenység esetén, ha a fogadó típusa **AzureSearchIndexSink**, a következő tulajdonságok érhetők el a typeProperties szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Meghatározza, hogy a rendszer egyesítse vagy lecserélje, ha már létezik dokumentum az indexben. Tekintse meg a [WriteBehavior tulajdonságot](#writebehavior-property).| Egyesítés (alapértelmezett)<br/>Feltöltés| Nem |
| writeBatchSize | Adatok feltöltése a Azure Search indexbe, ha a puffer mérete eléri a writeBatchSize. A részletekért tekintse meg a [WriteBatchSize tulajdonságot](#writebatchsize-property) . | 1 – 1 000. Az alapértelmezett érték a 1000. | Nem |

### <a name="writebehavior-property"></a>WriteBehavior tulajdonság
AzureSearchSink upsert az adatírás során. Más szóval, ha a dokumentum írásakor a dokumentum kulcsa már létezik a Azure Search indexben, Azure Search a meglévő dokumentumot frissíti, nem pedig ütközést okozó kivételt.

A AzureSearchSink a következő két upsert-viselkedést biztosítja (a AzureSearch SDK használatával):

- **Egyesítés**: az új dokumentum összes oszlopát egyesítheti a meglévővel. Az új dokumentumban null értékű oszlopok esetén a meglévő érték megmarad.
- **Feltöltés**: az új dokumentum lecseréli a meglévőt. Az új dokumentumban nem megadott oszlopok esetében az érték null, ha a meglévő dokumentumban nem null érték van megadva, vagy nem.

Az alapértelmezett viselkedés az **Egyesítés**.

### <a name="writebatchsize-property"></a>WriteBatchSize tulajdonság
A Azure Search szolgáltatás támogatja a dokumentumok kötegként való írását. Egy köteg 1 – 1 000 műveletet tartalmazhat. Egy művelet kezeli az egyik dokumentumot a feltöltési/egyesítési művelet végrehajtásához.

### <a name="data-type-support"></a>Adattípusok támogatása
A következő táblázat meghatározza, hogy támogatott-e Azure Search adattípusa.

| Azure Search adattípus | Azure Search fogadóban támogatott |
| ---------------------- | ------------------------------ |
| Sztring | I |
| Int32 | I |
| Int64 | I |
| duplán | I |
| Logikai | I |
| DataTimeOffset | I |
| Karakterlánc-tömb | N |
| Geographypoint adattípuson | N |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>JSON-példa: adatok másolása helyszíni SQL Serverból Azure Search indexbe

A következő minta a következőket mutatja be:

1. [AzureSearch](#linked-service-properties)típusú társított szolgáltatás.
2. [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties)típusú társított szolgáltatás.
3. [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [AzureSearchIndex](#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
4. [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) és [AzureSearchIndexSink](#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta idősorozat-adatok másolását végzi egy helyszíni SQL Server-adatbázisból egy Azure Search indexbe óránként. Az ebben a mintában használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

Első lépésként állítsa be az adatkezelési átjárót a helyszíni gépen. Az utasítások a helyszíni [helyszínek és a felhő közötti adatáthelyezést](data-factory-move-data-between-onprem-and-cloud.md) ismertetik.

**Azure Search társított szolgáltatás:**

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

**Társított szolgáltatás SQL Server**

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

**Bemeneti adatkészlet SQL Server**

A minta azt feltételezi, hogy létrehozott egy "Sajáttábla" táblát a SQL Serverban, és egy "timestampcolumn" nevű oszlopot tartalmaz az idősorozat-adatsorokhoz. Egyetlen adatkészletet használva több táblán is lekérdezheti ugyanazon az adatbázison belül, de az adatkészlet táblanév-typeProperty egyetlen táblázatot kell használnia.

A "külső": "true" beállítás azt tájékoztatja, Data Factory szolgáltatás, hogy az adatkészlet kívül esik az adat-előállítón, és nem az adat-előállító tevékenysége.

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

**Azure Search kimeneti adatkészlet:**

A minta a **Products**nevű Azure Search indexbe másolja az adatmásolt fájlokat. Data Factory nem hozza létre az indexet. A minta teszteléséhez hozzon létre egy indexet ezzel a névvel. Hozza létre a Azure Search indexet ugyanazzal a számú oszloppal, mint a bemeneti adatkészletben. A rendszer óránként új bejegyzéseket ad hozzá a Azure Search indexhez.

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

**Másolási tevékenység egy folyamatba SQL-forrással és Azure Search index fogadóval:**

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **SqlSource** értékre van állítva, a **fogadó típusa** pedig **AzureSearchIndexSink**. A **SqlReaderQuery** tulajdonsághoz megadott SQL-lekérdezés a másoláshoz az elmúlt órában kijelöli az adatforrást.

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

Ha egy felhőalapú adattárból Azure Searchba másol adatait, `executionLocation` tulajdonságra van szükség. A következő JSON-kódrészlet a másolási tevékenység `typeProperties` példaként való módosítását mutatja be. A támogatott értékek és további részletek a [Felhőbeli adattárak közötti Adatmásolás](data-factory-data-movement-activities.md#global) szakaszban olvashatók.

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


## <a name="copy-from-a-cloud-source"></a>Másolás felhőalapú forrásból
Ha egy felhőalapú adattárból Azure Searchba másol adatait, `executionLocation` tulajdonságra van szükség. A következő JSON-kódrészlet a másolási tevékenység `typeProperties` példaként való módosítását mutatja be. A támogatott értékek és további részletek a [Felhőbeli adattárak közötti Adatmásolás](data-factory-data-movement-activities.md#global) szakaszban olvashatók.

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

A másolási tevékenység definíciójában a forrás adatkészletből származó oszlopokat is leképezheti a fogadó adatkészletből származó oszlopokra. Részletekért lásd: [adatkészlet-oszlopok leképezése Azure Data Factoryban](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
A [másolási tevékenység teljesítményének és hangolásának útmutatójában](data-factory-copy-activity-performance.md) megismerheti azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés teljesítményére (másolási tevékenység) és az optimalizálás különféle módjaira.

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

* A [másolási](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) tevékenységről szóló oktatóanyag részletesen ismerteti a folyamat másolási tevékenységgel történő létrehozását.

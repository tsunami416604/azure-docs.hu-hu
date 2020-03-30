---
title: Adatok leküldése a keresési indexbe a Data Factory használatával
description: Ismerje meg, hogyan lehet adatokat leadni az Azure Cognitive Search Index az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5b1170f721cf8521cfe1762df0cc616c938ddf28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281560"
---
# <a name="push-data-to-an-azure-cognitive-search-index-by-using-azure-data-factory"></a>Adatok leküldése az Azure Cognitive Search indexébe az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-azure-search-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-azure-search.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [Azure Cognitive Search-összekötő t a V2-ben.](../connector-azure-search.md)

Ez a cikk ismerteti, hogyan használhatja a másolási tevékenység leküldéses adatokat egy támogatott forrásadat-tároló egy Azure Cognitive Search index. A támogatott forrásadat-tárolók a [támogatott források és fogadók](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla Forrás oszlopában találhatók. Ez a cikk az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) ről szóló cikkre épül, amely általános áttekintést nyújt az adatok mozgásáról a Másolási tevékenységgel és a támogatott adattár-kombinációkkal.

## <a name="enabling-connectivity"></a>Csatlakozás engedélyezése
Annak érdekében, hogy a Data Factory szolgáltatás egy helyszíni adattárhoz csatlakozzon, telepítse az Adatkezelési átjárót a helyszíni környezetben. Az átjárót telepítheti ugyanarra a számítógépre, amely en a forrás-adattort üzemeltetik, vagy egy külön számítógépre, hogy elkerülje az erőforrásokért való versengést az adattárral.

Az Adatkezelési átjáró biztonságos és felügyelt módon kapcsolja össze a helyszíni adatforrásokat a felhőszolgáltatásokkal. Az Adatkezelési átjáróval kapcsolatos részletekért olvassa el az [Adatok áthelyezése a helyszíni és a felhőbeli](data-factory-move-data-between-onprem-and-cloud.md) környezet között című témakört.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel, amely különböző eszközök/API-k használatával lefájlokatják a forrás-adattárból a keresési indexbe az adatokat.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló**használata. Olvassa el [az oktatóanyagot: Folyamat létrehozása a Másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakörben egy gyors útmutatót a folyamat másolása az adatok másolása varázslóval történő létrehozásához.

A következő eszközökkel is létrehozhat egy folyamatot: **Visual Studio,** **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**és REST **API.** Lásd: [Tevékenység-oktatóanyag másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című témakörben részletes útmutatást talál egy másolási tevékenységgel rendelkező folyamat létrehozásához.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépéseket hajthatja végre egy olyan folyamat létrehozásához, amely adatokat helyezi át a forrásadattárból a fogadó adattárába:

1. **Összekapcsolt szolgáltatások** létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására.
2. **Adatkészletek** létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására.
3. Hozzon létre egy **folyamatot** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, egy adatkészletet pedig kimenetként vesz fel.

A varázsló használatakor a Data Factory entitásokhoz (csatolt szolgáltatások, adatkészletek és a folyamat) json-definíciók automatikusan létrejönnek. Eszközök/API-k használatakor (a .NET API kivételével) ezeket a Data Factory entitásokat a JSON formátum használatával definiálhatja.  A data-indexbe adatok másolásához használt Data Factory-entitások JSON-definícióival rendelkező minta a [JSON-példa: Adatok másolása a helyszíni SQL Serverből egy Azure Cognitive Search index](#json-example-copy-data-from-on-premises-sql-server-to-azure-cognitive-search-index) szakaszba ebben a cikkben.

A következő szakaszok a keresési indexre jellemző Data Factory-entitások meghatározására használt JSON-tulajdonságok részleteit ismertetik:

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az alábbi táblázat az Azure Cognitive Search kapcsolt szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| -------- | ----------- | -------- |
| type | A típustulajdonságot a következőre kell állítani: **AzureSearch**. | Igen |
| url | A keresési szolgáltatás URL-címe. | Igen |
| kulcs | A keresési szolgáltatás felügyeleti kulcsa. | Igen |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben olvashat. A JSON adatkészletek szerkezete, rendelkezésre állása és házirendje minden adatkészlettípushoz hasonlóak. A **typeProperties** szakasz az adatkészlet egyes típusaiesetében eltérő. Az **AzureSearchIndex** típusú adatkészlet typeProperties szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| -------- | ----------- | -------- |
| type | A típustulajdonságot **az AzureSearchIndex**beállításra kell állítani.| Igen |
| indexName | A keresési index neve. A Data Factory nem hozza létre az indexet. Az indexnek léteznie kell az Azure Cognitive Search szolgáltatásban. | Igen |


## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben olvashat. Tulajdonságok, például név, leírás, bemeneti és kimeneti táblák, és a különböző házirendek állnak rendelkezésre minden típusú tevékenységek. Mivel a typeProperties szakaszban elérhető tulajdonságok az egyes tevékenységtípusoktól függően változnak. A Másolási tevékenység esetében a források és a fogadók típusától függően változnak.

A másolási tevékenység esetén, ha a fogadó **AzureSearchIndexSink**típusú, a következő tulajdonságok érhetők el a typeProperties szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| -------- | ----------- | -------------- | -------- |
| Írási viselkedés | Itt adható meg, hogy egyesítse vagy cserélje le a programot, ha már létezik dokumentum az indexben. Lásd a [WriteBehavior tulajdonságot](#writebehavior-property).| Egyesítés (alapértelmezett)<br/>Feltöltés| Nem |
| WriteBatchSize írás | Adatokat tölt fel a keresési indexbe, amikor a puffer mérete eléri a writeBatchSize-ot. A részleteket a [WriteBatchSize tulajdonságban](#writebatchsize-property) találja. | 1-ről 1000-re. Az alapértelmezett érték 1000. | Nem |

### <a name="writebehavior-property"></a>WriteBehavior tulajdonság
Az AzureSearchSink upserts adatok írásakor. Más szóval, amikor egy dokumentum írásakor, ha a dokumentumkulcs már létezik a keresési indexben, az Azure Cognitive Search frissíti a meglévő dokumentumot, nem pedig ütközési kivétel.

Az AzureSearchSink a következő két upsert viselkedést biztosítja (az AzureSearch SDK használatával):

- **Egyesítés:** az új dokumentum összes oszlopának egyesítése a meglévővel. Az új dokumentumban null értékű oszlopok esetén a meglévő értéke megmarad.
- **Feltöltés**: Az új dokumentum felváltja a meglévőt. Az új dokumentumban nem megadott oszlopok esetén az érték null értékű, függetlenül attól, hogy a meglévő dokumentumban nem null érték van-e vagy sem.

Az alapértelmezett viselkedés az **Egyesítés**.

### <a name="writebatchsize-property"></a>WriteBatchSize tulajdonság
Az Azure Cognitive Search szolgáltatás támogatja a dokumentumok kötegként i. Egy köteg 1–1000 műveletet tartalmazhat. Egy művelet egy dokumentumot kezel a feltöltési/egyesítési művelet végrehajtásához.

### <a name="data-type-support"></a>Adattípus-támogatás
Az alábbi táblázat azt határozza meg, hogy az Azure Cognitive Search adattípusa támogatott-e vagy sem.

| Az Azure Cognitive Search adattípusa | Az Azure Cognitive Search mosogatótámogatja |
| ---------------------- | ------------------------------ |
| Sztring | I |
| Int32 | I |
| Int64 | I |
| Double | I |
| Logikai | I |
| DataTimeOffset | I |
| Karakterlánc-tömb | N |
| FöldrajzPont | N |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-cognitive-search-index"></a>JSON-példa: Adatok másolása a helyszíni SQL Server ről az Azure Cognitive Search indexébe

A következő minta a következőket mutatja:

1. [AzureSearch](#linked-service-properties)típusú összekapcsolt szolgáltatás.
2. [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties)típusú csatolt szolgáltatás.
3. [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [AzureSearchIndex](#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
4. [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) és [AzureSearchIndexSink](#copy-activity-properties)programot használó másolási tevékenységgel rendelkező [folyamat.](data-factory-create-pipelines.md)

A minta idősorozat-adatokat másol egy helyszíni SQL Server-adatbázisból az index óránkénti kereséséhez. A mintában használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

Első lépésként állítsa be az adatkezelési átjárót a helyszíni gépen. Az utasítások a helyszíni helyek és a felhőalapú cikk [közötti átmozgatási](data-factory-move-data-between-onprem-and-cloud.md) adatokban találhatók.

**Azure Cognitive Search kapcsolt szolgáltatás:**

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

**SQL Server csatolt szolgáltatás**

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

A minta feltételezi, hogy létrehozott egy "MyTable" táblát az SQL Server ben, és tartalmaz egy "timestampcolumn" nevű oszlopot az idősorozat-adatokhoz. Ugyanazon az adatbázison belül több táblát is lekérdezhet egyetlen adatkészlet használatával, de az adatkészlet táblaname typeProperty eleméhez egyetlen táblát kell használni.

A "külső": "true" beállítás tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított.

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

**Azure Cognitive Search kimeneti adatkészlet:**

A minta adatokat másol egy Azure Cognitive Search index nevű **termékek.** A Data Factory nem hozza létre az indexet. A minta teszteléséhez hozzon létre egy indexet ezzel a névvel. Hozza létre a keresési indexet ugyanannyi oszloppal, mint a bemeneti adatkészletben. Az új bejegyzések óránként kerülnek a keresési indexbe.

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

**Az SQL-forrással és az Azure Cognitive Search Index fogadójával rendelkező folyamat tevékenységének másolása:**

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránként i. A folyamat JSON-definíciójában a **forrástípus** **sqlsource-ra** van állítva, **a fogadó** típusa pedig **Az AzureSearchIndexSink**. Az **SqlReaderQuery** tulajdonsághoz megadott SQL-lekérdezés kiválasztja a másolni kívánt adatokat az elmúlt órában.

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

Ha adatokat másol egy felhőbeli adattárból `executionLocation` az Azure Cognitive Search, tulajdonság szükséges. A következő JSON-kódrészlet példaként mutatja `typeProperties` be a Tevékenység másolása című dokumentumban szükséges módosítást. A támogatott értékekért és további részletekért tekintse meg az [adatok másolása a felhőbeli adattárolók között](data-factory-data-movement-activities.md#global) című szakaszt.

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


## <a name="copy-from-a-cloud-source"></a>Másolás felhőforrásból
Ha adatokat másol egy felhőbeli adattárból `executionLocation` az Azure Cognitive Search, tulajdonság szükséges. A következő JSON-kódrészlet példaként mutatja `typeProperties` be a Tevékenység másolása című dokumentumban szükséges módosítást. A támogatott értékekért és további részletekért tekintse meg az [adatok másolása a felhőbeli adattárolók között](data-factory-data-movement-activities.md#global) című szakaszt.

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

A forrásadatkészletből oszlopokat is leképezhet a fogadó-adatkészletből a másolási tevékenységdefinícióban. További információt az [Adatkészlet-oszlopok leképezése az Azure Data Factoryban című témakörben talál.](data-factory-map-columns.md)

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
A [Tevékenység másolása teljesítmény- és hangolási útmutatóban](data-factory-copy-activity-performance.md) megismerést talál az adatmozgatás (Másolási tevékenység) teljesítményét befolyásoló legfontosabb tényezőkről és optimalizálásának különböző módjairól.

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

* [A Tevékenység-oktatóanyag másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) lépésenkénti útmutatást tartalmaz egy másolási tevékenységgel rendelkező folyamat létrehozásához.

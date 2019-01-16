---
title: Adatok áthelyezése és- tárolókról az Azure Table |} A Microsoft Docs
description: Ismerje meg, hogyan helyezheti át az adatok Azure Table Storage, Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 07b046b1-7884-4e57-a613-337292416319
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6ab3e918feda3dcf898928f159ebf8e317a95527
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54331843"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Adatok importálására és az Azure Data Factory használatával az Azure-tábla
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-azure-table-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-azure-table-storage.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [a v2-ben az Azure Table Storage-összekötő](../connector-azure-table-storage.md).

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factoryban adatáthelyezést és-tárolókról az Azure Table Storage. Épül a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikket, amely megadja az adatok áthelyezését a másolási tevékenységgel rendelkező általános áttekintése. 

Másolhatja az adatokat bármely támogatott forrásadattár Azure Table Storage vagy az Azure Table Storage bármely támogatott fogadó adattárba. A másolási tevékenység által források vagy fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. 

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel az adatok és-tárolókról az Azure Table Storage áthelyezéséhez a különböző eszközök/API-k használatával.

A folyamat létrehozásának legegyszerűbb módja az, hogy használja a **másolása varázsló**. Lásd: [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md) gyors bemutató létrehozása egy folyamatot az adatok másolása varázsló használatával.

A következő eszközök használatával hozzon létre egy folyamatot: **Az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, **Azure Resource Manager-sablon**, **.NET API**, és  **REST API-val**. Lásd: [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját. 

Az eszközök vagy az API-kat használja, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani: 

1. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához.
2. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli. 
3. Hozzon létre egy **folyamat** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként. 

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) JSON-definíciói az Ön számára. Eszközök/API-k (kivéve a .NET API) használatakor adja meg a Data Factory-entitások a JSON formátumban. Az adatok másolása és- tárolókról az Azure Table Storage használt Data Factory-entitások JSON-definíciói minták, lásd: [JSON példák](#json-examples) című szakaszát.

Az alábbi szakaszok nyújtanak az Azure Table Storage adott Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részletekért: 

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
Használhatja az Azure blob storage összekapcsolása egy Azure data factory társított szolgáltatásokat két típusa van. Ezek a következők: **AzureStorage** társított szolgáltatást, és **AzureStorageSas** társított szolgáltatást. Az Azure Storage társított szolgáltatás az adat-előállító globális hozzáférést biztosít az Azure Storage. Mivel az Azure Storage SAS (közös hozzáférésű Jogosultságkód) társított szolgáltatás az adat-előállító korlátozott/időhöz kötött hozzáféréssel rendelkező biztosítja az Azure Storage. Nincs más két társított szolgáltatás közötti különbségek vannak. Válassza ki az igényeinek megfelelő társított szolgáltatás. A következő szakaszok további részletekkel szolgálnak ezek két társított szolgáltatást.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Szakaszok & adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például a szerkezetet, rendelkezésre állást és szabályzatát adatkészlet JSON szakaszok hasonlóak az összes adatkészlet esetében (az Azure SQL, az Azure blob-, az Azure table-, stb.).

A typeProperties szakasz eltérő az egyes adatkészlet, és az adattárban lévő adatok helyére vonatkozó információkat. A **typeProperties** szakasz az adatkészlet típusa **AzureTable** a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |Az Azure Table-adatbázispéldányban, amelyek a társított szolgáltatás hivatkozik a tábla neve. |Igen. A tableName egy azureTableSourceQuery nélkül van megadva, a tábla minden rekordját a célhelyre másolódnak. Ha egy azureTableSourceQuery is meg van adva, a cél, amely eleget tesz a lekérdezést a tábla rekordjai lesz másolva. |

### <a name="schema-by-data-factory"></a>Adat-előállítók által séma
A séma nélküli adattárakban, mint az Azure Table a Data Factory szolgáltatás kikövetkezteti a használandó sémát az alábbi módszerek egyikével:

1. Ha az adatok struktúráját használatával adja meg a **struktúra** tulajdonságot az adatkészlet-definícióban, a Data Factory szolgáltatás figyelembe veszi a sémát, ez a struktúra. Ebben az esetben ha egy sort tartalmaz egy oszlop értékét, null értékű biztosítunk azt.
2. Ha nem adja meg az adatok struktúráját használatával a **struktúra** tulajdonságot az adatkészlet-definícióban, adat-előállító kikövetkezteti a használandó sémát az első sor az adatok használatával. Ebben az esetben ha az első sor nem tartalmazza a teljes séma, egyes oszlopok vannak nem talált a másolási művelet eredményét.

Ezért sémamentes adatforrások esetében az ajánlott eljárás, hogy adja meg az adatok a struktúra a **struktúra** tulajdonság.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & definiálását tevékenységek tulajdonságainak teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Tulajdonságok, mint például a nevét, leírását, bemeneti és kimeneti adatkészleteket és szabályzatokat minden típusú tevékenységek érhetők el.

A tevékenység a typeProperties szakasz tulajdonságai másrészről tevékenységek minden típusának számától függ. A másolási tevékenységhez azok változhat a forrásként és fogadóként típusú is.

**AzureTableSource** támogatja a typeProperties szakasz a következő tulajdonságokkal:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| azureTableSourceQuery |Az egyéni lekérdezés segítségével olvassa el az adatokat. |Azure-tábla lekérdezési karakterláncot. Példák a következő szakaszban talál. |Nem. A tableName egy azureTableSourceQuery nélkül van megadva, a tábla minden rekordját a célhelyre másolódnak. Ha egy azureTableSourceQuery is meg van adva, a cél, amely eleget tesz a lekérdezést a tábla rekordjai lesz másolva. |
| azureTableSourceIgnoreTableNotFound |Adja meg, hogy swallow a kivétel a tábla nem létezik. |IGAZ<br/>FALSE |Nem |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery examples
Ha az Azure Table oszlop karakterlánc típusú:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Ha az Azure Table oszlop dátum/idő típus:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**AzureTableSink** támogatja a typeProperties szakasz a következő tulajdonságokkal:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Alapértelmezett partíciós kulcsérték, amely a fogadó által használható. |Egy karakterláncértéket. |Nem |
| azureTablePartitionKeyName |Adja meg az oszlop, amelynek az értékekkel, partíciókulcsok nevét. Ha nincs megadva, a partíciókulcs AzureTableDefaultPartitionKeyValue lesz. |Egy oszlop neve. |Nem |
| azureTableRowKeyName |Adja meg az oszlop, amelynek oszlopértékek sor kulcsaként használt nevét. Ha nincs megadva, használjon egy GUID Azonosítót minden egyes sorára. |Egy oszlop neve. |Nem |
| azureTableInsertType |Adatok beszúrása az Azure-tábla a módot.<br/><br/>Ez a tulajdonság szabja meg, hogy rendelkeznek-e létező sorok egyeztetésével partíció-és a kimeneti tábla cserélni vagy egyesített értékekre. <br/><br/>(Egyesítési és cserélje ki) ezen beállítások működésének kapcsolatos további információkért lásd: [vagy egyesítési entitás beszúrása](https://msdn.microsoft.com/library/azure/hh452241.aspx) és [entitás cseréje vagy beszúrása](https://msdn.microsoft.com/library/azure/hh452242.aspx) témaköröket. <br/><br> Ez a beállítás a sor szintjén, a tábla szintjén nem vonatkozik, és sem a lehetőség törli a kimeneti tábla sorait, amelyek a bemeneti adatok nem léteznek. |Egyesítés (alapértelmezett)<br/>cserélje le |Nem |
| WriteBatchSize |Szúr be az Azure-tábla adatait, ha elérte a writeBatchSize vagy writeBatchTimeout. |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| writeBatchTimeout |Adatok beszúrása az Azure-tábla, ha elérte a writeBatchSize vagy writeBatchTimeout |Időtartam<br/><br/>Példa: "00: 20:00" (20 perc) |Nem (az alapértelmezett tároló ügyfél alapértelmezett időtúllépési érték 90 másodperc) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Képezze le a forrásoszlop translatorral a JSON-tulajdonság előtt a céloszlop használhatja a azureTablePartitionKeyName céloszlop.

A következő példában a céloszlop forrásoszlop DivisionID van leképezve: DivisionID.  

```JSON
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```
A partíciókulcs a DivisionID van megadva.

```JSON
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID",
    "writeBatchSize": 100,
    "writeBatchTimeout": "01:00:00"
}
```
## <a name="json-examples"></a>JSON-példák
Az alábbi példák megadják példa JSON-definíciók, amelyek segítségével létrehoz egy folyamatot használatával [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell-lel](data-factory-copy-activity-tutorial-using-powershell.md). Adatok másolása az Azure Table Storage és az Azure Blob-adatbázis mutatnak. Azonban az adatok átmásolhatók **közvetlenül** bármely, bármelyik támogatott forrás fogadók. További információkért lásd: a "támogatott adattárak és formátumok" szakasz a [adatok áthelyezése másolási tevékenységgel](data-factory-data-movement-activities.md).

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Példa: Adatok másolása az Azure-táblát az Azure Blob
Az alábbi mintában látható:

1. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (tábla- és blob használatos).
2. Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [AzureTable](#dataset-properties).
3. Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [AzureTableSource](#activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta az alapértelmezett partíció Azure-táblában egy blobhoz tartozó minden órában adatokat másolja. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

**Az Azure storage társított szolgáltatást:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Az Azure Data Factory az Azure Storage-beli társított szolgáltatások két típusát támogatja: **AzureStorage** és **AzureStorageSas**. Az első címtárra, adja meg a kapcsolati karakterláncot, amely tartalmazza a kulcsát, és az újabb már, adja meg a közös hozzáférésű Jogosultságkód (SAS) URI-t. Lásd: [társított szolgáltatások](#linked-service-properties) című szakasz részletezi.  

**Az Azure Table bemeneti adatkészlet:**

A minta azt feltételezi, hogy létrehozott egy "MyTable" táblát az Azure Table.

Beállítás az "external": "true" tájékoztatja a Data Factory szolgáltatásban, hogy az adatkészletet a data factory a külső, és nem hozzák az adat-előállító adott tevékenységéhez.

```JSON
{
  "name": "AzureTableInput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
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

**Azure blobkimeneti adatkészlet:**

Adatokat írt egy új blob minden órában (frequency: óra, időköz: 1). A mappa elérési útját a BLOB a feldolgozás alatt álló szelet kezdő időpontja alapján dinamikusan kiértékeli. A mappa elérési útját használja, év, hónap, nap és óra részei a kezdési időpontot.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Másolási tevékenységgel rendelkező AzureTableSource és BlobSink folyamatot:**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **AzureTableSource** és **fogadó** típusa **BlobSink**. Az SQL-lekérdezést a megadott **AzureTableSourceQuery** tulajdonság kiválasztja az adatokat az alapértelmezett partíció óránként másolásához.

```JSON
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureTabletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureTableInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureTableSource",
                        "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                    },
                    "sink": {
                        "type": "BlobSink"
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

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>Példa: Adatok másolása Azure blobból az Azure Table
Az alábbi mintában látható:

1. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (tábla- és blob használatos)
2. Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
3. Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureTable](#dataset-properties).
4. A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és [AzureTableSink](#copy-activity-properties).

A minta másolatokat idősorozat-adatokat egy Azure blobból az Azure-tábla óránként történik. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

**(Az Azure Table és a Blob) az Azure storage társított szolgáltatást:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

Az Azure Data Factory az Azure Storage-beli társított szolgáltatások két típusát támogatja: **AzureStorage** és **AzureStorageSas**. Az első címtárra, adja meg a kapcsolati karakterláncot, amely tartalmazza a kulcsát, és az újabb már, adja meg a közös hozzáférésű Jogosultságkód (SAS) URI-t. Lásd: [társított szolgáltatások](#linked-service-properties) című szakasz részletezi.

**Azure blobbemeneti adatkészlet:**

Adatok felülettől új blob minden órában (frequency: óra, időköz: 1). A mappa elérési útját és nevét a BLOB dinamikusan a feldolgozás alatt álló szelet kezdő időpontja alapján értékeli ki. A mappa elérési útjának év, hónap és nap részét a kezdési időpont és fájlnevet a kezdő időpontja óra részét használja. "external": "true" beállítással, hogy az adatkészletet a data factory a külső, és nem egy adat-előállító tevékenység által előállított arról tájékoztatja a Data Factory szolgáltatásban.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
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

**Azure-tábla kimeneti adatkészlet:**

A minta adatokat másol az Azure Table "MyTable" nevű tábla. Hozzon létre egy Azure-táblát az azonos számú oszlopot tartalmaz a Blob CSV-fájl várt módon. Új sorok hozzáadódnak a tábla minden órában.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**A BlobSource és AzureTableSink a folyamat másolási tevékenysége:**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **BlobSource** és **fogadó** típusa **AzureTableSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoTable",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureTableOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "AzureTableSink",
            "writeBatchSize": 100,
            "writeBatchTimeout": "01:00:00"
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
## <a name="type-mapping-for-azure-table"></a>Az Azure Table-leképezés típusa
Ahogy korábban már említettük, az a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) a cikkben a másolási tevékenység végzi az automatikus típuskonverziók a fogadó-típusokat az alábbi kétlépéses módszer a forrás-típusok közül.

1. A natív forrástípusok átalakítása typ .NET
2. A .NET-típusból átalakítása natív fogadó típusa

Amikor adatok áthelyezése Azure táblából, a következő & [Azure Table Storage-szolgáltatás által definiált leképezések](https://msdn.microsoft.com/library/azure/dd179338.aspx) használt .NET típusát, és ez fordítva is igaz az Azure-tábla OData-típusok közül.

| OData-adatok típusa | .NET Type | Részletek |
| --- | --- | --- |
| Edm.Binary |byte[] |Bájttömb legfeljebb 64 KB-os. |
| Edm.Boolean |Logikai |Logikai érték. |
| Edm.DateTime |DateTime |Egy 64 bites érték, egyezményes világidő (UTC) szerint kifejezett. A támogatott dátum és idő tartomány kezdődik 12:00 éjféltől. január 1, i 1601. (C.E.,) (UTC). A tartomány vége. December 31-9999. |
| Edm.Double |double |Egy 64 bites lebegőpontos értéket. |
| Edm.Guid |GUID |A 128 bites globálisan egyedi azonosítóját. |
| Edm.Int32 |Int32 |Egy 32 bites egész számot. |
| Edm.Int64 |Int64 |Egy 64 bites egész számot. |
| Edm.String |Karakterlánc |UTF-16 kódolású érték. Karakterlánc-értékek legfeljebb 64 KB lehet. |

### <a name="type-conversion-sample"></a>Átalakítás minta
Az alábbi minta az adatok másolása az Azure-Blobból az Azure Table-típuskonverziók van.

Tegyük fel, hogy a Blob-adatkészlet CSV formátumban van, és három oszlopot tartalmaz. Ezek közül egyik napja a héten francia rövidített használatával egyéni dátum és idő formátumú dátum-idő oszlop.

Adja meg a Blob forrásadatkészlet módon az oszlopok típusdefiníciók együtt.

```JSON
{
    "name": " AzureBlobInput",
    "properties":
    {
        "structure":
        [
            { "name": "userid", "type": "Int64"},
            { "name": "name", "type": "String"},
            { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
        ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "external": true,
        "availability":
        {
            "frequency": "Hour",
            "interval": 1,
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
Adja meg az adattípus-hozzárendelés az Azure-tábla OData-típus .NET típusra, azt határozzák meg a tábla az Azure Table a következő sémával.

**Az Azure tábla sémája:**

| Oszlop neve | Típus |
| --- | --- |
| felhasználói azonosító |Edm.Int64 |
| név |Edm.String |
| lastlogindate |Edm.DateTime |

Ezt követően adja meg az Azure Table adatkészlet a következőképpen. Nem kell megadnia "struktúra" szakasz a típusú adatokkal, mivel a típussal kapcsolatos információk már meg van adva az alapul szolgáló adattár.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

Ebben az esetben a Data Factory automatikusan írja be az átalakítás Datetime mező például a "fr-fr" kulturális környezet használatával, amikor az adatok áthelyezése a blobból az Azure Table egyéni dátum és idő formátumban.

> [!NOTE]
> Fogadó-adatkészlet az oszlopok a forrásadatkészlet oszlopok leképezésére, lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Az adatáthelyezés (másolási tevékenység) az Azure Data Factory és a különféle módokon optimalizálhatja azt, hogy hatással lehet a teljesítményre legfontosabb tényezők kapcsolatos további információkért lásd: [másolási tevékenységek teljesítményéhez és teljesítményhangolási útmutatóból](data-factory-copy-activity-performance.md).

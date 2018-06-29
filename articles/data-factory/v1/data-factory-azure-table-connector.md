---
title: Adatok áthelyezése az Azure tábla |} Microsoft Docs
description: Megtudhatja, hogyan helyezi át az adatokat és a Azure Table Storage Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 07b046b1-7884-4e57-a613-337292416319
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3a24e919f1bbde6188e3655399f1ef843fbec23b
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052990"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Adatok áthelyezése, és az Azure tábla Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verziójával](data-factory-azure-table-connector.md)
> * [(Az aktuális verzió) 2-es verzió](../connector-azure-table-storage.md)

> [!NOTE]
> Ez a cikk a Data Factory 1 verziójára vonatkozik. A Data Factory szolgáltatásnak aktuális verziójának használatakor lásd [Azure Table Storage-összekötőt, a V2](../connector-azure-table-storage.md).

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory helyezi át az adatokat az Azure Table Storage és a. Buildekről nyújtanak a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, amelynek során adatátvitel a másolási tevékenység az általános áttekintést. 

Bármely támogatott forrás adattároló Azure Table Storage vagy az Azure Table Storage bármely támogatott fogadó adattárolóhoz adatainak másolhatja. Adatforrások vagy mosdók a másolási tevékenység által támogatott adattárolókhoz listájáért lásd: a [adattárolókhoz támogatott](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. 

## <a name="getting-started"></a>Első lépések
A másolási tevékenység, amely helyezi át az adatokat az Azure Table Storage és a különböző eszközök/API-k használatával létrehozhat egy folyamatot.

Hozzon létre egy folyamatot a legegyszerűbb módja használatára a **másolása varázsló**. Lásd: [oktatóanyag: hozzon létre egy folyamatot, másolása varázslóval](data-factory-copy-data-wizard-tutorial.md) létrehozásával egy folyamatot, az adatok másolása varázsló segítségével gyorsan útmutatást.

Az alábbi eszközöket használhatja a folyamatokat létrehozni: **Azure-portálon**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-sablon** , **.NET API**, és **REST API-t**. Lásd: [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját. 

Akár az eszközök vagy API-k, hajtsa végre a következő lépésekkel hozza létre egy folyamatot, amely mozgatja az adatokat a forrás-tárolóban a fogadó tárolóban: 

1. Hozzon létre **összekapcsolt szolgáltatások** bemeneti és kimeneti adatok csatolásához tárolja a a data factory.
2. Hozzon létre **adatkészletek** a másolási művelet bemeneti és kimeneti adatok. 
3. Hozzon létre egy **csővezeték** , amely fogad egy bemeneti adatkészlet és egy kimeneti adatkészletet másolási tevékenységgel. 

A varázsló használatakor a Data Factory entitások (összekapcsolt szolgáltatások adatkészletek és a feldolgozási sor) JSON-definíciók automatikusan létrejönnek. Eszközök/API-k (kivéve a .NET API-t) használata esetén adja meg a Data Factory entitások a JSON formátum használatával.  A mintában használt adatok másolása az Azure Table Storage az adat-előállító entitások JSON-definíciók, lásd: [JSON példák](#json-examples) című szakaszát. 

A következő szakaszok részletesen bemutatják, amely segítségével az Azure Table Storage megadása a Data Factory tartozó entitások JSON-tulajdonságok: 

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok
Az összekapcsolt szolgáltatások használatával egy Azure blobtárolóba összekapcsolása egy Azure data factory két típusa van. Ezek: **AzureStorage** társított szolgáltatás és **AzureStorageSas** társított szolgáltatás. Az Azure tárolás társított szolgáltatása az adat-előállítóban globális hozzáférést biztosít az Azure Storage. Mivel az Azure Storage SAS (közös hozzáférésű Jogosultságkód) kapcsolódó szolgáltatás korlátozott/időhöz kötött hozzáféréssel a data factory biztosítja az Azure Storage. Nincsenek más különbségek a következő két összekapcsolt szolgáltatások között. Válassza ki az igényeinek megfelelő társított szolgáltatás. A következő szakaszokban további részleteket a következő két összekapcsolt szolgáltatások.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Szakaszok & meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például struktúra, a rendelkezésre állás és a házirend a DataSet adatkészlet JSON hasonlítanak minden adatkészlet esetében (Azure SQL, az Azure blob, Azure-tábla, stb.).

A typeProperties szakasz más adatkészlet egyes típusai és információkat nyújt azokról az adattárban adatok helyét. A **typeProperties** szakasz az adatkészlet típusú **AzureTable** a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |Az az Azure tábla adatbázispéldány táblájának, amelyre a társított szolgáltatás neve hivatkozik. |Igen. Amikor egy Táblanév egy azureTableSourceQuery nélkül van megadva, a tábla összes rekordot a cél lesz másolva. Ha egy azureTableSourceQuery is meg van adva, a cél a táblázatból, amely eleget tesz a lekérdezés rekordok lesz másolva. |

### <a name="schema-by-data-factory"></a>Adat-előállító sémája
Például az Azure tábla tárolóinak sémamentes adatokra a Data Factory szolgáltatásnak kikövetkezteti a séma a következő módszerek valamelyikével:

1. Ha az adatok szerkezete használatával adja meg a **struktúra** tulajdonsághoz a DataSet adatkészlet-definícióban a Data Factory szolgáltatásnak eleget tegyen a séma szerint ez a struktúra. Ebben az esetben ha egy sort tartalmaz egy olyan oszlop értékét, null értékű biztosított azt.
2. Ha nem adja meg az adatok szerkezete használatával a **struktúra** tulajdonság az adatkészlet-definícióban, adat-előállító kikövetkezteti a séma az adatok első sora használatával. Ebben az esetben ha az első sort tartalmazza a teljes séma, azokat az oszlopokat vannak nem talált a másolási művelet eredménye.

Ezért sémamentes adatforrások, az ajánlott eljárás, hogy adja meg az adatok szerkezete a **struktúra** tulajdonság.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [létrehozása folyamatok](data-factory-create-pipelines.md) cikk. Az összes tevékenység tulajdonságai, például nevét, leírását, valamint bemeneti és kimeneti adatkészletek és házirendek érhetők el.

A typeProperties szakaszban a tevékenység tulajdonságai a tevékenységek minden típusának függenek. A másolási tevékenység során két érték források és mosdók típusától függően.

**AzureTableSource** typeProperties szakaszban a következő tulajdonságok támogatja:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| azureTableSourceQuery |Az egyéni lekérdezés segítségével adatokat olvasni. |Azure-tábla lekérdezési karakterlánc. Példák a következő szakaszban. |Nem. Amikor egy Táblanév egy azureTableSourceQuery nélkül van megadva, a tábla összes rekordot a cél lesz másolva. Ha egy azureTableSourceQuery is meg van adva, a cél a táblázatból, amely eleget tesz a lekérdezés rekordok lesz másolva. |
| azureTableSourceIgnoreTableNotFound |Azt jelzi, hogy a tábla kivétel swallow nem létezik. |IGAZ<br/>HAMIS |Nem |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery példák
Ha Azure táblaoszlop karakterlánc típusú:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Ha Azure táblaoszlop dátum/idő típusú:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**AzureTableSink** typeProperties szakaszban a következő tulajdonságok támogatja:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Alapértelmezett partíció kulcs értékét, amely a fogadó által használható. |Egy karakterlánc-érték. |Nem |
| azureTablePartitionKeyName |Adja meg az oszlop, amelynek értékeket fogja használni, mint partíciókulcsok nevét. Ha nincs megadva, a partíciós kulcs AzureTableDefaultPartitionKeyValue lesz. |Egy oszlop neve. |Nem |
| azureTableRowKeyName |Adja meg az oszlop, amelynek oszlop értékeit sor kulcsaként vannak használatban. Ha nincs megadva, minden egyes sorára használjon a GUID Azonosítót. |Egy oszlop neve. |Nem |
| azureTableInsertType |A mód lehet adatokat beszúrni az Azure-tábla.<br/><br/>Ez a tulajdonság szabja meg, hogy rendelkeznek-e a meglévő sorokat a táblában az egyező partíció-és sorkulcsok cseréje vagy egyesített értékükre. <br/><br/>Ezeket a beállításokat (lemezegyesítési és -csere) működése, lásd: [Insert vagy az egyesítéses entitás](https://msdn.microsoft.com/library/azure/hh452241.aspx) és [Insert vagy az entitás cseréje](https://msdn.microsoft.com/library/azure/hh452242.aspx) témaköröket. <br/><br> Ez a beállítás a sor szintjén, a táblázatok szintjén nem vonatkozik, és sem a lehetőség törli a kimeneti táblához, amely nem szerepel a bemeneti sorokat. |Egyesítés (alapértelmezett)<br/>cserélje le |Nem |
| writeBatchSize |Amikor writeBatchSize vagy writeBatchTimeout találati adatok beillesztése az Azure-tábla. |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| writeBatchTimeout |Adatok szúr be az Azure-táblázatra, ha a writeBatchSize vagy writeBatchTimeout találati |A TimeSpan<br/><br/>Példa: "00: 20:00" (20 perc) |Nem (alapértelmezett tároló ügyfél alapértelmezett időtúllépési érték 90 másodperc) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
A forrásoszlop hozzárendelése a fordító JSON tulajdonság használatával, mint a azureTablePartitionKeyName a céloszlop használatba vétele előtt céloszlop.

A következő példában forrásoszlop DivisionID a céloszlop van leképezve: DivisionID.  

```JSON
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```
A DivisionID a partíciós kulcs van megadva.

```JSON
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID",
    "writeBatchSize": 100,
    "writeBatchTimeout": "01:00:00"
}
```
## <a name="json-examples"></a>JSON-példák
Az alábbi példák megadják minta JSON-definíciókat tartalmazzon, segítségével hozzon létre egy folyamatot [Azure-portálon](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Adatok másolása az Azure Table Storage és az Azure Blob adatbázis mutatnak. Azonban az adatok átmásolhatók **közvetlenül** bármelyik bármelyik a támogatott adatforrások fogadók esetében. További információkért lásd: "támogatott adattárolókhoz és formátumok" szakasz a [adatok áthelyezése a másolási tevékenység segítségével](data-factory-data-movement-activities.md).

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Példa: Adatok másolása az Azure tábla az Azure Blob
A következő példában:

1. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (blob & tábla használatos).
2. Bemeneti [dataset](data-factory-create-datasets.md) típusú [AzureTable](#dataset-properties).
3. Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [AzureTableSource](#activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta adataihoz az Azure tábla egy blobba az alapértelmezett partíció óránként másolja. A mintákat a következő szakaszok ismertetik ezeket a mintákat használt JSON-tulajdonságok.

**Az Azure tárolás társított szolgáltatásának:**

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
Az Azure Data Factory két típusú Azure Storage társított szolgáltatásokat támogat: **AzureStorage** és **AzureStorageSas**. Az első címtárra a kapcsolati karakterlánc, amely tartalmazza a fiókkulcs ad meg, és a későbbi egy, a közös hozzáférésű Jogosultságkód (SAS) Uri megadása. Lásd: [összekapcsolt szolgáltatások](#linked-service-properties) című szakaszban talál információt.  

**Az Azure tábla bemeneti adatkészlet:**

A példa feltételezi, hogy létrehozott egy "MyTable" tábla Azure tábla.

"External" beállítása: "true" arról tájékoztatja a Data Factory szolgáltatásnak, hogy az adatkészlet külső data factoryval való és adat-előállító tevékenység nem hozzák.

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

**Az Azure Blob kimeneti adatkészlet:**

Adatot ír egy új blob minden órában (gyakoriság: óra, időköz: 1). A mappa elérési útját a BLOB a szelet által feldolgozott kezdési ideje alapján dinamikusan történik. A mappa elérési útját használja, év, hónap, nap és a kezdési idő órában részeit.

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

**Másolási tevékenység során a folyamat AzureTableSource és BlobSink:**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **AzureTableSource** és **fogadó** típusúra **BlobSink**. A megadott SQL-lekérdezés **AzureTableSourceQuery** tulajdonság kiválasztja azokat az adatokat az alapértelmezett partíció óránként másolja.

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

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>Példa: Adatok másolása az Azure Blob az Azure tábla
A következő példában:

1. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (blob & tábla használatos)
2. Bemeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
3. Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureTable](#dataset-properties).
4. A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és [AzureTableSink](#copy-activity-properties).

A minta másolatok idősorozat adatokat az Azure blob az Azure tábla óránként. A mintákat a következő szakaszok ismertetik ezeket a mintákat használt JSON-tulajdonságok.

**A társított szolgáltatásnak Azure storage (az Azure tábla & Blob):**

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

Az Azure Data Factory két típusú Azure Storage társított szolgáltatásokat támogat: **AzureStorage** és **AzureStorageSas**. Az első címtárra a kapcsolati karakterlánc, amely tartalmazza a fiókkulcs ad meg, és a későbbi egy, a közös hozzáférésű Jogosultságkód (SAS) Uri megadása. Lásd: [összekapcsolt szolgáltatások](#linked-service-properties) című szakaszban talál információt.

**Az Azure Blob bemeneti adatkészletet:**

Adatok van felvett egy új blobból minden órában (gyakoriság: óra, időköz: 1). A mappa elérési útját és nevét a BLOB dinamikusan értékeli ki a kezdési időt a szelet által feldolgozott alapján. A mappa elérési útját használja év, hónap és nap részét kezdési idejét, valamint fájl nevét a kezdő időpontja óra részét. "external": "true" beállítás arról értesíti az, hogy az adatkészlet data factoryval való külső, és egy tevékenység adat-előállító nem hozzák a Data Factory szolgáltatásnak.

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

A minta másolja az adatokat az Azure Table "MyTable" nevű tábla. Hozzon létre egy Azure-tábla azonos számú oszlopot tartalmaz a Blob CSV-fájl várt. Új sorok hozzáadásakor a tábla minden órában.

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

**Másolási tevékenység során a folyamat BlobSource és AzureTableSink:**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **BlobSource** és **fogadó** típusúra **AzureTableSink**.

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
## <a name="type-mapping-for-azure-table"></a>Az Azure-tábla leképezésének
Ahogyan az a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, a másolási tevékenység az eseményforrás-típusnak a következő kétlépéses módszert típusok gyűjtése automatikus típuskonverziók hajt végre.

1. A natív eseményforrás-típusnak átalakítása .NET-típusa
2. .NET-típus konvertálása natív a fogadó típusa

Ha megköveteli az adatok & Azure táblából, a következő [Azure Table szolgáltatás által meghatározott hozzárendelések](https://msdn.microsoft.com/library/azure/dd179338.aspx) használják az Azure tábla OData típusok .NET-típus, és ez fordítva is igaz.

| Az OData-adattípus | .NET-típusa | Részletek |
| --- | --- | --- |
| Edm.Binary |Byte] |Bájttömb legfeljebb 64 KB. |
| Edm.Boolean |Logikai érték |Logikai érték. |
| Edm.DateTime |DateTime |Egy 64 bites érték kifejezett, egyezményes világidő (UTC). A támogatott dátum és idő tartomány kezdődik 12:00 éjféltől. január 1, i 1601. (SZ) (UTC). A tartomány vége December 31 9999. |
| Edm.Double |double |Egy 64 bites lebegőpontos értéket. |
| Edm.Guid |GUID |A 128 bites globálisan egyedi azonosítóját. |
| Edm.Int32 |Int32 |Egy 32 bites egész számot. |
| Edm.Int64 |Int64 |Egy 64 bites egész számot. |
| Edm.String |Sztring |Az UTF-16 kódolású érték. Karakterlánc-értékek legfeljebb 64 KB lehet. |

### <a name="type-conversion-sample"></a>Átalakítás minta
Az alábbi minta van az adatok másolása az Azure Blob az Azure Table típuskonverziók együtt.

Tegyük fel, hogy a Blob-adathalmazra CSV formátumban van, és három oszlopot tartalmaz. A hét napjára rövidített francia nevekkel egyéni dátum és idő formátumú dátum és idő oszlop az egyik.

Adja meg a Blob-forrás adatkészlet típusdefiníciók az oszlopok együtt az alábbiak szerint.

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
Azure tábla OData-típus a .NET-típus típusleképezéshez tekintve határozzák meg a tábla Azure tábla a következő sémával.

**Az Azure tábla sémája:**

| Oszlop neve | Típus |
| --- | --- |
| felhasználói azonosítóját |Edm.Int64 |
| név |Edm.String |
| lastlogindate |Edm.DateTime |

A következő határozza meg az Azure Table-adatkészlet a következőképpen. Nem kell a "structure" szakasz megadását a típussal kapcsolatos információk, mert a típus adatainak már meg van adva az alapul szolgáló adattár.

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

Ebben az esetben Data Factory automatikusan írja be a többek között a DateTime típusú mező egyéni dátum és idő formátumban. a "fr-fr" kulturális környezet használatával, amikor adatokat Blob Azure Table átalakításra.

> [!NOTE]
> Képezze le a fogadó adatkészletből oszlopok forrás adatkészletből oszlopokat, lásd: [Azure Data Factory dataset oszlopai leképezési](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Teljesítmény- és hangolása
Című témakörben olvashat kulcsfontosságú szerepet játszik adatátvitelt jelölik a (másolási tevékenység során) az Azure Data Factory és különböző módokon optimalizálhatja azt, hogy hatás teljesítményét, [másolási tevékenység teljesítmény- és hangolása útmutató](data-factory-copy-activity-performance.md).

---
title: Adatok áthelyezése az Azure-táblára/onnan
description: Megtudhatja, hogyan helyezhetát át az adatokat az Azure Table Storage-ba az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 07b046b1-7884-4e57-a613-337292416319
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 462d54a9d89d6f03aed5e221fa02609da786c8c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260448"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Adatok áthelyezése az Azure Table-be és onnan az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-azure-table-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-azure-table-storage.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [Azure Table Storage-összekötő a V2-ben című témakört.](../connector-azure-table-storage.md)

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység et az Azure Data Factory adatok áthelyezése/ az Azure Table Storage-ból. Az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkre épül, amely általános áttekintést nyújt az adatmozgásról a másolási tevékenységgel. 

Bármely támogatott forrásadattárból adatokat másolhat az Azure Table Storage-ba vagy az Azure Table Storage bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként vagy fogadóként támogatott adattárak listáját a [Támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblában található. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel, amely az adatokat áthelyezi az Azure Table Storage-ba és onnan különböző eszközök/API-k használatával.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló**használata. Olvassa el [az oktatóanyagot: Folyamat létrehozása a Másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakörben egy gyors útmutatót a folyamat másolása az adatok másolása varázslóval történő létrehozásához.

A következő eszközökkel is létrehozhat egy folyamatot: **Visual Studio,** **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**és REST **API.** Lásd: [Tevékenység-oktatóanyag másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című témakörben részletes útmutatást talál egy másolási tevékenységgel rendelkező folyamat létrehozásához. 

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépéseket hajthatja végre egy olyan folyamat létrehozásához, amely adatokat helyezi át a forrásadattárból a fogadó adattárába: 

1. **Összekapcsolt szolgáltatások** létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására.
2. **Adatkészletek** létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására. 
3. Hozzon létre egy **folyamatot** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, egy adatkészletet pedig kimenetként vesz fel. 

A varázsló használatakor a Data Factory entitásokhoz (csatolt szolgáltatások, adatkészletek és a folyamat) json-definíciók automatikusan létrejönnek. Eszközök/API-k használatakor (a .NET API kivételével) ezeket a Data Factory entitásokat a JSON formátum használatával definiálhatja. Az Azure Table Storage-ba és onnan adatok másolására használt Data Factory-entitások JSON-definícióival rendelkező mintákat lásd: A cikk [JSON-példái](#json-examples) című részében.

A következő szakaszok az Azure Table Storage-ra jellemző Data Factory-entitások meghatározásához használt JSON-tulajdonságok részleteit ismertetik: 

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai
Az Összekapcsolt szolgáltatásoknak két típusa van, amelynek segítségével egy Azure blobstorage-t egy Azure-adatelőszolgáltatáshoz kapcsolhat. Ezek a következők: **AzureStorage-kapcsolt** szolgáltatás és **AzureStorageSas** kapcsolt szolgáltatás. Az Azure Storage-hoz kapcsolódó szolgáltatás biztosítja az adatgyár globális hozzáférést biztosít az Azure Storage-hoz. Mivel az Azure Storage SAS (megosztott hozzáférésű aláírás) összekapcsolt szolgáltatás biztosítja az adatgyár korlátozott/időhöz kötött hozzáférést az Azure Storage-hoz. Nincs más különbség a két összekapcsolt szolgáltatás között. Válassza ki az igényeinek megfelelő összekapcsolt szolgáltatást. A következő szakaszok további részleteket tartalmaznak a két összekapcsolt szolgáltatásról.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálására szolgáló & tulajdonságok teljes listáját az [Adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben olvashat. A json-i adatkészletek például a struktúra, a rendelkezésre állás és a szabályzat hasonlóak az összes adatkészlettípushoz (Azure SQL, Azure blob, Azure table stb.).

A typeProperties szakasz az adatkészlet egyes típusainál eltérő, és tájékoztatást nyújt az adatok helyéről az adattárban. Az **AzureTable** típusú adatkészlet **typeProperties** szakasza a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |A tábla neve az Azure Table Database-példányban, amelyre a csatolt szolgáltatás hivatkozik. |Igen. Ha egy tableName azureTableSourceQuery nélkül van megadva, a tábla összes rekordja a célhelyre kerül. Ha egy azureTableSourceQuery is meg van adva, a tábla azon rekordjai, amelyek megfelelnek a lekérdezésnek, a rendszer a célhelyre másolja. |

### <a name="schema-by-data-factory"></a>Séma adatgyár szerint
Sémamentes adattárak, például az Azure Table esetében a Data Factory szolgáltatás az alábbi módokon következtet a sémából:

1. Ha az adatkészlet-definícióban a **struktúra** tulajdonság használatával adja meg az adatok szerkezetét, a Data Factory szolgáltatás ezt a struktúrát sémaként tartja tiszteletben. Ebben az esetben, ha egy sor nem tartalmaz értéket egy oszlophoz, null értéket ad meg.
2. Ha nem adja meg az adatok szerkezetét az adatkészlet-definícióban a **struktúra** tulajdonság használatával, a Data Factory az adatok első sorának használatával következtet a sémából. Ebben az esetben, ha az első sor nem tartalmazza a teljes sémát, néhány oszlop kimarad a másolási művelet eredményében.

Ezért sémamentes adatforrások esetében ajánlott az adatok szerkezetének megadása a **struktúra** tulajdonság használatával.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálására rendelkezésre álló szakaszok & tulajdonságok teljes listáját a [Folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben olvashat. Tulajdonságok, például név, leírás, bemeneti és kimeneti adatkészletek és házirendek állnak rendelkezésre minden típusú tevékenységek.

A tevékenység típustulajdonságai szakaszában elérhető tulajdonságok viszont az egyes tevékenységtípusoktól függően változnak. Másolási tevékenység esetén a források és a fogadók típusától függően változnak.

**Az AzureTableSource** a következő tulajdonságokat támogatja a typeProperties szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| azureTableSourceQuery |Az adatok olvasásához használja az egyéni lekérdezést. |Azure-tábla lekérdezési karakterlánc. Tekintse meg a következő szakaszban található példákat. |Nem. Ha egy tableName azureTableSourceQuery nélkül van megadva, a tábla összes rekordja a célhelyre kerül. Ha egy azureTableSourceQuery is meg van adva, a tábla azon rekordjai, amelyek megfelelnek a lekérdezésnek, a rendszer a célhelyre másolja. |
| azureTableSourceIgnoreTableNotFound |Adja meg, hogy a tábla kivételének kivétele nem létezik-e. |IGAZ<br/>HAMIS |Nem |

### <a name="azuretablesourcequery-examples"></a>példák az azureTableSourceQuery
Ha az Azure Table oszlop karakterlánc típusú:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Ha az Azure Table oszlop dátumidő-típusú:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**Az AzureTableSink** a következő tulajdonságokat támogatja a typeProperties szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue érték |A fogadó által használható alapértelmezett partíciókulcs-érték. |Karakterlánc-érték. |Nem |
| azureTablePartitionKeyName |Adja meg annak az oszlopnak a nevét, amelynek értékeit partíciókulcsként használják. Ha nincs megadva, az AzureTableDefaultPartitionKeyValue lesz a partíciókulcs. |Oszlopnév. |Nem |
| azureTableRowKeyName |Adja meg annak az oszlopnak a nevét, amelynek oszlopértékeit sorkulcsként használják. Ha nincs megadva, minden sorhoz használjon GUID azonosítót. |Oszlopnév. |Nem |
| azureTableInsertType |Az Azure-táblába adatok beszúrásának módja.<br/><br/>Ez a tulajdonság azt szabályozza, hogy a kimeneti tábla egyező partícióval és sorkulcsokkal rendelkező meglévő sorai tették-e lecserélve vagy egyesítve a kimeneti tábla meglévő sorait. <br/><br/>Ha többet szeretne tudni arról, hogyan működnek ezek a beállítások (egyesítés e és csere), olvassa el az [Entitás beszúrása vagy egyesítése,](https://msdn.microsoft.com/library/azure/hh452241.aspx) valamint [az Entitás beszúrása vagy cseréje](https://msdn.microsoft.com/library/azure/hh452242.aspx) témakört. <br/><br> Ez a beállítás a sor szintjén érvényes, nem a táblázat szintjén, és egyik lehetőség sem törli a kimeneti tábla olyan sorait, amelyek nem léteznek a bemenetben. |egyesítés (alapértelmezett)<br/>Helyettesít |Nem |
| writeBatchSize |Adatokat szúr be az Azure-táblába, ha a writeBatchSize vagy writeBatchTimeout levan lépve. |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| writeBatchTimeout |Adatok beszúrása az Azure-táblába a writeBatchSize vagy writeBatchTimeout leütése esetén |időtartomány<br/><br/>Példa: "00:20:00" (20 perc) |Nem (Alapértelmezett a tárolóügyfél alapértelmezett időtúlértéke 90 mp) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Rendeljen hozzá egy forrásoszlopot egy céloszlophoz a fordító JSON tulajdonsághasználatával, mielőtt a céloszlopot azureTablePartitionKeyName néven használhatna.

A következő példában a DivisionID forrásoszlop a céloszlophoz van rendelve: DivisionID.  

```JSON
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```
A DivisionID partíciókulcsként van megadva.

```JSON
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID",
    "writeBatchSize": 100,
    "writeBatchTimeout": "01:00:00"
}
```
## <a name="json-examples"></a>Példák json-példák
Az alábbi példák minta JSON-definíciókat tartalmaznak, amelyek segítségével folyamatot hozhat létre a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy az [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával. Ezek azt mutatják, hogyan másolhat adatokat, és az Azure Table Storage és az Azure Blob Database. Az adatok azonban **közvetlenül** a források bármelyikéből átmásolhatók a támogatott fogadók bármelyikébe. További információt az Adatok áthelyezése a [Másolási tevékenység használatával](data-factory-data-movement-activities.md)című szakaszban talál" "Támogatott adattárak és formátumok" című szakaszban.

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Példa: Adatok másolása az Azure Table-ből az Azure Blobba
A következő minta a következőket mutatja:

1. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) típusú összekapcsolt szolgáltatás (mindkét tábla & blobhoz).
2. [AzureTable](#dataset-properties)típusú bemeneti [adatkészlet.](data-factory-create-datasets.md)
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
4. Az AzureTableSource és [a BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)programot használó Másolási tevékenységet tartalmazó [folyamat.](data-factory-create-pipelines.md)

A minta az Azure-tábla alapértelmezett partíciójának adatait másolja át óránként egy blobba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

**Azure storage-hoz kapcsolódó szolgáltatás:**

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
Az Azure Data Factory kétféle Azure Storage-kapcsolt szolgáltatást támogat: **az AzureStorage-t** és **az AzureStorageSas-t.** Az elsőhöz adja meg a fiókkulcsot tartalmazó kapcsolati karakterláncot, a későbbihez pedig a Megosztott hozzáférésű aláírás (SAS) Uri-t. A részleteket lásd a [Csatolt szolgáltatások](#linked-service-properties) című szakaszban.  

**Azure Table bemeneti adatkészlet:**

A minta feltételezi, hogy létrehozott egy "MyTable" táblát az Azure Table-ben.

"külső" beállítás: az "igaz" tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított.

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

**Azure Blob kimeneti adatkészlet:**

Az adatok óránként egy új blobba (gyakoriság: óra, időköz: 1) kerül beírásra. A blob mappaelérési útja dinamikusan kiértékelve a feldolgozás alatt álló szelet kezdési időpontja alapján történik. A mappa elérési útja a kezdési időpont év-, hónap-, nap- és órarészeit használja.

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

**Az AzureTableSource és a BlobSink folyamatában végzett tevékenység másolása:**

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránként i. A folyamat JSON-definíciójában a **forrástípus** **az AzureTableSource-ra** van állítva, **a fogadó** típusa pedig **BlobSink**. Az **AzureTableSourceQuery** tulajdonsággal megadott SQL-lekérdezés óránként kiválasztja az alapértelmezett partíció adatait.

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

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>Példa: Adatok másolása az Azure Blobból az Azure Table-be
A következő minta a következőket mutatja:

1. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) típusú csatolt szolgáltatás (mindkét tábla & blobhoz használható)
2. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú bemeneti [adatkészlet.](data-factory-create-datasets.md)
3. [AzureTable](#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
4. A [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és [az AzureTableSink](#copy-activity-properties)programot használó másolási tevékenységet tartalmazó [folyamat.](data-factory-create-pipelines.md)

A minta idősorozat-adatokat másol egy Azure-blobból egy Azure-táblába óránként. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

**Azure storage (az Azure Table & blob esetén is) kapcsolt szolgáltatás:**

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

Az Azure Data Factory kétféle Azure Storage-kapcsolt szolgáltatást támogat: **az AzureStorage-t** és **az AzureStorageSas-t.** Az elsőhöz adja meg a fiókkulcsot tartalmazó kapcsolati karakterláncot, a későbbihez pedig a Megosztott hozzáférésű aláírás (SAS) Uri-t. A részleteket lásd a [Csatolt szolgáltatások](#linked-service-properties) című szakaszban.

**Azure Blob bemeneti adatkészlet:**

Az adatokat óránként veszi fel egy új blob (gyakoriság: óra, időköz: 1). A blob mappaelérési útja és fájlneve dinamikusan kiértékelésre kerül a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési időpont év, hónap és nap részét, a fájlnév pedig a kezdési időpont órarészét használja. "külső": "true" beállítás tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet az adat-előállítón kívül van, és nem az adat-előállító tevékenység által előállított.

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

**Azure Table kimeneti adatkészlet:**

A minta adatokat másol egy "MyTable" nevű táblába az Azure Table-ben. Hozzon létre egy Azure-táblát ugyanannyi oszlopot, mint ablob CSV-fájl tartalmaznia. A rendszer óránként új sorokat ad a táblához.

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

**Tevékenység másolása folyamatban a BlobSource és az AzureTableSink használatával:**

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránként i. A folyamat JSON-definíciójában a **forrástípus** **BlobSource** lesz állítva, **a fogadó** típusa pedig **Az AzureTableSink**.

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
## <a name="type-mapping-for-azure-table"></a>Típus-leképezés az Azure-táblához
Az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) ről szóló cikkben említettek szerint a Másolás tevékenység automatikus típuskonverziót hajt végre a forrástípusokról a fogadótípusokra a következő kétlépéses megközelítéssel.

1. Konvertálás natív forrástípusokból .NET-típussá
2. Konvertálás .NET típusból natív fogadótípussá

Amikor adatokat helyez át & az Azure Table szolgáltatásból, az Azure Table szolgáltatás által meghatározott alábbi [leképezések](https://msdn.microsoft.com/library/azure/dd179338.aspx) kerülnek a rendszerbe az Azure Table OData-típusokból a .NET-típusba, és fordítva.

| OData-adatok típusa | .NET típus | Részletek |
| --- | --- | --- |
| Edm.Bináris |bájt[] |Legfeljebb 64 KB bájtok ból álló tömb. |
| Edm.Boolean |Bool |Logikai érték. |
| Edm.DateTime |DateTime |64 bites érték koordinált világidőben (UTC) kifejezve. A támogatott DateTime tartomány 1601. (C.E.), UTC. A tartomány 9999. |
| Edm.Double |double |64 bites lebegőpontos érték. |
| Edm.Guid |Guid |128 bites globálisan egyedi azonosító. |
| Edm.Int32 |Int32 |Egy 32 bites egész szám. |
| Edm.Int64 |Int64 |Egy 64 bites egész szám. |
| Edm.String |Sztring |UTF-16 kódolású érték. A karakterlánc-értékek legfeljebb 64 KB lehetnek. |

### <a name="type-conversion-sample"></a>Típus-konvertálási minta
A következő minta az adatok másolása egy Azure Blob az Azure Table típusú konverziók.

Tegyük fel, hogy a Blob-adatkészlet CSV formátumú, és három oszlopot tartalmaz. Az egyik egy datetime oszlop egyéni datetime formátummal, amely rövidített francia neveket használ a hét napjára.

Adja meg a Blob Source adatkészletet az alábbiak szerint az oszlopok típusdefinícióival együtt.

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
Mivel a típus leképezés az Azure Table OData típusú .NET típusú, a következő sémával határozza meg a táblát az Azure Table.As Type Mapping from Azure Table OData type to .NET type, you would define the table in Azure Table with the following schema.

**Azure Table séma:**

| Oszlop neve | Típus |
| --- | --- |
| Userid |Edm.Int64 |
| név |Edm.String |
| lastlogindate |Edm.DateTime |

Ezután adja meg az Azure Table adatkészletet az alábbiak szerint. Nem kell megadnia a "struktúra" szakaszt a típusadatokkal, mivel a típusinformáció már meg van adva az alapul szolgáló adattárban.

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

Ebben az esetben a Data Factory automatikusan írja be a konverziókat, beleértve a Datetime mezőt az egyéni datetime formátummal az "fr-fr" kulturális környezet használatával, amikor adatokat helyez át a Blobból az Azure Table-be.

> [!NOTE]
> Ha oszlopokat szeretne leképezni a forrásadatkészletről a fogadó adatkészletoszlopaira, olvassa el [az Adatkészletoszlopok leképezése az Azure Data Factoryban című témakört.](data-factory-map-columns.md)

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
Ha meg szeretné tudni, hogy milyen kulcsfontosságú tényezők befolyásolják az adatok mozgását (másolási tevékenység) az Azure Data Factoryban, és különböző módokon optimalizálhatja azt, olvassa el [a Tevékenység teljesítményének másolása & hangolási útmutatócímű témakört.](data-factory-copy-activity-performance.md)

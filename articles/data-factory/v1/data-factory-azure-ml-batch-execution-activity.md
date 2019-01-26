---
title: Azure Data Factory segítségével prediktív adatcsatornák létrehozása |} A Microsoft Docs
description: Ismerteti, hogyan hozhat létre Azure Data Factory és az Azure Machine Learning prediktív adatcsatornák létrehozása
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 4fad8445-4e96-4ce0-aa23-9b88e5ec1965
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: c280a1f7e060ab7637e8d0b2484951f72b58a89c
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55081194"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Az Azure Machine Learning és az Azure Data Factory prediktív adatcsatornák létrehozása

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive-tevékenység](data-factory-hive-activity.md)
> * [Pig-tevékenység](data-factory-pig-activity.md)
> * [MapReduce-tevékenység](data-factory-map-reduce.md)
> * [Hadoop Streamelési tevékenységben](data-factory-hadoop-streaming-activity.md)
> * [Spark-tevékenység](data-factory-spark.md)
> * [Machine Learning kötegelt végrehajtási tevékenység](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning Update-erőforrástevékenység](data-factory-azure-ml-update-resource-activity.md)
> * [Tárolt eljárási tevékenység](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-tevékenység](data-factory-usql-activity.md)
> * [.NET egyéni tevékenység](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Bevezetés
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [adatok átalakítása a machine learning használatával a Data Factory](../transform-data-using-machine-learning.md).


### <a name="azure-machine-learning"></a>Azure Machine Learning
[Az Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) lehetővé teszi, hogy hozhat létre, tesztelheti és üzembe helyezése a prediktív elemzési megoldások. Egy magas szintű szempontjából ez történik, három lépésben:

1. **Betanítási kísérlet létrehozása**. Az Azure Machine Learning studio használatával teheti meg ezt a lépést. Az Azure Machine Learning studio olyan, amellyel taníthat vagy tesztelhet egy prediktív elemzési modellt használ a betanítási adatok által biztosított együttműködési környezettel vizuális fejlesztői környezet.
2. **Alakítsa át egy prediktív kísérletet**. A modell rendelkezik betanítva a adatokkal, és készen áll az új adatok pontozása céljából, előkészítése, és egyszerűsíthetők a pontozó kísérlet során.
3. **Helyezze üzembe webszolgáltatásként, amely**. A pontozó kísérlet során egy Azure-webszolgáltatásként teheti közzé. Adatokat küldeni a modell használatával a webes szolgáltatás végpontját és a kap eredmény előrejelzéseket kínál a modellt.

### <a name="azure-data-factory"></a>Azure Data Factory
A Data Factory egy felhőalapú adatintegrációs szolgáltatás, amellyel előkészíthető és automatizálható az adatok **továbbítása** és **átalakítása**. Létrehozhat olyan adatintegrációs megoldásokat az Azure Data Factory használatával, különböző adattárakból származó adatokat, az adatok átalakíthatók/feldolgozhatók, és az eredmények pedig az adattárakban közzétehetők.

A Data Factoryval az adatok továbbítására és átalakítására szolgáló adatfolyamatokat hozhat létre, majd ütemezés szerint futtathatja a folyamatot (óránként, napi, heti stb. gyakorisággal). Ezenkívül látványos vizualizációkkal jelenítheti meg az adatfolyamatok közötti leszármaztatási és függőségi kapcsolatokat, valamint egyetlen, egységesített nézetben figyelheti az összes folyamatot, így egyszerűen kiszűrheti a problémákat és beállíthatja a figyelési riasztásokat.

Lásd: [az Azure Data Factory bemutatását](data-factory-introduction.md) és [az első folyamat létrehozása](data-factory-build-your-first-pipeline.md) gyorsan az Azure Data Factory szolgáltatás használatának első lépései a cikkeket.

### <a name="data-factory-and-machine-learning-together"></a>A Data Factory és a gépi tanulás együtt
Az Azure Data Factory lehetővé teszi, hogy egyszerűen hozzon létre egy közzétett használó folyamatok [Azure Machine Learning] [ azure-machine-learning] webszolgáltatás prediktív elemzőeszközöket. Használatával a **kötegelt végrehajtási tevékenység** az Azure Data Factory-folyamatot, az Azure Machine Learning studio-webszolgáltatás, hogy előrejelzéseket végezzen az adatok a Batch szolgáltatásban lévő hívhatók meg. Lásd: [meghívása az Azure Machine Learning studio webszolgáltatás a kötegelt végrehajtási tevékenység használatával](#invoking-an-azure-ml-web-service-using-the-batch-execution-activity) című szakasz részletezi.

Az idő múlásával a prediktív modelleket a az Azure Machine Learning studio kísérletek pontozási kell kell retrained új bemeneti adatkészletek használatával. A Data Factory-folyamatot egy Azure Machine Learning studio-modell ténytábláknál a következő lépések végrehajtásával:

1. Tegye közzé a tanítási kísérlet (nem prediktív kísérletté) webszolgáltatásként. Ebben a lépésben az Azure Machine Learning studióban végezhet el, ahogy tette elérhetővé a prediktív kísérletté webszolgáltatásként, amely az előző forgatókönyvben.
2. Az Azure Machine Learning studio kötegelt végrehajtási tevékenység használatával lehet meghívni a webszolgáltatás a betanítási kísérlet. Alapvetően használhatja az Azure Machine Learning studio kötegelt végrehajtási tevékenység képzési webszolgáltatás és a pontozási webszolgáltatás meghívandó.

Miután elkészült, az átképezési, a pontozási webszolgáltatás (prediktív kísérletté webszolgáltatásként közzétéve) az újonnan betanított modell használatával módosítsa a **Erőforrástevékenység frissítése az Azure Machine Learning studio**. Lásd: [frissítési modellek használata az Update-Erőforrástevékenység](data-factory-azure-ml-update-resource-activity.md) részleteivel.

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Egy webszolgáltatás, használja a kötegelt végrehajtási tevékenység
Azure Data Factory használata adatok áthelyezése és feldolgozása, és hajtsa végre az Azure Machine Learning segítségével kötegelt végrehajtási. A legfelső szintű lépések a következők:

1. Létrehoz egy társított Azure Machine Learning szolgáltatást. A következő értékeket lesz szüksége:

   1. **Kérés URI-ja** a kötegelt műveleti API-t. A kérelem URI kattintva nyithatja meg a **KÖTEGELT végrehajtási** a szolgáltatások weblapon található hivatkozásra.
   2. **API-kulcs** a közzétett Azure Machine Learning webszolgáltatás. Az API-kulcsot a webszolgáltatás, amelyet közzétételét kattintva nyithatja meg.
   3. Használja a **AzureMLBatchExecution** tevékenység.

      ![Machine Learning-irányítópult](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![A Batch URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Forgatókönyv: A kísérletek webes szolgáltatás bemenete/kimenete, amely az adatoknak az Azure Blob Storage használatával
Ebben a forgatókönyvben az Azure Machine Learning Web service-adatok egy Azure blob Storage-fájlból előrejelzéseket tesz, és a blob Storage-Előrejelzési eredményeket tárolja. A következő JSON-kódrészlet meghatározza egy Data Factory-folyamatot egy olyan AzureMLBatchExecution tevékenységgel. A tevékenység rendelkezik az adatkészlet **DecisionTreeInputBlob** bemenetként, és **DecisionTreeResultBlob** a kimenetként. A **DecisionTreeInputBlob** átadott, a web Service a bemenetnek használatával a **webServiceInput** JSON-tulajdonságot. A **DecisionTreeResultBlob** átadott kimenetként a webalkalmazás-szolgáltatás használatával a **webServiceOutputs** JSON-tulajdonságot.

> [!IMPORTANT]
> Ha a webszolgáltatás több bemenet vesz igénybe, használja a **webServiceInputs** tulajdonság használata helyett **webServiceInput**. Tekintse meg a [webszolgáltatás igényel több bemenet](#web-service-requires-multiple-inputs) szakasz egy példát a webServiceInputs tulajdonságot használja.
>
> Az adatkészletek által hivatkozott a **webServiceInput**/**webServiceInputs** és **webServiceOutputs** tulajdonságok (az  **typeProperties**) is szerepelnie kell a tevékenység **bemenetek** és **kimenete**.
>
> Az Azure Machine Learning studio-kísérlet, a web service bemeneti és kimeneti portokkal és globális paraméterek alapértelmezett neve lehet ("input1", "input2"), amelyek testre szabhatók. A nevek webServiceInputs webServiceOutputs és globalParameters beállításokat használhat a kísérletek a nevek pontosan egyeznie kell. A minta-kérések forgalma kötegelt végrehajtási súgóoldalán az Azure Machine Learning studio végpont ellenőrzése a várt leképezés tekintheti meg.
>
>

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchExecution",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "DecisionTreeInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "DecisionTreeResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "typeProperties":
        {
            "webServiceInput": "DecisionTreeInputBlob",
            "webServiceOutputs": {
                "output1": "DecisionTreeResultBlob"
            }
        },
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```
> [!NOTE]
> Csak bemenetek és kimenetek AzureMLBatchExecution tevékenység argumentumként átadhatók paraméterek a webszolgáltatást. Ha például a fenti JSON-kódrészletben DecisionTreeInputBlob a AzureMLBatchExecution tevékenység, amelyet a webszolgáltatás bemeneteként webServiceInput paraméteren keresztül egy bemeneti.
>
>

### <a name="example"></a>Példa
Ebben a példában a bemeneti és kimeneti adatok tárolásához Azure Storage használ.

Azt javasoljuk, hogy mindenképpen haladjon végig a [Data Factory használatának első folyamatát] [ adf-build-1st-pipeline] oktatóanyag, mielőtt továbblépne ebben a példában keresztül. A Data Factory Editor használatával hozzon létre a Data Factory-összetevők (társított szolgáltatások, adatkészletek, folyamat) ebben a példában.

1. Hozzon létre egy **társított szolgáltatás** számára a **Azure Storage**. Ha a bemeneti és kimeneti fájlok különböző tárfiókokban, két társított szolgáltatást kell. Íme egy példa JSON:

    ```JSON
    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
        }
      }
    }
    ```
2. Hozzon létre a **bemeneti** Azure Data Factory **adatkészlet**. Néhány további adat-előállító adatkészletek, ellentétben ezek az adatkészletek tartalmaznia kell mindkét **folderPath** és **fileName** értékeket. A particionálás segítségével minden egyes kötegelt végrehajtási (az összes adatszelet) feldolgozása vagy előállítani egyedi bemeneti és kimeneti fájlok miatt. Szükség lehet néhány felsőbb szintű tevékenység a bemeneti alakítsa át a CSV fájlformátum, és helyezze a tárfiókot az egyes szeletekhez tartalmazza. Ebben az esetben, nem tartalmazza a **külső** és **externalData** az alábbi példa, és a DecisionTreeInputBlob látható beállításokat egy másik tevékenység kimeneti adatkészletét lenne.

    ```JSON
    {
      "name": "DecisionTreeInputBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/input",
          "fileName": "in.csv",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
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

    A bemeneti csv-fájl az oszlop fejlécsor kell rendelkeznie. Ha használja a **másolási tevékenység** létrehozása és áthelyezése a fürt megosztott kötetei szolgáltatás a blob storage-ba, a fogadó tulajdonságot kell beállítania **blobWriterAddHeader** való **igaz**. Példa:

    ```JSON
    sink:
    {
        "type": "BlobSink",
        "blobWriterAddHeader": true
    }
    ```

    A csv-fájl nem rendelkezik a fejléc sorában, a következő hiba jelenhet meg: **Hiba történt a tevékenységben: Hibakarakterlánc olvasása. Nem várt token: StartObject. Elérési út: "%, 1. sor, 1 elhelyezése**.
3. Hozzon létre a **kimeneti** Azure Data Factory **adatkészlet**. Ebben a példában használja a particionálás hozhat létre egyedi kimeneti elérési utat az összes szelet végrehajtását. A particionálás nélkül a tevékenység felülírja a fájlt.

    ```JSON
    {
      "name": "DecisionTreeResultBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/scored/{folderpart}/",
          "fileName": "{filepart}result.csv",
          "partitionedBy": [
            {
              "name": "folderpart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyyMMdd"
              }
            },
            {
              "name": "filepart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HHmmss"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 15
        }
      }
    }
    ```
4. Hozzon létre egy **társított szolgáltatás** típusa: **AzureMLLinkedService**, biztosít az API-kulcsot, és modellezheti a kötegelt végrehajtás URL-CÍMÉT.

    ```JSON
    {
      "name": "MyAzureMLLinkedService",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
          "mlEndpoint": "https://[batch execution endpoint]/jobs",
          "apiKey": "[apikey]"
        }
      }
    }
    ```
5. Végül a folyamat, amely tartalmazza készíthet egy **AzureMLBatchExecution** tevékenység. Futásidőben a folyamat a következő lépéseket hajtja végre:

   1. A bemeneti adatkészletek olvassa be a bemeneti fájl helyét.
   2. Hívja meg az Azure Machine Learning kötegelt végrehajtási API
   3. A kötegelt végrehajtás kimenetének másol a blob, a kimeneti adatkészlet megadott.

      > [!NOTE]
      > AzureMLBatchExecution tevékenység rendelkezhet, nulla vagy több bemeneti és a egy vagy több kimenetek.
      >
      >

    ```JSON
    {
        "name": "PredictivePipeline",
        "properties": {
            "description": "use AzureML model",
            "activities": [
            {
                "name": "MLActivity",
                "type": "AzureMLBatchExecution",
                "description": "prediction analysis on batch input",
                "inputs": [
                {
                    "name": "DecisionTreeInputBlob"
                }
                ],
                "outputs": [
                {
                    "name": "DecisionTreeResultBlob"
                }
                ],
                "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob"
                    }
                },
                "policy": {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            }
            ],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
        }
    }
    ```

      Mindkét **start** és **záró** időpontok kell [ISO formátumban](http://en.wikipedia.org/wiki/ISO_8601). Példa: 2014-10-14T16:32:41Z. A **záró** idő megadása nem kötelező. Ha nem ad meg értéket a **záró** tulajdonságot, akkor számítjuk ki, hogy "**kezdő időpont + 48 óra.**" A folyamat határozatlan ideig történő futtatásához adja meg a **9999-09-09** értéket az **end** (befejezés) tulajdonsághoz. A JSON-tulajdonságokkal kapcsolatos információkért lásd: [JSON Scripting Reference](https://msdn.microsoft.com/library/dn835050.aspx) (Referencia a JSON-parancsprogramokhoz).

      > [!NOTE]
      > Adja meg a AzureMLBatchExecution beviteli tevékenység nem kötelező.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Forgatókönyv: Író vagy olvasó modulok használata az adatoknak a különböző tárolók kísérletek
Egy másik gyakori forgatókönyv, amikor az Azure Machine Learning studio-kísérletek létrehozása, ha írási és olvasási szerepkörökhöz modulok használják. Az olvasó modul használható adatokat tölthet be egy kísérletet, és a író modul az adatok mentése a kísérletekből. Írási és olvasási szerepkörökhöz modullal kapcsolatos részletekért lásd: [olvasó](https://msdn.microsoft.com/library/azure/dn905997.aspx) és [író](https://msdn.microsoft.com/library/azure/dn905984.aspx) témakörök az MSDN könyvtárában.

Az írási és olvasási szerepkörökhöz modulok használata esetén hasznos lehet az író vagy olvasó modul minden egyes tulajdonság egy webes szolgáltatás paraméter használható. Webes paraméterek lehetővé teszik az értékek konfigurálása során. Például létrehozhat egy olvasó modul, amely használja az Azure SQL Database egy kísérlet: XXX.database.windows.net. Miután a web service telepítve lett, a web service, adjon meg egy másik Azure SQL Server YYY.database.windows.net nevű felhasználói számára engedélyezni szeretné. A Web service paramétert használhatja, hogy ezt az értéket kell konfigurálni.

> [!NOTE]
> Webes szolgáltatás bemeneti és kimeneti eltérnek a webszolgáltatás-paraméterek. Az első esetben láthatta, hogyan egy bemeneti és kimeneti adható meg az Azure Machine Learning studio webszolgáltatás. Ebben a forgatókönyvben adja át a paramétereket egy webszolgáltatás, amelyek megfelelnek az író vagy olvasó modulok tulajdonságait.
>
>

Tekintsük át egy webszolgáltatás-paraméterek a forgatókönyvet. Egy telepített Azure Machine Learning webszolgáltatás, amelyet egy olvasó modul használ adatokat olvasni az Azure Machine Learning által támogatott adatforrások egyik rendelkezik (például: Az Azure SQL Database). A kötegelt végrehajtás hajtja végre, miután az eredmények használatával írt író modul (az Azure SQL Database).  Nincs webes szolgáltatás bemeneteit és kimeneteit a kísérletek vannak definiálva. Ebben az esetben javasoljuk, hogy konfigurálja a megfelelő webszolgáltatás-paraméterek az írási és olvasási szerepkörökhöz modulok. Ez a konfiguráció lehetővé teszi, hogy az író vagy olvasó modulok konfigurálását, ha a AzureMLBatchExecution tevékenységgel. Azt adja meg a webszolgáltatás-paraméterek a **globalParameters** a következő szakasz a tevékenység JSON.

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

Is [Data Factory-függvények](data-factory-functions-variables.md) paramétereket lévő értékek átadja a webes szolgáltatás, az alábbi példában látható módon:

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> A webszolgáltatás-paraméterek kis-és nagybetűket, ezért győződjön meg arról, hogy a nevét adja meg, ha a tevékenység a JSON egyeznek azokkal a webszolgáltatás által elérhetővé tett.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Egy olvasó modul segítségével adatokat olvasni az Azure-Blobban található több fájl
A big data-folyamatok tevékenységekben, mint például a Pig és Hive hozhat létre egy vagy több kimeneti fájlok, a bővítmények nincsenek. Például a külső Hive tábla megadása esetén a külső Hive-tábla adatai is tárolhatók az Azure blob storage-ban a következő név 000000_0. Kísérlet a reader module segítségével több fájlok olvasásához, és használatukhoz ismeretekkel.

Az Azure Machine Learning-kísérletből a reader module segítségével, ha az Azure Blob is megadhat bemenetként. A fájlok az Azure blob Storage a kimeneti fájlokat is lehetnek (például: 000000_0), amely a Pig and Hive parancsfájl futtatása a HDInsight által készített. Az olvasó modul lehetővé teszi, hogy olvassa el a (nincs kiterjesztésű) fájlokat úgy konfigurálja a **tároló elérési útja a blob/directory**. A **tároló elérési útja** a tárolóra mutat, és **directory/blob** mutat, az alábbi képen látható módon a fájlokat tartalmazó mappát. A csillag, \*) **azt jelenti, hogy a tároló/mappában található összes fájl (azt jelenti, adatok/aggregateddata/év = 2014/hó – 6 /\*)** a kísérlet részeként olvasható.

![Az Azure Blob tulajdonságai](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Példa
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>A webszolgáltatás-paraméterek AzureMLBatchExecution tevékenységgel rendelkező folyamat

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure Machine Learning studio model with sql azure reader/writer",
    "activities": [
      {
        "name": "MLSqlReaderSqlWriterActivity",
        "type": "AzureMLBatchExecution",
        "description": "test",
        "inputs": [
          {
            "name": "MLSqlInput"
          }
        ],
        "outputs": [
          {
            "name": "MLSqlOutput"
          }
        ],
        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
        "typeProperties":
        {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
                "output1": "MLSqlOutput"
            }
              "globalParameters": {
                "Database server name": "<myserver>.database.windows.net",
                "Database name": "<database>",
                "Server user account name": "<user name>",
                "Server user account password": "<password>"
              }
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        },
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

A fenti JSON-példa:

* A telepített Azure Machine Learning Web service olvasási/írási adatokat az Azure SQL Database a és a egy olvasó és a egy író modul használatával. A webszolgáltatás tünteti fel a következő négy paraméterek:  Adatbázis-kiszolgáló nevét, adatbázisnevet, Server felhasználói fiók nevét és kiszolgáló felhasználói fiók jelszavát.
* Mindkét **start** és **záró** időpontok kell [ISO formátumban](http://en.wikipedia.org/wiki/ISO_8601). Példa: 2014-10-14T16:32:41Z. A **záró** idő megadása nem kötelező. Ha nem ad meg értéket a **záró** tulajdonságot, akkor számítjuk ki, hogy "**kezdő időpont + 48 óra.**" A folyamat határozatlan ideig történő futtatásához adja meg a **9999-09-09** értéket az **end** (befejezés) tulajdonsághoz. A JSON-tulajdonságokkal kapcsolatos információkért lásd: [JSON Scripting Reference](https://msdn.microsoft.com/library/dn835050.aspx) (Referencia a JSON-parancsprogramokhoz).

### <a name="other-scenarios"></a>Egyéb forgatókönyvek
#### <a name="web-service-requires-multiple-inputs"></a>Webszolgáltatás igényel több bemenet
Ha a webszolgáltatás több bemenet vesz igénybe, használja a **webServiceInputs** tulajdonság használata helyett **webServiceInput**. Az adatkészletek által hivatkozott a **webServiceInputs** is szerepelnie kell a tevékenység **bemenetek**.

Az Azure Machine Learning studio-kísérlet, a web service bemeneti és kimeneti portokkal és globális paraméterek alapértelmezett neve lehet ("input1", "input2"), amelyek testre szabhatók. A nevek webServiceInputs webServiceOutputs és globalParameters beállításokat használhat a kísérletek a nevek pontosan egyeznie kell. A minta-kérések forgalma kötegelt végrehajtási súgóoldalán az Azure Machine Learning studio végpont ellenőrzése a várt leképezés tekintheti meg.

```JSON
{
    "name": "PredictivePipeline",
    "properties": {
        "description": "use AzureML model",
        "activities": [{
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [{
                "name": "inputDataset1"
            }, {
                "name": "inputDataset2"
            }],
            "outputs": [{
                "name": "outputDataset"
            }],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties": {
                "webServiceInputs": {
                    "input1": "inputDataset1",
                    "input2": "inputDataset2"
                },
                "webServiceOutputs": {
                    "output1": "outputDataset"
                }
            },
            "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
            }
        }],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
    }
}
```

#### <a name="web-service-does-not-require-an-input"></a>Webszolgáltatás nem szükséges egy bemeneti
Az Azure Machine Learning studio kötegelt végrehajtási webes szolgáltatások futtatása minden olyan munkafolyamatok, például R vagy Python parancsfájlok, előfordulhat, hogy a bemenetet nem igénylő használható. Vagy előfordulhat, hogy konfigurálni a kísérlet egy olvasó modul, amely nem biztosít semmilyen GlobalParameters. Ebben az esetben a AzureMLBatchExecution tevékenység úgy lesz beállítva, a következő:

```JSON
{
    "name": "scoring service",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "myBlob"
        }
    ],
    "typeProperties": {
        "webServiceOutputs": {
            "output1": "myBlob"
        }
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-does-not-require-an-inputoutput"></a>Webszolgáltatás nem szükséges egy bemeneti/kimeneti
Előfordulhat, hogy az Azure Machine Learning studio kötegelt végrehajtási webszolgáltatás nincs konfigurálva webszolgáltatás kimenetet. Ebben a példában nem a webszolgáltatás bemeneti vagy kimeneti, sem bármely GlobalParameters vannak konfigurálva. Továbbra is fennáll a következőn: maga a tevékenység kimenetét, de ez nem egy webServiceOutput van megadva.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "placeholderOutputDataset"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Webes szolgáltatás használ olvasók és írók, és a tevékenység-végrehajtás csak akkor, ha a többi tevékenység sikeres volt
Az Azure Machine Learning studio webes szolgáltatás írási és olvasási szerepkörökhöz modulok futtatását, vagy bármely GlobalParameters anélkül is konfigurálhatók. Azonban előfordulhat, hogy beágyazni kívánt szolgáltatás hívása egy adott folyamat által használt adatkészlet-függőségek meghívni a szolgáltatást, csak akkor, amikor egy felsőbb rétegbeli feldolgozása befejeződött. Más műveletet is indíthat a kötegelt végrehajtás ezzel a megközelítéssel befejezése után. Ebben az esetben fejezhető ki a tevékenység bemeneti és kimeneti, használatával anélkül, hogy ezek közül bármelyik, a webszolgáltatás bemeneti vagy kimeneti elnevezési függőségeket.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "inputs": [
        {
            "name": "upstreamData1"
        },
        {
            "name": "upstreamData2"
        }
    ],
    "outputs": [
        {
            "name": "downstreamData"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

A **takeaways** vannak:

* Ha a kísérleti végpont egy webServiceInput használ: egy blob-adatkészlet képviseli, és a szerepel a tevékenység bemeneti és a webServiceInput tulajdonság. Ellenkező esetben a webServiceInput tulajdonság nincs megadva.
* Ha a kísérleti végpont webServiceOutput(s) használ: blob-adatkészletek képviseli, és a szerepelnek a tevékenység kimeneteiből és webServiceOutputs tulajdonságában. A tevékenység kimenete és webServiceOutputs vannak leképezve a kísérletben egyes kimeneti nevével kiegészítve. Ellenkező esetben a webServiceOutputs tulajdonság nincs megadva.
* A kísérlet végpont globalParameter(s) tesz közzé, ha azok szerepelnek a tevékenység globalParameters tulajdonság esetén érték-párokként. Ellenkező esetben a globalParameters tulajdonság nincs megadva. A kulcsokat a kis-és nagybetűket. [Az Azure Data Factory-függvények](data-factory-functions-variables.md) is használhatnak az értékeket.
* További adatkészletek szerepelni fog a tevékenység bemeneti és kimeneti tulajdonságait, anélkül, hogy a tevékenység typeProperties hivatkozik rá. Ezek az adatkészletek végrehajtási szelet-függőségek használatával szabályozhatja, de egyébként figyelmen kívül hagyják a AzureMLBatchExecution tevékenység.


## <a name="updating-models-using-update-resource-activity"></a>Frissítési modellek használata az Update-Erőforrástevékenység
Miután elkészült, az átképezési, a pontozási webszolgáltatás (prediktív kísérletté webszolgáltatásként közzétéve) az újonnan betanított modell használatával módosítsa a **Erőforrástevékenység frissítése az Azure Machine Learning studio**. Lásd: [frissítési modellek használata az Update-Erőforrástevékenység](data-factory-azure-ml-update-resource-activity.md) részleteivel.

### <a name="reader-and-writer-modules"></a>Olvasó és író modul
Egy általános forgatókönyv a webszolgáltatás-paraméterek használata Azure SQL-olvasók és írók. Adatok betöltése az Azure Machine Learning Studio kívüli adatkezelési szolgáltatások kísérlet az olvasó modul szolgál. Az író modul, a kísérletek adatainak mentése az Azure Machine Learning Studio kívüli adatkezelési szolgáltatások.

További információk az Azure Blob/Azure-beli SQL író vagy olvasó: [olvasó](https://msdn.microsoft.com/library/azure/dn905997.aspx) és [író](https://msdn.microsoft.com/library/azure/dn905984.aspx) témakörök az MSDN könyvtárában. A példa az előző szakaszban használt az Azure Blob-olvasó és az Azure Blob-író. Ez a szakasz bemutatja az Azure SQL-olvasó és az Azure SQL-író segítségével.

## <a name="frequently-asked-questions"></a>Gyakori kérdések
**KÉRDÉS:** A big data-adatcsatornákat által előállított több fájl van. A AzureMLBatchExecution tevékenység segítségével dolgozhat a fájlokon?

**VÁLASZ:** Igen. Tekintse meg a **egy olvasó modul segítségével adatokat olvasni az Azure-Blobban található több fájl** című szakasz részletezi.

## <a name="azure-machine-learning-studio-batch-scoring-activity"></a>Az Azure Machine Learning studio kötegelt pontozási tevékenység
Ha használja a **AzureMLBatchScoring** tevékenység, amely integrálható az Azure Machine Learning, azt javasoljuk, hogy használja-e a legújabb **AzureMLBatchExecution** tevékenység.

A AzureMLBatchExecution tevékenység bemutatott Azure SDK-t és az Azure PowerShell 2015 augusztus kiadásában.

Ha azt szeretné, hogy tovább használhassa a AzureMLBatchScoring tevékenység, továbbra is ez a szakasz elolvasása.

### <a name="azure-machine-learning-studio-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Az Azure Machine Learning studio az Azure Storage szolgáltatást a bemeneti és kimeneti kötegelt pontozási tevékenység

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchScoring",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "ScoringInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "ScoringResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

### <a name="web-service-parameters"></a>Webszolgáltatás-paraméterek
Adja meg a webszolgáltatás-paraméterek értékeit, adjon hozzá egy **typeProperties** részt a **AzureMLBatchScoringActivity** című rész a folyamat JSON-Fájljában, az alábbi példában látható módon:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
Is [Data Factory-függvények](data-factory-functions-variables.md) paramétereket lévő értékek átadja a webes szolgáltatás, az alábbi példában látható módon:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> A webszolgáltatás-paraméterek kis-és nagybetűket, ezért győződjön meg arról, hogy a nevét adja meg, ha a tevékenység a JSON egyeznek azokkal a webszolgáltatás által elérhetővé tett.
>
>

## <a name="see-also"></a>Lásd még:
* [Azure blog bejegyzésében: Ismerkedés az Azure Data Factory és az Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/

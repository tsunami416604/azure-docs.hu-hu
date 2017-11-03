---
title: "Hozzon létre Azure Data Factory használatával prediktív adatok folyamatok |} Microsoft Docs"
description: "Ismerteti, hogyan hozzon létre Azure Data Factory és az Azure Machine Learning a prediktív folyamatok létrehozása"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 4fad8445-4e96-4ce0-aa23-9b88e5ec1965
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 3169584bc884107ccd34b01264683d8c73c0fecb
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Hozzon létre prediktív folyamatok Azure Machine Learning és az Azure Data Factory használatával

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive-tevékenység](data-factory-hive-activity.md) 
> * [A Pig-tevékenység](data-factory-pig-activity.md)
> * [MapReduce művelethez](data-factory-map-reduce.md)
> * [Hadoop Streamelési tevékenységben](data-factory-hadoop-streaming-activity.md)
> * [A Spark-tevékenység](data-factory-spark.md)
> * [Machine Learning kötegelt végrehajtási tevékenység](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning Update-erőforrástevékenység](data-factory-azure-ml-update-resource-activity.md)
> * [Tárolt eljárási tevékenység](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-tevékenység](data-factory-usql-activity.md)
> * [.NET egyéni tevékenység](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Bevezetés
> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [gépi tanulás használatával a Data Factory 2-es adatok](../transform-data-using-machine-learning.md).


### <a name="azure-machine-learning"></a>Azure Machine Learning
[Az Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) lehetővé teszi létrehozása, tesztelése és telepítése a prediktív elemzési megoldások. Magas szintű szempontból elkészült a három lépést:

1. **Hozzon létre egy tanítási kísérletet**. Ebben a lépésben az Azure ML Studio úgy teheti meg. Az ML studio olyan együttműködési Látványelem-fejlesztési környezet, amelyekkel betanítása és tesztelése egy prediktív elemzési modell betanítási adatok használatával.
2. **Alakítsa át egy prediktív kísérletté**. Miután a modell még betanítva meglévő adatokkal, és készen áll az új adatok pontozása céljából, előkészítése, és a kísérlet pontozó egyszerűsítésére.
3. **A webszolgáltatás üzembe**. A pontozási kísérlet közzéteheti Azure webszolgáltatásként. Adatokat küldeni a modell a webes szolgáltatás végpontját keresztül, és fogadni eredmény előrejelzéseket eredete a modell.  

### <a name="azure-data-factory"></a>Azure Data Factory
A Data Factory egy felhőalapú adatintegrációs szolgáltatás, amellyel előkészíthető és automatizálható az adatok **továbbítása** és **átalakítása**. Adatok integrációs megoldásokat Azure Data Factory használatával különböző adattárolókhoz származó adatok, átalakító/folyamat az adatokat, és az eredmény adatokat közzé az adattároló hozhat létre.

A Data Factoryval az adatok továbbítására és átalakítására szolgáló adatfolyamatokat hozhat létre, majd ütemezés szerint futtathatja a folyamatot (óránként, napi, heti stb. gyakorisággal). Ezenkívül látványos vizualizációkkal jelenítheti meg az adatfolyamatok közötti leszármaztatási és függőségi kapcsolatokat, valamint egyetlen, egységesített nézetben figyelheti az összes folyamatot, így egyszerűen kiszűrheti a problémákat és beállíthatja a figyelési riasztásokat.

Lásd: [Bevezetés az Azure Data Factory](data-factory-introduction.md) és [felépítheti első folyamatát](data-factory-build-your-first-pipeline.md) cikkeket, ha gyorsan az Azure Data Factory szolgáltatásban.

### <a name="data-factory-and-machine-learning-together"></a>Adat-előállító és a gépi tanulás együtt
Az Azure Data Factory lehetővé teszi, hogy könnyen létrehozhat egy közzétett használó folyamatok [Azure Machine Learning] [ azure-machine-learning] webszolgáltatás prediktív elemzéséhez. Használja a **kötegelt végrehajtási tevékenység** egy Azure Data Factory-folyamat a hívhat meg az Azure gépi tanulás webszolgáltatás számára a előrejelzéseket készítsen a kötegben lévő adatokat. Lásd: [meghívása az Azure gépi tanulás webszolgáltatás a kötegelt végrehajtási tevékenység](#invoking-an-azure-ml-web-service-using-the-batch-execution-activity) című szakaszban talál információt.

Az Azure ml kísérletek pontozási prediktív modelleket idővel kell kell retrained új bemeneti adatkészletek használata. A Data Factory-folyamat az Azure ML modellje is működik, az alábbi lépések végrehajtásával:

1. Tegye közzé a tanítási kísérletet (nem prediktív kísérletté) webszolgáltatásként. Az előző példában a webszolgáltatásként teszi közzé a prediktív kísérletté hasonló módon teheti meg ebben a lépésben az Azure ML Studio.
2. Az Azure ML kötegelt végrehajtási tevékenység segítségével meghívni a webszolgáltatás a tanítási kísérletet. Alapvetően használhatja az Azure ML kötegelt végrehajtási tevékenység képzési webszolgáltatás és a pontozási webszolgáltatás meghívására.

Miután elkészült, az átképezési, frissítése a pontozási webszolgáltatás (prediktív kísérletté webszolgáltatásként kitett) újonnan betanított modell használatával a **Azure ML Update Erőforrástevékenység**. Lásd: [modellek használata az Update-Erőforrástevékenység frissítése](data-factory-azure-ml-update-resource-activity.md) cikkben alább.

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Meghívja a webszolgáltatást kötegelt végrehajtási tevékenység
Azure Data Factory használatával levezényelni adatmozgatást és a feldolgozás, és hajtsa végre az Azure Machine Learning segítségével kötegelt végrehajtáshoz. A legfelső szintű lépések a következők:

1. Hozzon létre egy Azure Machine Learning társított szolgáltatást. A következő értékek lesz szüksége:

   1. **A kérelmi URI** a kötegelt végrehajtás API számára. A kérelem URI-CÍMÉN található kattintva a **KÖTEGELT végrehajtási** hivatkozás a szolgáltatások weblapon.
   2. **Az API-kulcs** a közzétett Azure Machine Learning webszolgáltatás. Az API-kulcsot a webszolgáltatás, amelyet a közzétett kattintva találja.
   3. Használja a **AzureMLBatchExecution** tevékenység.

      ![Machine Learning irányítópult](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Kötegelt URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Forgatókönyv: Kísérletek Web service bemenetek/kimenetek adataira az Azure Blob Storage használata
Ebben a forgatókönyvben az Azure Machine Learning Web service lehetővé teszi az adatok segítségével az Azure blob Storage-fájlból való előrejelzéseket és az előrejelzés eredményt a blob Storage tárolóban. A következő JSON határozza meg a Data Factory-folyamathoz egy AzureMLBatchExecution tevékenységet. A tevékenység rendelkezik az adatkészlet **DecisionTreeInputBlob** bemenetként és **DecisionTreeResultBlob** kimeneteként. A **DecisionTreeInputBlob** átadása a webszolgáltatás által bemeneteként használja a **típus** JSON tulajdonság. A **DecisionTreeResultBlob** objektumnak átadott kimenetként a webszolgáltatás által használ a **webServiceOutputs** JSON tulajdonság.  

> [!IMPORTANT]
> Ha a webszolgáltatás több bemeneti vesz igénybe, használja a **webServiceInputs** tulajdonság használata helyett **típus**. Tekintse meg a [webszolgáltatás több bemeneti értéket igényel](#web-service-requires-multiple-inputs) szakasz egy példát a webServiceInputs tulajdonság használatával.
>
> Által hivatkozott adatkészletek a **típus**/**webServiceInputs** és **webServiceOutputs** tulajdonságok (a  **typeProperties**) is szerepelnie kell a tevékenység **bemenetek** és **kimenete**.
>
> Az Azure ML kísérletben webszolgáltatás bemenetét és a kimeneti portok és a globális paraméterek alapértelmezett neve lehet ("input1", "input2"), amely testre szabható. WebServiceInputs, webServiceOutputs és globalParameters beállítások használt neveket pontosan egyeznie kell a kísérleti nevét. A minta-kérések forgalma a a várt leképezés ellenőrzése az Azure ML végpont kötegelt végrehajtási súgó lapon tekintheti meg.
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
> Csak be- és kimenetekkel a AzureMLBatchExecution tevékenység argumentumként átadhatók paraméterek a webszolgáltatással. Például a fenti JSON-részlet DecisionTreeInputBlob a AzureMLBatchExecution tevékenység, amelyet a rendszer továbbad a webszolgáltatás bemeneteként típus paraméteren keresztül bemenete.   
>
>

### <a name="example"></a>Példa
Ebben a példában a bemeneti és kimeneti adatok tárolásához Azure tárolást használ.

Javasoljuk, hogy olvassa végig a [felépítheti első folyamatát Data Factory] [ adf-build-1st-pipeline] oktatóanyag, mielőtt továbblépne ebben a példában keresztül. A Data Factory Editor használja ebben a példában az adat-előállító összetevők (társított szolgáltatások, adatkészleteket, adatcsatorna) létrehozásához.   

1. Hozzon létre egy **társított szolgáltatás** a a **Azure Storage**. Ha a bemeneti és kimeneti fájlok eltérő tárfiókokból, két összekapcsolt szolgáltatások szüksége. Íme egy JSON-példa:

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
2. Hozzon létre a **bemeneti** Azure Data Factory **dataset**. Néhány egyéb adat-előállító adatkészletek ellentétben ezek az adatkészletek tartalmaznia kell mindkét **folderPath** és **Fájlnév** értékeket. Particionálás segítségével minden egyes kötegelt végrehajtási (minden adatszelet) feldolgozni vagy tud létrehozni egyedi bemeneti és kimeneti fájlok miatt. Szükség lehet néhány felsőbb szintű tevékenység bemeneti átalakítása a CSV fájlformátum, és naplózza azt a tárfiókot az egyes szeletek tartalmazza. Ebben az esetben, nem tartalmazhat a **külső** és **externalData** látható a következő példa, és a DecisionTreeInputBlob beállításait egy másik tevékenység kimeneti adatkészlet lenne.

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

    A bemeneti csv-fájlban a oszlop fejlécsor kell rendelkeznie. Ha használja a **másolási tevékenység** létrehozása/áthelyezése a fürt megosztott kötetei szolgáltatás a blob-tárolóba, célszerű a fogadó tulajdonság **blobWriterAddHeader** a **igaz**. Példa:

    ```JSON
    sink:
    {
        "type": "BlobSink",     
        "blobWriterAddHeader": true
    }
    ```

    A csv-fájl nem rendelkezik a fejlécsor, jelenhet meg a következő hibával: **hiba a tevékenység: Hiba történt a karakterlánc olvasásakor. Váratlan lexikális elem: StartObject. Elérési út ", 1. sor, 1 elhelyezése**.
3. Hozzon létre a **kimeneti** Azure Data Factory **dataset**. A példa particionálás minden szelet végrehajtásra egyedi kimeneti elérési utat hoz létre. A particionálás nélkül a tevékenység felülírná a fájlt.

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
4. Hozzon létre egy **társított szolgáltatás** típusú: **AzureMLLinkedService**, az API-kulcsot biztosító, és a modell a kötegelt végrehajtás URL-cím.

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
5. Végezetül szerzői a folyamat, amely tartalmazza egy **AzureMLBatchExecution** tevékenység. Futásidőben a folyamat a következő lépéseket végzi el:

   1. A bemeneti fájl helyét lekérése a bemeneti adatkészletek.
   2. Meghívja az Azure Machine Learning kötegelt végrehajtási API
   3. A kötegelt végrehajtás kimenetének másolása a kimeneti adatkészlet megadott blob.

      > [!NOTE]
      > AzureMLBatchExecution tevékenység állhat nulla vagy több be- és egy vagy több kimenetekkel.
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

      Mindkét **start** és **end** időpontok szerepelnie kell [ISO formátum](http://en.wikipedia.org/wiki/ISO_8601). Például: 2014-10-14T16:32:41Z. A **end** idő megadása nem kötelező. Ha nem ad meg értéket a **end** tulajdonságot, akkor a program "**kezdés + 48 óra.**" A folyamat határozatlan ideig történő futtatásához adja meg a **9999-09-09** értéket az **end** (befejezés) tulajdonsághoz. A JSON-tulajdonságokkal kapcsolatos információkért lásd: [JSON Scripting Reference](https://msdn.microsoft.com/library/dn835050.aspx) (Referencia a JSON-parancsprogramokhoz).

      > [!NOTE]
      > Adja meg a AzureMLBatchExecution a megadott tevékenység nem kötelező megadni.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Forgatókönyv: Kísérleteket, tekintse meg a különböző tárolók adatainak olvasási/írási modulok használata
Azure ML kísérletek létrehozásakor egy másik gyakori forgatókönyv, ha írási és olvasási szerepkörökhöz modulok használják. Az olvasó modul használatával adatok betöltése az a kísérlet, és a író modul az adatok mentése a kísérletekből. Írási és olvasási szerepkörökhöz modullal kapcsolatos részletekért lásd: [olvasó](https://msdn.microsoft.com/library/azure/dn905997.aspx) és [író](https://msdn.microsoft.com/library/azure/dn905984.aspx) témakörök MSDN könyvtárában.     

Az írási és olvasási szerepkörökhöz modulok használata esetén ajánlott használni egy webszolgáltatási paraméter olvasási/írási modulok mindegyik tulajdonság. Ezek a paraméterek lehetővé teszik a adja meg a beállításokat futásidőben. Például egy olvasó modul, amely használja az Azure SQL Database segítségével létrehozhat egy kísérlet: XXX.database.windows.net. A webszolgáltatás telepítése után később engedélyezni kívánja a fogyasztók webszolgáltatás egy másik Azure SQL Server YYY.database.windows.net nevű megadásához. A webszolgáltatási paraméter segítségével engedélyezze ezt az értéket be kell állítani.

> [!NOTE]
> Webes szolgáltatás bemeneti és kimeneti eltérnek a webszolgáltatás-paramétereket. Az első esetben láthatta, hogyan egy bemeneti és kimeneti adható meg az Azure gépi tanulás webszolgáltatás. Ebben a forgatókönyvben a paraméternek az adott webszolgáltatás, amely megfelel az olvasási/írási modulok tulajdonságai át.
>
>

Vizsgáljuk meg egy olyan helyzetben használhat webszolgáltatás-paramétereket. Egy telepített Azure Machine Learning webszolgáltatás, amelyet használ egy olvasó modul adatokat olvasni az adatforrásokat, Azure Machine Learning által támogatott egyik rendelkezik (például: Azure SQL Database). A kötegelt végrehajtás hajtja végre, az eredmények írt író modul (az Azure SQL Database) használatával.  Nincs a webszolgáltatás bemenetei és kimenetei a kísérletek vannak definiálva. Ebben az esetben ajánlott úgy beállítani, hogy az írási és olvasási szerepkörökhöz modulok vonatkozó webszolgáltatás-paramétereket. Ez a konfiguráció lehetővé teszi, hogy az olvasási/írási modulok kell konfigurálni a AzureMLBatchExecution tevékenység használatakor. Azt adja meg a webszolgáltatás-paramétereket a **globalParameters** a tevékenység JSON szakasz az alábbiak szerint.

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

Is [Data Factory funkciók](data-factory-functions-variables.md) paraméterek szereplő értéket átadja a webes szolgáltatás, a következő példában látható módon:

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> A webszolgáltatás-paramétereket kis-és nagybetűket, ezért figyeljen oda arra, hogy a nevét adja meg, ha a tevékenység JSON egyeznek azokkal a webszolgáltatás által elérhetővé tett tárolókra.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Egy olvasó modul segítségével több fájlból az Azure Blob-adatok olvasása
Big Data típusú adatok folyamatok a tevékenységeket, például a Pig és Hive is létrehozhat egy vagy több kimeneti fájlokat a bővítmények nincsenek. Például egy külső Hive tábla megadása esetén a külső Hive tábla adatai tárolhatja az Azure blob storage a következő név 000000_0 rendelkező. Kísérlet az olvasó modul segítségével több fájlok olvasását, és előrejelzéseket használhatja őket.

Az olvasó modul használata az Azure Machine Learning a kísérlet, a Azure Blob bemenetként is megadhat. A fájlok az Azure blob Storage tárolóban lehetnek a kimeneti fájlok (Példa: 000000_0), amely a HDInsight-on futó Pig és a Hive parancsfájlok hozzák létre. Az olvasó modul lehetővé teszi, hogy olvassa el a (nincs kiterjesztésű) fájlokat úgy konfigurálja a **elérési útját, tároló könyvtár/blob**. A **tároló elérési útja** mutat, a tároló és **könyvtár/blob** mutat, a következő ábrán látható módon a fájlokat tartalmazó mappát. A csillag Ez azt jelenti, hogy \*) **azt jelenti, hogy a tároló/mappában lévő összes fájl (Ez azt jelenti, hogy adatokat/aggregateddata/év 2014/hónap-6 = /\*)** olvassa el a kísérlet során.

![Az Azure Blob tulajdonságai](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Példa
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>A webszolgáltatás-paramétereket AzureMLBatchExecution tevékenység-feldolgozási folyamat

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure ML model with sql azure reader/writer",
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

* A telepített Azure Machine Learning Web service olvasási/írási adatokat az vagy egy Azure SQL-adatbázis egy olvasó és író modul használatával. Ez a webszolgáltatás mutatja meg a következő négy paraméterek: adatbázis-kiszolgáló neve, a adatbázis neve, a kiszolgáló felhasználói fiók nevét és a kiszolgáló felhasználói fiók jelszavát.  
* Mindkét **start** és **end** időpontok szerepelnie kell [ISO formátum](http://en.wikipedia.org/wiki/ISO_8601). Például: 2014-10-14T16:32:41Z. A **end** idő megadása nem kötelező. Ha nem ad meg értéket a **end** tulajdonságot, akkor a program "**kezdés + 48 óra.**" A folyamat határozatlan ideig történő futtatásához adja meg a **9999-09-09** értéket az **end** (befejezés) tulajdonsághoz. A JSON-tulajdonságokkal kapcsolatos információkért lásd: [JSON Scripting Reference](https://msdn.microsoft.com/library/dn835050.aspx) (Referencia a JSON-parancsprogramokhoz).

### <a name="other-scenarios"></a>Egyéb forgatókönyvek
#### <a name="web-service-requires-multiple-inputs"></a>Webszolgáltatás több bemeneti értéket igényel
Ha a webszolgáltatás több bemeneti vesz igénybe, használja a **webServiceInputs** tulajdonság használata helyett **típus**. Által hivatkozott adatkészletek a **webServiceInputs** is szerepelnie kell a tevékenység **bemenetek**.

Az Azure ML kísérletben webszolgáltatás bemenetét és a kimeneti portok és a globális paraméterek alapértelmezett neve lehet ("input1", "input2"), amely testre szabható. WebServiceInputs, webServiceOutputs és globalParameters beállítások használt neveket pontosan egyeznie kell a kísérleti nevét. A minta-kérések forgalma a a várt leképezés ellenőrzése az Azure ML végpont kötegelt végrehajtási súgó lapon tekintheti meg.

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

#### <a name="web-service-does-not-require-an-input"></a>Webszolgáltatás nem szükséges bemeneti
Az Azure ML kötegelt végrehajtási webszolgáltatások segítségével futtassa a munkafolyamatokat, például R vagy Python parancsfájlok, amelyekhez nem szükséges a bemeneti adatok. Vagy a kísérlet az olvasó modul, amely nem fedi fel minden GlobalParameters is megadhatók. Ebben az esetben a AzureMLBatchExecution tevékenység úgy lesz beállítva, az alábbiak szerint:

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
Az Azure ML kötegelt végrehajtási webszolgáltatás esetleg nincs konfigurálva webszolgáltatás kimenetet. Ebben a példában nincs webszolgáltatás bemeneti vagy kimeneti, sem bármely GlobalParameters vannak konfigurálva. Még nincs konfigurálva a tevékenység, maga a kimenettel, de ez nem egy webServiceOutput van megadva.

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

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Webes szolgáltatás által használt olvasók és írók, és csak akkor, ha más tevékenységek sikeres volt a tevékenység fut
Az Azure ML web service írási és olvasási szerepkörökhöz modulok vagy bármely GlobalParameters anélkül futtatásához is megadhatók. Azonban érdemes lehet hívások beágyazása a folyamat által használt adatkészlet függőségek meghívni a szolgáltatás csak akkor, ha egy fölérendelt feldolgozása befejeződött. Más műveleteket is el lehet indítani, a kötegelt végrehajtás ezzel a megközelítéssel befejezése után. Ebben az esetben is express nélkül elnevezési valamelyiket, a webszolgáltatás bemeneti vagy kimeneti tevékenység bemenetekhez és kimenetekhez, használja a függőségeket.

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

* Ha a kísérlet végpont egy típus használ: egy blob-adathalmazra képviseli, és a tevékenység bemeneti és a típus tulajdonság tartalmazza. A típus tulajdonság nincs megadva.
* Ha a kísérlet végpont használ webServiceOutput(s): blob adatkészletek képviseli, és tartalmazza a tevékenység kimeneteiből és webServiceOutputs tulajdonságában. A tevékenység kimenete és webServiceOutputs vannak leképezve a kísérletben minden kimeneti nevével. Ellenkező esetben a webServiceOutputs tulajdonság nincs megadva.
* Ha a kísérlet végpont globalParameter(s) azt mutatja, a számukra tevékenység globalParameters tulajdonságában kulcs, érték párként. Ellenkező esetben a globalParameters tulajdonság nincs megadva. A kulcsokban kis-és nagybetűket. [Az Azure Data Factory funkciók](data-factory-functions-variables.md) értékek használható.
* További adathalmazokat szerepelni fog a tevékenység bemenetei és kimenetei tulajdonságait, anélkül, hogy a tevékenység typeProperties hivatkozik rá. Ezek az adatkészletek szelet függőségek végrehajtását szabályozására, de a AzureMLBatchExecution tevékenység ellenkező esetben figyelmen kívül hagyja.


## <a name="updating-models-using-update-resource-activity"></a>A modellek használata az Update-Erőforrástevékenység frissítése
Miután elkészült, az átképezési, frissítése a pontozási webszolgáltatás (prediktív kísérletté webszolgáltatásként kitett) újonnan betanított modell használatával a **Azure ML Update Erőforrástevékenység**. Lásd: [modellek használata az Update-Erőforrástevékenység frissítése](data-factory-azure-ml-update-resource-activity.md) cikkben alább.

### <a name="reader-and-writer-modules"></a>Olvasási és írási modulok
Egy általános forgatókönyv webszolgáltatás-paramétereket az Azure SQL-olvasók és írók. Az olvasó modul az adatok betöltése az Azure Machine Learning Studio kívül adatszolgáltatásokból felügyeleti kísérlet szolgál. Az író modul az adatok mentése a kísérletekből az Azure Machine Learning Studio kívüli adatok szolgáltatások.  

Azure Blob vagy az Azure SQL olvasási/írási kapcsolatos részletekért lásd: [olvasó](https://msdn.microsoft.com/library/azure/dn905997.aspx) és [író](https://msdn.microsoft.com/library/azure/dn905984.aspx) témakörök MSDN könyvtárában. A példa az előző szakaszban használt Azure-Blob és az Azure Blob olvasási. Ez a szakasz ismerteti az Azure SQL-olvasó és az Azure SQL-író segítségével.

## <a name="frequently-asked-questions"></a>Gyakori kérdések
**K:** a big Data típusú adatok folyamatok által létrehozott több fájl van. A AzureMLBatchExecution tevékenység használatával a fájlok?

**V:** Igen. Tekintse meg a **egy olvasó modul segítségével adatokat olvasni az Azure Blob több fájl** című szakaszban talál információt.

## <a name="azure-ml-batch-scoring-activity"></a>Azure ML kötegelt pontozási tevékenység
Ha használja a **AzureMLBatchScoring** tevékenység integrálása az Azure Machine Learning, azt javasoljuk, hogy használja-e a legújabb **AzureMLBatchExecution** tevékenység.

A AzureMLBatchExecution tevékenység megjelent az Azure SDK és Azure PowerShell 2015. augusztus kiadásában.

Ha azt szeretné, hogy tovább használja a AzureMLBatchScoring tevékenység, továbbra is az egész ebben a szakaszban.  

### <a name="azure-ml-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Bemeneti/kimeneti az Azure Storage használata az Azure ML kötegelt pontozási tevékenység

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

### <a name="web-service-parameters"></a>Webszolgáltatás-paramétereket
Adható meg érték a webszolgáltatás-paramétereket, vegye fel a **typeProperties** szakaszban a **AzureMLBatchScoringActivty** az adatcsatorna JSON szakasz a következő példában látható módon:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
Is [Data Factory funkciók](data-factory-functions-variables.md) paraméterek szereplő értéket átadja a webes szolgáltatás, a következő példában látható módon:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> A webszolgáltatás-paramétereket kis-és nagybetűket, ezért figyeljen oda arra, hogy a nevét adja meg, ha a tevékenység JSON egyeznek azokkal a webszolgáltatás által elérhetővé tett tárolókra.
>
>

## <a name="see-also"></a>Lásd még:
* [Az Azure blogbejegyzést: Ismerkedés az Azure Data Factory és az Azure gépi tanulás](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/

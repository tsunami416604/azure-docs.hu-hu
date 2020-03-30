---
title: Prediktív adatfolyamatok létrehozása az Azure Data Factory használatával
description: Bemutatja, hogyan hozhat létre prediktív folyamatokat az Azure Data Factory és az Azure Machine Learning használatával
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: eba5df587d6bd6dda6083314cfb94836c6669393
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73683146"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Prediktív folyamatok létrehozása az Azure Machine Learning és az Azure Data Factory használatával

> [!div class="op_single_selector" title1="Átalakítási tevékenységek"]
> * [Hive-tevékenység](data-factory-hive-activity.md)
> * [Sertés tevékenység](data-factory-pig-activity.md)
> * [MapReduce tevékenység](data-factory-map-reduce.md)
> * [Hadoop streaming tevékenység](data-factory-hadoop-streaming-activity.md)
> * [Szikratevékenység](data-factory-spark.md)
> * [Machine Learning kötegelt végrehajtási tevékenység](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning Update-erőforrástevékenység](data-factory-azure-ml-update-resource-activity.md)
> * [Tárolt eljárási tevékenység](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-tevékenység](data-factory-usql-activity.md)
> * [.NET egyéni tevékenység](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Bevezetés
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [adatok átalakítása gépi tanulással a Data Factory alkalmazásban című témakört.](../transform-data-using-machine-learning.md)


### <a name="azure-machine-learning"></a>Azure Machine Learning
[Az Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) lehetővé teszi, hogy prediktív elemzési megoldásokat hozzon létre, teszteljen és telepítsen. Magas szintű szempontból ez három lépésben történik:

1. **Hozzon létre egy betanítási kísérletet.** Ezt a lépést az Azure Machine Learning-stúdió használatával. Az Azure Machine Learning studio egy együttműködésen alapuló vizuális fejlesztői környezet, amely segítségével betaníthatja és tesztelheti a prediktív elemzési modellt a betanítási adatok használatával.
2. **Alakítsa át prediktív kísérletté.** Miután a modell betanítása a meglévő adatokkal, és készen áll arra, hogy használja az új adatok pontozására, előkészíti és racionalizálja a kísérletet a pontozáshoz.
3. **Telepítse webszolgáltatásként.** Közzéteheti a pontozási kísérlet egy Azure-webszolgáltatás. Adatokat küldhet a modellnek ezen a webszolgáltatás végpontján keresztül, és a modell bekövetkező eredmény-előrejelzéseket fogadhat.

### <a name="azure-data-factory"></a>Azure Data Factory
A Data Factory egy felhőalapú adatintegrációs szolgáltatás, amely vezényli és automatizálja az adatok **mozgását** és **átalakítását.** Az Azure Data Factory használatával adatintegrációs megoldásokat hozhat létre, amelyek különböző adattárakból származó adatokat képesek beadni, átalakítani/feldolgozni az adatokat, és közzétenni az eredményadatokat az adattárakban.

A Data Factoryval az adatok továbbítására és átalakítására szolgáló adatfolyamatokat hozhat létre, majd ütemezés szerint futtathatja a folyamatot (óránként, napi, heti stb. gyakorisággal). Ezenkívül látványos vizualizációkkal jelenítheti meg az adatfolyamatok közötti leszármaztatási és függőségi kapcsolatokat, valamint egyetlen, egységesített nézetben figyelheti az összes folyamatot, így egyszerűen kiszűrheti a problémákat és beállíthatja a figyelési riasztásokat.

Az Azure Data Factory szolgáltatás gyors megkezdéséhez tekintse meg az [Azure Data Factory – bevezetés](data-factory-introduction.md) és az első [folyamatcikkek összeállítása](data-factory-build-your-first-pipeline.md) című témakört.

### <a name="data-factory-and-machine-learning-together"></a>A Data Factory és a Machine Learning együtt
Az Azure Data Factory lehetővé teszi, hogy könnyedén hozzon létre folyamatokat, amelyek egy közzétett [Azure Machine Learning][azure-machine-learning] webszolgáltatást használnak a prediktív elemzéshez. A **batch-végrehajtási tevékenység** egy Azure Data Factory-folyamat használatával meghívhat egy Azure Machine Learning-stúdió webszolgáltatás, hogy előrejelzéseket az adatok kötegelt. A részletekért tekintse meg az Azure Machine Learning-stúdió webszolgáltatásának meghívása a Batch-végrehajtási tevékenység szakasz használatával című témakört.

Idővel az Azure Machine Learning-stúdió pontozási kísérleteit az Azure Machine Learning-stúdió ban lévő prediktív modelleket újra kell képezni az új bemeneti adatkészletek használatával. Az Azure Machine Learning-stúdió modelljét újra betaníthatja egy Data Factory-folyamatból az alábbi lépésekkel:

1. Tegye közzé a betanítási kísérletet (nem prediktív kísérlet) webszolgáltatásként. Ezt a lépést az Azure Machine Learning-stúdióban is úgy teheti, hogy az előző forgatókönyvben webszolgáltatásként elérhetővé tegye a prediktív kísérletet.
2. Használja az Azure Machine Learning studio batch-végrehajtási tevékenység meghívásához a webes szolgáltatás a betanítási kísérlet. Alapvetően használhatja az Azure Machine Learning studio batch-végrehajtási tevékenység meghívására egyaránt betanítási webszolgáltatás és pontozási webszolgáltatás.

Miután végzett az átképzés, frissítse a pontozási webszolgáltatás (prediktív kísérlet elérhetővé vált webszolgáltatásként) az újonnan betanított modell segítségével az **Azure Machine Learning studio Update Resource Activity.** A részleteket a [Modellek frissítése az Erőforrás-tevékenység frissítése](data-factory-azure-ml-update-resource-activity.md) című cikkben olvashatja.

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Webes szolgáltatás meghívása kötegelt végrehajtási tevékenység használatával
Az Azure Data Factory segítségével vénítani az adatok mozgását és feldolgozását, majd az Azure Machine Learning használatával kötegelt végrehajtást hajt végre. A legfelső szintű lépések a következők:

1. Hozzon létre egy Azure Machine Learning-alapú szolgáltatást. A következő értékekre van szüksége:

   1. **Uri kérése** a batch-végrehajtási API-hoz. Az URI kérése a webszolgáltatások lapján található **BATCH EXECUTION** hivatkozásra kattintva kereshető.
   2. **API-kulcs** a közzétett Azure Machine Learning webszolgáltatáshoz. Az API-kulcsot a közzétett webszolgáltatásra kattintva találhatja meg.
   3. Használja az **AzureMLBatchExecution** tevékenységet.

      ![Gépi tanulási irányítópult](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Köteg URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Eset: Webszolgáltatás-bemeneteket/-kimeneteket használó kísérletek, amelyek az Azure Blob Storage-ban lévő adatokra hivatkoznak
Ebben a forgatókönyvben az Azure Machine Learning webszolgáltatás előrejelzéseket készít egy fájlból származó adatok használatával egy Azure blob storage-ban, és tárolja az előrejelzési eredményeket a blob storage.In this scenario, the Azure Machine Learning Web service makes predictions using data from a file in an Azure blob storage and stores the prediction results in the blob storage. A következő JSON egy AzureMLBatchExecution tevékenységgel rendelkező Data Factory-folyamatot határoz meg. A tevékenység a **DecisionTreeInputBlob** adatkészletet bemenetként, **a DecisionTreeResultBlob-ot** pedig kimenetként. A **DecisionTreeInputBlob** a **webService Input** JSON tulajdonság használatával a webService bemeneteként kerül átadásra. A **DecisionTreeResultBlob** a **webServiceOutputs** JSON tulajdonság használatával kimenetként kerül átadásra a webservice-szolgáltatásnak.

> [!IMPORTANT]
> Ha a webszolgáltatás több bemenetet is használ, a **webServiceInputtulajdonság** ot használja a **webServiceInput**használata helyett. Lásd: A [webszolgáltatás több bemeneti szakaszt igényel](#web-service-requires-multiple-inputs) a webServiceInputs tulajdonság használatának például.
>
> A **webServiceInputs**/és **webServiceOutputs** tulajdonságok (a **typeProperties**) által hivatkozott adatkészleteket is tartalmaznia kell a **tevékenységbemenetekben** és **-kimenetekben.****webServiceInputs**
>
> Az Azure Machine Learning studió-kísérletben a webszolgáltatás bemeneti és kimeneti portjai és a globális paraméterek alapértelmezett nevekkel ("input1", "input2") rendelkeznek, amelyeket testre szabhat. A webServiceInputs, webServiceOutputs és globalParameters beállításokhoz használt neveknek pontosan meg kell egyezniük a kísérletekben szereplő nevekkel. Megtekintheti a mintakérelem hasznos adatait az Azure Machine Learning studio végpont batch-végrehajtási súgólapján a várt leképezés ellenőrzéséhez.
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
> Csak az AzureMLBatchExecution tevékenység bemenetei és kimenetei adhatók át paraméterekként a webszolgáltatásnak. Például a fenti JSON-kódrészletben a DecisionTreeInputBlob az AzureMLBatchExecution tevékenység bemenete, amely a webServiceInput paraméteren keresztül a webszolgáltatás bemeneteként kerül átadásra.
>
>

### <a name="example"></a>Példa
Ez a példa az Azure Storage-t használja a bemeneti és a kimeneti adatok tárolásához.

Azt javasoljuk, hogy menjen át az [első folyamat létrehozása a Data Factory oktatóanyag,][adf-build-1st-pipeline] mielőtt átmegyünk ebben a példában. A Data Factory Editor segítségével hozzon létre data factory összetevők (csatolt szolgáltatások, adatkészletek, csővezeték) ebben a példában.

1. **Hozzon** létre egy összekapcsolt szolgáltatást az **Azure Storage-hoz.** Ha a bemeneti és kimeneti fájlok különböző tárfiókokban vannak, két összekapcsolt szolgáltatásra van szükség. Íme egy JSON példa:

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
2. Hozza létre a **bemeneti** Azure Data Factory **adatkészletet.** Más Data Factory adatkészletekkel ellentétben ezeknek az adatkészleteknek **mappaPath** és **fileName** értékeket is tartalmazniuk kell. A particionálás segítségével minden kötegvégrehajtást (minden adatszeletet) egyedi bemeneti és kimeneti fájlok feldolgozására vagy létrehozásához használhat. Előfordulhat, hogy a bemeneti bemenet ET CSV fájlformátumba való átalakításához és az egyes szeletek tárfiókba való átalakításához meg kell adnia. Ebben az esetben a következő példában látható **külső** és **externalData-beállításokat** nem szeretné megadni, és a DecisionTreeInputBlob egy másik tevékenység kimeneti adatkészlete lenne.

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

    A bemeneti csv-fájlnak oszlopfejlécsorral kell rendelkeznie. Ha a **másolási tevékenység segítségével** hozza létre/helyezze át a csv-t a blobstorage-ba, állítsa a **sink tulajdonságblobWriterAddHeader** **true**értékre. Példa:

    ```JSON
    sink:
    {
        "type": "BlobSink",
        "blobWriterAddHeader": true
    }
    ```

    Ha a csv-fájlban nem található a fejlécsor, a következő hiba jelenhet meg: Hiba a Tevékenység: Hiba a **karakterlánc olvasásakor. Váratlan token: StartObject. Elérési út', 1.**
3. Hozza létre a **kimeneti** Azure Data Factory **adatkészletet.** Ez a példa particionálással hoz létre egy egyedi kimeneti elérési utat minden szelet végrehajtáshoz. A particionálás nélkül a tevékenység felülírná a fájlt.

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
4. Hozzon létre egy **összekapcsolt szolgáltatás** típusa: **AzureMLLinkedService,** amely az API-kulcs és a modell kötegelt végrehajtási URL-címet.

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
5. Végül egy **AzureMLBatchExecution-tevékenységet** tartalmazó folyamat létrehozása. Futásidőben a folyamat a következő lépéseket hajtja végre:

   1. Leállítja a bemeneti fájl helyét a bemeneti adatkészletekből.
   2. Elindítja az Azure Machine Learning kötegelt végrehajtási API-ját
   3. A kötegelt végrehajtási kimenetet a kimeneti adatkészletben megadott blobba másolja.

      > [!NOTE]
      > Az AzureMLBatchExecution tevékenység nulla vagy több bemenettel és egy vagy több kimenettel rendelkezhet.
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

      Mind **a kezdő,** mind a **záró** dátumnak [ISO formátumúnak](https://en.wikipedia.org/wiki/ISO_8601)kell lennie. Például: 2014-10-14T16:32:41Z. A **befejezési** idő nem kötelező. Ha nem ad meg értéket a **záró** tulajdonsághoz, akkor a program a "**start + 48 óra"** értékként számítja ki. A folyamat határozatlan ideig történő futtatásához adja meg a **9999-09-09** értéket az **end** (befejezés) tulajdonsághoz. A JSON-tulajdonságokkal kapcsolatos információkért lásd: [JSON Scripting Reference](https://msdn.microsoft.com/library/dn835050.aspx) (Referencia a JSON-parancsprogramokhoz).

      > [!NOTE]
      > Az AzureMLBatchExecution tevékenység bemenetének megadása nem kötelező.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Eset: A Reader/Writer modulokat használó kísérletek különböző tárolókban lévő adatokra való hivatkozáshoz
Egy másik gyakori forgatókönyv az Azure Machine Learning studiói kísérletek létrehozásakor a Reader és a Writer modulok használata. Az olvasó modul adatok at tölt be egy kísérletbe, és az író modul az adatok mentése a kísérletekből. Az olvasó- és írómodulokról az MSDN-könyvtár [Olvasó](https://msdn.microsoft.com/library/azure/dn905997.aspx) és [író](https://msdn.microsoft.com/library/azure/dn905984.aspx) témakörei című témakörben olvashat részletesen.

Az olvasó- és írómodulok használatakor célszerű webszolgáltatás-paramétert használni ezen olvasó/író modulok minden egyes tulajdonságához. Ezek a webes paraméterek lehetővé teszik az értékek futásközbeni konfigurálását. Létrehozhat például egy kísérletet egy Olvasómodullal, amely egy Azure SQL Database: XXX.database.windows.net. A webszolgáltatás üzembe helyezése után engedélyezni szeretné, hogy a webszolgáltatás felhasználói egy másik Azure SQL Servert adjanak meg, amelyet YYY.database.windows.net. Az érték konfigurálásának engedélyezéséhez webszolgáltatás-paramétert is használhat.

> [!NOTE]
> A webszolgáltatás bemenete és kimenete eltér a webszolgáltatás paramétereitől. Az első forgatókönyvben azt is láthatta, hogy egy Azure Machine Learning-stúdió webszolgáltatáshoz hogyan adható meg egy bemeneti és kimeneti szolgáltatás. Ebben az esetben adja át az olvasó/író modulok tulajdonságainak megfelelő webszolgáltatás paramétereit.
>
>

Nézzünk meg egy forgatókönyvet a webszolgáltatás paramétereinek használatára. Van egy üzembe helyezett Azure Machine Learning webszolgáltatás, amely egy olvasó modul thasználja az adatok olvasásához az Azure Machine Learning által támogatott adatforrások (például: Azure SQL Database). A kötegelt végrehajtás végrehajtása után az eredmények egy Writer modul (Azure SQL Database) használatával írják.  A kísérletekben nincswebszolgáltatás-bemenet és kimenet definiálva. Ebben az esetben azt javasoljuk, hogy konfigurálja a megfelelő webszolgáltatás-paramétereket az olvasó és író modulokhoz. Ez a konfiguráció lehetővé teszi, hogy az olvasó/író modulok konfigurálása az AzureMLBatchExecution tevékenység használatakor. A JSON tevékenység **globalParameters** szakaszában a webszolgáltatás paramétereit az alábbiak szerint adhatja meg.

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

A Data [Factory függvények](data-factory-functions-variables.md) a webszolgáltatás paramétereinek értékeinek áthárításában is használható, ahogy az a következő példában látható:

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> A webszolgáltatás paraméterei nem felelnek meg a kis- és nagybetűknek, ezért győződjön meg arról, hogy a JSON tevékenységben megadott nevek megegyeznek a webszolgáltatás által elérhetővé tett nevekkel.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Egy Olvasó modul használata több fájl adatainak olvasásához az Azure Blobban
A Pig és a Hive tevékenységekkel rendelkező big data-folyamatok egy vagy több kimeneti fájlt hozhatnak létre bővítmények nélkül. Ha például megad egy külső Hive-táblát, a külső Hive-tábla adatait az Azure blob storage a következő névvel 000000_0 tárolja. Az olvasómodul segítségével több fájlt is elolvashat, és előrejelzésekhez használhatja őket.

Ha az olvasómodult egy Azure Machine Learning-kísérletben használja, megadhatja az Azure Blob ot bemenetként. Az Azure blob storage-ban lévő fájlok lehetnek a kimeneti fájlok (Példa: 000000_0), amelyeket a HDInsight-on futó Pig és Hive parancsfájl oka. Az olvasó modul lehetővé teszi a fájlok olvasását (bővítmények nélkül) a **Tárolóhoz vezető út, könyvtár/blob**konfigurálásával. A tároló elérési útja a **tárolóra** mutat, és a **könyvtár/blob** a fájlokat tartalmazó mappára mutat, ahogy az az alábbi képen látható. A csillag azt adja \* **meg, hogy a tárolóban/mappában lévő összes fájl (azaz az adatok/aggregateddata/year=2014/month-6/\*)** a kísérlet részeként olvasható.

![Az Azure Blob tulajdonságai](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Példa
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Folyamat az AzureMLBatchExecution tevékenységwebszolgáltatás-paraméterekkel

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

A fenti JSON példában:

* A telepített Azure Machine Learning webszolgáltatás egy olvasó t és egy író modult használ az adatok olvasásához/írásához/írásához egy Azure SQL-adatbázisba. Ez a webszolgáltatás a következő négy paramétert teszi elérhetővé: Adatbázis-kiszolgáló neve, Adatbázis neve, Kiszolgálói felhasználói fiók neve és Kiszolgálói felhasználói fiók jelszava.
* Mind **a kezdő,** mind a **záró** dátumnak [ISO formátumúnak](https://en.wikipedia.org/wiki/ISO_8601)kell lennie. Például: 2014-10-14T16:32:41Z. A **befejezési** idő nem kötelező. Ha nem ad meg értéket a **záró** tulajdonsághoz, akkor a program a "**start + 48 óra"** értékként számítja ki. A folyamat határozatlan ideig történő futtatásához adja meg a **9999-09-09** értéket az **end** (befejezés) tulajdonsághoz. A JSON-tulajdonságokkal kapcsolatos információkért lásd: [JSON Scripting Reference](https://msdn.microsoft.com/library/dn835050.aspx) (Referencia a JSON-parancsprogramokhoz).

### <a name="other-scenarios"></a>Egyéb forgatókönyvek
#### <a name="web-service-requires-multiple-inputs"></a>A webszolgáltatás több bemenetet igényel
Ha a webszolgáltatás több bemenetet is használ, a **webServiceInputtulajdonság** ot használja a **webServiceInput**használata helyett. A **webServiceInputs** által hivatkozott adatkészleteket a Tevékenység **bemeneteknek**is tartalmazniuk kell.

Az Azure Machine Learning studió-kísérletben a webszolgáltatás bemeneti és kimeneti portjai és a globális paraméterek alapértelmezett nevekkel ("input1", "input2") rendelkeznek, amelyeket testre szabhat. A webServiceInputs, webServiceOutputs és globalParameters beállításokhoz használt neveknek pontosan meg kell egyezniük a kísérletekben szereplő nevekkel. Megtekintheti a mintakérelem hasznos adatait az Azure Machine Learning studio végpont batch-végrehajtási súgólapján a várt leképezés ellenőrzéséhez.

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

#### <a name="web-service-does-not-require-an-input"></a>A webszolgáltatás nem igényel bemenetet
Az Azure Machine Learning studio kötegelt végrehajtási webszolgáltatások bármilyen munkafolyamat, például R vagy Python-parancsfájlok futtatásához használhatók, amelyek nem igényelnek bemenetet. Vagy lehet, hogy a kísérlet egy Reader modullal van konfigurálva, amely nem teszi elérhetővé a GlobalParameters paramétert. Ebben az esetben az AzureMLBatchExecution tevékenység a következőképpen lesz konfigurálva:

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

#### <a name="web-service-does-not-require-an-inputoutput"></a>A webszolgáltatás nem igényel bemenetet/kimenetet
Előfordulhat, hogy az Azure Machine Learning studio batch-végrehajtási webszolgáltatása nem rendelkezik konfigurált webszolgáltatás-kimenettel. Ebben a példában nincs webszolgáltatás bemenet vagy kimenet, és nincsenek globalparameters konfigurálva. Még mindig van egy kimenet konfigurálva a tevékenység maga, de nem adja meg a webServiceOutput.

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

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>A WebSzolgáltatás olvasókat és írókat használ, és a tevékenység csak akkor fut, ha más tevékenységek sikeresek voltak
Az Azure Machine Learning studio webszolgáltatás-olvasó és írómodulok konfigurálhatók globalparameters használatával vagy anélkül. Előfordulhat azonban, hogy olyan folyamatba szeretne szolgáltatáshívásokat beágyazni, amely adatkészlet-függőségeket használ a szolgáltatás meghívásához, ha néhány upstream feldolgozás befejeződött. Ezzel a módszerrel a kötegelt végrehajtás befejezése után is elindíthat ja. Ebben az esetben a függőségeket tevékenységbemenetek és -kimenetek használatával fejezheti ki anélkül, hogy bármelyiket webszolgáltatás-bemenetként vagy kimenetként nevezné meg.

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

Az **elvihető ételek** a következők:

* Ha a kísérlet végpontja egy webServiceInput: azt egy blob adatkészlet jelöli, és szerepel a tevékenység bemenetek és a webServiceInput tulajdonság. Ellenkező esetben a webServiceInput tulajdonság nem marad meg.
* Ha a kísérlet végpontja webServiceOutput(s) használ: azokat blob adatkészletek képviselik, és a tevékenységkimenetekben és a webServiceOutputs tulajdonságban szerepelnek. A tevékenység kimenetek és webServiceOutputs vannak leképezve a kísérlet egyes kimeneteinek neve. Ellenkező esetben a webServiceOutputs tulajdonság kimarad.
* Ha a kísérlet végpontja elérhetővé teszi a globalParameter(ka)t, azok a tevékenység globalParameters tulajdonságában vannak megadva kulcsként, értékpárokként. Ellenkező esetben a globalParameters tulajdonság kimarad. A kulcsok ban a kis- és nagybetűket nem lehet kiegyeznem. [Az Azure Data Factory-függvények](data-factory-functions-variables.md) használhatók az értékekben.
* További adatkészletek is szerepelhetnek a tevékenység bemenetek és kimenetek tulajdonságai, anélkül, hogy a tevékenység típusaTulajdonságok hivatkozva. Ezek az adatkészletek szabályozzák a végrehajtási szelet függőségek használatával, de egyébként figyelmen kívül hagyja az AzureMLBatchExecution tevékenység.


## <a name="updating-models-using-update-resource-activity"></a>Modellek frissítése az Erőforrás-tevékenység frissítése szolgáltatással
Miután végzett az átképzés, frissítse a pontozási webszolgáltatás (prediktív kísérlet elérhetővé vált webszolgáltatásként) az újonnan betanított modell segítségével az **Azure Machine Learning studio Update Resource Activity.** A részleteket a [Modellek frissítése az Erőforrás-tevékenység frissítése](data-factory-azure-ml-update-resource-activity.md) című cikkben olvashatja.

### <a name="reader-and-writer-modules"></a>Olvasó- és írómodulok
A webszolgáltatás-paraméterek használatának gyakori forgatókönyve az Azure SQL-olvasók és -írók használata. Az olvasómodul segítségével adatokat tölthet be egy kísérletbe az Azure Machine Learning Studio-n kívüli adatkezelési szolgáltatásokból. Az írómodul célja, hogy a kísérletekből származó adatokat az Azure Machine Learning Studio-n kívüli adatkezelési szolgáltatásokba mentse.

Az Azure Blob/Azure SQL-olvasó/-író témakörökben az MSDN-könyvtár [Olvasó-](https://msdn.microsoft.com/library/azure/dn905997.aspx) és [írótémakörei](https://msdn.microsoft.com/library/azure/dn905984.aspx) ben olvashat részletesen. Az előző szakaszban az Azure Blob-olvasót és az Azure Blob-írót használta. Ez a szakasz az Azure SQL-olvasó és az Azure SQL-író használatával ismerteti.

## <a name="frequently-asked-questions"></a>Gyakori kérdések
**K:** Több fájlom van, amelyeket a big data-folyamatok hoznak létre. Használhatom az AzureMLBatchExecution tevékenységet az összes fájlon való munkára?

**V:** Igen. A részletekért tekintse meg az **Olvasó használata modulban az Azure Blob több fájlból származó adatok olvasásához.**

## <a name="azure-machine-learning-studio-batch-scoring-activity"></a>Azure Machine Learning stúdió kötegelt pontozási tevékenység
Ha az **AzureMLBatchScoring tevékenységet** használja az Azure Machine Learningdel való integrációhoz, javasoljuk, hogy a legújabb **AzureMLBatchExecution** tevékenységet használja.

Az AzureMLBatchExecution tevékenység az Azure SDK és az Azure PowerShell 2015 augusztusi kiadásában kerül bevezetésre.

Ha továbbra is szeretné használni az AzureMLBatchScoring tevékenységet, folytassa az olvasást ebben a szakaszban.

### <a name="azure-machine-learning-studio-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Azure Machine Learning studio batch scoring tevékenység az Azure Storage használatával bemeneti/kimeneti

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

### <a name="web-service-parameters"></a>Webszolgáltatás paraméterei
A webszolgáltatás-paraméterek értékeinek megadásához adjon hozzá egy **typeProperties** szakaszt a JSON-folyamat **AzureMLBatchScoringActivity** szakaszához a következő példában látható módon:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
A Data [Factory függvények](data-factory-functions-variables.md) a webszolgáltatás paramétereinek értékeinek áthárításában is használható, ahogy az a következő példában látható:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> A webszolgáltatás paraméterei nem felelnek meg a kis- és nagybetűknek, ezért győződjön meg arról, hogy a JSON tevékenységben megadott nevek megegyeznek a webszolgáltatás által elérhetővé tett nevekkel.
>
>

## <a name="see-also"></a>Lásd még:
* [Azure-blogbejegyzés: Első lépések az Azure Data Factory és az Azure Machine Learning használatával](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: https://azure.microsoft.com/services/machine-learning/

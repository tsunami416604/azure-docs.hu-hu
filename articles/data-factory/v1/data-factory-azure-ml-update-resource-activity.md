---
title: Machine Learning modellek frissítése a Azure Data Factory használatával | Microsoft Docs
description: Ismerteti, hogyan hozhat létre prediktív folyamatokat a Azure Data Factory és a Azure Machine Learning használatával
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
ms.openlocfilehash: a980f269c8b88618ffa3311c05310a88ade379ed
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70140470"
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>Azure Machine Learning modellek frissítése az erőforrás frissítése tevékenység használatával

> [!div class="op_single_selector" title1="Átalakítási tevékenységek"]
> * [Struktúra tevékenysége](data-factory-hive-activity.md) 
> * [Pig-tevékenység](data-factory-pig-activity.md)
> * [MapReduce tevékenység](data-factory-map-reduce.md)
> * [Hadoop streaming-tevékenység](data-factory-hadoop-streaming-activity.md)
> * [Spark-tevékenység](data-factory-spark.md)
> * [Machine Learning kötegelt végrehajtási tevékenység](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning Update-erőforrástevékenység](data-factory-azure-ml-update-resource-activity.md)
> * [Tárolt eljárási tevékenység](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-tevékenység](data-factory-usql-activity.md)
> * [.NET egyéni tevékenység](data-factory-use-custom-activities.md)


> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg a Machine learning-modellek frissítése a Data Factory-ben](../update-machine-learning-models.md)című témakört.

Ez a cikk a fő Azure Data Factory Azure Machine Learning integrációs cikket egészíti ki: [Prediktív folyamatokat hozhat létre Azure Machine learning és Azure Data Factory használatával](data-factory-azure-ml-batch-execution-activity.md). Ha még nem tette meg, tekintse át a fő cikket, mielőtt beolvassa ezt a cikket. 

## <a name="overview"></a>Áttekintés
Idővel az Azure ML-pontozási kísérletek prediktív modelljeit új bemeneti adatkészletek használatával kell áttanítani. Miután végzett az újraképzéssel, frissítenie kell a pontozási webszolgáltatást az áttelepített ML-modellel. Az Azure ML-modellek webszolgáltatásokon keresztül történő átképzésének és frissítésének tipikus lépései a következők:

1. Hozzon létre egy kísérletet az [Azure ml Studioban](https://studio.azureml.net).
2. Ha elégedett a modellel, az Azure ML Studio használatával közzéteheti a webes szolgáltatásokat a betanítási **kísérlet** és a pontozási/**prediktív kísérlet**során.

A következő táblázat ismerteti az ebben a példában használt webszolgáltatásokat.  További részletekért lásd: [Machine learning modellek átképzése programozott](../../machine-learning/machine-learning-retrain-models-programmatically.md) módon.

- A betanítási **webszolgáltatás** betanítási és betanított modelleket hoz létre. Az átképzés kimenete egy. ilearner fájl az Azure Blob Storage-ban. Az **alapértelmezett végpont** automatikusan létrejön, amikor webszolgáltatásként teszi közzé a betanítási kísérletet. Több végpontot is létrehozhat, de a példa csak az alapértelmezett végpontot használja.
- **Pontozási webszolgáltatás** – a rendszer címkézetlen adatpéldákat fogad, és előrejelzéseket készít. Az előrejelzés kimenete különböző formákat tartalmazhat, például egy. csv-fájlt vagy egy Azure SQL-adatbázis sorait a kísérlet konfigurációjától függően. A rendszer automatikusan létrehozza az alapértelmezett végpontot, amikor a prediktív kísérletet webszolgáltatásként teszi közzé. 

Az alábbi ábrán az Azure ML-ben megjelenő tanítási és pontozási végpontok közötti kapcsolat látható.

![Webszolgáltatások](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

A betanítási **webszolgáltatás** az **Azure ml batch végrehajtási tevékenységének**használatával hívható meg. A betanítási webszolgáltatás meghívása ugyanaz, mint egy Azure ML webszolgáltatás (pontozási webszolgáltatás) meghívása az adatpontozási szolgáltatáshoz. Az előző fejezetekben részletesen ismertetjük, hogyan hívhat meg egy Azure ML-webszolgáltatást egy Azure Data Factory folyamatból. 

A **pontozási** webszolgáltatást az **Azure ml Update Resource tevékenység** használatával lehet meghívni a webszolgáltatás az újonnan betanított modellel való frissítéséhez. A következő példák a társított szolgáltatás definícióit biztosítják: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>A pontozási webszolgáltatás egy klasszikus webszolgáltatás
Ha a pontozási webszolgáltatás egy **klasszikus webszolgáltatás**, akkor a Azure Portal használatával hozza létre a második **nem alapértelmezett és frissíthető végpontot** . A lépések a [végpontok létrehozása](../../machine-learning/machine-learning-create-endpoint.md) című cikkben olvashatók. Miután létrehozta a nem alapértelmezett frissíthető végpontot, hajtsa végre a következő lépéseket:

* Kattintson a **Batch-végrehajtás** elemre a **mlEndpoint** JSON-tulajdonság URI-értékének lekéréséhez.
* Kattintson az **erőforrás frissítése** hivatkozásra az **updateResourceEndpoint** JSON-tulajdonság URI-értékének lekéréséhez. Az API-kulcs maga a végpont oldalon található (a jobb alsó sarokban).

![frissíthető végpont](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

Az alábbi példa egy JSON-definíciót biztosít a AzureML társított szolgáltatáshoz. A társított szolgáltatás a apiKey használja a hitelesítéshez.  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": "endpoint2Key",
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>A pontozás webszolgáltatása Azure Resource Manager webszolgáltatás 
Ha a webszolgáltatás az új típusú webszolgáltatás, amely egy Azure Resource Manager-végpontot tesz elérhetővé, nem kell hozzáadnia a második **nem alapértelmezett** végpontot. A társított szolgáltatás **updateResourceEndpoint** formátuma: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

A webszolgáltatások Azure Machine Learning webszolgáltatások [portálján](https://services.azureml.net/)található webszolgáltatás lekérdezése során az URL-címekre vonatkozó értékeket is lekérheti. Az új típusú frissítési erőforrás-végponthoz HRE (Azure Active Directory) token szükséges. **ServicePrincipalId** és **ServicePrincipalKey**meghatározása a AzureML társított szolgáltatásban. Lásd: [egyszerű szolgáltatásnév létrehozása és engedélyek kiosztása az Azure-erőforrások kezeléséhez](../../active-directory/develop/howto-create-service-principal-portal.md). Itt látható egy példa AzureML társított szolgáltatás definíciója: 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": "xxxxxxxxxxxx",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": "xxxxx",
            "tenant": "mycompany.com"
        }
    }
}
```

A következő forgatókönyv további részleteket tartalmaz. Ez egy példa az Azure ML-modellek átképzésére és frissítésére egy Azure Data Factory folyamatból.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Forgatókönyv: az Azure ML-modellek átképzése és frissítése
Ez a szakasz egy olyan mintát tartalmaz, amely az **Azure ml batch végrehajtási tevékenységét** használja a modell újratanításához. A folyamat az **Azure ml Update erőforrás** -tevékenységgel is frissíti a modellt a pontozási webszolgáltatásban. A szakasz a példában szereplő társított szolgáltatások, adatkészletek és folyamatok JSON-kódrészleteit is tartalmazza.

Itt látható a mintavételezési folyamat diagram nézete. Amint láthatja, az Azure ML batch végrehajtási tevékenysége betanítja a betanítási adatokat, és létrehoz egy képzési kimenetet (iLearner-fájlt). Az Azure ML frissítési erőforrás tevékenysége ezt a betanítási kimenetet veszi át, és frissíti a modellt a pontozási webszolgáltatás végpontján. Az erőforrás frissítése tevékenység nem hoz létre kimenetet. A placeholderBlob csak egy olyan próbabábu kimeneti adatkészlete, amelyre a Azure Data Factory szolgáltatásnak szüksége van a folyamat futtatásához.

![folyamat diagramja](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Azure Blob Storage társított szolgáltatás:
Az Azure Storage a következő adatkészleteket tartalmazza:

* betanítási adatgyűjtés. Az Azure ML betanítási webszolgáltatás bemeneti adata.  
* iLearner-fájl. Az Azure ML betanítási webszolgáltatás kimenete. Ez a fájl a frissítési erőforrás tevékenységének bemenete is.  

Itt látható a társított szolgáltatás JSON-definíciója:

```JSON
{
    "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="training-input-dataset"></a>Betanítás bemeneti adatkészlete:
A következő adatkészlet az Azure ML betanítási webszolgáltatás bemeneti betanítási adatait mutatja be. Az Azure ML batch végrehajtási tevékenysége ezt az adatkészletet bemenetként veszi át.

```JSON
{
    "name": "trainingData",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "labeledexamples",
            "fileName": "labeledexamples.arff",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
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

### <a name="training-output-dataset"></a>Kimeneti adatkészlet betanítása:
A következő adatkészlet az Azure ML betanítási webszolgáltatás kimeneti iLearner-fájlját jelöli. Az Azure ML batch végrehajtási tevékenysége létrehozza ezt az adatkészletet. Ez az adatkészlet az Azure ML frissítési erőforrás tevékenységének bemenete is.

```JSON
{
    "name": "trainedModelBlob",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "trainingoutput",
            "fileName": "model.ilearner",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        }
    }
}
```

### <a name="linked-service-for-azure-ml-training-endpoint"></a>Társított szolgáltatás az Azure ML betanítási végpontja számára
A következő JSON-kódrészlet definiál egy Azure Machine Learning társított szolgáltatást, amely a betanítási webszolgáltatás alapértelmezett végpontját mutat.

```JSON
{    
    "name": "trainingEndpoint",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
              "apiKey": "myKey"
        }
      }
}
```

Az **Azure ml Studio**tegye a következőket a **MlEndpoint** és a **apiKey**értékeinek lekéréséhez:

1. A bal oldali menüben kattintson a **Web Services** elemre.
2. A webszolgáltatások listájában kattintson a betanítási **webszolgáltatás** elemre.
3. Kattintson az API- **kulcs** szövegmező melletti Másolás lehetőségre. Illessze be a vágólapra a kulcsot a Data Factory JSON-szerkesztőbe.
4. Az **Azure ml Studióban**kattintson a **Batch-végrehajtási** hivatkozás elemre.
5. Másolja a kérési **URI** -t a **kérelem** szakaszból, és ILLESSZE be a Data Factory JSON-szerkesztőbe.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Társított szolgáltatás az Azure ML frissíthető pontozási végpontja számára:
A következő JSON-kódrészlet definiál egy Azure Machine Learning társított szolgáltatást, amely a pontozási webszolgáltatás nem alapértelmezett frissíthető végpontján mutat.  

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="placeholder-output-dataset"></a>Helyőrző kimeneti adatkészlete:
Az Azure ML-frissítési erőforrás-tevékenysége nem eredményez kimenetet. Azure Data Factory azonban egy kimeneti adatkészletet kell vezetnie egy folyamat ütemtervének megadásához. Ezért ebben a példában egy dummy/helyőrző adathalmazt használunk.  

```JSON
{
    "name": "placeholderBlob",
    "properties": {
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "any",
            "format": {
                "type": "TextFormat"
            }
        }
    }
}
```

### <a name="pipeline"></a>Folyamat
A folyamat két tevékenységgel rendelkezik: **AzureMLBatchExecution** és **AzureMLUpdateResource**. Az Azure ML batch végrehajtási tevékenysége bemenetként veszi át a betanítási adatokat, és kimenetként létrehoz egy iLearner-fájlt. A tevékenység behívja a betanítási webszolgáltatást (webszolgáltatásként közzétett betanítási kísérlet) a bemeneti betanítási adatokkal, és a webszolgáltatásból fogadja a ilearner-fájlt. A placeholderBlob csak egy olyan próbabábu kimeneti adatkészlete, amelyre a Azure Data Factory szolgáltatásnak szüksége van a folyamat futtatásához.

![folyamat diagramja](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "Training Exp for ADF ML [trained model]",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "outputs": [
                    {
                        "name": "placeholderBlob"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
    }
}
```

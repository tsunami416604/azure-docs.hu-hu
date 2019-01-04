---
title: Frissítse a Machine Learning-modellek az Azure Data Factory használatával |} A Microsoft Docs
description: Ismerteti, hogyan hozhat létre Azure Data Factory és az Azure Machine Learning prediktív adatcsatornák létrehozása
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 0c0e0e3983344bba76f5f305ecaf73f91110f3bc
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020081"
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>Frissítése az Azure Machine Learning-modellek használata az Update-Erőforrástevékenység

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


> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [frissítése a machine learning-modellek a Data Factory](../update-machine-learning-models.md).

Ez a cikk a fő Azure Data Factory - integráció a cikk az Azure Machine Learning egészíti ki: [Az Azure Machine Learning és az Azure Data Factory prediktív adatcsatornák létrehozása](data-factory-azure-ml-batch-execution-activity.md). Ha ezt még nem tette meg, ez a cikk elolvasása előtt tekintse át a fő cikket. 

## <a name="overview"></a>Áttekintés
Az idő múlásával a prediktív modelleket a kísérletek pontozási Azure ML új bemeneti adatkészletek használatával lehet retrained kell. Miután végzett átképezési retrained gépi Tanulási modell a pontozási webszolgáltatás frissíteni kívánt. A jellemzően előforduló lépéseket ahhoz, hogy megőrzési és frissítése webszolgáltatásokkal Azure ML-modellek a következők:

1. A kísérlet létrehozásának [Azure ML Studio](https://studio.azureml.net).
2. Ha elégedett a modellel, használja az Azure ML Studio mindkét webszolgáltatások közzététele a **betanítási kísérlet** és pontozási /**prediktív kísérletté**.

A következő táblázat ismerteti az ebben a példában használt webes szolgáltatások.  Lásd: [Retrain Machine Learning-modellek](../../machine-learning/machine-learning-retrain-models-programmatically.md) részleteiről.

- **Webszolgáltatás képzési** – kap a betanítási adatok, és elkészíti a betanított modellek. Az átképezési kimenete egy Azure Blob Storage-.ilearner fájlt. A **alapértelmezett végpont** esetében, amikor közzéteszi a betanítási kísérlet webszolgáltatásként, amely automatikusan létrejön. További végpontokat hozhat létre, de az példában csak az alapértelmezett végpont.
- **Pontozási webszolgáltatás** – címke nélküli adatok példák kap, és előrejelzéseket tesz. Előrejelzési kimenete lehet a különféle formában, például egy .csv-fájlt vagy egy Azure SQL database, a kísérlet konfigurációjától függően a sorokat. Az alapértelmezett végpont automatikusan létrejön, amikor közzéteszi a prediktív kísérletté webszolgáltatásként. 

A következő képen látható a tanítási és pontozási Azure ML végpontok közötti kapcsolatot ábrázolja.

![Webszolgáltatások](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Hívhat a **webszolgáltatás képzési** használatával a **Azure ML kötegelt végrehajtási tevékenység**. Egy képzési webszolgáltatás megegyezik az Azure Machine Learning webszolgáltatás (pontozási webszolgáltatás) meghívása pontozási adatok. Az előző szakaszokban részletesen ismertetjük az Azure Data Factory-folyamatot az az Azure Machine Learning webszolgáltatás meghívandó foglalkozunk. 

Hívhat a **pontozási webszolgáltatás** használatával a **Azure ML Update Erőforrástevékenység** a webszolgáltatás újonnan betanított modell frissítésével. Az alábbi példák a kapcsolódószolgáltatás-definíciók megadása: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Pontozási webszolgáltatás a klasszikus webszolgáltatások
Ha a pontozási webszolgáltatás egy **klasszikus webszolgáltatás**, hozzon létre a második **nem alapértelmezett és frissíthető végpont** az Azure portal használatával. Lásd: [végpontok létrehozása](../../machine-learning/machine-learning-create-endpoint.md) ismertető cikket. Miután létrehozta a nem alapértelmezett frissíthető végpont, tegye a következőket:

* Kattintson a **KÖTEGELT végrehajtási** beolvasni az URI értékét a **mlEndpoint** JSON-tulajdonságot.
* Kattintson a **erőforrás frissítése** hivatkozásra az URI értékét a **updateResourceEndpoint** JSON-tulajdonságot. Az API-kulcs van oldalon a végpont magát (a jobb alsó sarokban).

![frissíthető végpont](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

Az alábbi példa az AzureML társított szolgáltatás egy példa JSON-definíciót tartalmaz. A társított szolgáltatást, a apikey tulajdonsággal végzett tesztelése használ.  

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

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>Pontozási webszolgáltatás az Azure Resource Manager webszolgáltatás 
Ha a webszolgáltatás, amely egy Azure Resource Manager-végpontot tesz közzé az új típusú, nem kell hozzáadnia a második **nem alapértelmezett** végpont. A **updateResourceEndpoint** formátum van a hivatkozott szolgáltatásban található: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Kérheti értékek helyen jogosultak az URL-cím számára a webszolgáltatás a lekérdezésekor a [Azure Machine Learning Web Services portálon](https://services.azureml.net/). Az új típusú erőforrás végpontjának frissítése szükséges egy aad-ben (Azure Active Directory) tokent. Adja meg **servicePrincipalId** és **servicePrincipalKey**az AzureML társított szolgáltatást. Lásd: [egyszerű szolgáltatás létrehozása és kezelése az Azure-erőforrás engedélyek hozzárendelése](../../active-directory/develop/howto-create-service-principal-portal.md). Íme egy példa az AzureML társított szolgáltatás definíciójában: 

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

A következő esetben további részletekkel. Rendelkezik egy példa átképezési és a egy Azure Data Factory-folyamatot az Azure ML-modellek frissítése.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Forgatókönyv: átképezési, és a egy Azure Machine Learning-modellek
Ez a szakasz egy minta folyamatát, amely tartalmazza a **Azure ML kötegelt végrehajtási tevékenység** a modellek újratanítása. A folyamatot is alkalmaz a **Azure Machine Learning Update Resource-tevékenységek** az pontozási webszolgáltatás a modell frissítése. A szakasz is biztosít JSON-kódrészletek a társított szolgáltatások, adatkészletek és a példában található folyamat.

Ez a diagram nézet a mintául szolgáló folyamat. Ahogy láthatjuk, az Azure ML kötegelt végrehajtási tevékenység bemenetből fogad a tanítási adatokat, és a egy képzési kimenetet (iLearner-fájlt). Az Azure ML Update-Erőforrástevékenység képzési kimenet vesz igénybe, és frissíti a modell a pontozási webszolgáltatás végpontja. Az Update-Erőforrástevékenység nem állít elő semmilyen kimenetet. A placeholderBlob csak egy helyőrző kimeneti adatkészletet, amely az Azure Data Factory szolgáltatás által a folyamat futtatásához szükséges.

![adatfolyamat ábrája](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Az Azure Blob storage-beli társított szolgáltatást:
Az Azure Storage tárolja, a következő adatokat:

* betanítási adatok. A bemeneti adatok az Azure Machine Learning betanítási webszolgáltatáshoz.  
* iLearner-fájlt. A kimenet az Azure Machine Learning betanítási webszolgáltatásból. Ez a fájl is a bemeneti az Update-erőforrástevékenység.  

Itt látható a minta a társított szolgáltatás JSON-definíciót:

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

### <a name="training-input-dataset"></a>Betanítási adatkészletet:
A következő adatkészlet a bemeneti betanítási adatok az Azure Machine Learning betanítási webszolgáltatás jelenti. Az Azure ML kötegelt végrehajtási tevékenység bemeneteként vesz igénybe ehhez az adatkészlethez.

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

### <a name="training-output-dataset"></a>Betanítási kimeneti adatkészletet:
A következő adatkészlet a kimeneti iLearner-fájlt az Azure Machine Learning betanítási webszolgáltatásból jelenti. Az Azure ML kötegelt végrehajtási tevékenység hoz létre ehhez az adatkészlethez. Ez az adatkészlet is az Azure Machine Learning Update Resource-tevékenységek bemeneti.

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

### <a name="linked-service-for-azure-ml-training-endpoint"></a>Az Azure Machine Learning betanítási végponthoz társított szolgáltatás
A következő JSON-kódrészlet definiál egy társított Azure Machine Learning szolgáltatás, amely az alapértelmezett végpont az képzési webszolgáltatás mutat.

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

A **Azure ML Studio**, tegye a következőket használatával lekérjük az értékeket a **mlEndpoint** és **apikey tulajdonsággal végzett tesztelése**:

1. Kattintson a **WEBSZOLGÁLTATÁSOK** a bal oldali menüben.
2. Kattintson a **webszolgáltatás képzési** webes szolgáltatások listájában.
3. Kattintson a Tovább gombra másolási **API-kulcs** szövegmezőben. Illessze be a vágólapra a kulcsot a Data Factory JSON-szerkesztőt.
4. Az a **Azure ML studio**, kattintson a **KÖTEGELT végrehajtási** hivatkozásra.
5. Másolás a **Request URI** a a **kérelem** szakaszt, és illessze be a Data Factory JSON-szerkesztőt.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Frissíthető pontozási végpontjához Azure ML társított szolgáltatás:
A következő JSON-kódrészlet definiál egy társított Azure Machine Learning szolgáltatás, amely a pontozási webszolgáltatás a nem alapértelmezett frissíthető végpontra mutat.  

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

### <a name="placeholder-output-dataset"></a>Helyőrző kimeneti adatkészlet:
Az Azure Machine Learning Update Resource-tevékenységek nem ad kimenetet. Azure Data Factory azonban meghajtó az ütemezés a folyamat a kimeneti adatkészlet szükséges. Dummy/helyőrző adatkészlet tehát ebben a példában használjuk.  

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
A folyamat két tevékenységet tartalmaz: **AzureMLBatchExecution** és **AzureMLUpdateResource**. Az Azure ML kötegelt végrehajtási tevékenység bemeneteként a betanítási adatok vesz igénybe, és állít elő kimenetként egy iLearner-fájlt. A tevékenység hívja meg a képzés webszolgáltatás (betanítási kísérlet webszolgáltatásként közzétéve) és a bemeneti betanítási adatok, és fogadja a webszolgáltatás a ilearner-fájlt. A placeholderBlob csak egy helyőrző kimeneti adatkészletet, amely az Azure Data Factory szolgáltatás által a folyamat futtatásához szükséges.

![adatfolyamat ábrája](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

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

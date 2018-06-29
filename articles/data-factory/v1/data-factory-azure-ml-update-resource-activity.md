---
title: Azure Data Factory használatával gépi tanulási modellek módosítása |} Microsoft Docs
description: Ismerteti, hogyan hozzon létre Azure Data Factory és az Azure Machine Learning a prediktív folyamatok létrehozása
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 3eb9f765b5ffe1118b3b25aff8ecdde327021a54
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046479"
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>Frissítés Azure Machine Learning modellek használata az Update-Erőforrástevékenység

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


> [!NOTE]
> Ez a cikk a Data Factory 1 verziójára vonatkozik. A Data Factory szolgáltatásnak aktuális verziójának használatakor lásd [machine learning modellek adat-előállítóban módosítása](../update-machine-learning-models.md).

Ez a cikk kiegészíti a fő Azure Data Factory - Azure Machine Learning integrációs cikk: [létrehozása az Azure Machine Learning és az Azure Data Factory használatával prediktív folyamatok](data-factory-azure-ml-batch-execution-activity.md). Ha még nem tette meg, tekintse át a fő cikk keresztül ez a cikk elolvasása előtt. 

## <a name="overview"></a>Áttekintés
Az Azure ml kísérletek pontozási prediktív modelleket idővel kell kell retrained új bemeneti adatkészletek használata. Miután elkészült, az átképezési, a pontozási webszolgáltatás retrained ML-modell frissíteni kívánt. A tipikus lépések elvégzésével engedélyezniük megőrzési és frissítési webszolgáltatásokkal Azure ML-modellek a következők:

1. A kísérlet létrehozásának [Azure ML Studio](https://studio.azureml.net).
2. Ha elégedett a modellel, mind a webes szolgáltatásokat használja Azure ML Studio a **tanítási kísérletet** és pontozási /**prediktív kísérletté**.

A következő táblázat ismerteti a webszolgáltatásokat, ebben a példában használt.  Lásd: [Machine Learning-modellek szoftveres](../../machine-learning/machine-learning-retrain-models-programmatically.md) részleteiről.

- **Webszolgáltatás betanítása** - tanítási adatokat fogad, és hozza létre a betanított modellek. A átképezési eredménye egy .ilearner fájlt az Azure Blob Storage tárolóban. A **alapértelmezett végpont** automatikusan létrejön a képzés közzétételekor kísérletezik webszolgáltatásként. Létrehozhat további végpontok, de a példában csak az alapértelmezett végpont.
- **Webszolgáltatás pontozási** – címke nélküli adatok példák kap, és lehetővé teszi az előrejelzés. Előrejelzés kimenete különböző formát, például egy CSV-fájlt vagy egy Azure SQL-adatbázis, a kísérlet konfigurációjától függően sorok rendelkezhetnek. Az alapértelmezett végpont automatikusan létrejön egy webszolgáltatás prediktív kísérletté közzétételekor. 

Az alábbi képen képzési és a végpontok pontozás az Azure ml közötti kapcsolatot ábrázolja.

![Webszolgáltatások](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Hívhat meg a **webszolgáltatás betanítása** használatával a **Azure ML kötegelt végrehajtási tevékenység**. Egy képzési webszolgáltatás indítására legyen, mint az Azure gépi tanulás webszolgáltatás (pontozási webszolgáltatás) való pontozási adatokat. Az előző szakaszokban részletesen az Azure Data Factory-folyamat az az Azure ML webszolgáltatás meghívására foglalkozik. 

Hívhat meg a **webszolgáltatás pontozási** használatával a **Azure ML Update Erőforrástevékenység** frissítheti a webszolgáltatás a újonnan betanított modell. Az alábbi példák megadják a kapcsolódószolgáltatás-definíciók: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Webszolgáltatás pontozási egy klasszikus webszolgáltatás-bővítmény
Ha a pontozási webszolgáltatás egy **klasszikus webszolgáltatás**, hozzon létre a második **nem alapértelmezett és frissíthető végpont** az Azure portál használatával. Lásd: [végpontok létrehozása](../../machine-learning/machine-learning-create-endpoint.md) cikk lépéseit. Miután létrehozta a nem alapértelmezett frissíthető végpont, tegye a következőket:

* Kattintson a **KÖTEGELT végrehajtási** URI értékének eléréséhez a **mlEndpoint** JSON tulajdonság.
* Kattintson a **frissítés erőforrás** hivatkozásra az URI értéke a **updateResourceEndpoint** JSON tulajdonság. Az API-kulcsot a végpont lapon magát (a jobb alsó sarokban) van.

![frissíthető végpont](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

A következő példa egy minta az AzureML társított szolgáltatás JSON-definícióból biztosít. A társított szolgáltatás, a apiKey használ.  

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

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>Webszolgáltatás pontozás az Azure Resource Manager webszolgáltatás 
Ha a webszolgáltatás egy webszolgáltatás, amely elérhetővé teszi az Azure Resource Manager-végpont új típusú, nem kell hozzáadnia a második **nem alapértelmezett** végpont. A **updateResourceEndpoint** formátumban van a hivatkozott szolgáltatásban található: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Kaphat értékek hely tartozó felhasználók számára az URL-címben a webkiszolgáló lekérdezésekor a [Azure Machine Learning Web Services portálra](https://services.azureml.net/). A frissítés erőforrás végpont új típusú van szükség az (Azure Active Directory) AAD-tokent. Adja meg **servicePrincipalId** és **servicePrincipalKey**az AzureML társított szolgáltatás. Lásd: [egyszerű szolgáltatásnév létrehozása és hozzárendelése az Azure erőforrások kezeléséhez szükséges jogokat](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Íme egy minta AzureML társított szolgáltatás definíciójának: 

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

Az alábbi forgatókönyvet további részleteket tartalmaz. Rendelkezik egy példa átképezési, és az Azure Data Factory-folyamat az Azure ML modellek frissítéséhez.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Forgatókönyv: átképezési, és az Azure ML modellje frissítése
Ez a témakör egy minta folyamatot, amely használja a **Azure ML kötegelt végrehajtási tevékenység** a modell működik. A folyamatot is alkalmaz a **Azure ML Update erőforrástevékenység** az pontozási webszolgáltatás a modell frissítése. A szakasz is biztosít JSON kódtöredékek az összekapcsolt szolgáltatások, adatkészleteket és a példában szereplő folyamat.

Ez a diagram nézet a minta-feldolgozási folyamat. Ahogy látja, az Azure ML kötegelt végrehajtási tevékenység a képzés bemenetből fogad adatokat, és a képzési kimenetet (iLearner-fájlt). Az Azure ML Update-Erőforrástevékenység időt vesz igénybe a képzés kimenetet, és frissíti a modellt a pontozási webszolgáltatási végpontot. Az Update-Erőforrástevékenység nem ad kimenetet. A placeholderBlob csak az Azure Data Factory szolgáltatásnak a feldolgozási sor futtatásához szükséges üres kimeneti adatkészlet.

![folyamat diagramja](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Az Azure Blob storage társított szolgáltatásnak:
Az Azure Storage a következő adatokat tartalmazza:

* betanítási adata. A bemeneti adatok az Azure ML képzési webszolgáltatáshoz.  
* iLearner-fájlt. Az Azure ML képzési webszolgáltatás kimenetét. Ez a fájl egyben a frissítési erőforrás tevékenység bemeneti.  

Ez a minta társított szolgáltatás JSON-definícióból:

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

### <a name="training-input-dataset"></a>Képzési bemeneti adatkészlet:
A következő adatkészlet jelenti. a bemeneti betanítási adatok, az Azure ML képzési webszolgáltatáshoz. Az Azure ML kötegelt végrehajtási tevékenység ehhez az adatkészlethez bemenetként vesz igénybe.

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

### <a name="training-output-dataset"></a>Képzési kimeneti adatkészlet:
A következő adatkészlet a kimenetet iLearner-fájlt az Azure ML képzési webszolgáltatás jelöli. Az Azure ML kötegelt végrehajtási tevékenység létrehozza az adatkészletet. Ez az adatkészlet esetében is az Azure ML Update erőforrás tevékenység bemeneti.

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

### <a name="linked-service-for-azure-ml-training-endpoint"></a>Azure ML képzési végponthoz társított szolgáltatás
A következő JSON-részlet egy Azure Machine Learning társított szolgáltatás mutat, az alapértelmezett végpont az képzési webszolgáltatás határozza meg.

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

A **Azure ML Studio**, hajtsa végre a következő értékek **mlEndpoint** és **apiKey**:

1. Kattintson a **WEBSZOLGÁLTATÁSOK** a bal oldali menüben.
2. Kattintson a **webszolgáltatás betanítása** a webes szolgáltatások közül.
3. Jelölje be a másolási **API-kulcs** szövegmezőben. Illessze be a kulcsot a vágólapra a Data Factory JSON-szerkesztőt.
4. Az a **Azure ML studio**, kattintson a **KÖTEGELT végrehajtási** hivatkozásra.
5. Másolás a **kérelem URI-azonosítója** a a **kérelem** szakaszt, és illessze be a Data Factory JSON-szerkesztőt.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Azure ML frissíthető pontozási végponthoz társított szolgáltatás:
A következő JSON-részlet egy csatolt Azure Machine Learning szolgáltatás, amely a nem alapértelmezett frissíthető végpontra pontozási webszolgáltatás mutató határozza meg.  

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
Az Azure ML Update erőforrás tevékenység nem ad kimenetet. Azure Data Factory azonban annak az adatcsatorna ütemezés egy kimeneti adatkészlet szükséges. A helyőrző/helyőrző dataset ezért ebben a példában használjuk.  

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
A folyamat két tevékenység rendelkezik: **AzureMLBatchExecution** és **AzureMLUpdateResource**. Az Azure ML kötegelt végrehajtási tevékenység lekéri a tanítási adatokat bemeneti adatként, és egy kimenetként iLearner-fájlt hoz létre. A tevékenység hív meg, a képzési webszolgáltatás (a tanítási kísérletet webszolgáltatásként kitett) a bemeneti betanítási adatok, és megkapja a webservice ilearner-fájlt. A placeholderBlob csak az Azure Data Factory szolgáltatásnak a feldolgozási sor futtatásához szükséges üres kimeneti adatkészlet.

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

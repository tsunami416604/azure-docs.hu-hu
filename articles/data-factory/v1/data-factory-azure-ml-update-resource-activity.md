---
title: Machine Learning-modellek frissítése az Azure Data Factory használatával
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
ms.openlocfilehash: afc79badd19fa180e631f1f8fa9735567a0b1e33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978713"
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>Az Azure Machine Learning-modellek frissítése erőforrás-tevékenység frissítésével

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


> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a Gépi tanulási modellek frissítése a Data Factory alkalmazásban című [témakört.](../update-machine-learning-models.md)

Ez a cikk kiegészíti az Azure Data Factory – Azure Machine Learning integrációs cikket: [Prediktív folyamatok létrehozása az Azure Machine Learning és az Azure Data Factory használatával.](data-factory-azure-ml-batch-execution-activity.md) Ha még nem tette meg, olvassa el a cikket, mielőtt elolvasná ezt a cikket. 

## <a name="overview"></a>Áttekintés
Idővel az Azure ML pontozási kísérletek prediktív modelljeit újra kell képezni az új bemeneti adatkészletek használatával. Miután végzett az átképzés, frissíteni szeretné a pontozási webszolgáltatás az átedzett ml-modell. Az Azure ML-modellek webszolgáltatásokon keresztüli átképzésének és frissítésének tipikus lépései a következők:

1. Hozzon létre egy kísérletet az [Azure Machine Learning Studio (klasszikus)](https://studio.azureml.net).
2. Ha elégedett a modellel, használja az Azure Machine Learning Studio (klasszikus) webes szolgáltatások közzétételét mind a **képzési kísérlet** és pontozási /**prediktív kísérlet.**

Az alábbi táblázat ismerteti a példában használt webszolgáltatásokat.  A részletekért tekintse meg a [Machine Learning-modellek programozott újratanítását.](../../machine-learning/machine-learning-retrain-models-programmatically.md)

- **Betanítási webszolgáltatás** – betanítási adatok fogadása és képzett modellek et készít. Az átképzés kimenete egy .ilearner fájl egy Azure Blob storage.The output of the retraining is an .ilearner file in an Azure Blob storage. Az **alapértelmezett végpont** automatikusan létrejön az Ön számára, amikor a betanítási kísérlet webszolgáltatásként teszi közzé. Több végpontot is létrehozhat, de a példa csak az alapértelmezett végpontot használja.
- **Pontozási webszolgáltatás** – Címzetlen adatpéldák fogadása és előrejelzések et kap. Az előrejelzés kimenete lehet különböző formák, például egy .csv fájlt vagy sorokat egy Azure SQL-adatbázisban, a ttól függően, hogy a kísérlet konfigurációját. Az alapértelmezett végpont automatikusan létrejön az Ön számára, amikor közzéteszi a prediktív kísérlet webszolgáltatásként. 

Az alábbi képen a betanítási és pontozási végpontok közötti kapcsolat az Azure ML-ben.

![Webszolgáltatások](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Meghívhatja a **betanítási webszolgáltatás** az **Azure ML kötegelt végrehajtási tevékenység**használatával. Egy betanítási webszolgáltatás meghívása megegyezik egy Azure ML webszolgáltatás (pontozási webszolgáltatás) meghívásával az adatok pontozásához. Az előző szakaszok részletesen ismertetik, hogyan hívhat meg egy Azure ML-webszolgáltatást egy Azure Data Factory-folyamatból. 

A pontozási **webszolgáltatás** meghívása az **Azure ML update erőforrás-tevékenység** az újonnan betanított modell frissítéséhez a szolgáltatás frissítése. A következő példák csatolt szolgáltatásdefiníciókat tartalmaznak: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Pontozási webszolgáltatás egy klasszikus webszolgáltatás
Ha a pontozási webszolgáltatás egy **klasszikus webszolgáltatás,** hozza létre a második **nem alapértelmezett és updatable végpont** az Azure Portal használatával. A lépésekről a [Végpontok létrehozása](../../machine-learning/machine-learning-create-endpoint.md) című cikkben olvashat. A nem alapértelmezett updatable végpont létrehozása után tegye a következő lépéseket:

* Kattintson **a BATCH EXECUTION** elemre az **mlEndpoint** JSON tulajdonság URI-értékének leéséhez.
* Kattintson **az ERŐFORRÁS-hivatkozás frissítése** gombra az **updateResourceEndpoint** JSON tulajdonság URI-értékének lekérni. Az API-kulcs maga a végpontoldalon található (a jobb alsó sarokban).

![updatable végpont](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

A következő példa egy minta JSON-definíciót tartalmaz az AzureML-alapú szolgáltatáshoz. A csatolt szolgáltatás az apiKey-t használja a hitelesítéshez.  

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

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>A pontozási webszolgáltatás az Azure Resource Manager webszolgáltatása 
Ha a webszolgáltatás az azure Resource Manager-végpontot elérhetővé t, új típusú webszolgáltatás, nem kell hozzáadnia a második **nem alapértelmezett** végpontot. A csatolt szolgáltatás **updateResourceEndpoint** formátuma: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Az [Azure Machine Learning Web Services Portal](https://services.azureml.net/)webszolgáltatásban a webszolgáltatás lekérdezéseke során lekaphatja az URL-címben lévő helyőrzők értékeit. Az új típusú frissítési erőforrás-végpont hoz egy AAD (Azure Active Directory) jogkivonatot. Adja meg **a servicePrincipalId** és **servicePrincipalKey** az Azure Machine Learning csatolt szolgáltatás. [Megtudhatja, hogy miként hozhat létre egyszerű szolgáltatást, és hogyan rendelhet engedélyeket az Azure-erőforrás kezeléséhez.](../../active-directory/develop/howto-create-service-principal-portal.md) Íme egy AzureML-alapú szolgáltatásdefiníció: 

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

A következő forgatókönyv további részleteket tartalmaz. Egy példa az Azure ML-modellek átképzése és frissítése egy Azure Data Factory-folyamat.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Forgatókönyv: Átképzés és frissítés egy Azure ML-modell
Ez a szakasz egy mintafolyamatot biztosít, amely az **Azure ML-kötegelt végrehajtási tevékenységet** használja egy modell újratanításához. A folyamat is használja az **Azure ML update resource tevékenység** a modell frissítése a pontozási webszolgáltatásban. A szakasz json-kódrészleteket is biztosít a példában az összes csatolt szolgáltatáshoz, adatkészlethez és folyamathoz.

Itt van a mintafolyamat diagramnézete. Mint látható, az Azure ML batch-végrehajtási tevékenység veszi a betanítási bemenet, és egy betanítási kimenet (iLearner fájl) hoz létre. Az Azure ML-frissítési erőforrás-tevékenység veszi ezt a betanítási kimenetet, és frissíti a modellt a pontozási webszolgáltatás végpontjában. Az erőforrás-frissítési tevékenység nem hoz létre kimenetet. A placeholderBlob csak egy dummy kimeneti adatkészlet, amely az Azure Data Factory szolgáltatás futtatásához szükséges.

![folyamatábra](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Azure Blob storage-hoz csatolt szolgáltatás:
Az Azure Storage a következő adatokat tartalmazza:

* betanítási adatok. Az Azure ML betanítási webszolgáltatás bemeneti adatai.  
* iLearner fájlt. A kimenet az Azure ML betanítási webszolgáltatás. Ez a fájl egyben az Erőforrás frissítése tevékenység bemenete is.  

Itt van a minta JSON meghatározása a csatolt szolgáltatás:

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

### <a name="training-input-dataset"></a>Betanítási bemeneti adatkészlet:
A következő adatkészlet az Azure Machine Learning betanítási webszolgáltatás bemeneti betanítási adatait jelöli. Az Azure Machine Learning batch-végrehajtási tevékenység ezt az adatkészletet bemenetként veszi fel.

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

### <a name="training-output-dataset"></a>Betanítási kimeneti adatkészlet:
A következő adatkészlet az Azure ML betanítási webszolgáltatás kimeneti iLearner-fájlját jelöli. Az Azure ML kötegelt végrehajtási tevékenység ezt az adatkészletet hozza létre. Ez az adatkészlet is az Azure ML-frissítési erőforrás tevékenység bemenete.

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

### <a name="linked-service-for-azure-machine-learning-training-endpoint"></a>Csatolt szolgáltatás az Azure Machine Learning képzési végpontjának
A következő JSON-kódrészlet egy Azure Machine Learning-alapú szolgáltatást határoz meg, amely a betanítási webszolgáltatás alapértelmezett végpontjára mutat.

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

Az **Azure Machine Learning Studio (klasszikus)** alkalmazásban az **mlEndpoint** és az **apiKey**értékek lehívásához tegye a következőket:

1. Kattintson a bal oldali menü **WEB SERVICES** parancsára.
2. Kattintson a **webes szolgáltatások betanítási szolgáltatására** a webszolgáltatások listájában.
3. Kattintson a Másolás elemre az **API-kulcs** szövegmezője mellett. Illessze be a kulcsot a vágólapra a Data Factory JSON szerkesztőbe.
4. Az **Azure Machine Learning Studio (klasszikus)** kattintson **a BATCH EXECUTION** hivatkozásra.
5. Másolja a **kérelem URI-t** a **Kérelem** szakaszból, és illessze be a Data Factory JSON-szerkesztőbe.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Csatolt szolgáltatás az Azure ML updatable pontozási végpont:
A következő JSON-kódrészlet egy Azure Machine Learning-alapú szolgáltatást határoz meg, amely a pontozási webszolgáltatás nem alapértelmezett javítható végpontjára mutat.  

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
Az Azure ML-frissítési erőforrás tevékenység nem hoz létre kimenetet. Az Azure Data Factory azonban egy kimeneti adatkészletet igényel a folyamat ütemezésének megvalósításához. Ezért ebben a példában egy dummy/placeholder adatkészletet használunk.  

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
A folyamat két tevékenységet kínál: **AzureMLBatchExecution** és **AzureMLUpdateResource.** Az Azure ML batch-végrehajtási tevékenység bemenetként veszi fel a betanítási adatokat, és egy iLearner-fájlt hoz létre kimenetként. A tevékenység meghívja a betanítási webszolgáltatás (webes szolgáltatásként elérhetővé tett betanítási kísérlet) a bemeneti betanítási adatokat, és megkapja az ilearner fájlt a webszolgáltatás. A placeholderBlob csak egy dummy kimeneti adatkészlet, amely az Azure Data Factory szolgáltatás futtatásához szükséges.

![folyamatábra](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

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

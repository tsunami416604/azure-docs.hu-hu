---
title: Frissítse a machine learning-modellek az Azure Data Factory használatával |} A Microsoft Docs
description: Ismerteti, hogyan hozhat létre Azure Data Factory segítségével prediktív adatcsatornák létrehozása és a gépi tanulás
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: de3407b45fb82aa38bec7b84fef34c1654676122
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016215"
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>Az Azure Machine Learning-modellek frissítése az Update-erőforrástevékenység
Ez a cikk a fő Azure Data Factory - integráció a cikk az Azure Machine Learning egészíti ki: [Az Azure Machine Learning és az Azure Data Factory prediktív adatcsatornák létrehozása](transform-data-using-machine-learning.md). Ha ezt még nem tette meg, ez a cikk elolvasása előtt tekintse át a fő cikket. 

## <a name="overview"></a>Áttekintés
A modell Azure Machine Learning-modellek modellezést, a folyamat részeként a betanított és mentve. Ezt követően használhatja azt egy prediktív webszolgáltatás létrehozására. A Web service a webhelyek, az irányítópultok és a mobile apps majd használhatók fel.

Machine Learning segítségével létrehozott modellek ben általában nem statikusak. Az új adatok válnak elérhetővé, vagy ha a fogyasztói API-rendelkezik a saját adatait a modell kell lennie retrained. Tekintse meg [egy gépi tanulási modellek szoftveres Átképezése](../machine-learning/machine-learning-retrain-machine-learning-model.md) hogyan ténytábláknál az Azure Machine Learning-modell részleteit. 

Átképezési fordulhat elő, gyakran. A Batch Execution-tevékenység és az Update-erőforrástevékenység üzembe helyezheti átképezési és a Data Factory segítségével prediktív webszolgáltatás frissítése az Azure Machine Learning-modellhez. 

A következő képen látható betanítást, prediktív webszolgáltatások közötti kapcsolatot ábrázolja. 

![Webszolgáltatások](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Az Azure Machine Learning update-erőforrástevékenység 

A következő JSON-kódrészlet definiál egy Azure Machine Learning Batch Execution-tevékenység.

```json
{
    "name": "amlUpdateResource",
    "type": "AzureMLUpdateResource",
    "description": "description",
    "linkedServiceName": {
        "type": "LinkedServiceReference",
        "referenceName": "updatableScoringEndpoint2"
    },
    "typeProperties": {
        "trainedModelName": "ModelName",
        "trainedModelLinkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "StorageLinkedService"
                },
        "trainedModelFilePath": "ilearner file path"
    }
}
```




| Tulajdonság                      | Leírás                              | Szükséges |
| :---------------------------- | :--------------------------------------- | :------- |
| név                          | A folyamat a tevékenység neve     | Igen      |
| leírás                   | A tevékenység leírása leíró szöveg.  | Nem       |
| type                          | Az Azure Machine Learning Update Resource-tevékenységek, a tevékenység típusa van **AzureMLUpdateResource**. | Igen      |
| linkedServiceName             | Az Azure Machine Learning társított szolgáltatás updateResourceEndpoint tulajdonsága tartalmaz. | Igen      |
| trainedModelName              | A Web Service kísérletben frissíteni kell a Trained Model-modul neve | Igen      |
| trainedModelLinkedServiceName | A frissítési művelet által feltöltött ilearner-fájlt tároló Azure Storage társított szolgáltatás neve | Igen      |
| trainedModelFilePath          | A relatív elérési útja a trainedModelLinkedService, amelyek a frissítési művelet által feltöltött ilearner-fájlt | Igen      |


## <a name="end-to-end-workflow"></a>Teljes körű munkafolyamat

Modell és a prediktív webszolgáltatások frissítés átképezési modellezést, a teljes folyamat az alábbi lépésekből áll: 

- Meghívása a **webszolgáltatás képzési** használatával a **kötegelt végrehajtási tevékenység**. Ugyanaz, mint egy ismertetett prediktív webszolgáltatás egy webszolgáltatás képzés Invoking [hozhatók létre prediktív adatcsatornák Azure Machine Learning és a Data Factory kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md). A képzési webszolgáltatás kimenete egy iLearner-fájlt, amely segítségével a prediktív webszolgáltatás frissítésével. 
- Meghívása a **erőforrás végpontjának frissítése** , a **prediktív webszolgáltatás** használatával a **Update-erőforrástevékenység** a webszolgáltatás újonnan betanított modell frissítésével. 

## <a name="azure-machine-learning-linked-service"></a>Az Azure Machine Learning-beli társított szolgáltatás

A fent említett végpontok közötti munkafolyamat működjön két társított Azure Machine Learning-szolgáltatások létrehozásához szüksége: 

1. Azure Machine Learning-társított szolgáltatást, a képzési webszolgáltatás, ezt a társított szolgáltatást használják kötegelt végrehajtási tevékenység ugyanúgy, mint a már említettük a [hozhatók létre prediktív adatcsatornák Azure Machine Learning és a Data Factory Batch használatával Végrehajtási tevékenység](transform-data-using-machine-learning.md). A különbség a betanítási webszolgáltatás kimenete egy iLearner-fájlt, amelyet majd Update-erőforrástevékenység a prediktív webszolgáltatás frissítésével. 
2. Az Azure Machine Learning a prediktív webszolgáltatás a frissítés erőforrás-végponthoz társított szolgáltatást. Ezt a társított szolgáltatást használják Update-erőforrástevékenység frissíteni a prediktív webszolgáltatás parancssorai lépés visszaadott iLearner-fájlt használ. 

A második társított Azure Machine Learning szolgáltatás a konfiguráció eltér az Azure Machine Learning Web Service klasszikus webszolgáltatást, vagy egy új webszolgáltatás esetén. A különbségek külön-külön az alábbi szakaszok ismertetik. 

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Webszolgáltatás az új Azure Resource Manager webszolgáltatás 

Ha a webszolgáltatás, amely egy Azure Resource Manager-végpontot tesz közzé az új típusú, nem kell hozzáadnia a második **nem alapértelmezett** végpont. A **updateResourceEndpoint** formátum van a hivatkozott szolgáltatásban található: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

Kérheti értékek helyen jogosultak az URL-cím számára a webszolgáltatás a lekérdezésekor a [Azure Machine Learning Web Services portálon](https://services.azureml.net/). 

Az új típusú frissítési erőforrás végpontjának szolgáltatás egyszerű hitelesítés szükséges. Használja az egyszerű szolgáltatásnév hitelesítése, egy alkalmazás entitás regisztrálása az Azure Active Directory (Azure AD), és adja meg azt a **közreműködői** vagy **tulajdonosa** szerepkört az előfizetés vagy az erőforrás-csoport helyét a web service tartozik. A részletek [egyszerű szolgáltatás létrehozása és kezelése az Azure-erőforrás engedélyek hozzárendelése](../active-directory/develop/howto-create-service-principal-portal.md). Jegyezze fel a következő értékeket, mert a társított szolgáltatás definiálásához használja:

- Alkalmazásazonosító
- Alkalmazáskulcs 
- Bérlőazonosító

Itt látható egy minta a társított szolgáltatás definíciójában: 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000  000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": {
            "type": "SecureString",
            "value": "APIKeyOfEndpoint1"
            },
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": {
            "type": "SecureString",
            "value": "servicePrincipalKey"
            },
            "tenant": "mycompany.com"
        }
    }
}
```

A következő esetben további részletekkel. Rendelkezik egy példa átképezési és a egy Azure Data Factory-folyamatot az Azure ML-modellek frissítése.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Minta: Átképezési és a egy Azure Machine Learning-modellek

Ez a szakasz egy minta folyamatát, amely tartalmazza a **Azure ML kötegelt végrehajtási tevékenység** a modellek újratanítása. A folyamatot is alkalmaz a **Azure Machine Learning Update Resource-tevékenységek** az pontozási webszolgáltatás a modell frissítése. A szakasz is biztosít JSON-kódrészletek a társított szolgáltatások, adatkészletek és a példában található folyamat.

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
A következő JSON-kódrészlet definiál egy társított Azure Machine Learning szolgáltatás, amely a pontozási webszolgáltatás frissíthető végpontra mutat.  

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

### <a name="pipeline"></a>Folyamat
A folyamat két tevékenységet tartalmaz: **AzureMLBatchExecution** és **AzureMLUpdateResource**. A kötegelt végrehajtási tevékenység bemeneteként a betanítási adatok vesz igénybe, és állít elő kimenetként egy iLearner-fájlt. A frissítés erőforrástevékenység majd vesz igénybe a iLearner-fájlt, és ezzel a prediktív webszolgáltatás frissítésével. 

```JSON
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "amlBEGetilearner",
                "description": "Use AML BES to get the ileaner file from training web service",
                "type": "AzureMLBatchExecution",
                "linkedServiceName": {
                    "referenceName": "trainingEndpoint",
                    "type": "LinkedServiceReference"
                },
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            }, 
                            "FilePath":"azuremltesting/input"
                        }, 
                        "input2": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference" 
                            }, 
                            "FilePath":"azuremltesting/input"
                        }        
                    },
                    "webServiceOutputs": {
                        "output1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"   
                            }, 
                            "FilePath":"azuremltesting/output"
                        }        
                    }
                }
            },
            {
                "name": "amlUpdateResource",
                "type": "AzureMLUpdateResource",
                "description": "Use AML Update Resource to update the predict web service",
                "linkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "updatableScoringEndpoint2"
                },
                "typeProperties": {
                    "trainedModelName": "ADFV2Sample Model [trained model]",
                    "trainedModelLinkedServiceName": {
                                "type": "LinkedServiceReference",
                                "referenceName": "StorageLinkedService"
                            },
                    "trainedModelFilePath": "azuremltesting/output/newModelForArm.ilearner"
                },
                "dependsOn": [ 
                    { 
                        "activity": "amlbeGetilearner", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ]

            }
        ]
    }
}
```
## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikkek, amelyek bemutatják, hogyan alakíthat át adatokat, egyéb módon: 

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce-tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streamelési tevékenységben](transform-data-using-hadoop-streaming.md)
* [Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)

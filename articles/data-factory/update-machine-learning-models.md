---
title: Gépi tanulási modellek frissítése Azure Data Factory használatával
description: Ismerteti, hogyan hozhat létre prediktív folyamatokat a Azure Data Factory és a gépi tanulás használatával
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: 3313c9c362a9b82cf7ed8db63479aaa5cf0c777e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683246"
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>Azure Machine Learning modellek frissítése az erőforrás frissítése tevékenység használatával
Ez a cikk a Azure Data Factory Azure Machine Learning integrációs cikket egészíti ki: [prediktív folyamatok létrehozása Azure Machine learning és Azure Data Factory használatával](transform-data-using-machine-learning.md). Ha még nem tette meg, tekintse át a fő cikket, mielőtt beolvassa ezt a cikket.

## <a name="overview"></a>Áttekintés
Azure Machine Learning modellek végrehajtott folyamata részeként a rendszer betanítja és menti a modellt. Ezt követően egy prediktív webszolgáltatás létrehozására használhatja. A webszolgáltatás ezután felhasználható a webhelyeken, az irányítópultokon és a Mobile apps szolgáltatásban.

A Machine Learning használatával létrehozott modellek általában nem statikusak. Mivel az új adatforgalom elérhetővé válik, vagy ha az API felhasználója saját adatmennyiséggel rendelkezik, a modellt újra kell tanítani. Tekintse át a [Machine learning modell](../machine-learning/machine-learning-retrain-machine-learning-model.md) újratanítása című témakört, amely részletesen ismerteti, hogyan lehet a modelleket Azure Machine learning-ben áttanítani.

Előfordulhat, hogy az újraképzés gyakran előfordul. A kötegelt végrehajtási tevékenységek és az erőforrás-frissítési tevékenységek segítségével működővé tenni a Azure Machine Learning modell átképzését és a prediktív webszolgáltatás frissítését a Data Factory használatával.

Az alábbi ábra a képzés és a prediktív webszolgáltatások közötti kapcsolatot ábrázolja.

![Webszolgáltatások](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Erőforrás-frissítési tevékenység Azure Machine Learning

A következő JSON-kódrészlet definiál egy Azure Machine Learning batch végrehajtási tevékenységet.

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

| Tulajdonság                      | Leírás                              | Kötelező |
| :---------------------------- | :--------------------------------------- | :------- |
| név                          | A folyamatban szereplő tevékenység neve     | Igen      |
| leírás                   | A tevékenység működését leíró szöveg  | Nem       |
| type                          | Azure Machine Learning frissítési erőforrás tevékenység esetén a tevékenység típusa **AzureMLUpdateResource**. | Igen      |
| linkedServiceName             | Azure Machine Learning társított szolgáltatás, amely a updateResourceEndpoint tulajdonságot tartalmazza. | Igen      |
| trainedModelName              | A frissítendő webszolgáltatási kísérletben szereplő betanított modell modul neve | Igen      |
| trainedModelLinkedServiceName | Az Azure Storage társított szolgáltatás neve, amely a frissítési művelettel feltöltött ilearner fájlt tartja | Igen      |
| trainedModelFilePath          | A trainedModelLinkedService relatív fájljának elérési útja, amely a frissítési művelet által feltöltött ilearner-fájlt jelöli. | Igen      |

## <a name="end-to-end-workflow"></a>Teljes körű munkafolyamat

A végrehajtott teljes folyamata és a prediktív webszolgáltatások frissítése a következő lépésekkel jár:

- Hívja meg a **betanítási webszolgáltatást** a **Batch végrehajtási tevékenység**használatával. A betanítási webszolgáltatás meghívása ugyanaz, mint a prediktív [folyamatok létrehozása Azure Machine learning és Data Factory batch-végrehajtási tevékenység használatával](transform-data-using-machine-learning.md)című témakörben ismertetett prediktív webszolgáltatás. A betanítási webszolgáltatás kimenete egy iLearner-fájl, amelyet a prediktív webszolgáltatás frissítésére használhat.
- A **prediktív webszolgáltatás** frissítési **erőforrás-végpontjának** meghívásával frissítse a webszolgáltatást az újonnan betanított modellel a webszolgáltatás **frissítése tevékenységgel** .

## <a name="azure-machine-learning-linked-service"></a>Társított szolgáltatás Azure Machine Learning

A fent említett végpontok közötti munkafolyamat működéséhez két Azure Machine Learning társított szolgáltatást kell létrehoznia:

1. Egy Azure Machine Learning társított szolgáltatás a betanítási webszolgáltatáshoz, ezt a társított szolgáltatást a Batch-végrehajtási tevékenységek ugyanúgy használják, mint a [prediktív folyamatok létrehozása Azure Machine learning és Data Factory kötegelt végrehajtás használatával. tevékenység](transform-data-using-machine-learning.md). Különbség a betanítási webszolgáltatás kimenete egy olyan iLearner-fájl, amelyet a rendszer a prediktív webszolgáltatás frissítéséhez használ a frissítési erőforrás tevékenységgel.
2. Egy Azure Machine Learning társított szolgáltatás a prediktív webszolgáltatás frissítési erőforrás-végpontján. Ez a társított szolgáltatás az erőforrás frissítése tevékenység használatával frissíti a prediktív webszolgáltatást a fenti lépésből visszaadott iLearner-fájllal.

A második Azure Machine Learning társított szolgáltatás esetében a konfiguráció eltérő, ha a Azure Machine Learning webszolgáltatása egy klasszikus webszolgáltatás vagy egy új webszolgáltatás. A különbségeket az alábbi szakaszokban külön tárgyaljuk.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>A webszolgáltatás új Azure Resource Manager webszolgáltatás

Ha a webszolgáltatás az új típusú webszolgáltatás, amely egy Azure Resource Manager-végpontot tesz elérhetővé, nem kell hozzáadnia a második **nem alapértelmezett** végpontot. A társított szolgáltatás **updateResourceEndpoint** formátuma:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

A webszolgáltatások Azure Machine Learning webszolgáltatások [portálján](https://services.azureml.net/)található webszolgáltatás lekérdezése során az URL-címekre vonatkozó értékeket is lekérheti.

Az új típusú frissítési erőforrás-végponthoz egyszerű szolgáltatásnév-hitelesítés szükséges. Az egyszerű szolgáltatás hitelesítésének használatához regisztráljon egy alkalmazás-entitást Azure Active Directory (Azure AD-ben), és adja meg az előfizetés **közreműködői** vagy **tulajdonosi** szerepkörét, vagy azt az erőforráscsoportot, amelyben a webszolgáltatás a webszolgáltatáshoz tartozik. Lásd: [egyszerű szolgáltatásnév létrehozása és engedélyek kiosztása az Azure-erőforrások kezeléséhez](../active-directory/develop/howto-create-service-principal-portal.md). Jegyezze fel a következő értékeket, amelyeket a társított szolgáltatás definiálásához használ:

- Alkalmazásazonosító
- Alkalmazás kulcsa
- Bérlőazonosító

Itt látható egy példa társított szolgáltatás definíciója:

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

A következő forgatókönyv további részleteket tartalmaz. A Azure Machine Learning Studio-modellek átképzésére és frissítésére például egy Azure Data Factory folyamatból.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Minta: Azure Machine Learning modell átképzése és frissítése

Ez a szakasz egy minta folyamatot biztosít, amely a **Azure Machine learning Studio batch végrehajtási tevékenységét** használja a modell újratanításához. A folyamat a **Azure Machine learning Studio Update erőforrás-tevékenységgel** is frissíti a modellt a pontozási webszolgáltatás szolgáltatásban. A szakasz a példában szereplő társított szolgáltatások, adatkészletek és folyamatok JSON-kódrészleteit is tartalmazza.

### <a name="azure-blob-storage-linked-service"></a>Azure Blob Storage társított szolgáltatás:
Az Azure Storage a következő adatkészleteket tartalmazza:

* betanítási adatgyűjtés. A Azure Machine Learning Studio betanítási webszolgáltatás bemeneti adata.
* iLearner-fájl. A Azure Machine Learning Studio Training web szolgáltatás kimenete. Ez a fájl a frissítési erőforrás tevékenységének bemenete is.

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

### <a name="linked-service-for-azure-machine-learning-studio-training-endpoint"></a>Társított szolgáltatás a Azure Machine Learning Studio betanítási végpontja számára
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

A **Azure Machine learning Studióban**tegye a következőket a **MlEndpoint** és a **apiKey**értékének beolvasásához:

1. A bal oldali menüben kattintson a **Web Services** elemre.
2. A webszolgáltatások listájában kattintson a **betanítási webszolgáltatás** elemre.
3. Kattintson az API- **kulcs** szövegmező melletti Másolás lehetőségre. Illessze be a vágólapra a kulcsot a Data Factory JSON-szerkesztőbe.
4. A **Azure Machine learning Studióban**kattintson a **Batch-végrehajtási** hivatkozás elemre.
5. Másolja a kérési **URI** -t a **kérelem** szakaszból, és ILLESSZE be a Data Factory JSON-szerkesztőbe.

### <a name="linked-service-for-azure-machine-learning-studio-updatable-scoring-endpoint"></a>Társított szolgáltatás Azure Machine Learning Studio frissíthető pontozási végponthoz:
A következő JSON-kódrészlet egy Azure Machine Learning társított szolgáltatást határoz meg, amely a pontozási webszolgáltatás frissíthető végpontján mutat.

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
A folyamat két tevékenységgel rendelkezik: **AzureMLBatchExecution** és **AzureMLUpdateResource**. A kötegelt végrehajtási tevékenység bemenetként veszi át a betanítási adatokat, és kimenetként létrehoz egy iLearner-fájlt. Ezután az erőforrás frissítése tevékenység elvégzi ezt a iLearner-fájlt, és ezzel frissíti a prediktív webszolgáltatást.

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
A következő cikkekből megtudhatja, hogyan alakíthat át más módon az adatátalakítást:

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Struktúra tevékenysége](transform-data-using-hadoop-hive.md)
* [Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-tevékenység](transform-data-using-hadoop-streaming.md)
* [Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)

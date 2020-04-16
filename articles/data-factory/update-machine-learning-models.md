---
title: Gépi tanulási modellek frissítése az Azure Data Factory használatával
description: Bemutatja, hogyan hozhat létre prediktív folyamatokat az Azure Data Factory és a gépi tanulás használatával
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
ms.openlocfilehash: 4488c174ba5ff35ec2709d7c1b9f3093b4ee90a3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409078"
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>Az Azure Machine Learning-modellek frissítése az Update-erőforrástevékenység használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk kiegészíti az Azure Data Factory – Azure Machine Learning integrációs cikket: [Prediktív folyamatok létrehozása az Azure Machine Learning és az Azure Data Factory használatával.](transform-data-using-machine-learning.md) Ha még nem tette meg, olvassa el a cikket, mielőtt elolvasná ezt a cikket.

## <a name="overview"></a>Áttekintés
Az Azure Machine Learning-modellek üzembe mentési folyamatának részeként a modell betanítása és mentése. Ezután prediktív webszolgáltatás létrehozásához használja. A webszolgáltatás ezután felhasználható webhelyeken, irányítópultokon és mobilalkalmazásokban.

A Machine Learning használatával létrehozott modellek általában nem statikusak. Ahogy új adatok válnak elérhetővé, vagy ha az API-fogyasztó saját adatokkal rendelkezik, a modellt újra be kell tanítani. A [Machine Learning-modell újratanítása a machine learning-modell újratanítása](../machine-learning/machine-learning-retrain-machine-learning-model.md) a modell újratanítása az Azure Machine Learningben.

Gyakran előfordulhat átképzés. A batch-végrehajtási tevékenység és az erőforrás-frissítési tevékenység, az Azure Machine Learning modell átképzése és frissítése a prediktív webszolgáltatás a Data Factory használatával.

Az alábbi képen a betanítás és a prediktív webszolgáltatások közötti kapcsolat látható.

![Webszolgáltatások](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Az Azure Machine Learning frissítési erőforrás-tevékenysége

A következő JSON-kódrészlet egy Azure Machine Learning batch-végrehajtási tevékenységet határoz meg.

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
| név                          | A folyamatban lévő tevékenység neve     | Igen      |
| leírás                   | A tevékenység tevékenységét leíró szöveg.  | Nem       |
| type                          | Az Azure Machine Learning Update Resource tevékenység esetén a tevékenység típusa **Az AzureMLUpdateResource.** | Igen      |
| linkedServiceName             | Az UpdateResourceEndpoint tulajdonságot tartalmazó Azure Machine Learning-kapcsolt szolgáltatás. | Igen      |
| trainedModelName              | A webszolgáltatás-kísérletbe betanított modellmodul neve, amelyfrissítendő | Igen      |
| trainedModelLinkedServiceName | A frissítési művelet által feltöltött ilearner-fájlt tároló Azure Storage-kapcsolt szolgáltatás neve | Igen      |
| trainedModelFilePath          | A relatív fájl elérési útja a trainedModelLinkedService-ben, amely a frissítési művelet által feltöltött ilearner fájlt ábrázolja | Igen      |

## <a name="end-to-end-workflow"></a>Teljes körű munkafolyamat

A modell átképzésének és a prediktív webszolgáltatások frissítésének teljes folyamata a következő lépésekkel jár:

- Hívja meg a **betanítási webszolgáltatást** a **Kötegelt végrehajtás tevékenység**használatával. A betanítási webszolgáltatás meghívása megegyezik az Azure Machine Learning és a Data Factory Batch Execution tevékenység használatával végzett [prediktív](transform-data-using-machine-learning.md)webszolgáltatás meghívásával. A betanítási webszolgáltatás kimenete egy iLearner fájl, amely a prediktív webszolgáltatás frissítéséhez használható.
- A **prediktív webszolgáltatás frissítési** **erőforrás-végpontjának** meghívása az **Erőforrás frissítése tevékenység** használatával frissítse a webszolgáltatást az újonnan betanított modellel.

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning csatolt szolgáltatás

Ahhoz, hogy a fent említett teljes körű munkafolyamat működjön, két Azure Machine Learning-hez kapcsolódó szolgáltatást kell létrehoznia:

1. Az Azure Machine Learning-alapú szolgáltatás a képzési webszolgáltatás, ez a csatolt szolgáltatás által használt batch-végrehajtási tevékenység ugyanúgy, mint ami az [Azure Machine Learning és a Data Factory Batch-végrehajtási tevékenység használatával prediktív folyamatok létrehozása](transform-data-using-machine-learning.md)című szakaszban említettek. Különbség a kimeneta a betanítási webszolgáltatás egy iLearner fájlt, amely et majd az Erőforrás frissítése tevékenység a prediktív webszolgáltatás frissítéséhez.
2. Egy Azure Machine Learning kapcsolódó szolgáltatás a prediktív webszolgáltatás frissítési erőforrás-végpontjával. Ezt a csatolt szolgáltatást az Erőforrás frissítése tevékenység használja a prediktív webszolgáltatás frissítéséhez a fenti lépésből visszaadott iLearner fájl használatával.

A második Azure Machine Learning-alapú szolgáltatás esetében a konfiguráció eltérő, ha az Azure Machine Learning webszolgáltatás egy klasszikus webszolgáltatás vagy egy új webszolgáltatás. A különbségeket a következő szakaszok külön tárgyalják.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>A webszolgáltatás az Azure Resource Manager új webszolgáltatása

Ha a webszolgáltatás az azure Resource Manager-végpontot elérhetővé t, új típusú webszolgáltatás, nem kell hozzáadnia a második **nem alapértelmezett** végpontot. A csatolt szolgáltatás **updateResourceEndpoint** formátuma:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

Az [Azure Machine Learning Web Services Portal](https://services.azureml.net/)webszolgáltatásban a webszolgáltatás lekérdezéseke során lekaphatja az URL-címben lévő helyőrzők értékeit.

Az új típusú frissítési erőforrás-végpont egyszerű szolgáltatáshitelesítést igényel. Az egyszerű szolgáltatás hitelesítésének használatához regisztráljon egy alkalmazásentitást az Azure Active Directoryban (Azure AD), és adja meg az előfizetés vagy az erőforráscsoport **közreműködői** vagy **tulajdonosi** szerepkörét, ahová a webszolgáltatás tartozik. A [Tekintse meg, hogyan hozhat létre egyszerű szolgáltatást, és hogyan rendelhet engedélyeket az Azure-erőforrás kezeléséhez.](../active-directory/develop/howto-create-service-principal-portal.md) Jegyezze fel a következő értékeket, amelyek segítségével definiálja a csatolt szolgáltatást:

- Alkalmazásazonosító
- Alkalmazáskulcs
- Bérlőazonosító

Itt van egy minta összekapcsolt szolgáltatás definíció:

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

A következő forgatókönyv további részleteket tartalmaz. Egy példa az Azure Machine Learning stúdió modellek átképzése és frissítése egy Azure Data Factory-folyamat.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Minta: Átképzés és frissítés egy Azure Machine Learning-modell

Ez a szakasz egy mintafolyamatot biztosít, amely az **Azure Machine Learning studio batch-végrehajtási tevékenységet** használ egy modell újratanításához. A folyamat is használja az **Azure Machine Learning studio Update Resource tevékenység** a modell frissítése a pontozási webszolgáltatásban. A szakasz json-kódrészleteket is biztosít a példában az összes csatolt szolgáltatáshoz, adatkészlethez és folyamathoz.

### <a name="azure-blob-storage-linked-service"></a>Azure Blob storage-hoz csatolt szolgáltatás:
Az Azure Storage a következő adatokat tartalmazza:

* betanítási adatok. Az Azure Machine Learning studio webes betanítási webszolgáltatás bemeneti adatai.
* iLearner fájlt. Az Azure Machine Learning stúdió webes betanítási webszolgáltatáskimenete. Ez a fájl egyben az Erőforrás frissítése tevékenység bemenete is.

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

### <a name="linked-service-for-azure-machine-learning-studio-training-endpoint"></a>Csatolt szolgáltatás az Azure Machine Learning stúdió képzési végpontjához
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

Az **Azure Machine Learning-stúdióban**az **mlEndpoint** és **apiKey**értékek lehívásához tegye a következőket:

1. Kattintson a bal oldali menü **WEB SERVICES** parancsára.
2. Kattintson a **webes szolgáltatások betanítási szolgáltatására** a webszolgáltatások listájában.
3. Kattintson a Másolás elemre az **API-kulcs** szövegmezője mellett. Illessze be a kulcsot a vágólapra a Data Factory JSON szerkesztőbe.
4. Az **Azure Machine Learning stúdióban**kattintson a **BATCH EXECUTION** hivatkozásra.
5. Másolja a **kérelem URI-t** a **Kérelem** szakaszból, és illessze be a Data Factory JSON-szerkesztőbe.

### <a name="linked-service-for-azure-machine-learning-studio-updatable-scoring-endpoint"></a>Csatolt szolgáltatás az Azure Machine Learning stúdió updatable pontozási végpont:
A következő JSON-kódrészlet egy Azure Machine Learning-alapú szolgáltatást határoz meg, amely a pontozási webszolgáltatás updatable végpontjára mutat.

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
A folyamat két tevékenységet kínál: **AzureMLBatchExecution** és **AzureMLUpdateResource.** A batch-végrehajtási tevékenység a betanítási adatokat bemenetként veszi fel, és egy iLearner-fájlt hoz létre kimenetként. Az Erőforrás frissítése tevékenység ezután ezt az iLearner fájlt használja a prediktív webszolgáltatás frissítéséhez.

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
Az alábbi cikkekben elmagyarázhatja, hogyan alakíthatja át más módon az adatokat:

* [U-SQL tevékenység](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [Sertésaktivitás](transform-data-using-hadoop-pig.md)
* [MapReduce tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming tevékenység](transform-data-using-hadoop-streaming.md)
* [Szikraaktivitás](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)

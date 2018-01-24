---
title: "Machine learning modellek Azure Data Factory használatával módosítása |} Microsoft Docs"
description: "Ismerteti, hogyan hozzon létre Azure Data Factory használatával prediktív folyamatok létrehozása és a gépi tanulás"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: d4f7df5ceadf8c6219901af6e516453a88aeece7
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>Azure Machine Learning modellek frissítés erőforrás tevékenységgel módosítása
Ez a cikk kiegészíti a fő Azure Data Factory - Azure Machine Learning integrációs cikk: [létrehozása az Azure Machine Learning és az Azure Data Factory használatával prediktív folyamatok](transform-data-using-machine-learning.md). Ha még nem tette meg, tekintse át a fő cikk keresztül ez a cikk elolvasása előtt. 

## <a name="overview"></a>Áttekintés
Az Azure Machine Learning modellek végrehajtott folyamat részeként a modell betanítása és mentve. Majd a használatával hozzon létre egy predicative webszolgáltatás-bővítmény. A webszolgáltatás majd használni képes a webhelyek, az irányítópultok és a mobile apps szolgáltatásban.

Használata a Machine Learning modellek jellemzően nem statikus. Amint az elérhetővé válik az új adatok, vagy ha a fogyasztó API rendelkezik saját adataikat a modellnek támogatnia kell a retrained lehet. Tekintse meg [a gépi tanulási modellek újratanítása](../machine-learning/machine-learning-retrain-machine-learning-model.md) hogyan is működik az Azure Machine Learning modell vonatkozó további információért. 

Átképezési fordulhat elő, gyakran. A kötegelt végrehajtási tevékenység és a frissítés erőforrástevékenység üzembe az Azure Machine Learning modell átképezési, és a Data Factory használatával prediktív webszolgáltatás frissítése. 

A következő kép ábrázolja a képzés és prediktív webszolgáltatások közötti kapcsolat. 

![Webszolgáltatások](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Az Azure Machine Learning update-erőforrástevékenység 

A következő JSON-részlet Azure Machine Learning kötegelt végrehajtási tevékenység határozza meg.

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
| név                          | A tevékenység a folyamat neve.     | Igen      |
| leírás                   | A tevékenység mit leíró szöveg.  | Nem       |
| type                          | Az Azure Machine Learning frissítés erőforrás tevékenység, a tevékenység típusa nem **AzureMLUpdateResource**. | Igen      |
| linkedServiceName             | Az Azure Machine Learning kapcsolódó szolgáltatás, amely updateResourceEndpoint tulajdonság tartalmazza. | Igen      |
| trainedModelName              | A webszolgáltatási kísérletben frissíteni kell a Trained Model-modul nevét | Igen      |
| trainedModelLinkedServiceName | A frissítési művelet által feltöltött ilearner-fájlt tároló Azure Storage társított szolgáltatás neve | Igen      |
| trainedModelFilePath          | A frissítési művelet által feltöltött ilearner-fájlt képviselő trainedModelLinkedService relatív elérési út | Igen      |


## <a name="end-to-end-workflow"></a>Teljes körű munkafolyamat

A modell és a frissítés a prediktív webszolgáltatások átképezési végrehajtott teljes folyamata a következő lépésekből áll: 

- Hívása a **webszolgáltatás betanítása** használatával a **kötegelt végrehajtási tevékenység**. A képzési webszolgáltatás meghívása megegyezik egy prediktív ismertetett webszolgáltatás indítására [létrehozása az Azure Machine Learning és a Data Factory kötegelt végrehajtási tevékenység prediktív folyamatok](transform-data-using-machine-learning.md). A képzés webszolgáltatás eredménye egy iLearner-fájlt, a prediktív webes szolgáltatásnak a frissítésére használhat. 
- Meghívása a **erőforrás végpontjának frissítése** , a **prediktív webszolgáltatás** használatával a **frissítése erőforrástevékenység** frissítheti a webszolgáltatás a újonnan betanított modell. 

## <a name="azure-machine-learning-linked-service"></a>Az Azure Machine Learning társított szolgáltatás

A fent említett végpontok közötti munkafolyamat működéséhez két Azure Machine Learning társított szolgáltatások létrehozásához szüksége: 

1. Az Azure Machine Learning a társított szolgáltatás, a képzési webszolgáltatás, szolgáltatásnak használják kötegelt végrehajtási tevékenység mit szerepel azonos módon [létrehozása az Azure Machine Learning és a Data Factory kötegelt prediktív folyamatok Végrehajtási tevékenység](transform-data-using-machine-learning.md). A különbség a képzés webszolgáltatás eredménye egy iLearner-fájlt, amely majd erőforrás módosítása tevékenység által frissítésére szolgál a prediktív webszolgáltatáshoz. 
2. Az Azure Machine Learning a prediktív webszolgáltatás a frissítés erőforrás végponthoz társított szolgáltatás. A társított szolgáltatás segítségével erőforrás módosítása tevékenység által frissítése a prediktív webszolgáltatás promptjai lépés visszaadott iLearner-fájlt. 

A második csatolt Azure Machine Learning szolgáltatáshoz a konfiguráció esetén különböző az Azure Machine Learning webszolgáltatásba egy klasszikus webszolgáltatás-bővítmény vagy egy új webszolgáltatás-bővítmény. A különbségeket az alábbi szakaszok külön esik szó. 

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Webszolgáltatás az új Azure Resource Manager webszolgáltatás 

Ha a webszolgáltatás egy webszolgáltatás, amely elérhetővé teszi az Azure Resource Manager-végpont új típusú, nem kell hozzáadnia a második **nem alapértelmezett** végpont. A **updateResourceEndpoint** formátumban van a hivatkozott szolgáltatásban található: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Kaphat értékek hely tartozó felhasználók számára az URL-címben a webkiszolgáló lekérdezésekor a [Azure Machine Learning Web Services portálra](https://services.azureml.net/). 

Az új típusú frissítés erőforrás végpont szolgáltatás egyszerű hitelesítés szükséges. Szolgáltatás egyszerű hitelesítést használ, egy alkalmazás entitás regisztrálni kell az Azure Active Directory (Azure AD), és adja meg azt a **közreműködő** vagy **tulajdonos** szerepkört az előfizetés vagy az erőforrás csoport helye a webszolgáltatás tartozik. A részletek [egyszerű szolgáltatásnév létrehozása és hozzárendelése az Azure erőforrások kezeléséhez szükséges jogokat](../azure-resource-manager/resource-group-create-service-principal-portal.md). Jegyezze fel a következő érték, melynek segítségével határozza meg a társított szolgáltatás:

- Alkalmazásazonosító
- Alkalmazás kulcs 
- Bérlőazonosító

Íme egy minta kapcsolódó szolgáltatásdefiníció: 

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
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. ",
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

Az alábbi forgatókönyvet további részleteket tartalmaz. Rendelkezik egy példa átképezési, és az Azure Data Factory-folyamat az Azure ML modellek frissítéséhez.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Példa: Átképezési, és az Azure Machine Learning modell frissítése

Ez a témakör egy minta folyamatot, amely használja a **Azure ML kötegelt végrehajtási tevékenység** a modell működik. A folyamatot is alkalmaz a **Azure ML Update erőforrástevékenység** az pontozási webszolgáltatás a modell frissítése. A szakasz is biztosít JSON kódtöredékek az összekapcsolt szolgáltatások, adatkészleteket és a példában szereplő folyamat.

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
Az alábbi JSON kódrészletet az Azure Machine Learning társított szolgáltatást az pontozási webszolgáltatás frissíthető végpontra mutató határozza meg.  

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
A folyamat két tevékenység rendelkezik: **AzureMLBatchExecution** és **AzureMLUpdateResource**. A kötegelt végrehajtási tevékenység lekéri a tanítási adatokat bemeneti adatként, és egy kimenetként iLearner-fájlt hoz létre. A frissítés erőforrástevékenység majd időt vesz igénybe a iLearner-fájlt, és frissítheti a prediktív webszolgáltatás. 

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
Tekintse meg a következő cikkekben talál, amely ismerteti az adatok más módon: 

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [A Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce művelethez](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streamelési tevékenységben](transform-data-using-hadoop-streaming.md)
* [A Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [A tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)

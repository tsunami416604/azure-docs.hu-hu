---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: f500df6a592769928470d22468ff2fdff18293a5
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88748857"
---
A dokumentumban szereplő bejegyzések a `inferenceconfig.json` [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) osztály paramétereinek felelnek meg. A következő táblázat ismerteti a JSON-dokumentum entitásai közötti leképezést, valamint a metódus paramétereit:

| JSON-entitás | Metódus paramétere | Leírás |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | A rendszerképhez futtatandó kódot tartalmazó helyi fájl elérési útja. |
| `sourceDirectory` | `source_directory` | Választható. Az összes fájlt tartalmazó mappák elérési útja a rendszerkép létrehozásához, amely megkönnyíti a mappában vagy almappában található fájlok elérését. A webszolgáltatások függőségeiként feltölthet egy teljes mappát a helyi gépről. Megjegyzés: a entry_script, a conda_file és a extra_docker_file_steps útvonalak relatív elérési utak az source_directory elérési úthoz. |
| `environment` | `environment` | Választható.  Azure Machine Learning [környezet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py).|

Azure Machine Learning [környezet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) teljes specifikációit a következtetési konfigurációs fájlban is megadhatja. Ha ez a környezet nem létezik a munkaterületen, akkor a Azure Machine Learning létrehozza azt. Ellenkező esetben a Azure Machine Learning szükség esetén frissíti a környezetet. A következő JSON egy példa:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn==0.22.1",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Meglévő Azure Machine Learning- [környezetet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) is HASZNÁLHAT külön CLI-paraméterekben, és eltávolíthatja a "környezet" kulcsot a következtetési konfigurációs fájlból. Használja az-e nevet a környezet neveként, a--EV pedig a környezet verzióját. Ha nem ad meg--EV-t, a rendszer a legújabb verziót fogja használni. Íme egy példa a következtetési konfigurációs fájlra:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

A következő parancs bemutatja, hogyan helyezhet üzembe egy modellt a korábbi következtetési konfigurációs fájl (myInferenceConfig.json) használatával. 

Egy meglévő Azure Machine Learning- [környezet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) legújabb verzióját is használja (AzureML-minimal néven).

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```

---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 5102e8f75da14c58e948e81aaa418539dd18869a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159411"
---
A dokumentum `inferenceconfig.json` bejegyzései leképezik az [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) osztály paramétereit. Az alábbi táblázat a JSON-dokumentumban szereplő entitások és a módszer paraméterei közötti leképezést ismerteti:

| JSON entitás | Metódus paramétere | Leírás |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | A lemezképhez futtatandó kódot tartalmazó helyi fájl elérési útja. |
| `sourceDirectory` | `source_directory` | Választható. A kép létrehozásához az összes fájlt tartalmazó mappák elérési útja, amely megkönnyíti a mappában vagy almappában lévő fájlok elérését. A webszolgáltatás függőségeként feltölthet egy teljes mappát a helyi számítógépről. Megjegyzés: a entry_script, conda_file és extra_docker_file_steps elérési utak a source_directory elérési úthoz viszonyított görbék. |
| `environment` | `environment` | Választható.  Azure Machine Learning [környezetben.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)|

Az Azure Machine [Learning-környezet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) teljes specifikációit a következtetés konfigurációs fájlban is felveheti. Ha ez a környezet nem létezik a munkaterületen, az Azure Machine Learning létrehozza azt. Ellenkező esetben az Azure Machine Learning szükség esetén frissíti a környezetet. A következő JSON egy példa:

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
                            "scikit-learn",
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

Egy meglévő Azure Machine [Learning-környezetben](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) is használhatja a leválasztott CLI-paramétereket, és eltávolíthatja a "környezet" kulcsot a következtetés konfigurációs fájlból. Használja az -e a környezet nevét, és --ev a környezeti verzió. Ha nem adja meg a --ev értéket, a legújabb verzió lesz használva. Íme egy példa egy következtetés konfigurációs fájl:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

A következő parancs bemutatja, hogyan telepíthet egy modellt az előző következtetéskonfigurációs fájl (myInferenceConfig.json) használatával. 

Egy meglévő Azure Machine [Learning-környezet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) (AzureML-Minimal) legújabb verzióját is használja.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```

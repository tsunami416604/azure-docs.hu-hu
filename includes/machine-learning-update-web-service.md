---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: f1eee95cf35b831fc2a213044d700fd5afbdfc96
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997952"
---
A web service frissítéséhez használja a `update` metódust. Frissítheti a webszolgáltatást egy olyan új modell, bejegyzési parancsfájl vagy függőség használatára, amely megadható egy következtetési konfiguráció használatával. További információkért lásd a webszolgáltatások [frissítését](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-)ismertető témakört.

> [!IMPORTANT]
> A modell új verziójának létrehozásakor manuálisan kell frissítenie az összes használni kívánt szolgáltatást.

**Az SDK használata**

A következő kód bemutatja, hogyan használható az SDK a webszolgáltatások modelljének, környezetének és bejegyzési parancsfájljának frissítéséhez:

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# register new model
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s)
service.update(models=[new_model], inference_config=inference_config)
print(service.state)
print(service.get_logs())
```

**A parancssori felület használata**

A webszolgáltatásokat az ML CLI használatával is frissítheti. Az alábbi példa bemutatja egy új modell regisztrálását, majd a webszolgáltatás frissítését az új modell használatára:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> Ebben a példában egy JSON-dokumentum segítségével továbbítja a modell adatait a regisztrációs parancsból az Update parancsba.
>
> Ha a szolgáltatást új bejegyzési parancsfájl vagy környezet használatára szeretné frissíteni, hozzon létre egy [következtetési konfigurációs fájlt](/azure/machine-learning/service/reference-azure-machine-learning-cli#inference-configuration-schema) , és adja meg `ic` a paramétert.

További információ: az [ml Service Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update) referenciája.
---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: d36bf2db05113656a77e76ff900d95910f313c73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477255"
---
Webszolgáltatás frissítéséhez használja `update` a módszert. Frissítheti a webszolgáltatást, hogy egy új modellt, egy új bejegyzésparancsfájlt vagy egy következtetési konfigurációban megadható új függőségeket használjon. További információt a [Webservice.update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-)dokumentumában talál.

> [!IMPORTANT]
> Amikor egy modell új verzióját hozza létre, manuálisan kell frissítenie a használni kívánt szolgáltatást.
>
> Az SDK nem használható az Azure Machine Learning-tervezőáltal közzétett webszolgáltatás frissítéséhez.

**Az SDK használata**

A következő kód bemutatja, hogyan lehet az SDK segítségével frissíteni a modell, a környezet és a bejegyzés parancsfájl egy webszolgáltatás:

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
print(service.state)
print(service.get_logs())
```

**A CLI használata**

A webszolgáltatást az ML CLI használatával is frissítheti. A következő példa bemutatja egy új modell regisztrálását, majd egy webszolgáltatás frissítését az új modell használatához:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> Ebben a példában egy JSON-dokumentum segítségével adja át a modell adatait a regisztrációs parancsból a frissítési parancsba.
>
> Ha frissíteni szeretné a szolgáltatást új bejegyzésparancsfájl vagy környezet használatára, hozzon `ic` létre egy [következtetéskonfigurációs fájlt,](/azure/machine-learning/service/reference-azure-machine-learning-cli#inference-configuration-schema) és adja meg azt a paraméterrel.

További információt az [az ml szolgáltatásfrissítésdokumentációjában](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update) talál.
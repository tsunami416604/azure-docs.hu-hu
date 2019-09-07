---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: 11cc93e0eb11d0422fae4fbbbf0a549df067daca
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390607"
---
Webszolgáltatások frissítéséhez használja a `update` metódust. Frissítheti a webszolgáltatást egy új modell, egy új bejegyzési parancsfájl vagy egy következtetési konfigurációban megadható új függőségek használatára. További információkért tekintse meg a [webszolgáltatások dokumentációját. frissítés](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-).

> [!IMPORTANT]
> A modell új verziójának létrehozásakor manuálisan kell frissítenie az összes használni kívánt szolgáltatást.

**Az SDK használata**

A következő kód bemutatja, hogyan használható az SDK a webszolgáltatások modelljének, környezetének és bejegyzési parancsfájljának frissítéséhez:

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

**A parancssori felület használata**

A webszolgáltatás a ML parancssori felület használatával is frissíthető. Az alábbi példa bemutatja egy új modell regisztrálását, majd egy webszolgáltatás frissítését az új modell használatára:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> Ebben a példában egy JSON-dokumentum segítségével továbbítja a modell adatait a regisztrációs parancsból az Update parancsba.
>
> Ha a szolgáltatást új bejegyzési parancsfájl vagy környezet használatára szeretné frissíteni, hozzon létre egy [következtetési konfigurációs fájlt](/azure/machine-learning/service/reference-azure-machine-learning-cli#inference-configuration-schema) , és adja meg `ic` a paramétert.

További információt az az [ml Service Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update) dokumentációjában talál.
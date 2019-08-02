---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: fef6225812980900ad55944644310433a6105de2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68556866"
---
Új modell létrehozásakor manuálisan kell frissítenie az új modellt használni kívánt szolgáltatásokat. A web service frissítéséhez használja a `update` metódust. A következő kód bemutatja, hogyan frissítheti a webszolgáltatások modelljét az SDK használatával:

```python
from azureml.core.webservice import Webservice
from azureml.core.model import Model

# register new model
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name=service_name, workspace=ws)

# Update to new model(s)
service.update(models=[new_model])
print(service.state)
print(service.get_logs())
```

A webszolgáltatásokat az ML CLI használatával is frissítheti. Az alábbi példa bemutatja egy új modell regisztrálását, majd a webszolgáltatás frissítését az új modell használatára:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> Ebben a példában egy JSON-dokumentum segítségével továbbítja a modell adatait a regisztrációs parancsból az Update parancsba.
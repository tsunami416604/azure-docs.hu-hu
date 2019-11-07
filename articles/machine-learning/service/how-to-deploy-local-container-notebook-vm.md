---
title: Modellek üzembe helyezése notebook virtuális gépeken
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan helyezheti üzembe a Azure Machine Learning modelleket webszolgáltatásként notebook-alapú virtuális gépek használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 10/25/2019
ms.openlocfilehash: d4e37b02b3d7a21546a04c8948fbbfb7262bfa6a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584771"
---
# <a name="deploy-a-model-to-azure-machine-learning-notebook-vms"></a>Modell üzembe helyezése Azure Machine Learning notebook virtuális gépeken

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ismerje meg, hogyan helyezhet üzembe egy modellt webszolgáltatásként a Azure Machine Learning notebook virtuális gépen a Azure Machine Learning használatával. Használjon notebookos virtuális gépeket, ha az alábbi feltételek egyike teljesül:

- Gyorsan üzembe kell helyeznie és ellenőriznie kell a modellt.
- A fejlesztés alatt álló modellt tesztel.

> [!TIP]
> Egy modell üzembe helyezése egy jegyzetfüzet-alapú virtuális gépen lévő Jupyter Notebookról egy _helyi üzembe helyezést_nyújtó webszolgáltatásra. Ebben az esetben a "helyi" számítógép a notebook virtuális gép. További információ a központi telepítésekről: [modellek üzembe helyezése a Azure Machine Learningsal](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Előfeltételek

- Azure Machine Learning munkaterület, amelyen fut egy jegyzetfüzet virtuális gép. További információ: [telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-notebook-vms"></a>Üzembe helyezés a notebook virtuális gépeken

A notebook virtuális gépe tartalmaz egy példaként szolgáló jegyzetfüzetet, amely bemutatja a helyi központi telepítéseket. A következő lépésekkel töltse be a jegyzetfüzetet, és telepítse a modellt webszolgáltatásként a virtuális gépen:

1. [Azure Machine learning Studióban](https://ml.azure.com)válassza ki a Azure Machine learning notebook virtuális gépeket.

1. Nyissa meg a `samples-*` alkönyvtárat, majd nyissa meg a `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`. A Megnyitás után futtassa a jegyzetfüzetet.

    ![Képernyőkép a futó helyi szolgáltatásról a notebookon](media/how-to-deploy-local-container-notebookvm/deploy-local-service.png)

1. A jegyzetfüzet megjeleníti azt az URL-címet és portot, amelyen a szolgáltatás fut. Például: `https://localhost:6789`. A port megjelenítéséhez `print('Local service port: {}'.format(local_service.port))` tartalmazó cellát is futtathatja.

    ![Képernyőkép a futó helyi szolgáltatás portról](media/how-to-deploy-local-container-notebookvm/deploy-local-service-port.png)

1. A szolgáltatás notebook virtuális gépről való teszteléséhez használja a `https://localhost:<local_service.port>` URL-címet. Távoli ügyfélről történő teszteléshez szerezze be a notebook virtuális gépen futó szolgáltatás nyilvános URL-címét. A nyilvános URL-cím a következő képlet használatával határozható meg: `https://<notebookvm_name>-<local_service_port>.<azure_region_of_notebook>.notebooks.azureml.net/score`. Például: `https://mynotebookvm-6789.eastus2.notebooks.azureml.net/score`.

## <a name="test-the-service"></a>A szolgáltatás tesztelése

Mintaadatok küldéséhez a futó szolgáltatáshoz használja a következő kódot. A `service_url` értékét cserélje le az előző lépés URL-címére:

```python
import requests
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')
access_token = "your bearer token"
headers = {'Content-Type':'application/json', 'Authorization': 'Bearer ' + access_token}
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>További lépések

* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [Üzembe helyezés hibaelhárítása](how-to-troubleshoot-deployment.md)
* [Biztonságos Azure Machine Learning webszolgáltatások SSL használatával](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett ML-modell felhasználása](how-to-consume-web-service.md)
* [A Azure Machine Learning modellek monitorozása a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelési modellekhez](how-to-enable-data-collection.md)
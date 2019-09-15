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
ms.date: 08/08/2019
ms.openlocfilehash: 046f998038c47a48a8528bf36d87ac836395eec2
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002829"
---
# <a name="deploy-a-model-to-notebook-vms"></a>Modell üzembe helyezése notebook virtuális gépeken

Ismerje meg, hogyan helyezhet üzembe egy modellt webszolgáltatásként a notebook virtuális gépén a Azure Machine Learning használatával. Használjon notebookos virtuális gépeket, ha az alábbi feltételek egyike teljesül:

- Gyors üzembe helyezése és a modell érvényesítése kell.
- A tesztelt egy olyan modell, fejlesztés alatt áll.

> [!TIP]
> Egy modell üzembe helyezése egy jegyzetfüzet-alapú virtuális gépen lévő Jupyter Notebookról egy _helyi üzembe helyezést_nyújtó webszolgáltatásra. Ebben az esetben a "helyi" számítógép a notebook virtuális gép. További információ a központi telepítésekről: [modellek üzembe helyezése a Azure Machine Learningsal](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Előfeltételek

- Azure Machine Learning munkaterület, amelyen fut egy jegyzetfüzet virtuális gép. További információ: [telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-notebook-vms"></a>Üzembe helyezés a notebook virtuális gépeken

A notebook virtuális gépe tartalmaz egy példaként szolgáló jegyzetfüzetet, amely bemutatja a helyi központi telepítéseket. A következő lépésekkel töltse be a jegyzetfüzetet, és telepítse a modellt webszolgáltatásként a virtuális gépen:

1. A [Azure Portal](https://portal.azure.com)válassza ki a Azure Machine learning notebook virtuális gépeket.

1. Nyissa `samples-*` meg az alkönyvtárat, `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`majd nyissa meg a t. A Megnyitás után futtassa a jegyzetfüzetet.

    ![Képernyőkép a futó helyi szolgáltatásról a notebookon](media/how-to-deploy-local-container-notebookvm/deploy-local-service.png)

1. A jegyzetfüzet megjeleníti azt az URL-címet és portot, amelyen a szolgáltatás fut. Például: `https://localhost:6789`. A portot tartalmazó `print('Local service port: {}'.format(local_service.port))` cellát is futtathatja a port megjelenítéséhez.

    ![Képernyőkép a futó helyi szolgáltatás portról](media/how-to-deploy-local-container-notebookvm/deploy-local-service-port.png)

1. A szolgáltatás notebook virtuális gépről való teszteléséhez használja az `https://localhost:<local_service.port>` URL-címet. Távoli ügyfélről történő teszteléshez szerezze be a notebook virtuális gépen futó szolgáltatás nyilvános URL-címét, a nyilvános URL-cím a következő képlet használatával határozható meg: `https://<notebookvm_name>-<local_service_port>.<azure_region_of_notebook>.notebooks.azureml.net/score`. Például: `https://mynotebookvm-6789.eastus2.notebooks.azureml.net/score`.

## <a name="test-the-service"></a>A szolgáltatás tesztelése

Mintaadatok küldéséhez a futó szolgáltatáshoz használja a következő kódot. Cserélje le a értéket `service_url` az előző lépés URL-címére:

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
service_url = "https://mynotebookvm-6789.eastus2.notebooks.azureml.net/score"
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>További lépések

* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [Üzembe helyezés hibaelhárítása](how-to-troubleshoot-deployment.md)
* [Biztonságos SSL-lel az Azure Machine Learning-webszolgáltatások](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett gépi Tanulási modell felhasználása](how-to-consume-web-service.md)
* [A Azure Machine Learning modellek monitorozása a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelési modellekhez](how-to-enable-data-collection.md)
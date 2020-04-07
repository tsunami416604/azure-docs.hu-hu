---
title: Modellek telepítése példányok kiszámításához
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan telepítheti az Azure Machine Learning-modelleket webszolgáltatásként számítási példányok használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: 09164580b8bdb249fc12d14e827ad799d51cab34
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756591"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Modell üzembe helyezése az Azure Machine Learning számítási példányaiba

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ismerje meg, hogyan használhatja az Azure Machine Learning et egy modell webszolgáltatásként való üzembe helyezéséhez az Azure Machine Learning számítási példányán. Számítási példányok használata, ha az alábbi feltételek valamelyike teljesül:

- Gyorsan üzembe kell helyeznie és érvényesítenie kell a modellt.
- Egy fejlesztés alatt áll.

> [!TIP]
> Egy modell üzembe helyezése egy Jupyter-jegyzetfüzetből egy számítási példányon, egy webszolgáltatásugyanazon a virtuális gépen egy _helyi központi telepítés._ Ebben az esetben a "helyi" számítógép a számítási példány. A központi telepítésekről további információt a [Modellek üzembe helyezése az Azure Machine Learning szolgáltatással](how-to-deploy-and-where.md)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Machine Learning-munkaterület egy futó számítási példány. További információt a Telepítési környezet és munkaterület című [témakörben talál.](tutorial-1st-experiment-sdk-setup.md)

## <a name="deploy-to-the-compute-instances"></a>Üzembe helyezés a számítási példányokra

Egy példa notebook, amely bemutatja a helyi központi telepítések szerepel nek a számítási példányban. A következő lépésekkel töltse be a notebookot, és telepítse a modellt webszolgáltatásként a virtuális gépen:

1. Az [Azure Machine Learning stúdióból](https://ml.azure.com)válassza ki az Azure Machine Learning számítási példányait.

1. Nyissa `samples-*` meg az alkönyvtárat, majd nyissa meg a t. `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb` Miután megnyitotta, futtassa a jegyzetfüzetet.

    ![Képernyőkép a jegyzetfüzeten futó helyi szolgáltatásról](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)

1. A jegyzetfüzet megjeleníti azt az URL-címet és portot, amelyen a szolgáltatás fut. Például: `https://localhost:6789`. A port megjelenítéséhez a `print('Local service port: {}'.format(local_service.port))` cellát is futtathatja.

    ![Képernyőkép a futó helyi szolgáltatásportról](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. A szolgáltatás teszteléséhez egy számítási példány, használja az `https://localhost:<local_service.port>` URL-címet. A tesztelés egy távoli ügyfél, a számítási példányon futó szolgáltatás nyilvános URL-címét. A nyilvános URL-cím a következő képlettel határozható meg; 
    * Notebook VM: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score`. 
    * Számítási példány: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score`. 

    Például: 
    * Notebook virtuális gép:`https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Számítási példány:`https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>A szolgáltatás tesztelése

Mintaadatok nak a futó szolgáltatásba történő elküldéséhez használja a következő kódot. Cserélje le `service_url` az előző lépés URL-címét:

> [!NOTE]
> A számítási példány központi telepítéséhez való hitelesítéskor a hitelesítés az Azure Active Directory használatával történik. A `interactive_auth.get_authentication_header()` példakódban lévő hívás hitelesíti az AAD-t, és egy fejlécet ad vissza, amely ezután a számítási példány szolgáltatásának hitelesítésére használható. További információ: [Hitelesítés beállítása az Azure Machine Learning-erőforrásokhoz és -munkafolyamatokhoz.](how-to-setup-authentication.md#interactive-authentication)
>
> Az Azure Kubernetes-szolgáltatás vagy az Azure Container Instances központi telepítéséhez való hitelesítéskor a rendszer egy másik hitelesítési módszert használ. További információ: [A hitelesítés beállítása az Azure Machine Learning-erőforrásokhoz és -munkafolyamatokhoz](how-to-setup-authentication.md#web-service-authentication)című témakörben talál.

```python
import requests
import json
from azureml.core.authentication import InteractiveLoginAuthentication

# Get a token to authenticate to the compute instance from remote
interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()

# Create and submit a request using the auth header
headers = auth_header
# Add content type header
headers.update({'Content-Type':'application/json'})

# Sample data to send to the service
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

# Replace with the URL for your compute instance, as determined from the previous section
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>További lépések

* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [Központi telepítés – hibaelhárítás](how-to-troubleshoot-deployment.md)
* [Webszolgáltatás biztonságossá tétele az Azure Machine Learning en keresztül a TLS használatával](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett ml-modell felhasználása](how-to-consume-web-service.md)
* [Az Azure Machine Learning-modellek figyelése az Application Insights segítségével](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelésben lévő modellekről](how-to-enable-data-collection.md)
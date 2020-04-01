---
title: Modellek üzembe helyezése az Azure Container-példányokba
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan telepítheti az Azure Machine Learning-modelleket webszolgáltatásként az Azure Container Instances használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 12/27/2019
ms.openlocfilehash: d460112394d7c7b7d2da4e8af41c0085b67226ec
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475475"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Modell üzembe helyezése az Azure Container-példányok ba
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ismerje meg, hogyan használhatja az Azure Machine Learning egy modell webszolgáltatásként az Azure Container Instances (ACI) használatával. Az Azure Container-példányok használata, ha az alábbi feltételek valamelyike teljesül:

- Gyorsan üzembe kell helyeznie és érvényesítenie kell a modellt. Nem kell, hogy hozzon létre ACI-tárolók idő előtt. Ezek a központi telepítési folyamat részeként jönnek létre.
- Egy fejlesztés alatt áll. 

Az ACI kvóta- és régióelérhetőségéről az [Azure Container Instances kvóták és régióelérhetősége](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) című cikkben olvashat.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Machine Learning-munkaterület. További információ: [Create an Azure Machine Learning workspace](how-to-manage-workspace.md).

- A munkaterületen regisztrált gépi tanulási modell. Ha nem rendelkezik regisztrált modellel, olvassa el a Modellek üzembe helyezése és [helye.](how-to-deploy-and-where.md)

- Az [Azure CLI-bővítmény a Machine Learning szolgáltatáshoz](reference-azure-machine-learning-cli.md), [az Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)vagy az [Azure Machine Learning Visual Studio Code bővítmény.](tutorial-setup-vscode-extension.md)

- A cikkben szereplő __Python-kódrészletek__ feltételezik, hogy a következő változók vannak beállítva:

    * `ws`- Állítsa be a munkaterületet.
    * `model`- Állítsa be a regisztrált modell.
    * `inference_config`- Állítsa be a következtetés konfigurációját a modell.

    A változók beállításáról a [Modellek üzembe helyezése című](how-to-deploy-and-where.md)témakörben talál további információt.

- A cikkben szereplő __CLI-kódrészletek__ feltételezik, `inferenceconfig.json` hogy létrehozott egy dokumentumot. A dokumentum létrehozásáról további információt a [Modellek üzembe helyezése című](how-to-deploy-and-where.md)témakörben talál.

## <a name="deploy-to-aci"></a>Üzembe helyezés az ACI-ban

Egy modell üzembe helyezéséhez az Azure Container Instances, hozzon létre egy __központi telepítési konfigurációt,__ amely leírja a szükséges számítási erőforrásokat. Például a magok száma és a memória. A modell és a webszolgáltatás üzemeltetéséhez szükséges környezetet is leíró __konfigurációra__van szükség. A következtetési konfiguráció létrehozásáról további információt a Modellek üzembe helyezése című témakörben [talál.](how-to-deploy-and-where.md)

### <a name="using-the-sdk"></a>Az SDK használata

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

A példában használt osztályokról, módszerekről és paraméterekről a következő referenciadokumentumokban talál további információt:

* [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Model.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webszolgáltatás.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>A CLI használata

A CLI használatával történő telepítéshez használja a következő parancsot. Cserélje `mymodel:1` le a regisztrált modell nevére és verziójára. Cserélje `myservice` le a nevét, hogy ezt a szolgáltatást:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

További információ: az [ml modell üzembe helyezési](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) referencia. 

## <a name="using-vs-code"></a>A VS Code használata

Tekintse [meg a modellek üzembe helyezését a VS Code segítségével.](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)

> [!IMPORTANT]
> Nem kell létrehozni a ACI-tároló előzetes teszteléséhez. Az ACI-tárolók szükség szerint jönnek létre.

## <a name="update-the-web-service"></a>A webszolgáltatás frissítése

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>További lépések

* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [Központi telepítés – hibaelhárítás](how-to-troubleshoot-deployment.md)
* [Webszolgáltatás biztonságossá tétele az Azure Machine Learning en keresztül a TLS használatával](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett ml-modell felhasználása](how-to-consume-web-service.md)
* [Az Azure Machine Learning-modellek figyelése az Application Insights segítségével](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelésben lévő modellekről](how-to-enable-data-collection.md)

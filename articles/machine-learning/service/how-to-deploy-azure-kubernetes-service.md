---
title: Modellek üzembe helyezése az Azure Kubernetes Service-ben
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan helyezheti üzembe a Azure Machine Learning modelleket webszolgáltatásként az Azure Kubernetes Service használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2019
ms.openlocfilehash: c32560f7bb182ac347e9e5a71b53b57cf80fac38
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034631"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Modell üzembe helyezése Azure Kubernetes Service-fürtön

Ismerje meg, hogyan helyezhet üzembe egy modellt webszolgáltatásként az Azure Kubernetes szolgáltatásban (ak) a Azure Machine Learning használatával. Az Azure Kubernetes szolgáltatás kiválóan alkalmas a nagy léptékű éles környezetekben való üzembe helyezésre. Ha a következő lehetőségek közül egyet vagy többet szeretne használni, használja az Azure Kubernetes szolgáltatást:

- __Gyors válaszidő__.
- A telepített szolgáltatás automatikus __skálázása__ .
- __Hardveres gyorsítási__ lehetőségek, például GPU és mező – programozható Gate-tömbök (FPGA).

> [!IMPORTANT]
> A fürttípus skálázás nem érhető el a Azure Machine Learning SDK-n keresztül. Az AK-fürtök csomópontjainak méretezésével kapcsolatos további információkért lásd: [a csomópontok számának skálázása egy AK-fürtben](../../aks/scale-cluster.md).

Az Azure Kubernetes szolgáltatásba való üzembe helyezéskor a __munkaterülethez csatlakoztatott__AK-fürtbe helyezi üzembe a szolgáltatást. Az AK-fürtök kétféleképpen csatlakoztathatók a munkaterülethez:

* Hozza létre az AK-fürtöt az Azure Machine Learning SDK, a Machine Learning parancssori felület, a [Azure Portal](https://portal.azure.com) vagy a [munkaterület kezdőlapja (előzetes verzió)](https://ml.azure.com)használatával. Ez a folyamat automatikusan csatlakoztatja a fürtöt a munkaterülethez.
* Csatoljon egy meglévő AK-fürtöt az Azure Machine Learning-munkaterülethez. A fürtök csatlakoztathatók a Azure Machine Learning SDK-val, Machine Learning parancssori felülettel vagy a Azure Portalsal.

> [!IMPORTANT]
> A létrehozási vagy a mellékleti folyamat egy egyszeri feladat. Ha egy AK-fürt csatlakozik a munkaterülethez, használhatja azt az üzembe helyezésekhez. Ha már nincs szüksége rá, leválaszthatja vagy törölheti az AK-fürtöt. A detatched vagy a törlés után a továbbiakban nem fogja tudni telepíteni a fürtöt.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Machine Learning-munkaterület. További információ: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).

- A munkaterületen regisztrált gépi tanulási modell. Ha nem rendelkezik regisztrált modellel, tekintse meg a [modellek üzembe helyezésének módját és helyét](how-to-deploy-and-where.md).

- Az [Azure CLI-bővítmény Machine learning szolgáltatáshoz](reference-azure-machine-learning-cli.md), [Azure Machine learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)-hoz vagy a [Azure Machine learning Visual Studio Code bővítményhez](how-to-vscode-tools.md).

- A cikkben szereplő __Python__ -kódrészletek azt feltételezik, hogy a következő változók vannak beállítva:

    * `ws`– Állítsa be a munkaterületre.
    * `model`– Állítsa be a regisztrált modellt.
    * `inference_config`– Állítsa be a modellre vonatkozó következtetési konfigurációt.

    A változók beállításával kapcsolatos további információkért lásd: [how és How to Deploy models (modellek üzembe helyezése](how-to-deploy-and-where.md)).

- A cikkben szereplő __CLI__ -kódrészletek azt feltételezik, hogy létrehozott `inferenceconfig.json` egy dokumentumot. A dokumentum létrehozásával kapcsolatos további információkért lásd: [how és How to Deploy models (modellek üzembe helyezése](how-to-deploy-and-where.md)).

## <a name="create-a-new-aks-cluster"></a>Új AK-fürt létrehozása

**Becsült idő**: Körülbelül 20 percet vesz igénybe.

Egy AK-fürt létrehozása vagy csatolása egy egyszeri folyamat a munkaterülethez. Újból felhasználhatja a fürt több telepítéshez. Ha törli a fürtöt vagy az azt tartalmazó erőforráscsoportot, létre kell hoznia egy új fürtöt, amikor legközelebb telepítenie kell. A munkaterülethez több AK-alapú fürtök is tartozhatnak.

> [!TIP]
> Ha Azure-Virtual Network használatával szeretné biztonságossá tenni az AK-fürtöt, először létre kell hoznia a virtuális hálózatot. További információ: [biztonságos kísérletezés és következtetés az Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

Ha AK-fürtöt kíván létrehozni a __fejlesztéshez__, az __ellenőrzéshez__és a __teszteléshez__ az éles környezet helyett, megadhatja a __fürt célját__ a __fejlesztői teszthez__.

> [!WARNING]
> Ha be van `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`állítva, a létrehozott fürt nem alkalmas a termelési szint forgalmára, és növelheti a következtetési időt. A fejlesztési és tesztelési fürtök szintén nem garantálják a hibatűrést. Legalább 2 virtuális processzort ajánlunk fejlesztési és tesztelési fürtökhöz.

Az alábbi példák bemutatják, hogyan hozhat létre egy új AK-fürtöt az SDK és a parancssori felület használatával:

**Az SDK használata**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> Ha [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)a `agent_count` és `DEV_TEST` `cluster_purpose` `agent_count` aesetében`vm_size` egyéni értékeket választ, és nem, akkor meg kell győződnie róla, hogy a megszorozva érték nagyobb vagy egyenlő, mint 12 virtuális processzor. `vm_size` Ha például olyan `vm_size` "standard D3 v2"-t használ, amely 4 virtuális processzorral rendelkezik, akkor 3 vagy annál nagyobb számot `agent_count` kell választania.
>
> Az Azure Machine Learning SDK nem támogatja az AK-fürtök méretezését. A fürt csomópontjainak méretezéséhez használja a Azure Portal az AK-fürt felhasználói felületét. Csak a csomópontok számát módosíthatja, nem a fürt virtuálisgép-méretét.

Az ebben a példában használt osztályokkal, metódusokkal és paraméterekkel kapcsolatos további információkért tekintse meg a következő dokumentációt:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.provisioning_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**A parancssori felület használata**

```azurecli
az ml computetarget create aks -n myaks
```

További információ: az [ml computetarget Create Ask](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) Reference.

## <a name="attach-an-existing-aks-cluster"></a>Meglévő AK-fürt csatolása

**Becsült idő:** Körülbelül 5 percet vesz igénybe.

Ha már rendelkezik AK-fürttel az Azure-előfizetésében, és 1.12-es verzióban van, akkor használhatja a lemezkép üzembe helyezéséhez.

> [!TIP]
> A meglévő AK-fürt lehet egy Azure-régióban, mint a Azure Machine Learning munkaterülete.
>
> Ha Azure-Virtual Network használatával szeretné biztonságossá tenni az AK-fürtöt, először létre kell hoznia a virtuális hálózatot. További információ: [biztonságos kísérletezés és következtetés az Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

> [!WARNING]
> Ha AK-fürtöt csatol egy munkaterülethez, megadhatja, hogyan fogja használni a fürtöt a `cluster_purpose` paraméter beállításával.
>
> Ha nem állítja be a `cluster_purpose` paramétert, vagy beállítja `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, akkor a fürtnek legalább 12 virtuális CPU-t kell használnia.
>
> Ha be van `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`állítva, akkor a fürtnek nem kell 12 virtuális processzorral rendelkeznie. A fejlesztéshez és teszteléshez legalább 2 virtuális processzort ajánlunk. A fejlesztéshez és teszteléshez konfigurált fürt azonban nem alkalmas a termelési szint forgalmára, és növelheti a következtetési időt. A fejlesztési és tesztelési fürtök szintén nem garantálják a hibatűrést.

A következő cikkekből megtudhatja, hogyan hozhat létre egy AK-fürtöt az Azure CLI vagy a portál használatával:

* [AK-fürt létrehozása (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [AK-fürt létrehozása (portál)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

Az alábbi példák bemutatják, hogyan csatolhat egy meglévő AK 1.12. # # fürtöt a munkaterülethez:

**Az SDK használata**

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)
```

Az ebben a példában használt osztályokkal, metódusokkal és paraméterekkel kapcsolatos további információkért tekintse meg a következő dokumentációt:

* [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute. Attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**A parancssori felület használata**

Meglévő fürt parancssori felülettel való csatolásához le kell kérnie a meglévő fürt erőforrás-AZONOSÍTÓját. Az érték beszerzéséhez használja a következő parancsot. Cserélje `myexistingcluster` le a nevet az AK-fürt nevére. Cserélje `myresourcegroup` le a helyére a fürtöt tartalmazó erőforráscsoportot:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Ez a parancs az alábbi szöveghez hasonló értéket ad vissza:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Ha a meglévő fürtöt a munkaterülethez szeretné csatolni, használja a következő parancsot. Cserélje `aksresourceid` le az értéket az előző parancs által visszaadott értékre. Cserélje `myresourcegroup` le a-t a munkaterületet tartalmazó erőforráscsoporthoz. Cserélje `myworkspace` le a nevet a munkaterület nevére.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

További információ: az [ml computetarget Attach AK](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) -referenciája.

## <a name="deploy-to-aks"></a>Üzembe helyezés az AKS-ben

Ha modellt szeretne üzembe helyezni az Azure Kubernetes szolgáltatásban, hozzon létre egy __központi telepítési konfigurációt__ , amely leírja a szükséges számítási erőforrásokat. Például a magok és a memória száma. Szüksége lesz egy __következtetésre__is, amely leírja a modell és a webszolgáltatás üzemeltetéséhez szükséges környezetet. A következtetések konfigurációjának létrehozásáról további információt a [modellek üzembe helyezésének módja és helye](how-to-deploy-and-where.md)című témakörben talál.

### <a name="using-the-sdk"></a>Az SDK használata

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Az ebben a példában használt osztályokkal, metódusokkal és paraméterekkel kapcsolatos további információkért tekintse meg a következő dokumentációt:

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Modell. Deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)
* [Webszolgáltatás. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>A parancssori felület használata

A CLI használatával történő üzembe helyezéshez használja a következő parancsot. Cserélje `myaks` le az t az AK számítási cél nevére. Cserélje `mymodel:1` le a nevet a regisztrált modell nevére és verziójára. Cserélje `myservice` le a nevet a következő szolgáltatáshoz:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aks-deploy-config.md)]

További információ: az [ml Model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) Reference. 

### <a name="using-vs-code"></a>A VS Code használata

A VS Code használatával kapcsolatos információkért lásd: [üzembe helyezés az AK-n keresztül a vs Code bővítménnyel](how-to-vscode-tools.md#deploy-and-manage-models).

> [!IMPORTANT] 
> A VS code-on keresztül történő üzembe helyezéshez az AK-fürt létrehozása vagy a munkaterülethez való csatolása szükséges.

## <a name="web-service-authentication"></a>Webszolgáltatás-hitelesítés

Az Azure Kubernetes szolgáltatásba való üzembe helyezéskor a __kulcs alapú__ hitelesítés alapértelmezés szerint engedélyezve van. Engedélyezheti a __jogkivonat-alapú__ hitelesítést is. A jogkivonat-alapú hitelesítéshez az szükséges, hogy az ügyfelek egy Azure Active Directory fiókot használjanak a hitelesítési jogkivonat igényléséhez, amely a központilag telepített szolgáltatásra irányuló kérések elvégzésére szolgál.

A hitelesítés __letiltásához__ állítsa `auth_enabled=False` be a paramétert a központi telepítési konfiguráció létrehozásakor. Az alábbi példa az SDK használatával letiltja a hitelesítést:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Az ügyfélalkalmazások általi hitelesítéssel kapcsolatos információkért tekintse meg a [webszolgáltatásként üzembe helyezett Azure Machine learning modell](how-to-consume-web-service.md)felhasználását ismertető témakört.

### <a name="authentication-with-keys"></a>Hitelesítés kulcsokkal

Ha a kulcsos hitelesítés engedélyezve van, a metódussal kérheti le az `get_keys` elsődleges és a másodlagos hitelesítési kulcsot:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Ha újra kell létrehoznia egy kulcsot, használja a következőt[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Hitelesítés jogkivonatokkal

A jogkivonat-hitelesítés engedélyezéséhez állítsa `token_auth_enabled=True` be a paramétert egy központi telepítés létrehozásakor vagy frissítésekor. Az alábbi példa lehetővé teszi a jogkivonat-hitelesítés használatát az SDK-val:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Ha engedélyezve van a jogkivonat-hitelesítés, a `get_token` metódus használatával kérhet le egy JWT-tokent és a jogkivonat lejárati idejét:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> A jogkivonat `refresh_by` időpontját követően új jogkivonatot kell kérnie.
>
> A Microsoft nyomatékosan javasolja, hogy a Azure Machine Learning munkaterületet ugyanabban a régióban hozza létre, mint az Azure Kubernetes Service-fürtöt. A webszolgáltatások tokenekkel történő hitelesítéséhez a webszolgáltatás meghívja a Azure Machine Learning munkaterület létrehozásához használt régiót. Ha a munkaterület régiója nem érhető el, akkor sem fogja tudni lehívni a webszolgáltatáshoz tartozó jogkivonatot, ha a fürt a munkaterülettől eltérő régióban található. Ez gyakorlatilag a jogkivonat-alapú hitelesítés nem érhető el, amíg a munkaterület régiója újra elérhetővé nem válik. Emellett minél nagyobb a távolság a fürt régiója és a munkaterület régiója között, annál hosszabb ideig tart a token beolvasása.

## <a name="update-the-web-service"></a>A web service frissítése

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>További lépések

* [Biztonságos kísérletezés és következtetés egy virtuális hálózaton](how-to-enable-virtual-network.md)
* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [Üzembe helyezés hibaelhárítása](how-to-troubleshoot-deployment.md)
* [Biztonságos SSL-lel az Azure Machine Learning-webszolgáltatások](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett gépi Tanulási modell felhasználása](how-to-consume-web-service.md)
* [A Azure Machine Learning modellek monitorozása a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelési modellekhez](how-to-enable-data-collection.md)

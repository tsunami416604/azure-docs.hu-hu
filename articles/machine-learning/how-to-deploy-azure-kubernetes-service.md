---
title: Modellek üzembe helyezése az Azure Kubernetes szolgáltatásban
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan telepítheti az Azure Machine Learning-modelleket webszolgáltatásként az Azure Kubernetes-szolgáltatás használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 01/16/2020
ms.openlocfilehash: 792964f28ddb3fcb10932b8de9499a9c7027960f
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475377"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Modell üzembe helyezése egy Azure Kubernetes-szolgáltatásfürtben
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ismerje meg, hogyan használhatja az Azure Machine Learning egy modell webszolgáltatásként az Azure Kubernetes szolgáltatás (AKS) használatával. Az Azure Kubernetes szolgáltatás nagy méretű éles környezetek esetén is alkalmas. Az Azure Kubernetes-szolgáltatást akkor használja, ha az alábbi képességek közül egyre van szüksége:

- __Gyors válaszidő__.
- Az üzembe helyezett szolgáltatás __automatikus skálázása.__
- __Hardveres gyorsítási__ lehetőségek, például GPU és mezőprogramozható kaputömbök (FPGA).

> [!IMPORTANT]
> Fürtskálázás nem érhető el az Azure Machine Learning SDK.Cluster scaling is not provided through the Azure Machine Learning SDK. Az AKS-fürt csomópontjainak méretezése című témakörben talál további információt [az AKS-fürt csomópontszámának méretezése című témakörben.](../aks/scale-cluster.md)

Az Azure Kubernetes szolgáltatásra való üzembe helyezéskor a munkaterülethez csatlakoztatott AKS-fürtre __telepíti a központi telepítést.__ Az AKS-fürtöket kétféleképpen kapcsolhatja össze a munkaterülettel:

* Hozza létre az AKS-fürtöt az Azure Machine Learning SDK, a Machine Learning CLI vagy az [Azure Machine Learning studio](https://ml.azure.com)használatával. Ez a folyamat automatikusan összekapcsolja a fürtöt a munkaterülettel.
* Meglévő AKS-fürt csatolása az Azure Machine Learning-munkaterülethez. A fürt az Azure Machine Learning SDK, a Machine Learning CLI vagy az Azure Machine Learning studio használatával csatolható.

> [!IMPORTANT]
> A létrehozási vagy csatolási folyamat egyszeri feladat. Miután egy AKS-fürt csatlakozik a munkaterülethez, használhatja azt a központi telepítésekhez. Leválaszthatja vagy törölheti az AKS-fürtöt, ha már nincs rá szüksége. Leválasztva vagy törölve a továbbiakban nem lesz képes telepíteni a fürtre.

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

## <a name="create-a-new-aks-cluster"></a>Új AKS-fürt létrehozása

**Becsült idő**: Körülbelül 20 perc.

Az AKS-fürt létrehozása vagy csatolása a munkaterület egyszeri folyamata. Ezt a fürtöt több központi telepítéshez is felhasználhatja. Ha törli a fürtöt vagy az azt tartalmazó erőforráscsoportot, a következő üzembe helyezéskor új fürtöt kell létrehoznia. A munkaterülethez több AKS-fürt is csatlakoztatható.

> [!TIP]
> Ha az AKS-fürtöt egy Azure virtuális hálózaton keresztül szeretné biztonságossá tenni, először létre kell hoznia a virtuális hálózatot. További információ: [Biztonságos kísérletezés és következtetés az Azure virtuális hálózattal.](how-to-enable-virtual-network.md#aksvnet)

Ha éles környezet helyett __fejlesztési,__ __érvényesítési__és __tesztelési__ AKS-fürtöt szeretne létrehozni, megadhatja a __fejlesztői__ __tesztelés__célját.

> [!WARNING]
> Ha beállítja, `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`a létrehozott fürt nem alkalmas éles szintű forgalomra, és növelheti a következtetési időket. A fejlesztői és tesztelési fürtök szintén nem garantálják a hibatűrést. Legalább 2 virtuális processzort ajánlunk fejlesztési és tesztelési fürtökhöz.

Az alábbi példák bemutatják, hogyan hozhat létre új AKS-fürtöt az SDK és a CLI használatával:

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
> Ha [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)a , és `agent_count` a `vm_size`, `cluster_purpose` és `DEV_TEST`a, és a, akkor győződjön meg arról, `agent_count` hogy szorozva `vm_size` 12 virtuális PROCESSZORokkal nagyobb vagy azzal egyenlő. Ha például a `vm_size` 4 virtuális processzort használó "Standard_D3_v2" (Standard_D3_v2) `agent_count` használ, akkor 3-as vagy nagyobb processzort kell választania.
>
> Az Azure Machine Learning SDK nem nyújt támogatást az AKS-fürt méretezése. A fürt csomópontjainak méretezéséhez használja a felhasználói felületet az AKS-fürthöz az Azure Machine Learning-stúdióban. Csak a csomópontszámát módosíthatja, a fürt virtuális gépméretét nem.

A példában használt osztályokról, módszerekről és paraméterekről a következő referenciadokumentumokban talál további információt:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [Számítási cél.create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [Számítási cél.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**A CLI használata**

```azurecli
az ml computetarget create aks -n myaks
```

További információ: az [az ml computetarget create aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) reference.

## <a name="attach-an-existing-aks-cluster"></a>Meglévő AKS-fürt csatolása

**Becsült idő:** Körülbelül 5 perc.

Ha már rendelkezik AKS-fürtaz Azure-előfizetésében, és ez az 1.17-es vagy alacsonyabb verzió, használhatja a lemezkép üzembe helyezéséhez.

> [!TIP]
> A meglévő AKS-fürt lehet az Azure Machine Learning-munkaterülettől eltérő Azure-régióban.
>
> Ha az AKS-fürtöt egy Azure virtuális hálózaton keresztül szeretné biztonságossá tenni, először létre kell hoznia a virtuális hálózatot. További információ: [Biztonságos kísérletezés és következtetés az Azure virtuális hálózattal.](how-to-enable-virtual-network.md#aksvnet)

Amikor a `cluster_purpose` paraméter beállításával AKS-fürtöt csatlakoztat egy munkaterülethez, megadhatja, hogyan használja a fürtöt.

Ha nem állítja `cluster_purpose` be a `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`paramétert, vagy nem állítja be a paramétert, akkor a fürtnek legalább 12 virtuális processzort kell rendelkezésre állnia.

Ha beállítja a , `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`majd a fürt nem kell 12 virtuális processzorok. Javasoljuk, hogy legalább 2 virtuális CPU-k fejlesztési/tesztelési. Azonban a fürt, amely konfigurálva van a fejlesztési/tesztelési nem alkalmas éles szintű forgalmat, és növelheti a következtetési idő. A fejlesztői és tesztelési fürtök szintén nem garantálják a hibatűrést.

> [!WARNING]
> Ne hozzon létre több, egyidejű mellékletet ugyanahhoz az AKS-fürthöz a munkaterületről. Ha például egy AKS-fürtöt két különböző névvel kapcsol egy munkaterülethez. Minden új melléklet megszakítja a korábbi melléklet(eke)t.
>
> Ha újra csatolni szeretne egy AKS-fürtöt, például módosítani szeretné a TLS-t vagy más fürtkonfigurációs beállítást, először el kell távolítania a meglévő mellékletet az [AksCompute.detach()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--)használatával.

Az AKS-fürt Azure CLI vagy portál használatával történő létrehozásáról az alábbi cikkekben talál további információt:

* [AKS-fürt (CLI) létrehozása](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [AKS-fürt (portál) létrehozása](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

Az alábbi példák bemutatják, hogyan csatolhat egy meglévő AKS-fürtöt a munkaterülethez:

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

A példában használt osztályokról, módszerekről és paraméterekről a következő referenciadokumentumokban talál további információt:

* [AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**A CLI használata**

Ha meglévő fürthöz szeretne csatolni a CLI-t, be kell szereznie a meglévő fürt erőforrásazonosítóját. Az érték levételéhez használja a következő parancsot. Cserélje `myexistingcluster` le az AKS-fürt nevét. Cserélje `myresourcegroup` le a fürtöt tartalmazó erőforráscsoportra:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Ez a parancs a következő höz hasonló értéket ad vissza:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Ha a meglévő fürtöt a munkaterülethez szeretné csatolni, használja a következő parancsot. Cserélje `aksresourceid` le az előző parancs által visszaadott értékre. Cserélje `myresourcegroup` le a munkaterületet tartalmazó erőforráscsoportra. Cserélje `myworkspace` le a munkaterület nevét.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

További információ: az [az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) reference.

## <a name="deploy-to-aks"></a>Üzembe helyezés az AKS-ben

Egy modell üzembe helyezéséhez az Azure Kubernetes-szolgáltatás, hozzon létre egy __központi telepítési konfigurációt,__ amely leírja a szükséges számítási erőforrásokat. Például a magok száma és a memória. A modell és a webszolgáltatás üzemeltetéséhez szükséges környezetet is leíró __konfigurációra__van szükség. A következtetési konfiguráció létrehozásáról további információt a Modellek üzembe helyezése című témakörben [talál.](how-to-deploy-and-where.md)

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

A példában használt osztályokról, módszerekről és paraméterekről a következő referenciadokumentumokban talál további információt:

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Model.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webszolgáltatás.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>A CLI használata

A CLI használatával történő telepítéshez használja a következő parancsot. Cserélje `myaks` le az AKS számítási cél nevére. Cserélje `mymodel:1` le a regisztrált modell nevére és verziójára. Cserélje `myservice` le a nevét, hogy ezt a szolgáltatást:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

További információ: az [ml modell üzembe helyezési](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) referencia.

### <a name="using-vs-code"></a>A VS Code használata

A VS-kód használatával kapcsolatos további tudnivalókért tekintse meg [az AKS-ra való telepítést a VS Code bővítményen keresztül.](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)

> [!IMPORTANT]
> A VS-kódon keresztül történő üzembe helyezéshez az AKS-fürtöt előre létre kell hozni, vagy csatolni kell a munkaterülethez.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Modellek üzembe helyezése az AKS-re ellenőrzött bevezetéssel (előzetes verzió)
Modellverziók elemzése és előléptetése ellenőrzött módon végpontok használatával. Egyetlen végpont mögött legfeljebb 6 verziót telepíthet, és konfigurálja a pontozási forgalom %-át az egyes telepített verziókra. Engedélyezheti, hogy az alkalmazáselemzések a végpontok és az üzembe helyezett verziók működési metrikáit tekintsék.

### <a name="create-an-endpoint"></a>Végpont létrehozása
Miután készen áll a modellek üzembe helyezésére, hozzon létre egy pontozási végpontot, és telepítse az első verziót. Az alábbi lépés bemutatja, hogyan telepítheti és hozhatja létre a végpontot az SDK használatával. Az első központi telepítés lesz definiálva, mint az alapértelmezett verzió, ami azt jelenti, hogy a meghatározatlan forgalom percentilis az összes verzió megy az alapértelmezett verzióra.  

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint",
version_name= "versiona",
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Verziók frissítése és hozzáadása végponthoz

Adjon hozzá egy másik verziót a végponthoz, és konfigurálja a pontozási forgalom percentilis megy a verzióra. Kétféle verzió létezik: egy kontroll és egy kezelési verzió. Nem lehet több kezelési verzió, hogy segítsen összehasonlítani egy kontroll verzió.

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
```

Frissítse a meglévő verziókat, vagy törölje őket egy végponton. Módosíthatja a verzió alapértelmezett típusát, vezérlőtípusát és a forgalom százalékos arányát.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)

# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```


## <a name="web-service-authentication"></a>Webszolgáltatás hitelesítése

Az Azure Kubernetes-szolgáltatásüzembe helyezéskor a __kulcsalapú__ hitelesítés alapértelmezés szerint engedélyezve van. A __tokenalapú__ hitelesítést is engedélyezheti. A jogkivonat-alapú hitelesítés megköveteli, hogy az ügyfelek egy Azure Active Directory-fiók használatával kérjenek egy hitelesítési jogkivonatot, amely az üzembe helyezett szolgáltatásnak történő kérelmek teljesítésére szolgál.

A hitelesítés __letiltásához__ állítsa be a `auth_enabled=False` paramétert a központi telepítési konfiguráció létrehozásakor. A következő példa letiltja a hitelesítést az SDK használatával:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Az ügyfélalkalmazásból történő hitelesítésről a [Webszolgáltatásként telepített Azure Machine Learning-modell](how-to-consume-web-service.md)felhasználásáról nyújt be információt.

### <a name="authentication-with-keys"></a>Hitelesítés kulcsokkal

Ha a kulcshitelesítés engedélyezve `get_keys` van, a módszerrel lekérheti az elsődleges és másodlagos hitelesítési kulcsot:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Ha újra kell generálnia egy kulcsot, használja a[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Hitelesítés jogkivonatokkal

A tokenhitelesítés engedélyezéséhez `token_auth_enabled=True` állítsa be a paramétert központi telepítés létrehozásakor vagy frissítésekor. A következő példa engedélyezi a tokenhitelesítést az SDK használatával:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Ha a tokenhitelesítés engedélyezve `get_token` van, a módszer segítségével lekérheti a JWT-jogkivonatot és a token lejárati idejét:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> A jogkivonat ideje után új jogkivonatot kell kérnie. `refresh_by`
>
> A Microsoft azt javasolja, hogy hozza létre az Azure Machine Learning-munkaterületet ugyanabban a régióban, mint az Azure Kubernetes service-fürt. A jogkivonattal történő hitelesítéshez a webszolgáltatás hívást kezdeményez arra a régióra, amelyben az Azure Machine Learning-munkaterület létrejön. Ha a munkaterület régiója nem érhető el, akkor nem lesz képes lekérni egy jogkivonatot a webszolgáltatáshoz, még akkor sem, ha a fürt a munkaterülettől eltérő régióban van. Ez hatékonyan eredményezi a token-alapú hitelesítés nem érhető el, amíg a munkaterület régiója újra elérhetővé válik. Ezenkívül minél nagyobb a távolság a fürt régiója és a munkaterület régiója között, annál tovább tart a jogkivonat beolvasása.

## <a name="update-the-web-service"></a>A webszolgáltatás frissítése

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>További lépések

* [Biztonságos kísérletezés és következtetés a virtuális hálózatban](how-to-enable-virtual-network.md)
* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [Központi telepítés – hibaelhárítás](how-to-troubleshoot-deployment.md)
* [Webszolgáltatás biztonságossá tétele az Azure Machine Learning en keresztül a TLS használatával](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett ml-modell felhasználása](how-to-consume-web-service.md)
* [Az Azure Machine Learning-modellek figyelése az Application Insights segítségével](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelésben lévő modellekről](how-to-enable-data-collection.md)

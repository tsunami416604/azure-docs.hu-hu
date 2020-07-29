---
title: ML modellek üzembe helyezése a Kubernetes szolgáltatásban
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan helyezheti üzembe a Azure Machine Learning modelleket webszolgáltatásként az Azure Kubernetes Service használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.openlocfilehash: ad34195e003e0ca2d73000d3482cc79c3dbe3ee0
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87372110"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Modell üzembe helyezése Azure Kubernetes Service-fürtön
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ismerje meg, hogyan helyezhet üzembe egy modellt webszolgáltatásként az Azure Kubernetes szolgáltatásban (ak) a Azure Machine Learning használatával. Az Azure Kubernetes szolgáltatás kiválóan alkalmas a nagy léptékű éles környezetekben való üzembe helyezésre. Ha a következő lehetőségek közül egyet vagy többet szeretne használni, használja az Azure Kubernetes szolgáltatást:

- __Gyors válaszidő__.
- A telepített szolgáltatás automatikus __skálázása__ .
- __Hardveres gyorsítási__ lehetőségek, például GPU és mező – programozható Gate-tömbök (FPGA).

> [!IMPORTANT]
> A fürt skálázása nincs megadva a Azure Machine Learning SDK-n keresztül. Az AK-fürtök csomópontjainak méretezésével kapcsolatos további információkért lásd: [a csomópontok számának skálázása egy AK-fürtben](../aks/scale-cluster.md).

Az Azure Kubernetes szolgáltatásba való üzembe helyezéskor a __munkaterülethez csatlakoztatott__AK-fürtbe helyezi üzembe a szolgáltatást. Az AK-fürtök kétféleképpen csatlakoztathatók a munkaterülethez:

* Hozza létre az AK-fürtöt az Azure Machine Learning SDK, a Machine Learning parancssori felület vagy a [Azure Machine learning Studio](https://ml.azure.com)használatával. Ez a folyamat automatikusan csatlakoztatja a fürtöt a munkaterülethez.
* Csatoljon egy meglévő AK-fürtöt az Azure Machine Learning-munkaterülethez. A fürtök csatlakoztathatók a Azure Machine Learning SDK, Machine Learning CLI vagy a Azure Machine Learning Studio használatával.

Az AK-fürt és a pénzmosás-munkaterület különböző erőforráscsoport lehet.

> [!IMPORTANT]
> A létrehozási vagy a mellékleti folyamat egy egyszeri feladat. Ha egy AK-fürt csatlakozik a munkaterülethez, használhatja azt az üzembe helyezésekhez. Ha már nincs szüksége rá, leválaszthatja vagy törölheti az AK-fürtöt. A leválasztást vagy törlést követően a továbbiakban nem fogja tudni telepíteni a fürtöt.

> [!IMPORTANT]
> Javasoljuk, hogy a webszolgáltatásba való üzembe helyezés előtt helyileg végezzen hibakeresést, további információ: [helyi hibakeresés](https://docs.microsoft.com/azure/machine-learning/how-to-troubleshoot-deployment#debug-locally)
>
> Azure Machine Learning- [központi telepítés helyi jegyzetfüzetre című témakört](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local) is használhatja.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Machine Learning-munkaterület. További információ: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).

- A munkaterületen regisztrált gépi tanulási modell. Ha nem rendelkezik regisztrált modellel, tekintse meg a [modellek üzembe helyezésének módját és helyét](how-to-deploy-and-where.md).

- Az [Azure CLI-bővítmény Machine learning szolgáltatáshoz](reference-azure-machine-learning-cli.md), [Azure Machine learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)-hoz vagy a [Azure Machine learning Visual Studio Code bővítményhez](tutorial-setup-vscode-extension.md).

- A cikkben szereplő __Python__ -kódrészletek azt feltételezik, hogy a következő változók vannak beállítva:

    * `ws`– Állítsa be a munkaterületre.
    * `model`– Állítsa be a regisztrált modellt.
    * `inference_config`– Állítsa be a modellre vonatkozó következtetési konfigurációt.

    A változók beállításával kapcsolatos további információkért lásd: [how és How to Deploy models (modellek üzembe helyezése](how-to-deploy-and-where.md)).

- A cikkben szereplő __CLI__ -kódrészletek azt feltételezik, hogy létrehozott egy `inferenceconfig.json` dokumentumot. A dokumentum létrehozásával kapcsolatos további információkért lásd: [how és How to Deploy models (modellek üzembe helyezése](how-to-deploy-and-where.md)).

- Ha egy AK-fürtöt csatlakoztat, amelynek [engedélyezett IP-tartománya engedélyezve van az API-kiszolgáló eléréséhez](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges), engedélyezze a pénzmosás Contol sík IP-tartományait az AK-fürthöz. A pénzmosás-vezérlési sík a párosított régiókban van üzembe helyezve, és az AK-fürtön üzembe helyezett hüvelyeket helyez üzembe. Az API-kiszolgálóhoz való hozzáférés nélkül a következtetést nem lehet központilag telepíteni. A [párosított régiók]( https://docs.microsoft.com/azure/best-practices-availability-paired-regions) [IP-tartományait](https://www.microsoft.com/en-us/download/confirmation.aspx?id=56519) is használhatja, ha egy AK-fürtben engedélyezi az IP-tartományokat.
 
 - A számítási névnek egyedinek kell lennie a munkaterületen belül
   - A név megadása kötelező, és legfeljebb 3 – 24 karakter hosszúságú lehet.
   - Az érvényes karakterek a kis-és nagybetűk, a számjegyek és a karakter.
   - A névnek betűvel kell kezdődnie
   - A névnek egyedinek kell lennie az Azure-régióban lévő összes számításban. Ha a választott név nem egyedi, akkor riasztás jelenik meg.
   
 - Ha a modelleket GPU-csomópontokra vagy FPGA-csomópontokra (vagy bármely konkrét SKU-ra) szeretné telepíteni, akkor létre kell hoznia egy fürtöt az adott SKU-val. Nem támogatott másodlagos csomópont-készlet létrehozása meglévő fürtben, valamint modellek üzembe helyezése a másodlagos csomópont-készletben.
 
 - Ha egy alapszintű Load Balancer (BLB) helyett a fürtben telepített standard Load Balancerra (SLB) van szüksége, hozzon létre egy fürtöt az AK-portálon/CLI/SDK-ban, majd csatolja a pénzmosás-munkaterülethez. 



## <a name="create-a-new-aks-cluster"></a>Új AK-fürt létrehozása

**Becsült idő**: körülbelül 10 perc.

Egy AK-fürt létrehozása vagy csatolása egy egyszeri folyamat a munkaterülethez. Ezt a fürtöt több központi telepítéshez is felhasználhatja. Ha törli a fürtöt vagy az azt tartalmazó erőforráscsoportot, létre kell hoznia egy új fürtöt, amikor legközelebb telepítenie kell. A munkaterülethez több AK-alapú fürtök is tartozhatnak.
 
A Azure Machine Learning mostantól támogatja az Azure Kubernetes szolgáltatás használatát, amelyhez engedélyezve van a privát kapcsolat.
Privát AK-fürt létrehozásához kövesse az [itt](https://docs.microsoft.com/azure/aks/private-clusters) található dokumentumokat

> [!TIP]
> Ha Azure-Virtual Network használatával szeretné biztonságossá tenni az AK-fürtöt, először létre kell hoznia a virtuális hálózatot. További információ: [biztonságos kísérletezés és következtetés az Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

Ha AK-fürtöt kíván létrehozni a __fejlesztéshez__, az __ellenőrzéshez__és a __teszteléshez__ az éles környezet helyett, megadhatja a __fürt célját__ a __fejlesztői teszthez__.

> [!WARNING]
> Ha be `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` van állítva, a létrehozott fürt nem alkalmas a termelési szint forgalmára, és növelheti a következtetési időt. A fejlesztési és tesztelési fürtök szintén nem garantálják a hibatűrést. Legalább 2 virtuális processzort ajánlunk fejlesztési és tesztelési fürtökhöz.

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
> Ha a és a esetében [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py) Egyéni értékeket választ `agent_count` , `vm_size` és `cluster_purpose` nem `DEV_TEST` , akkor meg kell győződnie róla, hogy a `agent_count` megszorozva érték `vm_size` nagyobb vagy egyenlő, mint 12 virtuális processzor. Ha például olyan `vm_size` "Standard_D3_v2"-t használ, amely 4 virtuális processzorral rendelkezik, akkor `agent_count` 3 vagy több közül kell választania.
>
> Az Azure Machine Learning SDK nem támogatja az AK-fürtök méretezését. A fürt csomópontjainak méretezéséhez használja a Azure Machine Learning Studióban az AK-fürt felhasználói felületét. Csak a csomópontok számát módosíthatja, nem a fürt virtuálisgép-méretét.

Az ebben a példában használt osztályokkal, metódusokkal és paraméterekkel kapcsolatos további információkért tekintse meg a következő dokumentációt:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute. provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget. wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**A parancssori felület használata**

```azurecli
az ml computetarget create aks -n myaks
```

További információ: az [ml computetarget Create AK](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) Reference.

## <a name="attach-an-existing-aks-cluster"></a>Meglévő AK-fürt csatolása

**Becsült idő:** Körülbelül 5 percet vesz igénybe.

Ha már rendelkezik AK-fürttel az Azure-előfizetésben, és 1,17-es vagy alacsonyabb verziójú, akkor a lemezkép üzembe helyezéséhez használhatja azt.

> [!TIP]
> A meglévő AK-fürt a Azure Machine Learning munkaterülettől eltérő Azure-régióban is lehet.
>
> Ha Azure-Virtual Network használatával szeretné biztonságossá tenni az AK-fürtöt, először létre kell hoznia a virtuális hálózatot. További információ: [biztonságos kísérletezés és következtetés az Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

Ha AK-fürtöt csatol egy munkaterülethez, megadhatja, hogyan fogja használni a fürtöt a `cluster_purpose` paraméter beállításával.

Ha nem állítja be a `cluster_purpose` paramétert, vagy beállítja `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD` , akkor a fürtnek legalább 12 virtuális CPU-t kell használnia.

Ha be van állítva `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` , akkor a fürtnek nem kell 12 virtuális processzorral rendelkeznie. A fejlesztéshez és teszteléshez legalább 2 virtuális processzort ajánlunk. A fejlesztéshez és teszteléshez konfigurált fürt azonban nem alkalmas a termelési szint forgalmára, és növelheti a következtetési időt. A fejlesztési és tesztelési fürtök szintén nem garantálják a hibatűrést.

> [!WARNING]
> Ne hozzon létre több, egyidejű mellékletet ugyanahhoz az AK-fürthöz a munkaterületről. Például egy AK-fürt csatolása egy munkaterülethez két különböző név használatával. Minden új melléklet megtöri az előző meglévő melléklet (eke) t.
>
> Ha egy AK-fürtöt újra szeretne csatlakoztatni, például a TLS vagy más fürtkonfiguráció beállításának módosításához, először el kell távolítania a meglévő mellékletet a [AksCompute. leválasztás ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--)használatával.

A következő cikkekből megtudhatja, hogyan hozhat létre egy AK-fürtöt az Azure CLI vagy a portál használatával:

* [AKS-fürt létrehozása (parancssori felület)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [AK-fürt létrehozása (portál)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)
* [AK-fürt létrehozása (ARM-sablon az Azure Gyorsindítás sablonjain)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

Az alábbi példák bemutatják, hogyan csatolhat egy meglévő AK-fürtöt a munkaterülethez:

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

* [AksCompute. attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute. Attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**A parancssori felület használata**

Meglévő fürt parancssori felülettel való csatolásához le kell kérnie a meglévő fürt erőforrás-AZONOSÍTÓját. Az érték beszerzéséhez használja a következő parancsot. Cserélje le a `myexistingcluster` nevet az AK-fürt nevére. Cserélje le a helyére a `myresourcegroup` fürtöt tartalmazó erőforráscsoportot:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Ez a parancs az alábbi szöveghez hasonló értéket ad vissza:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Ha a meglévő fürtöt a munkaterülethez szeretné csatolni, használja a következő parancsot. Cserélje le az `aksresourceid` értéket az előző parancs által visszaadott értékre. Cserélje le a- `myresourcegroup` t a munkaterületet tartalmazó erőforráscsoporthoz. Cserélje le a `myworkspace` nevet a munkaterület nevére.

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
* [AksWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Modell. Deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webszolgáltatás. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>A parancssori felület használata

A CLI használatával történő üzembe helyezéshez használja a következő parancsot. Cserélje le az `myaks` t az AK számítási cél nevére. Cserélje le a `mymodel:1` nevet a regisztrált modell nevére és verziójára. Cserélje le `myservice` a nevet a következő szolgáltatáshoz:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

További információ: az [ml Model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) Reference.

### <a name="using-vs-code"></a>A VS Code használata

A VS Code használatával kapcsolatos információkért lásd: [üzembe helyezés az AK-n keresztül a vs Code bővítménnyel](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> A VS code-on keresztül történő üzembe helyezéshez az AK-fürt létrehozása vagy a munkaterülethez való csatolása szükséges.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Modellek üzembe helyezése az AK-ban vezérelt bevezetéssel (előzetes verzió)

A modell verzióinak elemzése és előléptetése vezérelt módon, végpontok használatával. Akár hat verziót is üzembe helyezhet egyetlen végpont mögött. A végpontok a következő képességeket biztosítják:

* Adja __meg az egyes végpontok számára továbbított pontozási forgalom százalékos arányát__. Tegyük fel például, hogy a forgalom 20%-át a "teszt" végpontra és a 80%-ra irányítja

    > [!NOTE]
    > Ha nem veszi figyelembe a forgalom 100%-át, a fennmaradó százalékot a rendszer az __alapértelmezett__ végponti verzióra irányítja. Ha például a "teszt" végpont-verziót konfigurálja a forgalom 10%-ában, a "Prod" pedig 30%-ot, a fennmaradó 60%-ot az alapértelmezett végponti verzióra küldi a rendszer.
    >
    > Az első végponton létrehozott verzió automatikusan alapértelmezettként van konfigurálva. Ezt a `is_default=True` végpont verziójának létrehozásakor vagy frissítésekor lehet beállítani.
     
* Végpont verziójának címkézése __vezérlőelemként__ vagy __kezelésként__. Előfordulhat például, hogy az aktuális üzemi végpont verziója a vezérlő, míg a lehetséges új modellek kezelési verzióként vannak telepítve. A kezelési verziók teljesítményének kiértékelése után, ha az egyik a jelenlegi vezérlőt végzi el, az új éles környezetbe/szabályozásba kerül.

    > [!NOTE]
    > Csak __egy__ vezérlőt használhat. Több kezelést is használhat.

Az alkalmazások elemzése lehetővé teszi a végpontok és a telepített verziók működési mérőszámának megtekintését.

### <a name="create-an-endpoint"></a>Végpont létrehozása
Ha készen áll a modellek üzembe helyezésére, hozzon létre egy pontozási végpontot, és telepítse az első verziót. Az alábbi példa bemutatja, hogyan helyezheti üzembe és hozhatja létre a végpontot az SDK használatával. Az első üzembe helyezés alapértelmezett verzióként lesz meghatározva, ami azt jelenti, hogy az összes verzióban a nem meghatározott forgalom százalékos értéke az alapértelmezett verzió lesz.  

> [!TIP]
> A következő példában a konfiguráció a kezdeti végpont verzióját állítja be a forgalom 20%-ának kezelésére. Mivel ez az első végpont, ez az alapértelmezett verzió is. És mivel a forgalom más 80%-ában nem rendelkezünk más verziókkal, a rendszer az alapértelmezett értéket is átirányítja. Egészen addig, amíg a forgalom százalékos arányát elkerülő más verziók üzembe vannak helyezve, ez a forgalom 100%-át ténylegesen megkapja.

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint"
version_name= "versiona"
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 # Wait for he process to complete
 endpoint.wait_for_deployment(True)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Verziók frissítése és hozzáadása egy végponthoz

Adjon hozzá egy újabb verziót a végponthoz, és konfigurálja a pontozási forgalom százalékos értékének megadását a verzióra. A verzióknak két típusa van, egy vezérlő és egy kezelési verzió. Több kezelési verzió is felhasználható egyetlen vezérlő verziójának összehasonlítására.

> [!TIP]
> A következő kódrészlet által létrehozott második verzió a forgalom 10%-át fogadja el. Az első verzió 20%-ra van konfigurálva, így a forgalom csak a 30%-a lesz konfigurálva adott verzióra. A fennmaradó 70%-ot az első végpont-verzióra küldi a rendszer, mert az egyben az alapértelmezett verzió is.

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
endpoint.wait_for_deployment(True)
```

Meglévő verziók frissítése vagy törlése egy végponton. Megváltoztathatja a verzió alapértelmezett típusát, a vezérlő típusát és a forgalmi percentilis értéket. A következő példában a második verzió a 40%-ra növeli a forgalmat, és mostantól az alapértelmezett.

> [!TIP]
> A következő kódrészlet után a második verzió már alapértelmezett. Most már 40%-ra van konfigurálva, míg az eredeti verzió még 20%-ra van konfigurálva. Ez azt jelenti, hogy a forgalom 40%-át nem a verzió konfigurációi teszik ki. A maradék forgalom a második verzióra lesz átirányítva, mivel most már alapértelmezett. A forgalom 80%-át hatékonyan fogadja.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)
# Wait for the process to complete before deleting
endpoint.wait_for_deployment(true)
# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```


## <a name="web-service-authentication"></a>Webszolgáltatás-hitelesítés

Az Azure Kubernetes szolgáltatásba való üzembe helyezéskor a __kulcs alapú__ hitelesítés alapértelmezés szerint engedélyezve van. Engedélyezheti a __jogkivonat-alapú__ hitelesítést is. A jogkivonat-alapú hitelesítéshez az szükséges, hogy az ügyfelek egy Azure Active Directory fiókot használjanak a hitelesítési jogkivonat igényléséhez, amely a központilag telepített szolgáltatásra irányuló kérések elvégzésére szolgál.

A hitelesítés __letiltásához__ állítsa be a `auth_enabled=False` paramétert a központi telepítési konfiguráció létrehozásakor. Az alábbi példa az SDK használatával letiltja a hitelesítést:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Az ügyfélalkalmazások általi hitelesítéssel kapcsolatos információkért tekintse meg a [webszolgáltatásként üzembe helyezett Azure Machine learning modell](how-to-consume-web-service.md)felhasználását ismertető témakört.

### <a name="authentication-with-keys"></a>Hitelesítés kulcsokkal

Ha a kulcsos hitelesítés engedélyezve van, a `get_keys` metódussal kérheti le az elsődleges és a másodlagos hitelesítési kulcsot:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Ha újra kell létrehoznia egy kulcsot, használja a következőt[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Hitelesítés jogkivonatokkal

A jogkivonat-hitelesítés engedélyezéséhez állítsa be a `token_auth_enabled=True` paramétert egy központi telepítés létrehozásakor vagy frissítésekor. Az alábbi példa lehetővé teszi a jogkivonat-hitelesítés használatát az SDK-val:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Ha engedélyezve van a jogkivonat-hitelesítés, a metódus használatával kérhet `get_token` le egy JWT-tokent és a jogkivonat lejárati idejét:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> A jogkivonat időpontját követően új jogkivonatot kell kérnie `refresh_by` .
>
> A Microsoft nyomatékosan javasolja, hogy a Azure Machine Learning munkaterületet ugyanabban a régióban hozza létre, mint az Azure Kubernetes Service-fürtöt. A webszolgáltatások tokenekkel történő hitelesítéséhez a webszolgáltatás meghívja a Azure Machine Learning munkaterület létrehozásához használt régiót. Ha a munkaterület régiója nem érhető el, akkor sem fogja tudni lehívni a webszolgáltatáshoz tartozó jogkivonatot, ha a fürt a munkaterülettől eltérő régióban található. Ez gyakorlatilag a jogkivonat-alapú hitelesítés nem érhető el, amíg a munkaterület régiója újra elérhetővé nem válik. Emellett minél nagyobb a távolság a fürt régiója és a munkaterület régiója között, annál hosszabb ideig tart a token beolvasása.
>
> Jogkivonat lekéréséhez az Azure Machine Learning SDK-t vagy az az [ml Service Get-Access-Token](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-get-access-token) parancsot kell használnia.

## <a name="update-the-web-service"></a>Webszolgáltatás frissítése

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>További lépések

* [Biztonságos kísérletezés és következtetés egy virtuális hálózaton](how-to-enable-virtual-network.md)
* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [Üzembe helyezés hibaelhárítása](how-to-troubleshoot-deployment.md)
* [Webszolgáltatás biztonságossá tétele a TLS használatával Azure Machine Learning](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett ML-modell felhasználása](how-to-consume-web-service.md)
* [A Azure Machine Learning modellek monitorozása a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelési modellekhez](how-to-enable-data-collection.md)

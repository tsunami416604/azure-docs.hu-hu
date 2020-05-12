---
title: Hálózati elkülönítés & adatvédelem
titleSuffix: Azure Machine Learning
description: Egy elszigetelt Azure-Virtual Network Azure Machine Learning használatával biztosíthatja a kísérletezést/képzést, valamint a következtetési/pontozási feladatokat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 05/11/2020
ms.custom: contperfq4
ms.openlocfilehash: 5099cc2ce2228bcdbf49d3484e488e7373883ec0
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83119044"
---
# <a name="secure-your-machine-learning-lifecycles-with-private-virtual-networks"></a>A gépi tanulási életciklusok biztonságossá tétele privát virtuális hálózatokkal
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan különítheti el a kísérletezési/betanítási feladatokat és a következtetési/pontozási feladatokat Azure Machine Learning egy Azure-Virtual Network (vnet) belül. Emellett néhány *speciális biztonsági beállításról*is tájékozódhat, amelyek nem szükségesek az alapszintű és a kísérleti használati esetekben.

> [!WARNING]
> Ha a mögöttes tárterület virtuális hálózaton van, a felhasználók nem használhatják a Azure Machine Learning Studio webes felületét, beleértve a következőket:
> - drag-n-drop Designer
> - Automatikus gépi tanulás felhasználói felülete
> - Az Adatfeliratok felhasználói felülete
> - Adathalmazok felhasználói felülete
> - Notebooks
> 
> Ha megpróbál, a következőhöz hasonló hibaüzenet jelenik meg:`__Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.__`

## <a name="what-is-a-vnet"></a>Mi az a VNET?

A **virtuális hálózat** biztonsági határként működik, és az Azure-erőforrásokat a nyilvános internetről különíti el. Egy Azure-beli virtuális hálózatot is csatlakoztathat a helyszíni hálózathoz. A hálózatok összekapcsolásával biztonságosan betaníthatja a modelleket, és elérheti az üzembe helyezett modelleket a következtetésekhez.

A Azure Machine Learning más Azure-szolgáltatásokra támaszkodik a számítási erőforrások, más néven [számítási célok](concept-compute-target.md), a modellek betanítása és üzembe helyezése érdekében. A célok létrehozhatók egy virtuális hálózaton belül. Használhatja például Azure Machine Learning számítást a modell betanításához, majd a modell üzembe helyezéséhez az Azure Kubernetes szolgáltatásban (ak). 


## <a name="prerequisites"></a>Előfeltételek

+ Egy Azure Machine Learning [munkaterület](how-to-manage-workspace.md).

+ Az [Azure Virtual Network szolgáltatás](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) és az [IP-hálózatkezelés](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)általános munkaismerete.

+ Meglévő virtuális hálózat és alhálózat a számítási erőforrásokkal való használathoz.

## <a name="private-endpoints"></a>Privát végpontok

Az [Azure privát hivatkozását is engedélyezheti](how-to-configure-private-link.md) , ha privát végponton keresztül csatlakozik a munkaterülethez. A magánhálózati végpont a virtuális hálózaton belüli magánhálózati IP-címek készlete. [Ismerje meg, hogyan állíthatja be ezt a privát végpontot.](how-to-configure-private-link.md)



> [!TIP]
> A virtuális hálózat és a magánhálózati kapcsolat összekapcsolható a munkaterület és az egyéb Azure-erőforrások közötti kommunikáció védelme érdekében. Bizonyos kombinációk azonban nagyvállalati kiadási munkaterületet igényelnek. A következő táblázat segítségével megismerheti, hogy milyen forgatókönyvek szükségesek a vállalati kiadáshoz:
>
> | Forgatókönyv | Enterprise</br>Edition | Basic</br>Edition |
> | ----- |:-----:|:-----:| 
> | Nincs virtuális hálózat vagy privát hivatkozás | ✔ | ✔ |
> | Privát hivatkozás nélküli munkaterület. Egyéb erőforrások (a Azure Container Registry kivételével) egy virtuális hálózaton | ✔ | ✔ |
> | Privát hivatkozás nélküli munkaterület. Egyéb források privát hivatkozással | ✔ | |
> | Munkaterület privát hivatkozással. Egyéb erőforrások (a Azure Container Registry kivételével) egy virtuális hálózaton | ✔ | ✔ |
> | Munkaterület és bármely más, privát hivatkozással rendelkező erőforrás | ✔ | |
> | Munkaterület privát hivatkozással. Egyéb források magánhálózati vagy virtuális hálózat nélkül | ✔ | ✔ |
> | Azure Container Registry virtuális hálózaton | ✔ | |
> | Ügyfél által felügyelt kulcsok a munkaterülethez | ✔ | |
> 

> [!WARNING]
> Azure Machine Learning számítási példányok előzetes verziója nem támogatott olyan munkaterületen, amelyben engedélyezve van a magánhálózati hivatkozás.
> 
> A Azure Machine Learning nem támogatja olyan Azure Kubernetes-szolgáltatás használatát, amelyen engedélyezve van a privát kapcsolat. Ehelyett használhatja az Azure Kubernetes szolgáltatást egy virtuális hálózaton. További információkért lásd: [Azure-beli Virtual Network biztonságossá tétele és következtetések](how-to-enable-virtual-network.md)elvégzése az Azure-on belül.


<a id="amlcompute"></a>

## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>Számítási fürtök & példányok

Ha [felügyelt Azure Machine learning **számítási célt** ](concept-compute-target.md#azure-machine-learning-compute-managed) vagy [Azure Machine learning számítási **példányt** ](concept-compute-instance.md) szeretne használni egy virtuális hálózaton, a következő hálózati követelményeknek kell teljesülniük:

> [!div class="checklist"]
> * A virtuális hálózatnak ugyanabban az előfizetésben és régióban kell lennie, mint a Azure Machine Learning munkaterületnek.
> * A számítási példányhoz vagy fürthöz megadott alhálózatnak elegendő, nem hozzárendelt IP-címmel kell rendelkeznie a célként kijelölt virtuális gépek számának kielégítéséhez. Ha az alhálózat nem rendelkezik elegendő nem hozzárendelt IP-címmel, a számítási fürt részlegesen le lesz foglalva.
> * Ellenőrizze, hogy a virtuális hálózat előfizetése vagy erőforráscsoport biztonsági szabályzatai vagy zárolásai korlátozzák-e az engedélyeket a virtuális hálózat kezeléséhez. Ha a virtuális hálózat védelmét úgy tervezi, hogy korlátozza a forgalmat, hagyjon meg néhány portot a számítási szolgáltatás számára. További információ: a [szükséges portok](#mlcports) szakasz.
> * Ha egy virtuális hálózatban több számítási példányt vagy fürtöt szeretne létrehozni, előfordulhat, hogy egy vagy több erőforrásra vonatkozó kvóta-növelést kell kérnie.
> * Ha a munkaterülethez tartozó Azure Storage-fiók (ok) is védett virtuális hálózatban, akkor a Azure Machine Learning számítási példánnyal vagy fürttel azonos virtuális hálózatban kell lenniük. 

> [!TIP]
> A Machine Learning számítási példány vagy fürt automatikusan további hálózati erőforrásokat foglal le __a virtuális hálózatot tartalmazó erőforráscsoporthoz__. A szolgáltatás minden számítási példányhoz vagy fürthöz a következő erőforrásokat foglalja le:
> 
> * Egy hálózati biztonsági csoport
> * Egy nyilvános IP-cím
> * Egy Load Balancer
> 
> Fürtök esetében ezeket az erőforrásokat a rendszer minden alkalommal törli (és újból létrehozza), amikor a fürt 0 csomópontra van lebontva, azonban az erőforrásokat a rendszer a példány teljes törlése előtt megtartja (a Leállítás nem távolítja el az erőforrásokat). 
> Ezekre az erőforrásokra az előfizetésben meghatározott [erőforráskvóták](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) vonatkoznak.


### <a name="required-ports"></a><a id="mlcports"></a>Szükséges portok

Machine Learning Compute jelenleg a Azure Batch szolgáltatás használatával helyezi üzembe a virtuális gépeket a megadott virtuális hálózaton. Az alhálózatnak engedélyeznie kell a bejövő kommunikációt a Batch szolgáltatástól. Ezzel a kommunikációval ütemezhet a Machine Learning Compute-csomópontokon futó futtatásokat, és kommunikálhat az Azure Storage szolgáltatással és más erőforrásokkal. A Batch szolgáltatás hálózati biztonsági csoportokat (NSG) helyez üzembe a virtuális gépekhez csatolt hálózati adapterek (NIC-EK) szintjén. Ezek az NSG-k automatikusan konfigurálnak bejövő és kimenő szabályokat a következő forgalom engedélyezéséhez:

- Bejövő TCP-forgalom a 29876-es és a 29877-es portokon a __BatchNodeManagement__ __szolgáltatási címkéjén__ .

    ![A BatchNodeManagement szolgáltatás címkéjét használó bejövő szabály](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- Választható Bejövő TCP-forgalom a 22-es porton a távoli hozzáférés engedélyezéséhez. Csak akkor használja ezt a portot, ha az SSH használatával szeretne csatlakozni a nyilvános IP-címen.

- Kimenő forgalom bármilyen porton keresztül a virtuális hálózathoz.

- Kimenő forgalom bármilyen porton keresztül az internetre.

- A számítási példány bejövő TCP-forgalma a 44224-as porton a __AzureMachineLearning__ __szolgáltatási címkéjén__ .

Körültekintően járjon el a bejövő vagy kimenő szabályok módosításakor és hozzáadásakor a Batch által konfigurált NSG-kben. Ha egy NSG blokkolja a számítási csomópontok felé irányuló kommunikációt, a számítási szolgáltatás nem használhatóra állítja a számítási csomópontok állapotát.

Nem kell megadnia a NSG az alhálózat szintjén, mert a Azure Batch szolgáltatás konfigurálja a saját NSG. Ha azonban a megadott alhálózat társított NSG vagy tűzfallal rendelkezik, a korábban említettek szerint konfigurálja a bejövő és kimenő biztonsági szabályokat.

A Azure Portal NSG-szabályának konfigurációja az alábbi képeken látható:

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="A Machine Learning Computehoz tartozó bejövő NSG-szabályok" border="true":::



![A Machine Learning Compute kimenő NSG szabályai](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a>A virtuális hálózat kimenő kapcsolatának korlátozása

Ha nem szeretné az alapértelmezett kimenő szabályokat használni, és korlátozni szeretné a virtuális hálózat kimenő hozzáférését, kövesse az alábbi lépéseket:

- A kimenő internetkapcsolat megtagadása a NSG szabályok használatával.

- __Számítási példány__ vagy __számítási fürt__esetén korlátozza a kimenő forgalmat a következő elemekre:
   - Azure Storage a __Storage. RegionName__ __szolgáltatási címkéjével__ . Ahol az `{RegionName}` egy Azure-régió neve.
   - Azure Container Registry a __AzureContainerRegistry. RegionName__ __szolgáltatási címkéje__ segítségével. Ahol az `{RegionName}` egy Azure-régió neve.
   - Azure Machine Learning a __AzureMachineLearning__ __szolgáltatási címkéjének__ használatával
   - Azure Resource Manager a __AzureResourceManager__ __szolgáltatási címkéjének__ használatával
   - Azure Active Directory a __AzureActiveDirectory__ __szolgáltatási címkéjének__ használatával

A Azure Portal NSG-szabályának konfigurációja a következő képen látható:

[![A Machine Learning Compute kimenő NSG szabályai](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Ha a Microsoft által biztosított alapértelmezett Docker-rendszerképeket szeretné használni, és engedélyezi a felhasználó által felügyelt függőségeket, akkor a __MicrosoftContainerRegistry. Region_Name__ (például MicrosoftContainerRegistry. EastUS) __szolgáltatási címkét__ is kell használnia.
>
> Erre a konfigurációra akkor van szükség, ha az alábbi kódrészletekhez hasonló kóddal rendelkezik a betanítási szkriptek részeként:
>
> __RunConfig-képzés__
> ```python
> # create a new runconfig object
> run_config = RunConfiguration()
> 
> # configure Docker 
> run_config.environment.docker.enabled = True
> # For GPU, use DEFAULT_GPU_IMAGE
> run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
> run_config.environment.python.user_managed_dependencies = True
> ```
>
> __Kalkulátor-képzés__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="user-defined-routes-for-forced-tunneling"></a>A kényszerített bújtatáshoz tartozó felhasználó által megadott útvonalak

Ha a Machine Learning Compute kényszerített bújtatást használ, adja hozzá a [felhasználó által megadott útvonalakat (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) a számítási erőforrást tartalmazó alhálózathoz.

* Hozzon létre egy UDR minden olyan IP-címhez, amelyet a Azure Batch szolgáltatás használ azon a régióban, ahol az erőforrásai léteznek. Ezek a UDR lehetővé teszik a Batch szolgáltatás számára a feladatütemezés számítási csomópontjaival való kommunikációját. Adja hozzá azt a Azure Machine Learning-szolgáltatáshoz tartozó IP-címet is, ahol az erőforrások léteznek, mivel ez szükséges a számítási példányokhoz való hozzáféréshez. A Batch szolgáltatás és a Azure Machine Learning szolgáltatás IP-címeinek listájának megjelenítéséhez használja a következő módszerek egyikét:

    * Töltse le az [Azure IP-címtartományok és a szolgáltatás címkéit](https://www.microsoft.com/download/details.aspx?id=56519) , és keresse meg a és a fájlt `BatchNodeManagement.<region>` `AzureMachineLearning.<region>` , ahol `<region>` az az Azure-régió.

    * Az adatok letöltéséhez használja az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) -t. Az alábbi példa letölti az IP-cím adatait, és kiszűri az USA 2. keleti régiójának információit:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        ```

* A helyszíni hálózati berendezés nem tilthatja le az Azure Storage-ba irányuló kimenő forgalmat. Pontosabban az URL-címek a és a formátumúak `<account>.table.core.windows.net` `<account>.queue.core.windows.net` `<account>.blob.core.windows.net` .

A UDR hozzáadásakor adja meg az útvonalat az egyes kapcsolódó batch IP-címek előtagjaként, és állítsa be a __következő ugrás típusát__ az __Internet__értékre. Az alábbi képen látható példa erre a UDR mutat a Azure Portalban:

![UDR – példa a címek előtagjaként való használatra](./media/how-to-enable-virtual-network/user-defined-route.png)

További információ: [Azure batch készlet létrehozása egy virtuális hálózaton](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Számítási fürt létrehozása egy virtuális hálózaton

Machine Learning Compute-fürt létrehozásához kövesse az alábbi lépéseket:

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com/), majd válassza ki az előfizetést és a munkaterületet.

1. Válassza a bal oldali __számítás__ lehetőséget.

1. Válassza ki a __betanítási fürtök__ lehetőséget a középpontban, majd válassza a elemet __+__ .

1. Az __új betanítási fürt__ párbeszédpanelen bontsa ki a __Speciális beállítások__ szakaszt.

1. Ha ezt a számítási erőforrást virtuális hálózat használatára szeretné konfigurálni, hajtsa végre a következő műveleteket a __virtuális hálózat konfigurálása__ szakaszban:

    1. Az __erőforráscsoport__ legördülő listában válassza ki a virtuális hálózatot tartalmazó erőforráscsoportot.
    1. A __virtuális hálózat__ legördülő listában válassza ki azt a virtuális hálózatot, amely az alhálózatot tartalmazza.
    1. Az __alhálózat__ legördülő listában válassza ki a használni kívánt alhálózatot.

   ![Machine Learning Compute virtuális hálózati beállításai](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Machine Learning Compute-fürtöt a Azure Machine Learning SDK használatával is létrehozhat. A következő kód egy új Machine Learning Compute fürtöt hoz létre `default` egy nevű virtuális hálózat alhálózatában `mynetwork` :

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

A létrehozási folyamat befejeződése után a modellt egy kísérletben a fürt használatával kell betanítani. További információkért lásd: [számítási cél kiválasztása és használata képzéshez](how-to-set-up-training-targets.md).

## <a name="use-a-storage-account-for-your-workspace"></a>A munkaterülethez tartozó Storage-fiók használata

Ha egy virtuális hálózatban lévő munkaterülethez Azure Storage-fiókot szeretne használni, kövesse az alábbi lépéseket:

1. Hozzon létre egy számítási erőforrást (például egy Machine Learning számítási példányt vagy fürtöt) egy virtuális hálózat mögött, vagy rendeljen hozzá egy számítási erőforrást a munkaterülethez (például egy HDInsight-fürthöz, virtuális géphez vagy Azure Kubernetes Service-fürthöz). A számítási erőforrás lehet kísérletezés vagy modell üzembe helyezése.

   További információ: [Machine learning számítás használata](#amlcompute), [virtuális gép vagy HDInsight-fürt](#vmorhdi)használata, és az [Azure Kubernetes szolgáltatás használata](#aksvnet) című rész ebben a cikkben.

1. A Azure Portal lépjen a munkaterülethez csatolt tárterületre.

   [![Az Azure Machine Learning munkaterülethez csatolt tárterület](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Az **Azure Storage** lapon válassza a __tűzfalak és virtuális hálózatok__lehetőséget.

   ![A Azure Portal Azure Storage lapjának "tűzfalak és virtuális hálózatok" területén](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. A __tűzfalak és virtuális hálózatok__ oldalon hajtsa végre a következő műveleteket:
    - Válassza a __Kiválasztott hálózatok__ lehetőséget.
    - A __virtuális hálózatok__területen válassza a __meglévő virtuális hálózati kapcsolat hozzáadása__ elemet. Ez a művelet hozzáadja azt a virtuális hálózatot, ahol a számítás található (lásd: 1. lépés).

        > [!IMPORTANT]
        > A Storage-fióknak ugyanabban a virtuális hálózatban és alhálózatban kell lennie, mint a képzéshez vagy következtetéshez használt számítási példányok vagy fürtök.

    - Jelölje be a __megbízható Microsoft-szolgáltatások számára a Storage-fiók elérésének engedélyezése__ jelölőnégyzetet.

    > [!IMPORTANT]
    > Ha a Azure Machine Learning SDK-val dolgozik, a fejlesztési környezetnek képesnek kell lennie csatlakozni az Azure Storage-fiókhoz. Ha a Storage-fiók egy virtuális hálózaton belül van, a tűzfalnak engedélyeznie kell a hozzáférést a fejlesztői környezet IP-címéről.
    >
    > A Storage-fiókhoz való hozzáférés engedélyezéséhez keresse fel a Storage-fiókhoz tartozó __tűzfalakat és virtuális hálózatokat__ a *fejlesztői ügyfél webböngészőjéből*. Ezután használja az __ügyfél IP-címének hozzáadása__ jelölőnégyzetet az ügyfél IP-címének a __címtartományból__való hozzáadásához. A __címtartomány__ mező használatával manuálisan is megadhatja a fejlesztési környezet IP-címét. Miután hozzáadta az ügyfél IP-címét, az SDK-val elérheti a Storage-fiókot.

   [![A Azure Portal tűzfalak és virtuális hálózatok panelje](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!IMPORTANT]
> Az _alapértelmezett Storage-fiókot_ a virtuális hálózatban lévő Azure Machine learning vagy _nem alapértelmezett tárolási fiókok_ számára is elhelyezheti.
>
> A munkaterület létrehozásakor a rendszer automatikusan kiépíti az alapértelmezett Storage-fiókot.
>
> A nem alapértelmezett tárolási fiókok esetében a `storage_account` [ `Workspace.create()` függvény](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) paramétere lehetővé teszi egyéni Storage-fiók megadását az Azure erőforrás-azonosító alapján.


<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Ha az Azure Kubernetes Service (ak) szolgáltatást egy virtuális hálózatban szeretné hozzáadni a munkaterülethez, kövesse az alábbi lépéseket:

> [!IMPORTANT]
> Az alábbi eljárás megkezdése előtt kövesse a [speciális hálózatkezelés konfigurálása az Azure Kubernetes szolgáltatásban (ak)](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites) – útmutató és a fürt IP-címzésének megtervezése című témakör előfeltételeit.
>
> Az AK-példánynak és az Azure-beli virtuális hálózatnak ugyanabban a régióban kell lennie. Ha a virtuális hálózatban a munkaterület által használt Azure Storage-fiók (oka) t védi, azoknak ugyanabban a virtuális hálózatban kell lenniük, mint az AK-példány.

> [!WARNING]
> A Azure Machine Learning nem támogatja olyan Azure Kubernetes-szolgáltatás használatát, amelyen engedélyezve van a privát kapcsolat.

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com/), majd válassza ki az előfizetést és a munkaterületet.

1. Válassza a bal oldali __számítás__ lehetőséget.

1. Válassza a középpontban a kikövetkeztetés __fürtök__ lehetőséget, majd válassza a lehetőséget __+__ .

1. A New következtető __fürt__ párbeszédpanelen válassza a __speciális__ a __hálózati konfiguráció__alatt lehetőséget.

1. Ha ezt a számítási erőforrást virtuális hálózat használatára szeretné konfigurálni, hajtsa végre a következő műveleteket:

    1. Az __erőforráscsoport__ legördülő listában válassza ki a virtuális hálózatot tartalmazó erőforráscsoportot.
    1. A __virtuális hálózat__ legördülő listában válassza ki azt a virtuális hálózatot, amely az alhálózatot tartalmazza.
    1. Az __alhálózat__ legördülő listában válassza ki az alhálózatot.
    1. A __Kubernetes szolgáltatás címtartomány__ mezőjébe írja be a Kubernetes szolgáltatás címtartományt. Ez a címtartomány egy osztály nélküli tartományok közötti útválasztás (CIDR) jelölésű IP-címtartományt használ a fürt számára elérhető IP-címek definiálásához. Nem lehet átfedésben egyetlen alhálózati IP-tartománnyal sem (például 10.0.0.0/16).
    1. A __KUBERNETES DNS-szolgáltatás IP-címe__ mezőbe írja be a Kubernetes DNS-szolgáltatás IP-címét. Ezt az IP-címet a Kubernetes DNS szolgáltatáshoz rendeli a rendszer. A Kubernetes a szolgáltatási címtartomány (például 10.0.0.10) közé kell esnie.
    1. A __Docker Bridge-címe__ mezőbe írja be a Docker-híd címe mezőt. Ezt az IP-címet a Docker-híd rendeli hozzá. Nem lehet alhálózati IP-címtartományok vagy a Kubernetes szolgáltatási címtartomány (például 172.17.0.1/16).

   ![Azure Machine Learning: Machine Learning Compute virtuális hálózati beállítások](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Győződjön meg arról, hogy a virtuális hálózatot vezérlő NSG-csoport rendelkezik egy bejövő biztonsági szabállyal, amely engedélyezi a pontozási végpontot, hogy a virtuális hálózaton kívülről is meghívható legyen.
   > [!IMPORTANT]
   > Tartsa meg a NSG alapértelmezett kimenő szabályait. További információ: [biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)alapértelmezett biztonsági szabályai.

   [![Egy bejövő biztonsági szabály](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

A Azure Machine Learning SDK-val is hozzáadhatja az Azure Kubernetes szolgáltatást egy virtuális hálózatban. Ha már van egy AK-fürtje egy virtuális hálózaton, csatolja azt a munkaterülethez a következő témakörben leírtak szerint: [üzembe helyezés az AK](how-to-deploy-and-where.md)-ban. A következő kód létrehoz egy új AK-példányt `default` egy nevű virtuális hálózat alhálózatában `mynetwork` :

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

A létrehozási folyamat befejezésekor futtathat következtetéseket vagy modell-pontozást egy virtuális hálózat mögötti AK-fürtön. További információ: [üzembe helyezés az AK](how-to-deploy-and-where.md)-ban.

### <a name="use-private-ips-with-azure-kubernetes-service"></a>Magánhálózati IP-címek használata az Azure Kubernetes Service-szel

Alapértelmezés szerint egy nyilvános IP-cím van hozzárendelve az AK-beli üzemelő példányokhoz. Ha AK-t használ egy virtuális hálózaton belül, a magánhálózati IP-címet is használhatja helyette. A magánhálózati IP-címek csak a virtuális hálózaton vagy a csatlakoztatott hálózatokon belül érhetők el.

A magánhálózati IP-címek a _belső terheléselosztó_használatára való konfigurálásával engedélyezhetők. 

> [!IMPORTANT]
> Az Azure Kubernetes Service-fürt létrehozásakor nem engedélyezheti a magánhálózati IP-címet. Engedélyezni kell egy meglévő fürt frissítését.

A következő kódrészlet bemutatja, hogyan **hozhat létre egy új AK-fürtöt**, majd hogyan frissítheti egy magánhálózati IP-/belső terheléselosztó használatára:

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__Azure CLI__

```azurecli-interactive
az rest --method put --uri https://management.azure.com"/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>?api-version=2018-11-19 --body @body.json
```

A `body.json` parancs által hivatkozott fájl tartalma hasonló a következő JSON-dokumentumhoz:

```json
{ 
    "location": "<region>", 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>", 
        "computeType": "AKS", 
        "provisioningState": "Succeeded", 
        "properties": { 
            "loadBalancerType": "InternalLoadBalancer", 
            "agentCount": <agent-count>, 
            "agentVmSize": "vm-size", 
            "clusterFqdn": "<cluster-fqdn>" 
        } 
    } 
} 
```

> [!NOTE]
> A terheléselosztó jelenleg nem konfigurálható egy meglévő fürt __csatolási__ műveletének végrehajtásakor. Először csatlakoztatnia kell a fürtöt, majd egy frissítési műveletet kell végrehajtania a terheléselosztó módosításához.

A belső terheléselosztó az AK-val való használatáról további információt a [belső Load Balancer használata az Azure Kubernetes szolgáltatással](/azure/aks/internal-lb)című témakörben talál.

## <a name="use-azure-container-instances-aci"></a>Azure Container Instances használata (ACI)

A Azure Container Instances a modell telepítésekor dinamikusan jönnek létre. Annak engedélyezéséhez, hogy a Azure Machine Learning az ACI-t a virtuális hálózaton belül hozza létre, engedélyeznie kell az alhálózati __delegálást__ az üzemelő példány által használt alhálózathoz.

Ha egy virtuális hálózatban szeretné használni az ACI-t a munkaterületére, kövesse az alábbi lépéseket:

1. Ha engedélyezni szeretné az alhálózati delegálást a virtuális hálózaton, használja az [alhálózati delegálás hozzáadása vagy eltávolítása](../virtual-network/manage-subnet-delegation.md) című cikk információit. A delegálást engedélyezheti virtuális hálózat létrehozásakor, vagy hozzáadhatja egy meglévő hálózathoz.

    > [!IMPORTANT]
    > A delegálás engedélyezésekor használja `Microsoft.ContainerInstance/containerGroups` a __meghatalmazott alhálózatot a szolgáltatás__ értékéhez.

2. Telepítse a modellt a [AciWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-)használatával, használja a `vnet_name` és a `subnet_name` paramétereket. Állítsa be ezeket a paramétereket a virtuális hálózat nevére és az alhálózatra, ahol engedélyezte a delegálást.

## <a name="azure-firewall"></a>Azure Firewall

További információ a Azure Machine Learning és a Azure Firewall használatáról: [Azure Machine learning munkaterület használata Azure Firewall mögött](how-to-access-azureml-behind-firewall.md).

## <a name="azure-container-registry"></a>Azure Container Registry

> [!IMPORTANT]
> A Azure Container Registry (ACR) egy virtuális hálózaton belül helyezhető el, azonban a következő előfeltételeknek kell megfelelnie:
>
> * A Azure Machine Learning munkaterületének Enterprise Edition rendszernek kell lennie. A frissítéssel kapcsolatos további információkért lásd: [frissítés a Enterprise Edition](how-to-manage-workspace.md#upgrade)verzióra.
> * A Azure Container Registrynak prémium verziójúnak kell lennie. További információ a frissítésről: a [SKU módosítása](/azure/container-registry/container-registry-skus#changing-skus).
> * A Azure Container Registrynak ugyanabban a virtuális hálózatban és alhálózatban kell lennie, mint a betanításhoz vagy következtetéshez használt Storage-fióknak és számítási céloknak.
> * A Azure Machine Learning-munkaterületnek tartalmaznia kell egy [Azure Machine learning számítási fürtöt](how-to-set-up-training-targets.md#amlcompute).
>
>     Ha az ACR egy virtuális hálózat mögött van, Azure Machine Learning nem tud közvetlenül Docker-lemezképeket felépíteni. Ehelyett a rendszer a számítási fürtöt használja a rendszerképek létrehozásához.

1. A munkaterület Azure Container Registry nevének megkereséséhez használja az alábbi módszerek egyikét:

    __Azure Portal__

    A munkaterület Áttekintés szakaszában a __beállításjegyzék__ értéke a Azure Container Registryra hivatkozik.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Azure Container Registry a munkaterülethez" border="true":::

    __Azure CLI__

    Ha [telepítette az Azure CLI Machine learning bővítményét](reference-azure-machine-learning-cli.md), a `az ml workspace show` parancs használatával jelenítheti meg a munkaterület adatait.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    A parancs a következőhöz hasonló értéket ad vissza: `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` . A karakterlánc utolsó része a munkaterület Azure Container Registry neve.

1. A virtuális hálózathoz való hozzáférés korlátozásához kövesse a [hálózati hozzáférés konfigurálása a beállításjegyzékhez](../container-registry/container-registry-vnet.md#configure-network-access-for-registry)című témakör lépéseit. A virtuális hálózat hozzáadásakor válassza ki a virtuális hálózatot és az alhálózatot a Azure Machine Learning erőforrásaihoz.

1. A Azure Machine Learning Python SDK használatával konfigurálhat egy számítási fürtöt a Docker-rendszerképek létrehozásához. A következő kódrészlet bemutatja, hogyan teheti meg ezt:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > A Storage-fióknak, a számítási fürtnek és a Azure Container Registrynek a virtuális hálózat azonos alhálózatán kell lennie.
    
    További információkért tekintse meg a [frissítési ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) metódus-referenciát.

1. A következő Azure Resource Manager sablont kell alkalmaznia. Ez a sablon lehetővé teszi, hogy a munkaterület kommunikáljon az ACR-szel.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultArmId": {
        "type": "string"
        },
        "workspaceName": {
        "type": "string"
        },
        "containerRegistryArmId": {
        "type": "string"
        },
        "applicationInsightsArmId": {
        "type": "string"
        },
        "storageAccountArmId": {
        "type": "string"
        },
        "location": {
        "type": "string"
        }
    },
    "resources": [
        {
        "type": "Microsoft.MachineLearningServices/workspaces",
        "apiVersion": "2019-11-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "sku": {
            "tier": "enterprise",
            "name": "enterprise"
        },
        "properties": {
            "sharedPrivateLinkResources":
    [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
            "keyVault": "[parameters('keyVaultArmId')]",
            "containerRegistry": "[parameters('containerRegistryArmId')]",
            "applicationInsights": "[parameters('applicationInsightsArmId')]",
            "storageAccount": "[parameters('storageAccountArmId')]"
        }
        }
    ]
    }
    ```
    
## <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

A 2. generációs Azure Data Lake Storage az Azure Blob Storage-ra épülő big data-elemzési funkciók összessége. Használható a modellek Azure Machine Learning használatával való tanításához használt adattárolásra. 

Ha a Azure Machine Learning munkaterület virtuális hálózatán belül szeretné használni a 2. generációs Data Lake Storage, kövesse az alábbi lépéseket:

1. Hozzon létre egy Azure Data Lake Storage 2. generációs fiókot. További információ: [Azure Data Lake Storage Gen2 Storage-fiók létrehozása](../storage/blobs/data-lake-storage-quickstart-create-account.md).

1. Használja az előző szakasz 2-4-es lépéseit, és [használja a munkaterület Storage-fiókját](#use-a-storage-account-for-your-workspace)a fiók virtuális hálózatban való üzembe helyezéséhez.

Ha a Azure Machine Learningt Data Lake Storage Gen 2 virtuális hálózaton belül használja, kövesse az alábbi útmutatást:

* Ha az SDK használatával __hoz létre egy adatkészletet__, és a kódot futtató rendszer __nem a virtuális hálózaton__található, használja a `validate=False` paramétert. Ez a paraméter kihagyja az ellenőrzést, ami meghiúsul, ha a rendszer nem ugyanabban a virtuális hálózatban található, mint a Storage-fiók. További információ: [from_files ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) metódus.

* Ha Azure Machine Learning számítási példányt vagy számítási fürtöt használ a modellnek az adatkészlettel való betanításához, akkor annak a Storage-fiókkal azonos virtuális hálózatban kell lennie.

## <a name="key-vault-instance"></a>Key Vault-példány 

A munkaterülethez társított Key Vault-példányt a Azure Machine Learning a következő hitelesítő adatok tárolására használja:
* A társított Storage-fiókhoz tartozó kapcsolatok karakterlánca
* Az Azure Container repository példányainak jelszavai
* Adattárakhoz való kapcsolódási karakterláncok

Ha Azure Machine Learning kísérletezési képességeket szeretne használni a virtuális hálózat mögötti Azure Key Vault, kövesse az alábbi lépéseket:

1. Nyissa meg a munkaterülethez társított kulcstartót.

   [![Az Azure Machine Learning munkaterülethez társított kulcstartó](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. A **Key Vault** oldalon, a bal oldali panelen válassza a __tűzfalak és virtuális hálózatok__lehetőséget.

   ![A Key Vault panel "tűzfalak és virtuális hálózatok" szakasza](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. A __tűzfalak és virtuális hálózatok__ oldalon hajtsa végre a következő műveleteket:
    - __A hozzáférés engedélyezése lehetőségnél__válassza a __kiválasztott hálózatok__elemet.
    - A __virtuális hálózatok__területen válassza a __meglévő virtuális hálózatok hozzáadása__ lehetőséget annak a virtuális hálózatnak a hozzáadásához, ahol a kísérletezési számítás található.
    - A __megbízható Microsoft-szolgáltatások a tűzfal megkerülésének engedélyezése__területen válassza az __Igen__lehetőséget.

   [![A Key Vault panel "tűzfalak és virtuális hálózatok" szakasza](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)


## <a name="azure-databricks"></a>Azure Databricks

Ha Azure Databricks szeretne használni a munkaterülettel rendelkező virtuális hálózaton, a következő követelményeknek kell teljesülniük:

> [!div class="checklist"]
> * A virtuális hálózatnak ugyanabban az előfizetésben és régióban kell lennie, mint a Azure Machine Learning munkaterületnek.
> * Ha a munkaterülethez tartozó Azure Storage-fiók (ok) is védett virtuális hálózatban, akkor a Azure Databricks-fürttel megegyező virtuális hálózatban kell lenniük.
> * Az Azure Databricks által használt __databricks-__ és __databricks-__ alhálózatok mellett a virtuális hálózathoz létrehozott __alapértelmezett__ alhálózat is szükséges.

A Azure Databricks virtuális hálózattal való használatáról a [Azure Databricks üzembe helyezése az Azure-Virtual Network](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html)című témakörben olvashat bővebben.

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Virtuális gép vagy HDInsight-fürt

> [!IMPORTANT]
> A Azure Machine Learning csak az Ubuntut futtató virtuális gépeket támogatja.

Ha egy virtuális gépet vagy Azure HDInsight-fürtöt szeretne használni a munkaterülettel rendelkező virtuális hálózatban, kövesse az alábbi lépéseket:

1. Hozzon létre egy virtuális GÉPET vagy HDInsight-fürtöt a Azure Portal vagy az Azure CLI használatával, és helyezze üzembe a fürtöt egy Azure-beli virtuális hálózaton. További információkért tekintse át a következő cikkeket:
    * [Azure Virtual Networks létrehozása és kezelése Linux rendszerű virtuális gépekhez](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [HDInsight kiterjesztése Azure Virtual Network használatával](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Ha engedélyezni szeretné, hogy a Azure Machine Learning kommunikáljon a virtuális gép vagy fürt SSH-portjával, állítson be egy forrásoldali bejegyzést a hálózati biztonsági csoport számára. Az SSH-port általában a 22-es port. A forrásból érkező forgalom engedélyezéséhez hajtsa végre a következő műveleteket:

    * A __forrás__ legördülő listában válassza a __szolgáltatás címkéje__elemet.

    * A __forrás szolgáltatás címkéje__ legördülő listában válassza a __AzureMachineLearning__lehetőséget.

    * A __forrás porttartomány-tartományok__ legördülő listában válassza a elemet __*__ .

    * A __cél__ legördülő listában válassza a __bármelyik__lehetőséget.

    * A __cél porttartomány-tartományok__ legördülő listában válassza a __22__lehetőséget.

    * A __protokoll__területen válassza __a bármelyik__lehetőséget.

    * A __művelet__területen válassza az __Engedélyezés__lehetőséget.

   ![Bejövő szabályok a virtuális hálózaton belüli virtuálisgép-vagy HDInsight-fürtök kísérletezéséhez](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Tartsa meg a hálózati biztonsági csoport alapértelmezett kimenő szabályait. További információ: [biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)alapértelmezett biztonsági szabályai.

    Ha nem szeretné használni az alapértelmezett kimenő szabályokat, és szeretné korlátozni a virtuális hálózat kimenő hozzáférését, tekintse meg a [Kimenő kapcsolat korlátozása a virtuális hálózattal](#limiting-outbound-from-vnet) szakaszt.

1. Csatlakoztassa a virtuális gépet vagy a HDInsight-fürtöt a Azure Machine Learning munkaterülethez. További információ: [számítási célok beállítása a modell betanításához](how-to-set-up-training-targets.md).


## <a name="next-steps"></a>További lépések

* [Betanítási környezetek beállítása](how-to-set-up-training-targets.md)
* [Privát végpontok beállítása](how-to-configure-private-link.md)
* [A modellek üzembe helyezési helyének kiválasztása](how-to-deploy-and-where.md)
* [Webszolgáltatás biztonságossá tétele a TLS használatával Azure Machine Learning](how-to-secure-web-service.md)

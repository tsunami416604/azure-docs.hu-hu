---
title: Biztonságos kísérletek és következtetések a virtuális hálózaton
titleSuffix: Azure Machine Learning
description: megtudhatja, hogyan védheti meg a kísérletezési/betanítási feladatokat, valamint az Azure-Virtual Network belüli Azure Machine Learning következtetési/pontozási feladatait.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 11/13/2019
ms.openlocfilehash: 548b74dbaf36fa0a0b5f999d1de61a0c05241c61
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75690820"
---
# <a name="secure-azure-ml-experimentation-and-inference-jobs-within-an-azure-virtual-network"></a>Egy Azure-Virtual Networkon belül biztonságossá teheti az Azure ML-kísérletezést és a feladatok következtetéseit
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan védheti meg az Azure-Virtual Network (vnet) Azure Machine Learning kísérletezési/betanítási feladatait és következtetéseit/pontozási feladatait.

A **virtuális hálózat** biztonsági határként működik, és az Azure-erőforrásokat a nyilvános internetről különíti el. Egy Azure-beli virtuális hálózatot is csatlakoztathat a helyszíni hálózathoz. A hálózatok összekapcsolásával biztonságosan betaníthatja a modelleket, és elérheti az üzembe helyezett modelleket a következtetésekhez.

A Azure Machine Learning a számítási erőforrások egyéb Azure-szolgáltatásaira támaszkodik. A számítási erőforrások vagy [számítási célok](concept-compute-target.md)a modellek betanítására és üzembe helyezésére szolgálnak. A célok létrehozhatók egy virtuális hálózaton belül. Használhatja például a Microsoft Data Science Virtual Machinet egy modell betanításához, majd a modell üzembe helyezéséhez az Azure Kubernetes Service (ak) szolgáltatásban. A virtuális hálózatokkal kapcsolatos további információkért lásd: az [Azure Virtual Network áttekintése](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Ez a cikk részletes információkat is tartalmaz a *speciális biztonsági beállításokról*, az alapszintű és a kísérleti felhasználási esetekben nem szükséges információkról. A cikk bizonyos fejezetei számos különböző forgatókönyvhöz biztosítanak konfigurációs információkat. Nem kell megadnia az utasításokat sorrendben vagy teljes egészében.

> [!TIP]
> Hacsak nem kifejezetten kérik, az olyan erőforrások, mint például a Storage-fiókok vagy a számítási célok egy virtuális hálózaton belül, a gépi tanulási folyamatokkal és a nem folyamat-munkafolyamatokkal, például a parancsfájlok futtatásával is működni fognak.

> [!WARNING]
> A Microsoft nem támogatja a Azure Machine Learning Designer vagy az automatizált gépi tanulás (a studióból) használatát a virtuális hálózaton belüli erőforrásokkal.

## <a name="prerequisites"></a>Előfeltételek

+ Egy Azure Machine Learning [munkaterület](how-to-manage-workspace.md).

+ Az [Azure Virtual Network szolgáltatás](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) és az [IP-hálózatkezelés](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)általános munkaismerete.

+ Meglévő virtuális hálózat és alhálózat a számítási erőforrásokkal való használathoz.

## <a name="use-a-storage-account-for-your-workspace"></a>A munkaterülethez tartozó Storage-fiók használata

Ha Azure Storage-fiókot szeretne használni a virtuális hálózatban lévő munkaterülethez, tegye a következőket:

1. Hozzon létre egy számítási erőforrást (például egy Machine Learning számítási példányt vagy fürtöt) egy virtuális hálózat mögött, vagy rendeljen hozzá egy számítási erőforrást a munkaterülethez (például egy HDInsight-fürthöz, virtuális géphez vagy Azure Kubernetes Service-fürthöz). A számítási erőforrás lehet kísérletezés vagy modell üzembe helyezése.

   További információ: [Machine learning számítás használata](#amlcompute), [virtuális gép vagy HDInsight-fürt](#vmorhdi)használata, és az [Azure Kubernetes szolgáltatás használata](#aksvnet) című rész ebben a cikkben.

1. A Azure Portal lépjen a munkaterülethez csatolt tárterületre.

   [![a Azure Machine Learning munkaterülethez csatolt tárterületet](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Az **Azure Storage** lapon válassza a __tűzfalak és virtuális hálózatok__lehetőséget.

   ![A Azure Portal Azure Storage lapjának "tűzfalak és virtuális hálózatok" területén](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. A __tűzfalak és virtuális hálózatok__ lapon tegye a következőket:
    - Válassza a __Kiválasztott hálózatok__ lehetőséget.
    - A __virtuális hálózatok__területen válassza a __meglévő virtuális hálózati kapcsolat hozzáadása__ elemet. Ez a művelet hozzáadja azt a virtuális hálózatot, ahol a számítás található (lásd: 1. lépés).

        > [!IMPORTANT]
        > A Storage-fióknak ugyanabban a virtuális hálózatban kell lennie, mint a betanításhoz vagy következtetéshez használt számítási példányok vagy fürtök.

    - Jelölje be a __megbízható Microsoft-szolgáltatások számára a Storage-fiók elérésének engedélyezése__ jelölőnégyzetet.

    > [!IMPORTANT]
    > Ha a Azure Machine Learning SDK-val dolgozik, a fejlesztési környezetnek képesnek kell lennie csatlakozni az Azure Storage-fiókhoz. Ha a Storage-fiók egy virtuális hálózaton belül van, a tűzfalnak engedélyeznie kell a hozzáférést a fejlesztői környezet IP-címéről.
    >
    > A Storage-fiókhoz való hozzáférés engedélyezéséhez keresse fel a Storage-fiókhoz tartozó __tűzfalakat és virtuális hálózatokat__ a *fejlesztői ügyfél webböngészőjéből*. Ezután használja az __ügyfél IP-címének hozzáadása__ jelölőnégyzetet az ügyfél IP-címének a __címtartományból__való hozzáadásához. A __címtartomány__ mező használatával manuálisan is megadhatja a fejlesztési környezet IP-címét. Miután hozzáadta az ügyfél IP-címét, az SDK-val elérheti a Storage-fiókot.

   [![a "tűzfalak és virtuális hálózatok" panelt a Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!IMPORTANT]
> Az _alapértelmezett Storage-fiókot_ a virtuális hálózatban lévő Azure Machine learning vagy _nem alapértelmezett tárolási fiókok_ számára is elhelyezheti.
>
> A munkaterület létrehozásakor a rendszer automatikusan kiépíti az alapértelmezett Storage-fiókot.
>
> A nem alapértelmezett Storage-fiókok esetében a [`Workspace.create()` függvény](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) `storage_account` paramétere lehetővé teszi egyéni Storage-fiók megadását az Azure erőforrás-azonosító alapján.

## <a name="use-a-key-vault-instance-with-your-workspace"></a>Key Vault-példány használata a munkaterülettel

A munkaterülethez társított Key Vault-példányt a Azure Machine Learning a következő hitelesítő adatok tárolására használja:
* A társított Storage-fiókhoz tartozó kapcsolatok karakterlánca
* Az Azure Container repository példányainak jelszavai
* Adattárakhoz való kapcsolódási karakterláncok

Ha Azure Machine Learning kísérletezési képességeket szeretne használni a virtuális hálózat mögötti Azure Key Vault, tegye a következőket:
1. Nyissa meg a munkaterülethez társított kulcstartót.

   [a Azure Machine Learning munkaterülethez társított kulcstartó ![](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. A **Key Vault** oldalon, a bal oldali panelen válassza a __tűzfalak és virtuális hálózatok__lehetőséget.

   ![A Key Vault panel "tűzfalak és virtuális hálózatok" szakasza](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. A __tűzfalak és virtuális hálózatok__ lapon tegye a következőket:
    - __A hozzáférés engedélyezése lehetőségnél__válassza a __kiválasztott hálózatok__elemet.
    - A __virtuális hálózatok__területen válassza a __meglévő virtuális hálózatok hozzáadása__ lehetőséget annak a virtuális hálózatnak a hozzáadásához, ahol a kísérletezési számítás található.
    - A __megbízható Microsoft-szolgáltatások a tűzfal megkerülésének engedélyezése__területen válassza az __Igen__lehetőséget.

   [a Key Vault panel "tűzfalak és virtuális hálózatok" szakaszának ![](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

<a id="amlcompute"></a>

## <a name="compute-instance"></a>Machine Learning Compute használata

> [!NOTE]
> A számítási példányok (előzetes verzió) jelenleg csak az **USA északi középső** régióját vagy **Egyesült Királyság déli régiójat**tartalmazó munkaterületek esetében érhetők el, és hamarosan további régiókat is támogatnak.
> Ezen régiók egyikével hozzon létre egy számítási példányt, amelyet hozzáadhat a virtuális hálózathoz.

Ha egy virtuális hálózatban Azure Machine Learning számítási példányt vagy számítási fürtöt szeretne használni, a következő hálózati követelményeknek kell teljesülniük:

> [!div class="checklist"]
> * A virtuális hálózatnak ugyanabban az előfizetésben és régióban kell lennie, mint a Azure Machine Learning munkaterületnek.
> * A számítási példányhoz vagy fürthöz megadott alhálózatnak elegendő, nem hozzárendelt IP-címmel kell rendelkeznie a célként kijelölt virtuális gépek számának kielégítéséhez. Ha az alhálózat nem rendelkezik elegendő nem hozzárendelt IP-címmel, a számítási fürt részlegesen le lesz foglalva.
> * Ellenőrizze, hogy a virtuális hálózat előfizetése vagy erőforráscsoport biztonsági szabályzatai vagy zárolásai korlátozzák-e az engedélyeket a virtuális hálózat kezeléséhez. Ha a virtuális hálózat védelmét úgy tervezi, hogy korlátozza a forgalmat, hagyjon meg néhány portot a számítási szolgáltatás számára. További információ: a [szükséges portok](#mlcports) szakasz.
> * Ha egy virtuális hálózatban több számítási példányt vagy fürtöt szeretne létrehozni, előfordulhat, hogy egy vagy több erőforrásra vonatkozó kvóta-növelést kell kérnie.
> * Ha a munkaterülethez tartozó Azure Storage-fiók (ok) is védett virtuális hálózatban, akkor a Azure Machine Learning számítási példánnyal vagy fürttel azonos virtuális hálózatban kell lenniük. Ha ugyanabban a virtuális hálózatban hoz létre számítási példányt, le kell választania a Storage-fiók (oka) t a virtuális hálózatról, létre kell hoznia a számítási példányt a virtuális hálózatban, majd csatlakoztatnia kell a Storage-fiókot a virtuális hálózathoz.

A Machine Learning számítási példány vagy fürt automatikusan további hálózati erőforrásokat foglal le a virtuális hálózatot tartalmazó erőforráscsoporthoz. A szolgáltatás minden számítási példányhoz vagy fürthöz a következő erőforrásokat foglalja le:

* Egy hálózati biztonsági csoport
* Egy nyilvános IP-cím
* Egy Load Balancer

Ezekre az erőforrásokra az előfizetésben meghatározott [erőforráskvóták](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) vonatkoznak.


### <a id="mlcports"></a>Szükséges portok

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

[a Machine Learning Computehoz tartozó bejövő NSG-szabályok ![](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png#lightbox)

![A Machine Learning Compute kimenő NSG szabályai](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a>A virtuális hálózat kimenő kapcsolatának korlátozása

Ha nem szeretné az alapértelmezett kimenő szabályokat használni, és korlátozni szeretné a virtuális hálózat kimenő hozzáférését, tegye a következőket:

- A kimenő internetkapcsolat megtagadása a NSG szabályok használatával.

- A kimenő forgalom korlátozása a következőre:
   - Azure Storage a __Storage. Region_Name__ (például Storage. EastUS) __szolgáltatás címkéjével__
   - Azure Container Registry a __AzureContainerRegistry. Region_Name__ __szolgáltatás címkéjével__ (például AzureContainerRegistry. EastUS)
   - Azure Machine Learning a __AzureMachineLearning__ __szolgáltatási címkéjének__ használatával
   - Számítási példány esetén az Azure Cloud a __AzureCloud. Region_Name__ __szolgáltatás címkéjén__ keresztül (például AzureCloud. NorthCentralUS)

A Azure Portal NSG-szabályának konfigurációja a következő képen látható:

[a Machine Learning Computehoz tartozó kimenő NSG-szabályok ![](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

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
> Kalkulátor training__
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

* Hozzon létre egy UDR minden olyan IP-címhez, amelyet a Azure Batch szolgáltatás használ azon a régióban, ahol az erőforrásai léteznek. Ezek a UDR lehetővé teszik a Batch szolgáltatás számára a feladatütemezés számítási csomópontjaival való kommunikációját. A Batch szolgáltatás IP-címeinek listájának megjelenítéséhez használja a következő módszerek egyikét:

    * Töltse le az [Azure IP-címtartományok és a szolgáltatás címkéit](https://www.microsoft.com/download/details.aspx?id=56519) , és keresse meg a fájlt `BatchNodeManagement.<region>`, ahol `<region>` az Azure-régiója.

    * Az adatok letöltéséhez használja az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) -t. Az alábbi példa letölti az IP-cím adatait, és kiszűri az USA 2. keleti régiójának információit:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        ```

* A helyszíni hálózati berendezés nem tilthatja le az Azure Storage-ba irányuló kimenő forgalmat. Az URL-címek a `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`és `<account>.blob.core.windows.net`formátumban vannak.

A UDR hozzáadásakor adja meg az útvonalat az egyes kapcsolódó batch IP-címek előtagjaként, és állítsa be a __következő ugrás típusát__ az __Internet__értékre. Az alábbi képen látható példa erre a UDR mutat a Azure Portalban:

![UDR – példa a címek előtagjaként való használatra](./media/how-to-enable-virtual-network/user-defined-route.png)

További információ: [Azure batch készlet létrehozása egy virtuális hálózaton](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Számítási fürt létrehozása egy virtuális hálózaton

Machine Learning Compute-fürt létrehozásához tegye a következőket:

1. A [Azure Portal](https://portal.azure.com)válassza ki Azure Machine learning munkaterületét.

1. Az __alkalmazás__ szakaszban válassza a __számítás__lehetőséget, majd kattintson a __számítás hozzáadása__lehetőségre.

1. Ha ezt a számítási erőforrást virtuális hálózat használatára szeretné konfigurálni, tegye a következőket:

    a. A __hálózati konfiguráció beállításnál__válassza a __speciális__lehetőséget.

    b. Az __erőforráscsoport__ legördülő listában válassza ki a virtuális hálózatot tartalmazó erőforráscsoportot.

    c. A __virtuális hálózat__ legördülő listában válassza ki azt a virtuális hálózatot, amely az alhálózatot tartalmazza.

    d. Az __alhálózat__ legördülő listában válassza ki a használni kívánt alhálózatot.

   ![Machine Learning Compute virtuális hálózati beállításai](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Machine Learning Compute-fürtöt a Azure Machine Learning SDK használatával is létrehozhat. A következő kód egy új Machine Learning Compute fürtöt hoz létre egy `mynetwork`nevű virtuális hálózat `default` alhálózatában:

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

## <a name="use-azure-databricks"></a>Azure Databricks használata

Ha Azure Databricks szeretne használni a munkaterülettel rendelkező virtuális hálózaton, a következő követelményeknek kell teljesülniük:

> [!div class="checklist"]
> * A virtuális hálózatnak ugyanabban az előfizetésben és régióban kell lennie, mint a Azure Machine Learning munkaterületnek.
> * Ha a munkaterülethez tartozó Azure Storage-fiók (ok) is védett virtuális hálózatban, akkor a Azure Databricks-fürttel megegyező virtuális hálózatban kell lenniük.
> * Az Azure Databricks által használt __databricks-__ és __databricks-__ alhálózatok mellett a virtuális hálózathoz létrehozott __alapértelmezett__ alhálózat is szükséges.

A Azure Databricks virtuális hálózattal való használatáról a [Azure Databricks üzembe helyezése az Azure-Virtual Network](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html)című témakörben olvashat bővebben.

<a id="vmorhdi"></a>

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Virtuális gép vagy HDInsight-fürt használata

> [!IMPORTANT]
> A Azure Machine Learning csak az Ubuntut futtató virtuális gépeket támogatja.

Ha egy virtuális gépet vagy Azure HDInsight-fürtöt szeretne használni a munkaterülettel rendelkező virtuális hálózatban, tegye a következőket:

1. Hozzon létre egy virtuális GÉPET vagy HDInsight-fürtöt a Azure Portal vagy az Azure CLI használatával, és helyezze üzembe a fürtöt egy Azure-beli virtuális hálózaton. További információkért tekintse át a következő cikkeket:
    * [Azure Virtual Networks létrehozása és kezelése Linux rendszerű virtuális gépekhez](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [HDInsight kiterjesztése Azure Virtual Network használatával](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Ha engedélyezni szeretné, hogy a Azure Machine Learning kommunikáljon a virtuális gép vagy fürt SSH-portjával, állítson be egy forrásoldali bejegyzést a hálózati biztonsági csoport számára. Az SSH-port általában a 22-es port. A forrásból érkező forgalom engedélyezéséhez tegye a következőket:

    * A __forrás__ legördülő listában válassza a __szolgáltatás címkéje__elemet.

    * A __forrás szolgáltatás címkéje__ legördülő listában válassza a __AzureMachineLearning__lehetőséget.

    * A __forrásport-tartományok__ legördülő listában válassza a __*__ lehetőséget.

    * A __cél__ legördülő listában válassza a __bármelyik__lehetőséget.

    * A __cél porttartomány-tartományok__ legördülő listában válassza a __22__lehetőséget.

    * A __protokoll__területen válassza __a bármelyik__lehetőséget.

    * A __művelet__területen válassza az __Engedélyezés__lehetőséget.

   ![Bejövő szabályok a virtuális hálózaton belüli virtuálisgép-vagy HDInsight-fürtök kísérletezéséhez](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Tartsa meg a hálózati biztonsági csoport alapértelmezett kimenő szabályait. További információ: [biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)alapértelmezett biztonsági szabályai.

    Ha nem szeretné használni az alapértelmezett kimenő szabályokat, és szeretné korlátozni a virtuális hálózat kimenő hozzáférését, tekintse meg a [Kimenő kapcsolat korlátozása a virtuális hálózattal](#limiting-outbound-from-vnet) szakaszt.

1. Csatlakoztassa a virtuális gépet vagy a HDInsight-fürtöt a Azure Machine Learning munkaterülethez. További információ: [számítási célok beállítása a modell betanításához](how-to-set-up-training-targets.md).

<a id="aksvnet"></a>

## <a name="use-azure-kubernetes-service-aks"></a>Az Azure Kubernetes Service (ak) használata

Ha AK-t szeretne hozzáadni egy virtuális hálózathoz a munkaterülethez, tegye a következőket:

> [!IMPORTANT]
> Az alábbi eljárás megkezdése előtt kövesse a [speciális hálózatkezelés konfigurálása az Azure Kubernetes szolgáltatásban (ak)](https://docs.microsoft.com/azure/aks/configure-advanced-networking#prerequisites) – útmutató és a fürt IP-címzésének megtervezése című témakör előfeltételeit.
>
> Az AK-példánynak és az Azure-beli virtuális hálózatnak ugyanabban a régióban kell lennie. Ha a virtuális hálózatban a munkaterület által használt Azure Storage-fiók (oka) t védi, azoknak ugyanabban a virtuális hálózatban kell lenniük, mint az AK-példány.

1. A [Azure Portal](https://portal.azure.com)ellenőrizze, hogy a virtuális hálózatot vezérlő NSG van-e olyan Bejövő szabály, amely engedélyezve van a Azure Machine learning számára, ha a __AzureMachineLearning__ -t használja **forrásként**.

    [![Azure Machine Learning számítási panel hozzáadása](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png#lightbox)

1. Válassza ki Azure Machine Learning munkaterületét.

1. Az __alkalmazás__ szakaszban válassza a __számítás__lehetőséget, majd kattintson a __számítás hozzáadása__lehetőségre.

1. Ha ezt a számítási erőforrást virtuális hálózat használatára szeretné konfigurálni, tegye a következőket:

    - A __hálózati konfiguráció beállításnál__válassza a __speciális__lehetőséget.

    - Az __erőforráscsoport__ legördülő listában válassza ki a virtuális hálózatot tartalmazó erőforráscsoportot.

    - A __virtuális hálózat__ legördülő listában válassza ki azt a virtuális hálózatot, amely az alhálózatot tartalmazza.

    - Az __alhálózat__ legördülő listában válassza ki az alhálózatot.

    - A __Kubernetes szolgáltatás címtartomány__ mezőjébe írja be a Kubernetes szolgáltatás címtartományt. Ez a címtartomány egy osztály nélküli tartományok közötti útválasztás (CIDR) jelölésű IP-címtartományt használ a fürt számára elérhető IP-címek definiálásához. Nem lehet átfedésben egyetlen alhálózati IP-tartománnyal sem (például 10.0.0.0/16).

    - A __KUBERNETES DNS-szolgáltatás IP-címe__ mezőbe írja be a Kubernetes DNS-szolgáltatás IP-címét. Ezt az IP-címet a Kubernetes DNS szolgáltatáshoz rendeli a rendszer. A Kubernetes a szolgáltatási címtartomány (például 10.0.0.10) közé kell esnie.

    - A __Docker Bridge-címe__ mezőbe írja be a Docker-híd címe mezőt. Ezt az IP-címet a Docker-híd rendeli hozzá. Nem lehet alhálózati IP-címtartományok vagy a Kubernetes szolgáltatási címtartomány (például 172.17.0.1/16).

   ![Azure Machine Learning: Machine Learning Compute virtuális hálózati beállítások](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Győződjön meg arról, hogy a virtuális hálózatot vezérlő NSG-csoport rendelkezik egy bejövő biztonsági szabállyal, amely engedélyezi a pontozási végpontot, hogy a virtuális hálózaton kívülről is meghívható legyen.
   > [!IMPORTANT]
   > Tartsa meg a NSG alapértelmezett kimenő szabályait. További információ: [biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)alapértelmezett biztonsági szabályai.

   [bejövő biztonsági szabály ![](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

A Azure Machine Learning SDK-val is hozzáadhatja az Azure Kubernetes szolgáltatást egy virtuális hálózatban. Ha már van egy AK-fürtje egy virtuális hálózaton, csatolja azt a munkaterülethez a következő témakörben leírtak szerint: [üzembe helyezés az AK](how-to-deploy-and-where.md)-ban. A következő kód létrehoz egy új AK-példányt egy `mynetwork`nevű virtuális hálózat `default` alhálózatán:

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

## <a name="use-azure-firewall"></a>Azure Firewall használata

Azure Firewall használatakor konfigurálnia kell egy hálózati szabályt, hogy engedélyezze a következő címekre irányuló és onnan érkező forgalmat:

- `*.batchai.core.windows.net`
- `ml.azure.com`
- `*.azureml.ms`
- `*.experiments.azureml.net`
- `*.modelmanagement.azureml.net`
- `mlworkspace.azure.ai`
- `*.aether.ms`

A szabály hozzáadásakor állítsa a __protokollt__ bármelyik értékre, és a portokat `*`.

A hálózati szabályok konfigurálásával kapcsolatos további információkért lásd: [Azure Firewall telepítése és konfigurálása](/azure/firewall/tutorial-firewall-deploy-portal#configure-a-network-rule).

## <a name="next-steps"></a>Következő lépések

* [Képzési környezetek beállítása](how-to-set-up-training-targets.md)
* [A modellek üzembe helyezésének helye](how-to-deploy-and-where.md)
* [Modellek biztonságos üzembe helyezése SSL használatával](how-to-secure-web-service.md)


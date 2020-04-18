---
title: Biztonságos kísérletek és következtetések a virtuális hálózatban
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan biztosíthat kísérletezési/betanítási feladatokat és következtetési/pontozási feladatokat az Azure Machine Learningben egy Azure virtuális hálózaton belül.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 04/17/2020
ms.openlocfilehash: 1f3c9f86072eeedbc999946d0f846fbc3b41f94d
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641758"
---
# <a name="secure-azure-ml-experimentation-and-inference-jobs-within-an-azure-virtual-network"></a>Biztonságos Azure ML-kísérletezés és következtetési feladatok az Azure virtuális hálózaton belül
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben megtudhatja, hogyan biztosíthatja a kísérletezési/betanítási feladatok at és a következtetési/pontozási feladatokat az Azure Machine Learningben egy Azure virtuális hálózaton (vnet) belül.

A **virtuális hálózat** biztonsági határként működik, elkülönítve az Azure-erőforrásokat a nyilvános internetről. Az Azure virtuális hálózathoz is csatlakozhat a helyszíni hálózathoz. A hálózatokhoz való csatlakozással biztonságosan betaníthatja modelljeit, és hozzáférhet az üzembe helyezett modellekhez a következtetés érdekében.

Az Azure Machine Learning más Azure-szolgáltatásokra támaszkodik a számítási erőforrásokhoz. Számítási erőforrások, vagy [számítási célok](concept-compute-target.md), modellek betanítására és üzembe helyezésére szolgálnak. A célok virtuális hálózaton belül hozhatók létre. A Microsoft Data Science virtuális gép segítségével például betaníthat egy modellt, majd telepítheti a modellt az Azure Kubernetes-szolgáltatásra (AKS). A virtuális hálózatokról az [Azure virtuális hálózat áttekintése című témakörben olvashat bővebben.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)

Ez a cikk részletes információkat is tartalmaz a *speciális biztonsági beállításokról,* olyan információkról, amelyek nem szükségesek az alapszintű vagy kísérleti használati esetekhez. A cikk egyes szakaszai különböző forgatókönyvekhez nyújtanak konfigurációs információkat. Nem kell az utasításokat sorrendben vagy teljes egészében elvégeznie.

> [!TIP]
> Kivéve, ha kifejezetten lehívott, erőforrások használatával, például a tárfiókok vagy számítási célok egy virtuális hálózaton belül együtt fog működni mind a gépi tanulási folyamatok, és a nem csővezeték-munkafolyamatok, például a parancsfájl futtatása.

> [!WARNING]
> A Microsoft nem támogatja az Azure Machine Learning Studio szolgáltatásainak, például az automatikus ml, adatkészletek, adatcímkézés, tervező és jegyzetfüzetek használatát, ha az alapul szolgáló tároló virtuális hálózatengedélyezve van.

## <a name="prerequisites"></a>Előfeltételek

+ Egy Azure Machine [Learning-munkaterület.](how-to-manage-workspace.md)

+ Az Azure Virtual [Network szolgáltatás](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) és az [IP-hálózat](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)általános munkaismerete.

+ A számítási erőforrásokkal használható, már meglévő virtuális hálózat és alhálózat.

## <a name="use-a-storage-account-for-your-workspace"></a>Tárfiók használata a munkaterülethez

> [!WARNING]
> Ha az Azure Machine Learning-tervezőt használó adatszakértőkkel rendelkezik, hibaüzenetet kapnak, amikor egy virtuális hálózaton belüli tárfiókból származó adatokat jelenítmeg. A következő szöveg a kapott hiba:
>
> __Hiba: Nem lehet profilozni ezt az adatkészletet. Ennek az lehet az oka, hogy az adatok egy virtuális hálózat mögött vannak tárolva, vagy az adatok nem támogatják a profilt.__

Ha egy virtuális hálózat munkaterületéhez Azure storage-fiókot szeretne használni, kövesse az alábbi lépéseket:

1. Hozzon létre egy számítási erőforrást (például egy Machine Learning-számítási példányt vagy fürtöt) egy virtuális hálózat mögött, vagy csatlakoztasson egy számítási erőforrást a munkaterülethez (például egy HDInsight-fürthöz, virtuális géphez vagy az Azure Kubernetes-szolgáltatás fürthöz). A számítási erőforrás lehet kísérletezés vagy modell üzembe helyezéséhez.

   További információ: [A Machine Learning compute használata](#amlcompute), Használja a virtuális gép vagy a [HDInsight-fürt,](#vmorhdi)és [az Azure Kubernetes-szolgáltatás](#aksvnet) használata ebben a cikkben.

1. Az Azure Portalon nyissa meg a munkaterülethez csatlakoztatott tárolót.

   [![Az Azure Machine Learning-munkaterülethez csatlakoztatott tárhely](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Az **Azure Storage** lapon válassza __a Tűzfalak és a virtuális hálózatok__lehetőséget.

   ![Az Azure Portal Azure Storage lapján található "Tűzfalak és virtuális hálózatok" terület](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. A __Tűzfalak és a virtuális hálózatok__ lapon tegye a következő műveleteket:
    - Válassza a __Kiválasztott hálózatok__ lehetőséget.
    - A __Virtuális hálózatok csoportban__válassza a __Meglévő virtuális hálózati__ kapcsolat hozzáadása lehetőséget. Ez a művelet hozzáadja a virtuális hálózatot, ahol a számítási található (lásd az 1. lépést).

        > [!IMPORTANT]
        > A tárfióknak ugyanabban a virtuális hálózatban és alhálózatban kell lennie, mint a betanításhoz vagy következtetéshez használt számítási példányok vagy fürtök.

    - Jelölje be A __megbízható Microsoft-szolgáltatások hozzáférése a tárfiókhoz jelölőnégyzetet.__

    > [!IMPORTANT]
    > Az Azure Machine Learning SDK-val való munka során a fejlesztői környezetnek képesnek kell lennie az Azure Storage-fiókhoz való csatlakozásra. Ha a tárfiók egy virtuális hálózaton belül van, a tűzfalnak engedélyeznie kell a hozzáférést a fejlesztői környezet IP-címéről.
    >
    > A tárfiókhoz való hozzáférés engedélyezéséhez keresse fel a *fejlesztői ügyfél webböngészőjének* __tűzfalait és virtuális hálózatait.__ Ezután az __Ügyfél IP-címének hozzáadása__ jelölőnégyzetbe használja az ügyfél IP-címének hozzáadását a __CÍMTARTOMÁNYhoz.__ A __CÍMTARTOMÁNY__ mező segítségével manuálisan is megadhatja a fejlesztői környezet IP-címét. Miután az ügyfél IP-címét hozzáadta, hozzáférhet a tárfiókhoz az SDK használatával.

   [![A "Tűzfalak és virtuális hálózatok" ablaktábla az Azure Portalon](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!IMPORTANT]
> Az Azure Machine Learning _alapértelmezett tárfiókját,_ vagy _a nem alapértelmezett tárfiókokat_ virtuális hálózatba helyezheti.
>
> Az alapértelmezett tárfiók automatikusan kiépítve, amikor létrehoz egy munkaterületet.
>
> A nem alapértelmezett tárfiókok `storage_account` esetében a [ `Workspace.create()` függvény](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) ben lévő paraméter lehetővé teszi, hogy az Azure-erőforrás-azonosító val egyéni tárfiókot adjon meg.

## <a name="use-azure-data-lake-storage-gen-2"></a>Az Azure Data Lake Storage Gen 2 használata

Az Azure Data Lake Storage Gen 2 az Azure Blob storage-ra épülő big data-elemzési képességek összeskészlete. Az Azure Machine Learning modellekkel modellek betanításához használt adatok tárolására használható. 

A Data Lake Storage Gen 2 használatához az Azure Machine Learning-munkaterület virtuális hálózatán belül az alábbi lépéseket kell használnia:

1. Hozzon létre egy Azure Data Lake Storage gen 2 fiókot. További információ: [Azure Data Lake Storage Gen2 storage-fiók létrehozása.](../storage/blobs/data-lake-storage-quickstart-create-account.md)

1. Használja az előző [szakasz](#use-a-storage-account-for-your-workspace)2-4.

Ha az Azure Machine Learninget a Data Lake Storage Gen 2-vel használja egy virtuális hálózaton belül, kövesse az alábbi útmutatást:

* Ha az __SDK-t adatkészlet létrehozására__használja, és a kódot futtató rendszer `validate=False` nem a virtuális __hálózatban van,__ használja a paramétert. Ez a paraméter kihagyja az ellenőrzést, amely sikertelen lesz, ha a rendszer nem ugyanabban a virtuális hálózatban van, mint a tárfiók. További információt a [from_files()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) metódusban talál.

* Ha az Azure Machine Learning számítási példány vagy számítási fürt segítségével betanítása egy modell t használja az adatkészletet, akkor ugyanabban a virtuális hálózatban kell lennie, mint a tárfiók.

## <a name="use-a-key-vault-instance-with-your-workspace"></a>Kulcstartópéldány használata a munkaterülettel

A munkaterülethez társított kulcstartópéldányt az Azure Machine Learning a következő hitelesítő adatok tárolására használja:
* A társított tárfiók kapcsolati karakterlánca
* Jelszavak az Azure Container Repository példányaihoz
* Kapcsolati karakterláncok adattárakkal

Az Azure Machine Learning kísérletezési képességeinek használatához az Azure Key Vault virtuális hálózat mögött, kövesse az alábbi lépéseket:

1. Nyissa meg a munkaterülethez társított kulcstartót.

   [![Az Azure Machine Learning-munkaterülethez társított kulcstartó](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. A **Key Vault** lap bal oldali ablaktáblájában válassza a __Tűzfalak és a virtuális hálózatok__lehetőséget.

   ![A Key Vault ablaktáblájának "Tűzfalak és virtuális hálózatok" szakasza](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. A __Tűzfalak és a virtuális hálózatok__ lapon tegye a következő műveleteket:
    - A Hozzáférés engedélyezése a területen __csoportban__válassza a __Kijelölt hálózatok__lehetőséget.
    - A __Virtuális hálózatok csoportban__válassza __a Meglévő virtuális hálózatok hozzáadása__ lehetőséget a kísérletezési számítási helyt ad hozzá.
    - A __Megbízható Microsoft-szolgáltatások megkerülése a tűzfal megkerülése__csoportban válassza az __Igen__lehetőséget.

   [![A Key Vault ablaktáblájának "Tűzfalak és virtuális hálózatok" szakasza](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

<a id="amlcompute"></a>

## <a name="use-a-machine-learning-compute"></a><a name="compute-instance"></a>Gépi tanulási számítási használata

Az Azure Machine Learning számítási példányvagy számítási fürt virtuális hálózatban való használatához a következő hálózati követelményeknek kell megfelelni:

> [!div class="checklist"]
> * A virtuális hálózatnak ugyanabban az előfizetésben és régióban kell lennie, mint az Azure Machine Learning-munkaterületnek.
> * A számítási példányhoz vagy fürthöz megadott alhálózatnak elegendő hozzá nem hozzárendelt IP-címmel kell rendelkeznie a megcélzott virtuális gépek számának befogadásához. Ha az alhálózat nem rendelkezik elegendő hozzá nem rendelt IP-címmel, a számítási fürt részlegesen le lesz foglalva.
> * Ellenőrizze, hogy a biztonsági házirendek vagy zárolások a virtuális hálózat előfizetésében vagy erőforráscsoportjában korlátozzák-e a virtuális hálózat kezeléséhez szükséges engedélyeket. Ha azt tervezi, hogy a forgalom korlátozásával biztosítja a virtuális hálózatot, hagyjon nyitva néhány portot a számítási szolgáltatás számára. További információt a [Kötelező portok](#mlcports) című részben talál.
> * Ha több számítási példányt vagy fürtöt szeretne helyezni egy virtuális hálózatba, előfordulhat, hogy egy vagy több erőforrásra vonatkozóan kvótanövelést kell kérnie.
> * Ha az Azure Storage-fiók(ok) a munkaterület is biztonságos egy virtuális hálózatban, akkor ugyanabban a virtuális hálózatban kell lennie, mint az Azure Machine Learning számítási példány vagy fürt. 

> [!TIP]
> A Machine Learning számítási példány vagy fürt automatikusan lefoglalja a további hálózati erőforrásokat az erőforráscsoportban, amely tartalmazza a virtuális hálózatot. A szolgáltatás minden egyes számítási példányhoz vagy fürthöz a következő erőforrásokat foglalja le:
> 
> * Egy hálózati biztonsági csoport
> * Egy nyilvános IP-cím
> * Egy terheléselosztó
> 
> Ezekre az erőforrásokra az előfizetésben meghatározott [erőforráskvóták](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) vonatkoznak.


### <a name="required-ports"></a><a id="mlcports"></a>Kötelező portok

A Machine Learning Compute jelenleg az Azure Batch szolgáltatást használja a virtuális gépek kiépítési a megadott virtuális hálózatban. Az alhálózatnak engedélyeznie kell a Batch szolgáltatás bejövő kommunikációját. Ezzel a kommunikációval ütemezheti a gépi tanulási számítási csomópontok fut, és kommunikálni az Azure Storage és egyéb erőforrások. A Batch szolgáltatás hálózati biztonsági csoportokat (NSG-ket) ad hozzá a virtuális gépekhez csatlakoztatott hálózati adapterek (NIC-k) szintjén. Ezek az NSG-k automatikusan konfigurálnak bejövő és kimenő szabályokat a következő forgalom engedélyezéséhez:

- Bejövő TCP-forgalom a 29876-os és a 29877-es porton a __BatchNodeManagement__ __szolgáltatáscímkéből.__

    ![A BatchNodeManagement szolgáltatáscímkét használó bejövő szabály](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- (Nem kötelező) Bejövő TCP-forgalom a 22-es porton a távelérés engedélyezéséhez. Ezt a portot csak akkor használja, ha a nyilvános IP-címs SSH használatával szeretne csatlakozni.

- Kimenő forgalom bármilyen porton keresztül a virtuális hálózathoz.

- Kimenő forgalom bármilyen porton keresztül az internetre.

- Számítási példány bejövő TCP-forgalmat a 44224-es porton az __AzureMachineLearning__ __szolgáltatáscímkéből.__

Körültekintően járjon el a bejövő vagy kimenő szabályok módosításakor és hozzáadásakor a Batch által konfigurált NSG-kben. Ha egy NSG blokkolja a számítási csomópontok közötti kommunikációt, a számítási szolgáltatás a számítási csomópontok állapotát használhatatlanra állítja.

Nem kell megadnia az NSG-ket az alhálózati szinten, mert az Azure Batch szolgáltatás saját NSG-ket konfigurál. Ha azonban a megadott alhálózathoz NSG-k vagy tűzfal tartozik, konfigurálja a bejövő és kimenő biztonsági szabályokat a korábbiak szerint.

Az NSG-szabály konfigurációja az Azure Portalon a következő képeken látható:

[![A gépi tanulási számítás bejövő NSG-szabályai](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png#lightbox)

![A machine learning compute kimenő NSG-szabályai](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a>A virtuális hálózat kimenő kapcsolatának korlátozása

Ha nem szeretné használni az alapértelmezett kimenő szabályokat, és korlátozni szeretné a virtuális hálózat kimenő hozzáférését, kövesse az alábbi lépéseket:

- A kimenő internetkapcsolat megtagadása az NSG-szabályok használatával.

- Számítási __példány__ vagy __számítási fürt__esetén korlátozza a kimenő forgalmat a következő elemekre:
   - Az Azure Storage __szolgáltatáscímkéjének__ __használatával.RegionName__. Hol `{RegionName}` található egy Azure-régió neve.
   - Azure Container Registry, az __AzureContainerRegistry.RegionName__ __szolgáltatáscímkéjének__ használatával. Hol `{RegionName}` található egy Azure-régió neve.
   - Az Azure Machine Learning __szolgáltatáscímkéjének__ __AzureMachineLearning__ használatával
   - Az Azure Resource Manager az __AzureResourceManager__ __szolgáltatáscímkéjének__ használatával
   - Az Azure Active Directory az __AzureActiveDirectory__ __szolgáltatáscímkéjének__ használatával

Az NSG-szabály konfigurációja az Azure Portalon a következő képen látható:

[![A machine learning compute kimenő NSG-szabályai](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Ha a Microsoft által biztosított alapértelmezett Docker-lemezképek et kívánja használni, és engedélyezi a felhasználó által felügyelt függőségeket, akkor a __MicrosoftContainerRegistry.Region_Name__ (például MicrosoftContainerRegistry.EastUS) __szolgáltatáscímkét__ is használnia kell.
>
> Erre a konfigurációra akkor van szükség, ha a betanítási parancsfájlok részeként a következő kódrészletekhez hasonló kódkódokkal rendelkezik:
>
> __RunConfig képzés__
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
> __Becslő képzés__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="user-defined-routes-for-forced-tunneling"></a>Felhasználó által definiált útvonalak kényszerített bújtatáshoz

Ha kényszerített bújtatás a Machine Learning Compute, adja hozzá a [felhasználó által definiált útvonalak (UDRs)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) az alhálózat, amely tartalmazza a számítási erőforrást.

* Hozzon létre egy UDR minden IP-cím, amely az Azure Batch szolgáltatás által használt a régióban, ahol az erőforrások léteznek. Ezek az UD-k lehetővé teszik, hogy a Batch szolgáltatás kommunikáljon a feladatütemezés számítási csomópontjaival. A Batch szolgáltatás IP-címeinek listájának leéséhez használja az alábbi módszerek egyikét:

    * Töltse le az [Azure IP-tartományokat és a szolgáltatáscímkéket,](https://www.microsoft.com/download/details.aspx?id=56519) és keressen rá a fájlban, `BatchNodeManagement.<region>`hol `<region>` található az Azure-régió.

    * Az adatok letöltéséhez használja az [Azure CLI-t.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) A következő példa letölti az IP-cím adatait, és kiszűri az USA keleti régiójának adatait:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        ```

* Az Azure Storage kimenő forgalmat a helyszíni hálózati berendezés nem blokkolhatja. Pontosabban az URL-címek a `<account>.table.core.windows.net`( `<account>.queue.core.windows.net`és `<account>.blob.core.windows.net`a .

Az UdRs hozzáadásakor adja meg az egyes kapcsolódó Batch IP-címelőtagok útvonalát, és állítsa a __Következő ugrás típust__ __az Internetre.__ Az alábbi képen egy példa látható erre az UDR-re az Azure Portalon:

![Példa egy címelőtag UDR-re](./media/how-to-enable-virtual-network/user-defined-route.png)

További információ: [Azure Batch-készlet létrehozása virtuális hálózatban.](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Számítási fürt létrehozása virtuális hálózatban

Machine Learning-számítási fürt létrehozásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Machine Learning-stúdióba,](https://ml.azure.com/)majd válassza ki az előfizetést és a munkaterületet.

1. Válassza a bal oldalon a __Számítási__ lehetőséget.

1. Válassza a középről a Képzési __+__ fürtök __lehetőséget,__ majd válassza a lehetőséget.

1. Az __Új betanítási fürt__ párbeszédpanelen bontsa ki a __Speciális beállítások szakaszt.__

1. Ha azt szeretné, hogy a számítási erőforrás virtuális hálózatot használjon, hajtsa végre a következő műveleteket a __Virtuális hálózat konfigurálása__ szakaszban:

    1. Az __Erőforráscsoport__ legördülő listában jelölje ki a virtuális hálózatot tartalmazó erőforráscsoportot.
    1. A __Virtuális hálózat__ legördülő listában jelölje ki az alhálózatot tartalmazó virtuális hálózatot.
    1. Az __Alhálózat__ legördülő listában jelölje ki a használni kívánt alhálózatot.

   ![A Machine Learning Compute virtuális hálózati beállításai](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Machine Learning Számítási fürtaz Azure Machine Learning SDK használatával is létrehozhat. A következő kód létrehoz egy új Machine `default` Learning Számítási fürtöt `mynetwork`egy virtuális hálózat nevű alhálózatában:

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

Amikor a létrehozási folyamat befejeződik, betanítja a modellt a fürt használatával egy kísérletben. További információ: [Számítási cél kiválasztása és használata betanításhoz.](how-to-set-up-training-targets.md)

## <a name="use-azure-databricks"></a>Az Azure Databricks használata

Az Azure Databricks virtuális hálózatban való használatához a munkaterülettel a következő követelményeknek kell megfelelnie:

> [!div class="checklist"]
> * A virtuális hálózatnak ugyanabban az előfizetésben és régióban kell lennie, mint az Azure Machine Learning-munkaterületnek.
> * Ha az Azure Storage-fiók(ok) a munkaterület is biztonságos egy virtuális hálózatban, akkor ugyanabban a virtuális hálózatban kell lennie, mint az Azure Databricks-fürt.
> * A __databricks-private__ és __databricks-public__ alhálózatok az Azure Databricks által használt, a virtuális hálózathoz létrehozott __alapértelmezett__ alhálózat is szükséges.

Az Azure Databricks virtuális hálózattal való használatáról az [Azure Databricks telepítése az Azure virtuális hálózatban](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html)című témakörben talál konkrét információt.

<a id="vmorhdi"></a>

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Virtuális gép vagy HDInsight-fürt használata

> [!IMPORTANT]
> Az Azure Machine Learning csak az Ubuntut futtató virtuális gépeket támogatja.

Ha virtuális gépet vagy Azure HDInsight-fürtöt szeretne használni egy virtuális hálózatban a munkaterülettel, kövesse az alábbi lépéseket:

1. Hozzon létre egy virtuális gép vagy HDInsight-fürt az Azure Portalon vagy az Azure CLI használatával, és helyezze a fürt egy Azure virtuális hálózatba. További információkért tekintse át a következő cikkeket:
    * [Azure virtuális hálózatok létrehozása és kezelése Linuxos virtuális gépekhez](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [A HDInsight bővítése azure-beli virtuális hálózat használatával](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Annak engedélyezéséhez, hogy az Azure Machine Learning kommunikáljon a virtuális gép vagy fürt SSH-portjával, konfiguráljon egy forrásbejegyzést a hálózati biztonsági csoport számára. Az SSH port általában a 22-es port. Az ebből a forrásból származó forgalom engedélyezéséhez tegye a következő műveleteket:

    * A __Forrás__ legördülő listában válassza a __Szervizcímke__lehetőséget.

    * A __Forrásszolgáltatás címke__ legördülő listájában válassza az __AzureMachineLearning__lehetőséget.

    * A __Forrásport tartományok__ legördülő listában __*__ válassza a lehetőséget.

    * A __Cél__ legördülő listában válassza a __Bármely__lehetőséget.

    * A __Célport tartományok__ legördülő listában válassza a __22__lehetőséget.

    * A __Protokoll csoportban__válassza a __Bármely__lehetőséget.

    * A __Művelet csoportban__válassza az __Engedélyezés__lehetőséget.

   ![Bejövő szabályok a virtuális gépen vagy HDInsight-fürtön való kísérletezéshez egy virtuális hálózaton belül](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Tartsa meg a hálózati biztonsági csoport alapértelmezett kimenő szabályait. További információt a Biztonsági csoportok alapértelmezett biztonsági szabályai című témakörben [talál.](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)

    Ha nem szeretné használni az alapértelmezett kimenő szabályokat, és szeretné korlátozni a virtuális hálózat kimenő hozzáférését, tekintse meg a [Kimenő kapcsolat korlátozása a virtuális hálózatról szakaszban.](#limiting-outbound-from-vnet)

1. Csatolja a virtuális gép vagy a HDInsight-fürt az Azure Machine Learning-munkaterülethez. További információ: [Számítási célok beállítása a modellbetanításhoz.](how-to-set-up-training-targets.md)

<a id="aksvnet"></a>

## <a name="use-azure-kubernetes-service-aks"></a>Azure Kubernetes-szolgáltatás (AKS) használata

Ha virtuális hálózatban szeretné hozzáadni az AKS-t a munkaterülethez, kövesse az alábbi lépéseket:

> [!IMPORTANT]
> Az alábbi eljárás megkezdése előtt kövesse a [speciális hálózati szolgáltatások konfigurálása az Azure Kubernetes-szolgáltatás (AKS)](https://docs.microsoft.com/azure/aks/configure-advanced-networking#prerequisites) útmutatójában, és tervezze meg a fürt IP-címzését.
>
> Az AKS-példány nak és az Azure virtuális hálózatnak ugyanabban a régióban kell lennie. Ha biztonságossá teszi a munkaterület által egy virtuális hálózatban használt Azure Storage-fiók(oka)t, akkor ugyanabban a virtuális hálózatban kell lenniük, mint az AKS-példány.

> [!WARNING]
> Az Azure Machine Learning nem támogatja az Azure Kubernetes-szolgáltatás használatát, amelynek privát kapcsolata engedélyezve van.

1. Jelentkezzen be az [Azure Machine Learning-stúdióba,](https://ml.azure.com/)majd válassza ki az előfizetést és a munkaterületet.

1. Válassza a bal oldalon a __Számítási__ lehetőséget.

1. Válassza __a fürtök levonása__ lehetőséget a __+__ központból, majd válassza a lehetőséget.

1. Az __Új következtetésfürt párbeszédpanelen__ válassza a __Speciális__ lehetőséget a __Hálózati konfiguráció csoportban.__

1. Ha azt szeretné, hogy a számítási erőforrás virtuális hálózatot használjon, hajtsa végre a következő műveleteket:

    1. Az __Erőforráscsoport__ legördülő listában jelölje ki a virtuális hálózatot tartalmazó erőforráscsoportot.
    1. A __Virtuális hálózat__ legördülő listában jelölje ki az alhálózatot tartalmazó virtuális hálózatot.
    1. Az __Alhálózat__ legördülő listában jelölje ki az alhálózatot.
    1. A __Kubernetes szolgáltatás címtartománya__ mezőbe írja be a Kubernetes szolgáltatás címtartományát. Ez a címtartomány egy osztály nélküli tartományközi útválasztás (CIDR) jelölési IP-tartományt használ a fürt höz rendelkezésre álló IP-címek meghatározásához. Nem fedheti át az alhálózati IP-címtartományokat (például 10.0.0.0/16).
    1. A __Kubernetes DNS-szolgáltatás IP-címe__ mezőbe írja be a Kubernetes DNS-szolgáltatás IP-címét. Ez az IP-cím a Kubernetes DNS-szolgáltatáshoz van rendelve. A Kubernetes szolgáltatás címtartományán belül kell lennie (például 10.0.0.10).
    1. A __Docker-híd címmezőjébe__ írja be a Docker-híd címét. Ez az IP-cím a Docker-hídhoz van rendelve. Nem lehet alhálózati IP-tartományban, vagy a Kubernetes szolgáltatás címtartományában (például 172.17.0.1/16).

   ![Azure Machine Learning: Machine Learning számítási virtuális hálózati beállítások](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Győződjön meg arról, hogy a virtuális hálózatot vezérlő NSG-csoport bejövő biztonsági szabály engedélyezve van a pontozási végponthoz, hogy a virtuális hálózaton kívülről hívható legyen.
   > [!IMPORTANT]
   > Tartsa meg az NSG alapértelmezett kimenő szabályait. További információt a Biztonsági csoportok alapértelmezett biztonsági szabályai című témakörben [talál.](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)

   [![Bejövő biztonsági szabály](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

Az Azure Machine Learning SDK használatával is hozzáadhatja az Azure Kubernetes-szolgáltatást egy virtuális hálózatban. Ha már van AKS-fürtje egy virtuális hálózatban, csatolja azt a munkaterülethez az [AKS-re való telepítés](how-to-deploy-and-where.md)című részben leírtak szerint. A következő kód létrehoz egy új `default` AKS-példányt `mynetwork`egy virtuális hálózat nevű alhálózatában:

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

Amikor a létrehozási folyamat befejeződött, a virtuális hálózat mögötti AKS-fürtön következtetéseket vagy modellpontozást futtathat. További információ: [Hogyan telepíthető az AKS-be.](how-to-deploy-and-where.md)

### <a name="use-private-ips-with-azure-kubernetes-service"></a>Privát IP-k használata az Azure Kubernetes szolgáltatással

Alapértelmezés szerint egy nyilvános IP-cím van rendelve az AKS-telepítések. Ha az AKS-t virtuális hálózaton belül használja, használhat helyette privát IP-címet. A privát IP-címek csak a virtuális hálózaton vagy az összekapcsolt hálózatokon belül érhetők el.

A privát IP-cím engedélyezése az AKS _belső terheléselosztó_használatára való konfigurálásával történik. 

> [!IMPORTANT]
> Az Azure Kubernetes service-fürt létrehozásakor nem engedélyezheti a privát IP-címet. Egy meglévő fürt frissítéseként engedélyezni kell.

A következő kódrészlet bemutatja, hogyan **hozhat létre új AKS-fürtöt,** majd frissítheti azt egy privát IP/belső terheléselosztó használatára:

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

A parancs `body.json` által hivatkozott fájl tartalma hasonló a következő JSON-dokumentumhoz:

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
> Jelenleg nem konfigurálható a terheléselosztó, ha __csatolási__ műveletet hajt végre egy meglévő fürtön. Először csatolnia kell a fürtöt, majd egy frissítési műveletet kell végrehajtania a terheléselosztó módosításához.

A belső terheléselosztó AKS-szal való használatáról a [Belső terheléselosztó használata az Azure Kubernetes szolgáltatással című](/azure/aks/internal-lb)témakörben talál további információt.

## <a name="use-azure-container-instances-aci"></a>Azure Container-példányok (ACI) használata

Az Azure Container-példányok dinamikusan jönnek létre egy modell üzembe helyezésekor. Ahhoz, hogy az Azure Machine Learning a virtuális hálózaton belül hozzon létre ACI-t, engedélyeznie kell az __alhálózati delegálást__ a központi telepítés által használt alhálózathoz.

Ha az ACI-t virtuális hálózatban szeretné használni a munkaterülethez, kövesse az alábbi lépéseket:

1. Ha engedélyezni szeretné az alhálózati delegálást a virtuális hálózaton, használja az [alhálózati delegálás hozzáadása vagy eltávolítása](../virtual-network/manage-subnet-delegation.md) című cikkben található információkat. Engedélyezheti a delegálást, amikor virtuális hálózatot hoz létre, vagy hozzáadhatja egy meglévő hálózathoz.

    > [!IMPORTANT]
    > A delegálás `Microsoft.ContainerInstance/containerGroups` engedélyezésekor használja __a Delegált alhálózat ot a szolgáltatás__ értékéhez.

2. Telepítse a modellt az [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-)használatával, használja a `vnet_name` és `subnet_name` a paramétereket. Állítsa be ezeket a paramétereket arra a virtuális hálózatnévre és alhálózatra, ahol engedélyezte a delegálást.



## <a name="use-azure-firewall"></a>Az Azure tűzfal használata

Az Azure Firewall használatakor be kell állítania egy hálózati szabályt, amely engedélyezi a forgalmat a következő címekre és azokból:

- `*.batchai.core.windows.net`
- `ml.azure.com`
- `*.azureml.ms`
- `*.experiments.azureml.net`
- `*.modelmanagement.azureml.net`
- `mlworkspace.azure.ai`
- `*.aether.ms`

A szabály hozzáadásakor állítsa a __protokollt__ bármelyikre, a portokat pedig a ikonra. `*`

A hálózati szabályok konfigurálásáról az [Azure Firewall telepítése és konfigurálása](/azure/firewall/tutorial-firewall-deploy-portal#configure-a-network-rule)című témakörben talál további információt.

## <a name="use-azure-container-registry"></a>Az Azure Container Registry használata

> [!IMPORTANT]
> Az Azure Container Registry (ACR) virtuális hálózaton belül rekedhet, azonban meg kell felelnie az alábbi előfeltételeknek:
>
> * Az Azure Machine Learning-munkaterületnek Enterprise Edition-nek kell lennie. A frissítésről a [Frissítés a Nagyvállalati kiadásra című](how-to-manage-workspace.md#upgrade)témakörben talál további információt.
> * Az Azure Container Registry-nek prémium verziónak kell lennie. A frissítéssel kapcsolatos további információkért lásd: [A nagykonformatika módosítása.](/azure/container-registry/container-registry-skus#changing-skus)
> * Az Azure Container Registry kell lennie ugyanabban a virtuális hálózatban és alhálózatban, mint a tárfiók és számítási célok at a betanítás vagy következtetés.
> * Az Azure Machine Learning-munkaterületnek tartalmaznia kell egy [Azure Machine Learning-számítási fürtöt.](how-to-set-up-training-targets.md#amlcompute)
>
>     Ha az ACR egy virtuális hálózat mögött van, az Azure Machine Learning nem tudja azt közvetlenül docker-lemezképek készítésére használni. Ehelyett a számítási fürt a lemezképek létrehozásához használatos.

1. Az Azure Container-beállításjegyzék nevének megkereséséhez használja az alábbi módszerek egyikét:

    __Azure Portal__

    A munkaterület áttekintő szakaszában a __beállításjegyzék-érték__ az Azure Container Registry-re hivatkozik.

    ![Azure Container Registry a munkaterülethez](./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png)

    __Azure CLI__

    Ha [telepítette a Machine Learning bővítményt az Azure CLI,használhatja](reference-azure-machine-learning-cli.md)a `az ml workspace show` parancsot, hogy a munkaterület adatait.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Ez a parancs a `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"`hoz hasonló értéket ad vissza. A karakterlánc utolsó része az Azure Container Registry neve a munkaterülethez.

1. A virtuális hálózathoz való hozzáférés korlátozásához kövesse a [Hálózati hozzáférés konfigurálása a rendszerleíró adatbázishoz](../container-registry/container-registry-vnet.md#configure-network-access-for-registry)című. A virtuális hálózat hozzáadásakor válassza ki a virtuális hálózatot és az alhálózatot az Azure Machine Learning-erőforrásokhoz.

1. Az Azure Machine Learning Python SDK használatával konfigurálhat egy számítási fürtöt docker-lemezképek létrehozásához. A következő kódrészlet bemutatja ennek módját:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > A tárfióknak, a számítási fürtnek és az Azure Container Registrynek a virtuális hálózat ugyanazon alhálózatában kell lennie.
    
    További információt a [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) metódus hivatkozási pontjában talál.

1. Ha az Azure Machine Learning-munkaterülethez privát hivatkozást használ, és a munkaterületéhez az Azure Container Registry-t egy virtuális hálózatba helyezi, a következő Azure Resource Manager-sablont is alkalmaznia kell. Ez a sablon lehetővé teszi, hogy a munkaterület kommunikáljon az ACR-rel a privát kapcsolaton keresztül.

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

## <a name="next-steps"></a>További lépések

* [Betanítási környezetek beállítása](how-to-set-up-training-targets.md)
* [A modellek üzembe helyezési helyének kiválasztása](how-to-deploy-and-where.md)
* [Webszolgáltatás biztonságossá tétele az Azure Machine Learning en keresztül a TLS használatával](how-to-secure-web-service.md)

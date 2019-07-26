---
title: Kísérletek és következtetések futtatása egy virtuális hálózaton
titleSuffix: Azure Machine Learning service
description: Gépi tanulási kísérletek futtatása és az Azure-beli virtuális hálózatokon belüli biztonságossá tétele. Megtudhatja, hogyan hozhat létre számítási célokat a modellek betanításához és a virtuális hálózatokon belüli következtetésekhez. Ismerje meg a biztonságos virtuális hálózatok követelményeit, például a bejövő és a kimenő portok megkövetelését.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 07/10/2019
ms.openlocfilehash: 412eaac2f82a6d09761dcac53192916df215831f
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358791"
---
# <a name="securely-run-experiments-and-inference-inside-an-azure-virtual-network"></a>Kísérletek és következtetések biztonságos futtatása Azure-beli virtuális hálózaton belül

Ebből a cikkből megtudhatja, hogyan futtathatja a kísérleteit és következtetéseit egy virtuális hálózaton belül. A virtuális hálózat biztonsági határként működik, és az Azure-erőforrásokat a nyilvános internetről különíti el. Egy Azure-beli virtuális hálózatot is csatlakoztathat a helyszíni hálózathoz. Lehetővé teszi, hogy biztonságosan betanítsa a modelleket, és elérhetővé tegye az üzembe helyezett modelleket a következtetésekhez. A következtetés vagy a modell pontozása az a fázis, ahol az üzembe helyezett modellt az előrejelzéshez használják, leggyakrabban a termelési adatforgalomban.

A Azure Machine Learning szolgáltatás más Azure-szolgáltatásokra támaszkodik számítási erőforrásokhoz. A számítási erőforrások (számítási célok) a modellek betanítására és üzembe helyezésére szolgálnak. Ezeket a számítási célokat egy virtuális hálózaton belül lehet létrehozni. Például a Microsoft Data Science Virtual Machine használatával betaníthatja a modelleket, majd üzembe helyezheti a modellt az Azure Kubernetes szolgáltatásban (ak). A virtuális hálózatokkal kapcsolatos további információkért tekintse meg az [Azure Virtual Network áttekintése](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Ez a dokumentum azt feltételezi, hogy az Azure-beli virtuális hálózatokat és az IP-hálózatkezelést általában ismeri. Ez a dokumentum azt is feltételezi, hogy létrehozott egy virtuális hálózatot és alhálózatot a számítási erőforrásokhoz való használatra. Ha nem ismeri az Azure Virtual Networks szolgáltatást, olvassa el a következő cikkeket a szolgáltatás megismeréséhez:

* [IP-címzés](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)
* [Biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview)
* [Rövid útmutató: Virtuális hálózat létrehozása](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
* [Hálózati forgalom szűrése](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="storage-account-for-your-workspace"></a>A munkaterület Storage-fiókja

> [!IMPORTANT]
> A Azure Machine Learning szolgáltatás __alapértelmezett Storage__ -fiókja __csak a kísérletezés__során helyezhető el egy virtuális hálózaton.
>
> A __nem alapértelmezett Storage-fiókok esetében__a kísérletezéshez, illetve ha a következtetéshez Storage-fiókot használ, __korlátlan hozzáféréssel kell rendelkeznie a Storage__-fiókhoz.
> 
> Ha nem biztos abban, hogy módosította ezeket a beállításokat, vagy nem, tekintse meg az [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-network-security) __az alapértelmezett hálózati hozzáférési szabály módosítása__ című témakört. A következő lépésekkel engedélyezheti a hozzáférést az összes hálózatról a következtetések során, vagy a modell pontozásával.

Ha a virtuális hálózatban a munkaterület alapértelmezett Azure Storage-fiókját szeretné használni, kövesse az alábbi lépéseket:

1. Hozzon létre egy kísérletezési számítást. Machine Learning Compute egy virtuális hálózat mögött, vagy egy kísérletezési számítási feladatokhoz csatolja a munkaterületet. HDInsight-fürt vagy virtuális gép. További információ: a [Machine learning Compute használata](#use-machine-learning-compute) és [virtuális gépek vagy HDInsight használata](#use-a-virtual-machine-or-hdinsight-cluster) a jelen dokumentumban
2. Nyissa meg a munkaterülethez csatolt tárterületet. ![A Azure Machine Learning szolgáltatás munkaterületéhez csatolt Azure Storage-t bemutató Azure Portal képe](./media/how-to-enable-virtual-network/workspace-storage.png)
3. Az Azure Storage lapon válassza a __tűzfalak és virtuális hálózatok__lehetőséget. ![Az Azure Storage lapján található tűzfalakat és virtuális hálózatokat bemutató Azure Portal képe](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)
4. A __tűzfalak és virtuális hálózatok__ lapon válassza ki a következő bejegyzéseket:
    - Válassza a __Kiválasztott hálózatok__ lehetőséget.
    - A __virtuális hálózatok__területen válassza a __meglévő virtuális hálózat hozzáadása__ lehetőséget, és adja hozzá azt a virtuális hálózatot, amelyben a kísérletezési számítás található. (Lásd az 1. lépést.)
    - Jelölje be __a megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése ehhez a Storage-fiókhoz__lehetőséget.
![Az Azure Storage-ban a tűzfalakat és a virtuális hálózatokat megjelenítő Azure Portal képe](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png) 

5. A kísérlet futtatása közben a kísérletezési kódban módosítsa a futtatási konfigurációt a blob Storage használatára:
    ```python
    run_config.source_directory_data_store = "workspaceblobstore"
    ```
    
## <a name="key-vault-for-your-workspace"></a>A munkaterület kulcstartója
A munkaterülethez társított Key Vault-példányt a Azure Machine Learning szolgáltatás a különböző típusú hitelesítő adatok tárolására használja:
* A társított Storage-fiókhoz tartozó kapcsolatok karakterlánca
* Az Azure Container repository példányainak jelszavai
* Az adattárakhoz való kapcsolódási karakterláncok. 

Ha Azure Machine Learning kísérletezési képességeket szeretne használni a virtuális hálózat mögötti Key Vault, kövesse az alábbi lépéseket:
1. Lépjen a munkaterülethez tartozó Key Vault. ![A Azure Machine Learning szolgáltatás munkaterülethez társított Key Vault mutató Azure Portal képe](./media/how-to-enable-virtual-network/workspace-key-vault.png)
2. A Key Vault lapon válassza a __tűzfalak és virtuális hálózatok__ szakaszt. ![A Key Vault lapon a tűzfalakat és a virtuális hálózatokat bemutató Azure Portal képe](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)
3. A __tűzfalak és virtuális hálózatok__ lapon válassza ki a következő bejegyzéseket:
    - Válassza a __Kiválasztott hálózatok__ lehetőséget.
    - A __virtuális hálózatok__területen válassza a __meglévő virtuális hálózatok hozzáadása__ lehetőséget annak a virtuális hálózatnak a hozzáadásához, ahol a kísérletezési számítás található.
    - __A tűzfal megkerülésének engedélyezése a megbízható Microsoft-szolgáltatások számára__jelölőnégyzetet.
![Az Azure Portal képe, amely a tűzfalak és a virtuális hálózatok oldalt mutatja Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png) 


## <a name="use-machine-learning-compute"></a>Machine Learning Compute használata

Ha Azure Machine Learning számítást szeretne használni egy virtuális hálózatban, használja a következő információkat a hálózati követelményekkel kapcsolatban:

- A virtuális hálózatnak ugyanabban az előfizetésben és régióban kell lennie, mint a Azure Machine Learning szolgáltatás munkaterületének.

- A Machine Learning Compute fürthöz megadott alhálózatnak elegendő, nem hozzárendelt IP-címmel kell rendelkeznie ahhoz, hogy megfeleljen a fürthöz célként megnevezett virtuális gépek számának. Ha az alhálózat nem rendelkezik elegendő nem hozzárendelt IP-címmel, a fürt részlegesen le lesz foglalva.

- Ha a virtuális hálózat védelmét úgy tervezi, hogy korlátozza a forgalmat, hagyjon meg néhány portot a Machine Learning Compute szolgáltatás számára. További információ: [szükséges portok](#mlcports).

- Győződjön meg arról, hogy a virtuális hálózat előfizetése vagy erőforráscsoport biztonsági házirendje vagy zárolása korlátozza-e a virtuális hálózat kezelésére vonatkozó engedélyeket.

- Ha egy virtuális hálózatban több Machine Learning Compute-fürtöt fog elhelyezni, előfordulhat, hogy egy vagy több erőforrásra vonatkozóan kvóta-növelést kell kérnie.

    A Machine Learning Compute automatikusan lefoglalja a virtuális hálózatot tartalmazó erőforráscsoport további hálózati erőforrásait. A Azure Machine Learning szolgáltatás minden Machine Learning Compute fürthöz a következő erőforrásokat foglalja le:

    - Egy hálózati biztonsági csoport (NSG)

    - One public IP address

    - Egy Load Balancer

  Ezekre az erőforrásokra az előfizetésben meghatározott [erőforráskvóták](https://docs.microsoft.com/azure/azure-subscription-service-limits) vonatkoznak.

### <a id="mlcports"></a>Szükséges portok

Machine Learning Compute jelenleg a Azure Batch szolgáltatás használatával helyezi üzembe a virtuális gépeket a megadott virtuális hálózaton. Az alhálózatnak engedélyeznie kell a bejövő kommunikációt a Batch szolgáltatástól. Ez a kommunikáció a Machine Learning Compute csomópontokon futtatott futtatások és az Azure Storage szolgáltatással és más erőforrásokkal való kommunikációra szolgál. A Batch a virtuális gépekhez csatolt hálózati adapterek (NIC-EK) szintjén adja hozzá a NSG. Ezek az NSG-k automatikusan konfigurálnak bejövő és kimenő szabályokat a következő forgalom engedélyezéséhez:

- Bejövő TCP-forgalom a 29876-es és a 29877-es portokon a __BatchNodeManagement__ __szolgáltatási címkéjén__ .

    ![A BatchNodeManagement szolgáltatás címkét használó bejövő szabályt mutató Azure Portal képe](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)
 
- választható Bejövő TCP-forgalom a 22-es porton a távoli hozzáférés engedélyezéséhez. Erre a portra csak akkor van szükség, ha az SSH használatával szeretne csatlakozni a nyilvános IP-címen.
 
- Kimenő forgalom bármilyen porton keresztül a virtuális hálózathoz.

- Kimenő forgalom bármilyen porton keresztül az internetre. 

Körültekintően járjon el, ha módosítja vagy hozzáadja a bejövő/kimenő szabályokat a Batch által konfigurált NSG. Ha egy NSG blokkolja a számítási csomópontok felé irányuló kommunikációt, akkor a Machine Learning Compute szolgáltatás a számítási csomópontok állapotát használhatatlanra állítja.

Nem kell megadnia a NSG az alhálózat szintjén, mert a Batch konfigurálja a saját NSG. Ha azonban a megadott alhálózat NSG és/vagy tűzfalat társít, konfigurálja a korábban említett bejövő és kimenő biztonsági szabályokat. 

A következő képernyőképen látható, hogyan néz ki a NSG-szabály konfigurációja a Azure Portalban:

![Képernyőkép a Machine Learning Compute bejövő NSG szabályairól](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Képernyőkép a Machine Learning Compute kimenő NSG szabályairól](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a>A virtuális hálózat kimenő kapcsolatának korlátozása

Ha nem szeretné az alapértelmezett kimenő szabályokat használni, és korlátozni szeretné a virtuális hálózat kimenő hozzáférését, kövesse az alábbi lépéseket:

- Kimenő internetkapcsolat megtagadása a NSG szabályok használatával 

- Az Azure Storage-ba irányuló kimenő adatforgalom korlátozása (a __Storage. Region_Name__ __szolgáltatás címkéjével__ . Storage. EastUS), Azure Container Registry (a __AzureContainerRegistry. Region_Name__ __szolgáltatás címkéjével__ . AzureContainerRegistry. EastUS) és Azure Machine Learning szolgáltatás (a AzureMachineLearning __szolgáltatás__ címkéjével )

A következő képernyőképen látható, hogyan néz ki a NSG-szabály konfigurációja a Azure Portalban:

![Képernyőkép a Machine Learning Compute kimenő NSG szabályairól](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)

### <a name="user-defined-routes-for-forced-tunneling"></a>A kényszerített bújtatáshoz tartozó felhasználó által megadott útvonalak

Ha kényszerített bújtatást használ Azure Machine Learning számítási feladatokkal, a számítási erőforrást tartalmazó alhálózathoz hozzá kell adnia a [felhasználó által megadott útvonalakat (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) .

* Egy felhasználó által megadott útvonal a Azure Batch szolgáltatás által használt egyes IP-címekhez abban a régióban, ahol az erőforrások léteznek. Ezek a UDR lehetővé teszik a Batch szolgáltatás számára a feladatütemezés számítási csomópontjaival való kommunikációját. A Batch szolgáltatás IP-címeinek listájának lekéréséhez forduljon az Azure ügyfélszolgálatához.

* A helyszíni hálózati berendezés nem tilthatja le az Azure Storage-ba `<account>.table.core.windows.net`irányuló kimenő `<account>.blob.core.windows.net`forgalmat (pontosabban az űrlap `<account>.queue.core.windows.net`URL-címeit, és).

A felhasználó által megadott útvonalak hozzáadásakor adja meg az útvonalat az egyes kapcsolódó batch IP-cím előtaghoz, és állítsa be a __következő ugrás típusát__ az __Internet__értékre. Az alábbi képen látható példa erre a UDR mutat a Azure Portalban:

![Példa felhasználó által megadott útvonalra a címek előtagja számára](./media/how-to-enable-virtual-network/user-defined-route.png)

További információt az [Azure batch-készlet létrehozása virtuális hálózatban](../../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling) című cikkben talál.

### <a name="create-machine-learning-compute-in-a-virtual-network"></a>Machine Learning Compute létrehozása virtuális hálózaton

Machine Learning Compute-fürtnek a Azure Portal használatával történő létrehozásához kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)válassza ki a Azure Machine learning szolgáltatás munkaterületét.

1. Az __alkalmazás__ szakaszban válassza a __számítás__lehetőséget. Ezután válassza a __számítás hozzáadása__lehetőséget. 

    ![Számítás hozzáadása Azure Machine Learning szolgáltatásban](./media/how-to-enable-virtual-network/add-compute.png)

1. A számítási erőforrás virtuális hálózat használatára való konfigurálásához használja az alábbi beállításokat:

    - __Hálózati konfiguráció__: Válassza az __Advanced__ (Speciális) lehetőséget.

    - __Erőforráscsoport__: Válassza ki a virtuális hálózatot tartalmazó erőforráscsoportot.

    - __Virtuális hálózat__: Válassza ki az alhálózatot tartalmazó virtuális hálózatot.

    - __Alhálózat__: Válassza ki a használni kívánt alhálózatot.

   ![A Machine learning-számítás virtuális hálózati beállításait bemutató képernyőkép](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Machine Learning Compute-fürtöt a Azure Machine Learning SDK használatával is létrehozhat. A következő kód egy új Machine learning Compute fürtöt `default` hoz létre egy nevű `mynetwork`virtuális hálózat alhálózatában:

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

    # Wait for the cluster to complete, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

A létrehozási folyamat befejezésekor a modellt a fürt használatával is betaníthatja. További információkért lásd: [számítási cél kiválasztása és használata képzéshez](how-to-set-up-training-targets.md).

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Virtuális gép vagy HDInsight-fürt használata

Ha egy virtuális gépet vagy Azure HDInsight-fürtöt szeretne használni a munkaterülettel rendelkező virtuális hálózatban, kövesse az alábbi lépéseket:

> [!IMPORTANT]
> A Azure Machine Learning szolgáltatás csak az Ubuntut futtató virtuális gépeket támogatja.

1. Hozzon létre egy virtuális GÉPET vagy HDInsight-fürtöt az Azure Portal vagy az Azure CLI használatával, és helyezze egy Azure-beli virtuális hálózatba. További információ a következő dokumentumokban található:
    * [Azure Virtual Networks létrehozása és kezelése Linux rendszerű virtuális gépekhez](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [HDInsight kiterjesztése Azure Virtual Network használatával](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network) 

1. Ahhoz, hogy a Azure Machine Learning szolgáltatás kommunikáljon a virtuális gép vagy fürt SSH-portjával, konfigurálnia kell egy NSG. Az SSH-port általában a 22-es port. A forrásból érkező forgalom engedélyezéséhez használja a következő információkat:

    * __Forrás__: Válassza ki a __szolgáltatás címkéjét__.

    * __Forrásoldali szolgáltatás címkéje__: Válassza a __AzureMachineLearning__lehetőséget.

    * __Forrásport-tartományok__: Válassza __*__ a lehetőséget.

    * __Cél__: Válassza __a bármelyik__lehetőséget.

    * __Célport tartományai__: Válassza a __22-es__lehetőséget.

    * __Protokoll__: Válassza __a bármelyik__lehetőséget.

    * __Művelet__: Válassza az __Engedélyezés__lehetőséget.

   ![A virtuális hálózaton belüli virtuális gépeken vagy HDInsight-fürtökön a kísérletezéshez szükséges Bejövő szabályok képernyőképe](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Tartsa meg a NSG alapértelmezett kimenő szabályait. További információ: [biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)alapértelmezett biztonsági szabályai.

    Ha nem szeretné az alapértelmezett kimenő szabályokat használni, és korlátozni szeretné a virtuális hálózat kimenő hozzáférését, tekintse meg [a kimenő kapcsolatok korlátozása a virtuális hálózatról](#limiting-outbound-from-vnet) című témakört.
    
1. Csatlakoztassa a virtuális gépet vagy a HDInsight-fürtöt a Azure Machine Learning szolgáltatás munkaterületéhez. További információ: [számítási célok beállítása a modell](how-to-set-up-training-targets.md)betanításához.

## <a name="use-azure-kubernetes-service"></a>Az Azure Kubernetes Service használata

> [!IMPORTANT]
> A lépések végrehajtása előtt keresse meg a fürthöz tartozó előfeltételeket, és tervezze meg az IP-címzést. További információ: [speciális hálózatkezelés konfigurálása az Azure Kubernetes szolgáltatásban](https://docs.microsoft.com/azure/aks/configure-advanced-networking).
> 
>
> Tartsa meg a NSG alapértelmezett kimenő szabályait. További információ: [biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)alapértelmezett biztonsági szabályai.
>
> Az Azure Kubernetes szolgáltatásnak és az Azure-beli virtuális hálózatnak ugyanabban a régióban kell lennie.

Ha az Azure Kubernetes szolgáltatást egy virtuális hálózatban szeretné hozzáadni a munkaterülethez, kövesse az alábbi lépéseket a Azure Portalban:

1. Győződjön meg arról, hogy a virtuális hálózatot vezérlő NSG-csoport bejövő szabálya engedélyezve van Azure Machine Learning szolgáltatáshoz a __AzureMachineLearning__ __szolgáltatási címkéjén__ keresztül

    ![Számítás hozzáadása Azure Machine Learning szolgáltatásban](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)     
 
1. A [Azure Portal](https://portal.azure.com)válassza ki a Azure Machine learning szolgáltatás munkaterületét.

1. Az __alkalmazás__ szakaszban válassza a __számítás__lehetőséget. Ezután válassza a __számítás hozzáadása__lehetőséget. 

    ![Számítás hozzáadása Azure Machine Learning szolgáltatásban](./media/how-to-enable-virtual-network/add-compute.png)

1. A számítási erőforrás virtuális hálózat használatára való konfigurálásához használja az alábbi beállításokat:

    - __Hálózati konfiguráció__: Válassza az __Advanced__ (Speciális) lehetőséget.

    - __Erőforráscsoport__: Válassza ki a virtuális hálózatot tartalmazó erőforráscsoportot.

    - __Virtuális hálózat__: Válassza ki az alhálózatot tartalmazó virtuális hálózatot.

    - __Alhálózat__: Válassza ki az alhálózatot.

    - __Kubernetes szolgáltatási__címtartomány: Válassza ki a Kubernetes szolgáltatási címtartományt. Ez a címtartomány egy CIDR jelölésű IP-címtartományt használ a fürt számára elérhető IP-címek definiálásához. Az alhálózati IP-címtartományok nem lehetnek átfedésben. Példa: 10.0.0.0/16.

    - __Kubernetes DNS-szolgáltatás IP-címe__: Válassza ki a Kubernetes DNS-szolgáltatás IP-címét. Ezt az IP-címet a Kubernetes DNS szolgáltatáshoz rendeli a rendszer. Ennek a Kubernetes-szolgáltatási címtartomány belül kell lennie. Példa: 10.0.0.10.

    - __Docker-híd címe__: Válassza ki a Docker-híd címe elemet. Ezt az IP-címet a Docker-híd rendeli hozzá. Nem lehet alhálózati IP-címtartományok vagy a Kubernetes szolgáltatási címtartomány. Példa: 172.17.0.1/16.

   ![Azure Machine Learning szolgáltatás: Virtuális hálózati beállítások Machine Learning Compute](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Győződjön meg arról, hogy a virtuális hálózatot vezérlő NSG-csoport bejövő szabálya engedélyezve van a pontozási végpont számára, hogy a hívás a virtuális hálózaton kívülről is meghívható legyen.

    ![Számítás hozzáadása Azure Machine Learning szolgáltatásban](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)

    > [!TIP]
    > Ha már van egy AK-fürtje egy virtuális hálózaton, akkor csatolhatja azt a munkaterülethez. További információ: [üzembe helyezés az AK](how-to-deploy-to-aks.md)-ban.

A **Azure Machine learning SDK** -val is hozzáadhatja az Azure Kubernetes szolgáltatást egy virtuális hálózatban. A következő kód egy új Azure Kubernetes Service- `default` példányt hoz létre egy nevű `mynetwork`virtuális hálózat alhálózatában:

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

A létrehozási folyamat befejezésekor a virtuális hálózat mögötti AK-fürtökön megtekintheti a következtetést/pontszámot. További információ: [üzembe helyezés az AK](how-to-deploy-to-aks.md)-ban.

## <a name="next-steps"></a>További lépések

* [Képzési környezetek beállítása](how-to-set-up-training-targets.md)
* [Az üzembe helyezés modellek](how-to-deploy-and-where.md)
* [Modellek biztonságos üzembe helyezése SSL használatával](how-to-secure-web-service.md)


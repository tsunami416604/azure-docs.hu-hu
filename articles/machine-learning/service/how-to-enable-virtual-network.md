---
title: Futtassa a kísérletek tanuláshoz és következtetésekhez egy virtuális hálózaton
titleSuffix: Azure Machine Learning service
description: Futtassa a machine learning-kísérletek és következtetésekhez biztonságossá tétele egy Azure virtuális hálózaton belül. Ismerje meg, hogyan hozhat létre a számítási célokhoz modell betanítása és a egy virtuális hálózaton belüli következtetésekhez módjáról. Biztonságos virtuális hálózatok követelményeivel kapcsolatos további kötelező, például a bejövő és kimenő portokat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 01/08/2019
ms.openlocfilehash: 48c59ddc1e203030bd967911d536930cb94761d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66356178"
---
# <a name="securely-run-experiments-and-inference-inside-an-azure-virtual-network"></a>Biztonságos futtatására kísérletek tanuláshoz és következtetésekhez egy Azure virtuális hálózaton belül

Ebből a cikkből elsajátíthatja a kísérletek tanuláshoz és következtetésekhez virtuális hálózatokon belüli futtatásáról. Virtuális hálózat biztonsági határt, a nyilvános interneten keresztül az Azure-erőforrások elkülönítése funkcionál. A helyszíni hálózathoz is csatlakozhat az Azure-beli virtuális hálózathoz. Lehetővé teszi, hogy biztonságosan a modellek betanítása és a telepített modelljeit következtetésekhez eléréséhez. Következtetésekhez vagy a modell pontozása, nem a fázis, az üzembe helyezett modell előrejelzési leggyakrabban a termelési adatok szolgál.

Az Azure Machine Learning szolgáltatás más Azure-szolgáltatások számítási erőforrások is támaszkodik. A számítási erőforrások (számítási céljainak) segítségével betanítása és a modellek üzembe helyezése. Ezek a számítási céljainak egy virtuális hálózaton belül hozható létre. Például használhatja a Microsoft Data Science virtuális gép betanítja a modellt, és majd a modell üzembe helyezése az Azure Kubernetes Service (AKS). Virtuális hálózatokkal kapcsolatos további információkért lásd: a [Azure Virtual Network áttekintése](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="prerequisites"></a>Előfeltételek

Jelen dokumentum céljából feltételezzük, hogy Ön ismeri az Azure-beli virtuális hálózatok és IP általában a hálózat. Ez a dokumentum azt is feltételezi, hogy létrehozott egy virtuális hálózatot és alhálózatot a számítási erőforrások használatát. Ha nem ismeri az Azure Virtual Network szolgáltatással, olvassa el a szolgáltatással kapcsolatban további információt a következő cikkeket:

* [IP-címzés](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)
* [Biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview)
* [Rövid útmutató: Virtuális hálózat létrehozása](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
* [Hálózati forgalom szűrése](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="storage-account-for-your-workspace"></a>Storage-fiók a munkaterülethez

> [!IMPORTANT]
> A storage-fiókot csak Kísérletezési közben a virtuális hálózat mögött az Azure Machine Learning szolgáltatás munkaterületén csatolt helyezheti. Következtetésekhez korlátlan hozzáférést a storage-fiók szükséges. Ha nem biztos benne, hogy módosította ezeket a beállításokat, vagy nem, lásd: __módosítsa az alapértelmezett hálózati hozzáférési szabály__ a [konfigurálása az Azure Storage-tűzfalak és virtuális hálózatok](https://docs.microsoft.com/azure/storage/common/storage-network-security). A lépések segítségével engedélyezze a hozzáférést minden hálózatból elérhető következtetésekhez során, vagy a modell pontozása.

Az Azure Machine Learning-Kísérletezési képességek használata az Azure Storage mögött egy virtuális hálózatot, kövesse az alábbi lépéseket:

1. Hozzon létre egy Kísérletezési számítási ex. Learning Compute Machine mögött egy virtuális hálózatot, vagy egy Kísérletezési számítási csatolása ex a munkaterületen. HDInsight-fürt vagy a virtuális gép. További információkért lásd: [Machine Learning Compute](#use-machine-learning-compute) és [egy virtuális gép vagy HDInsight-fürt](#use-a-virtual-machine-or-hdinsight-cluster) szakaszok a jelen dokumentum
2. Nyissa meg a storage, a munkaterülethez csatlakozik. ![Az Azure portál Azure Storage, amely csatlakozik az Azure Machine Learning szolgáltatás munkaterület képe](./media/how-to-enable-virtual-network/workspace-storage.png)
3. Az Azure Storage oldalon válassza ki a __tűzfalak és virtuális hálózatok__. ![Kép az Azure portal megjelenítése a tűzfalak és virtuális hálózatok szakaszban Azure Storage az oldalon](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)
4. Az a __tűzfalak és virtuális hálózatok__ oldalon válassza ki az alábbi bejegyzéseket:
    - Válassza a __Kiválasztott hálózatok__ lehetőséget.
    - A __virtuális hálózatok__válassza __meglévő virtuális hálózat hozzáadása__ hozzáadása a virtuális hálózatot, amelyben a Kísérletezési számítási található. (Lásd az 1. lépés.)
    - Válassza ki __engedélyezése megbízható Microsoft-szolgáltatások a tárfiók__.
![Kép az Azure portal megjelenítése a tűzfalak és virtuális hálózatok lapján az Azure Storage](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png) 

5. A Kísérletezési kód kísérlet futtatásakor a blob storage használata futtatási konfigurációs módosítása:
    ```python
    run_config.source_directory_data_store = "workspaceblobstore"
    ```
    
## <a name="key-vault-for-your-workspace"></a>A munkaterület a Key vault
Key Vault-példány a munkaterülethez társított Azure Machine Learning szolgáltatás által használt különböző típusú hitelesítő adatok tárolásához:
* A társított tárfiók kapcsolati sztringje
* A jelszavakat az Azure tároló-beállításjegyzékbe példányok
* Kapcsolati karakterláncok adatokat tárolja. 

Egy virtuális hálózati funkciókat a Key Vault használata az Azure Machine Learning-kísérletezés kövesse az alábbi lépéseket:
1. Nyissa meg a Key Vault társítva a munkaterülethez. ![A Key Vault az Azure Machine Learning szolgáltatás munkaterülethez társított Azure portál képe](./media/how-to-enable-virtual-network/workspace-key-vault.png)
2. A Key Vault oldalon válassza ki a __tűzfalak és virtuális hálózatok__ szakaszban. ![Lemezkép az Azure portal megjelenítése a tűzfalak és virtuális hálózatok, a Key Vault lapon szakasz](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)
3. Az a __tűzfalak és virtuális hálózatok__ oldalon válassza ki az alábbi bejegyzéseket:
    - Válassza a __Kiválasztott hálózatok__ lehetőséget.
    - Alatt a __virtuális hálózatok__válassza __adja hozzá a meglévő virtuális hálózatok__ hozzáadása a virtuális hálózatot, amelyben a Kísérletezési számítási található.
    - Válassza ki __engedélyezése megbízható Microsoft-szolgáltatások a tűzfal megkerülését__.
![Lemezkép az Azure portal megjelenítése a tűzfalak és virtuális hálózatok lapon a Key vaultban](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png) 


## <a name="use-machine-learning-compute"></a>Machine Learning Compute használata

Használja az Azure Machine Learning Compute a virtuális hálózatban, használja a hálózati követelményeket a következő információkat:

- A virtuális hálózat az Azure Machine Learning szolgáltatás munkaterület azonos előfizetésben és régióban kell lennie.

- Az alhálózat megadva, a Machine Learning COMPUTE számítási fürt kell elegendő hozzá nem rendelt IP-címek a virtuális gépek számának megfelelően célzott a fürt számára. Ha az alhálózaton nincs elegendő hozzá nem rendelt IP-címek, a fürt részlegesen oszt ki.

- Ha azt tervezi, a virtuális hálózat védelme érdekében a forgalom korlátozásával, hagyja megnyitva a Machine Learning Compute szolgáltatás bizonyos portokat. További információkért lásd: [szükséges portok](#mlcports).

- Ellenőrizze, hogy a biztonsági házirendek vagy a virtuális hálózat előfizetésekhez vagy erőforráscsoportokhoz a zárolását korlátozza a virtuális hálózat engedélyeit.

- Ha egy virtuális hálózaton belüli több Machine Learning COMPUTE számítási fürt helyezni kívánja, szükség lehet egy vagy több, az erőforrások kvóta növelésére.

    Machine Learning Compute automatikusan foglalja le a további hálózati erőforrások az erőforráscsoportban, amely tartalmazza a virtuális hálózat. Minden egyes Machine Learning COMPUTE számítási fürt az Azure Machine Learning szolgáltatás a következő erőforrásokat foglal le:

    - Egy hálózati biztonsági csoport (NSG)

    - One public IP address

    - Egy terheléselosztó

  Ezekre az erőforrásokra az előfizetésben meghatározott [erőforráskvóták](https://docs.microsoft.com/azure/azure-subscription-service-limits) vonatkoznak.

### <a id="mlcports"></a> Szükséges portok

A megadott virtuális hálózatban lévő virtuális gépek kiépítése Azure Batch szolgáltatás jelenleg Machine Learning Compute használja. Az alhálózatnak engedélyeznie kell a Batch szolgáltatás a bejövő kommunikációhoz. Ehhez a kommunikációhoz használatos ütemezni futtatja, a Machine Learning COMPUTE számítási csomópontokon, és kommunikál az Azure Storage és egyéb erőforrásokat. A Batch NSG-k hozzáadása a virtuális gépekhez csatlakoztatott hálózati adapterek (NIC) szintjén. Ezek az NSG-k automatikusan konfigurálnak bejövő és kimenő szabályokat a következő forgalom engedélyezéséhez:

- TCP-forgalmat a 29876-os és a 29877-es portot a bejövő egy __Szolgáltatáscímke__ , __BatchNodeManagement__.

    ![Az Azure portál egy bejövő szabályt a BatchNodeManagement szolgáltatáscímke használatával képe](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)
 
- (nem kötelező) A bejövő TCP-forgalom engedélyezéséhez a távelérés 22-es port. Ez a port csak akkor van szükség, ha az SSH használata a nyilvános IP-cím a csatlakozni kíván.
 
- Kimenő forgalom bármilyen porton keresztül a virtuális hálózathoz.

- Kimenő forgalom bármilyen porton keresztül az internetre. 

Körültekintéssel, ha módosítja vagy bejövő/kimenő szabályok hozzáadása a Batch-konfigurált NSG-ket. Ha egy NSG blokkolja a számítási csomópontoknak kommunikációs, majd a Machine Learning COMPUTE számítási szolgáltatások állítja a számítási csomópontok állapotát használhatatlan lesz.

Adja meg az NSG-ket az alhálózatok, mivel a Batch konfigurálja a saját NSG-k nem kell. Azonban ha a megadott alhálózati NSG-k és/vagy tűzfal vannak társítva, konfigurálja a bejövő és kimenő biztonsági szabályok a korábban leírtaknak. 

Az alábbi képernyőképen látható, hogyan néz ki az NSG-szabály konfigurációját az Azure Portalon:

![A Machine Learning Compute szabályok NSG bejövő képernyőképe](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Képernyőkép a kimenő NSG-t a Machine Learning Compute szabályok](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a> Korlátozza a virtuális hálózatról kimenő kapcsolatok

Ha nem szeretné az alapértelmezett kimenő szabályok használata, és a virtuális hálózat kimenő korlátozni szeretné, kövesse az alábbi lépéseket:

- Az NSG-szabályok használatával kimenő internetkapcsolat megtagadása 

- Az Azure Storage kimenő forgalom korlátozása (használatával __Szolgáltatáscímke__ , __Storage.Region_Name__ ex. Storage.EastUS), az Azure Container Registry (használatával __Szolgáltatáscímke__ , __AzureContainerRegistry.Region_Name__ ex. AzureContainerRegistry.EastUS), és az Azure Machine Learning szolgáltatás (használatával __Szolgáltatáscímke__ , __AzureMachineLearning__)

Az alábbi képernyőképen látható, hogyan néz ki az NSG-szabály konfigurációját az Azure Portalon:

![Képernyőkép a kimenő NSG-t a Machine Learning Compute szabályok](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)

### <a name="user-defined-routes-for-forced-tunneling"></a>Felhasználó által megadott útvonalak a kényszerített bújtatás

Ha használja az Azure Machine Learning Compute kényszerített bújtatás, hozzá kell adnia [felhasználó által megadott útvonalak (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) az alhálózatra, amely a számítási erőforrást tartalmaz.

* Egy felhasználó által megadott útvonal abban a régióban, ahol az erőforrások léteznek-e az Azure Batch szolgáltatás által használt IP-címeket. Ezek az udr-EK feladat ütemezésére a számítási csomópontok kommunikálnak a batch szolgáltatás engedélyezése. A Batch szolgáltatás az IP-címek listájának beolvasása, forduljon az Azure ügyfélszolgálatához.

* Kimenő forgalom az Azure Storage (pontosabban az űrlap URL `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`, és `<account>.blob.core.windows.net`) nem a helyszíni hálózati berendezés blokkolja kell.

A felhasználó által megadott útvonalak hozzáadásakor adja meg az egyes kapcsolódó Batch IP-címelőtag útvonalát, és állítsa be __következő ugrás típusa__ való __Internet__. Az alábbi képen egy példa az udr-t az Azure Portalon:

![Példa egy címelőtag felhasználó által megadott útvonala](./media/how-to-enable-virtual-network/user-defined-route.png)

További információkért lásd: a [Azure Batch-készlet létrehozása egy virtuális hálózaton belül](../../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling) cikk.

### <a name="create-machine-learning-compute-in-a-virtual-network"></a>Hozzon létre a Machine Learning Compute a virtuális hálózaton

Machine Learning COMPUTE számítási fürt létrehozása az Azure portal használatával, kövesse az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com), válassza ki az Azure Machine Learning szolgáltatás munkaterületet.

1. Az a __alkalmazás__ szakaszban jelölje be __számítási__. Válassza ki __számítás hozzáadása__. 

    ![Az Azure Machine Learning szolgáltatás egy számítási hozzáadása](./media/how-to-enable-virtual-network/add-compute.png)

1. A számítási erőforrásokat egy virtuális hálózatot használja, használja az alábbi beállításokat konfigurálhatja:

    - __Hálózati konfiguráció__: Válassza az __Advanced__ (Speciális) lehetőséget.

    - __Erőforráscsoport__: Válassza ki az erőforráscsoportot, amely tartalmazza a virtuális hálózat.

    - __Virtuális hálózat__: Válassza ki a virtuális hálózat, amely tartalmazza az alhálózat.

    - __Alhálózat__: Válassza ki azt az alhálózatot, használja.

   ![A machine learning compute a virtuális hálózati beállításait ábrázoló képernyőfelvétel](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Az Azure Machine Learning SDK használatával is létrehozhat egy Machine Learning COMPUTE számítási fürtöt. Az alábbi kód létrehoz egy új Machine Learning COMPUTE számítási fürtöt az a `default` nevű virtuális hálózat alhálózati `mynetwork`:

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
                                                           vnet_resourcegroup_name = vnet_resourcegroup_name,
                                                           vnet_name = vnet_name,
                                                           subnet_name = subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    
    # Wait for the cluster to complete, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

A létrehozási folyamat végeztével a modell betaníthatja a fürt használatával. További információkért lásd: [válassza ki, és a egy számítási célnak használata képzéshez](how-to-set-up-training-targets.md).

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>A virtuális gép vagy HDInsight-fürt

Virtuális gép vagy Azure HDInsight-fürt használata a munkaterület egy virtuális hálózaton, kövesse az alábbi lépéseket:

> [!IMPORTANT]
> Az Azure Machine Learning szolgáltatás csak az Ubuntu rendszert futtató virtuális gépeket támogatja.

1. Egy virtuális gép vagy HDInsight-fürt létrehozása az Azure Portalon vagy az Azure CLI használatával, és helyezze egy Azure virtuális hálózatra. További információkért tekintse meg a következő dokumentumokat:
    * [Hozzon létre, és a Linux rendszerű virtuális gépekhez az Azure virtuális hálózatok kezelése](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [A HDInsight használata az Azure virtuális hálózat kiterjesztése](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network) 

1. Ahhoz, hogy az Azure Machine Learning szolgáltatás kommunikálni a virtuális gép vagy a fürt SSH-port, konfigurálnia kell az adatforrás-bejegyzés az NSG. Az SSH-port általában a 22-es portot. Ebből a forrásból érkező forgalom engedélyezéséhez használja a következő információkat:

    * __forrás__: Válassza ki __Szolgáltatáscímke__.

    * __Forrás szolgáltatáscímkéje__: Válassza ki __AzureMachineLearning__.

    * __Porttartományok forrás__: Válassza ki __*__ .

    * __Cél__: Válassza ki __bármely__.

    * __Célporttartományok__: Válassza ki __22-es__.

    * __Protokoll__: Válassza ki __bármely__.

    * __A művelet__: Válassza ki __engedélyezése__.

   ![Képernyőkép a virtuális hálózatban lévő virtuális gép vagy HDInsight fürtön Kísérletezési adatelemzésre bejövő szabályok](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Tartsa meg az alapértelmezett kimenő szabályokat az NSG. További információkért tekintse meg az alapértelmezett biztonsági szabályok az [biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Ha nem szeretné, hogy az alapértelmezett kimenő szabályok használata, és szeretné korlátozni a kimenő hozzáférést a virtuális hálózat, lásd: [korlátozza a virtuális hálózatról kimenő kapcsolatok](#limiting-outbound-from-vnet)
    
1. Csatlakoztassa a virtuális gép vagy HDInsight-fürt az Azure Machine Learning szolgáltatás munkaterületén. További információkért lásd: [állítsa be a modell betanítása és számítási célnak](how-to-set-up-training-targets.md).

## <a name="use-azure-kubernetes-service"></a>Az Azure Kubernetes Service használata

> [!IMPORTANT]
> Az Előfeltételek ellenőrzése és a lépések végrehajtása előtt a fürt IP-címzés tervezése. További információkért lásd: [hálózat az Azure Kubernetes Service speciális konfigurálása](https://docs.microsoft.com/azure/aks/configure-advanced-networking).
> 
>
> Tartsa meg az alapértelmezett kimenő szabályokat az NSG. További információkért tekintse meg az alapértelmezett biztonsági szabályok az [biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).
>
> Az Azure Kubernetes Service és az Azure virtuális hálózat ugyanabban a régióban kell lennie.

Azure Kubernetes Service hozzáadása egy virtuális hálózatban a munkaterülethez, kövesse az alábbi lépéseket az Azure Portalon:

1. Győződjön meg arról, hogy az NSG csoportot, amely szabályozza a virtuális hálózat bejövő szabály engedélyezve van az Azure Machine Learning szolgáltatás használatával __Szolgáltatáscímke__ , __AzureMachineLearning__

    ![Az Azure Machine Learning szolgáltatás egy számítási hozzáadása](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)     
 
1. Az a [az Azure portal](https://portal.azure.com), válassza ki az Azure Machine Learning szolgáltatás munkaterületet.

1. Az a __alkalmazás__ szakaszban jelölje be __számítási__. Válassza ki __számítás hozzáadása__. 

    ![Az Azure Machine Learning szolgáltatás egy számítási hozzáadása](./media/how-to-enable-virtual-network/add-compute.png)

1. A számítási erőforrásokat egy virtuális hálózatot használja, használja az alábbi beállításokat konfigurálhatja:

    - __Hálózati konfiguráció__: Válassza az __Advanced__ (Speciális) lehetőséget.

    - __Erőforráscsoport__: Válassza ki az erőforráscsoportot, amely tartalmazza a virtuális hálózat.

    - __Virtuális hálózat__: Válassza ki a virtuális hálózat, amely tartalmazza az alhálózat.

    - __Alhálózat__: Válassza ki az alhálózatot.

    - __Kubernetes-szolgáltatást címtartomány__: Válassza ki a Kubernetes-szolgáltatás-címtartományt. Ez a címtartomány CIDR jelölésrendszerben IP-címtartomány használja a fürtcsomópont számára elérhető IP-címek definiálásához. Az összes alhálózat IP-címtartományok nem lehetnek átfedésben. Példa: 10.0.0.0/16.

    - __Kubernetes DNS szolgáltatás IP-cím__: Válassza ki a Kubernetes DNS szolgáltatás IP-címe. Az IP-cím van rendelve a Kubernetes DNS-szolgáltatás. A Kubernetes szolgáltatás címtartományán belül kell lennie. Példa: 10.0.0.10.

    - __Docker híd cím__: Válassza ki a Docker híd címet. Az IP-címet a Docker híd van hozzárendelve. Nem lehet minden olyan alhálózat IP-címtartományok vagy a Kubernetes-szolgáltatás-címtartományt. Példa: 172.17.0.1/16.

   ![Azure Machine Learning service: A Machine Learning Compute a virtuális hálózati beállítások](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Győződjön meg arról, hogy az NSG csoportot, amely szabályozza a virtuális hálózat bejövő szabály, a pontozó végpont engedélyezve van, úgy, hogy azt nem hívható meg a virtuális hálózaton kívüli

    ![Az Azure Machine Learning szolgáltatás egy számítási hozzáadása](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)

    > [!TIP]
    > Ha már rendelkezik egy AKS-fürt virtuális hálózaton, a munkaterületet is csatlakoztatható. További információkért lásd: [hogyan helyezhet üzembe az aks](how-to-deploy-to-aks.md).

Is használhatja a **Azure Machine Learning SDK** Azure Kubernetes Service hozzáadása egy virtuális hálózaton. Az alábbi kód létrehoz egy új, az Azure Kubernetes Service-példányt a `default` nevű virtuális hálózat alhálózati `mynetwork`:

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
aks_target = ComputeTarget.create(workspace = ws,
                                  name = "myaks",
                                  provisioning_configuration = config)
```

A létrehozási folyamat befejezése után következtetésekhez/pontszám a virtuális hálózat mögött egy AKS-fürtöt is. További információkért lásd: [hogyan helyezhet üzembe az aks](how-to-deploy-to-aks.md).

## <a name="next-steps"></a>További lépések

* [Képzési környezetek beállítása](how-to-set-up-training-targets.md)
* [Az üzembe helyezés modellek](how-to-deploy-and-where.md)
* [Biztonságos helyezhet üzembe modelleket SSL-lel](how-to-secure-web-service.md)


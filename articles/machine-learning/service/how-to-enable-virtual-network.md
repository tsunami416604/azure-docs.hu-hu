---
title: Futtassa a kísérletek & következtetésekhez egy virtuális hálózaton
titleSuffix: Azure Machine Learning service
description: Futtassa a machine learning-kísérletek és következtetési biztonságossá tétele egy Azure virtuális hálózaton belül. Ismerje meg, hogyan hozhat létre a számítási célokhoz modell betanítása és a egy Azure virtuális hálózaton belüli következtetésekhez módjáról. Emellett ismerteti a védett virtuális hálózatok követelményei, mint például a bejövő és kimenő portokat szükséges.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 01/08/2019
ms.openlocfilehash: 2e7f6c066ea254fff90ba2f9ff1f559fdb680ddf
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55766690"
---
# <a name="securely-run-experiments-and-inferencing-inside-an-azure-virtual-network"></a>Biztonságos futtatására kísérletek vagy következtetési egy Azure virtuális hálózaton belül

Ebből a cikkből elsajátíthatja a kísérletek és a egy virtuális hálózaton belül következtetési futtatásához. Virtuális hálózat biztonsági határt, a nyilvános interneten keresztül az Azure-erőforrások elkülönítése funkcionál. Az Azure Virtual Network csatlakozhat a helyszíni hálózathoz is. Lehetővé teszi, hogy biztonságosan a modellek betanítása és a telepített modelljeit következtetési eléréséhez.

Az Azure Machine Learning szolgáltatás dolgot számítási erőforrások más Azure-szolgáltatásokra támaszkodik. A számítási erőforrások (számítási céljainak) segítségével betanítása és a modellek üzembe helyezése. Ezek a számítási céljainak egy virtuális hálózaton belül hozható létre. Például használhatja egy adatelemző virtuális gép betanítja a modellt, és a modell üzembe helyezése Azure Kubernetes Service-ben. Virtuális hálózatokkal kapcsolatos további információkért lásd: a [virtuális hálózatok áttekintése](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) dokumentumot.

## <a name="storage-account-for-your-workspace"></a>Storage-fiók a munkaterülethez

Az Azure Machine Learning szolgáltatás munkaterület létrehozásakor egy Azure Storage-fiók szükséges. Ne kapcsolja be ezt a tárfiókot vonatkozó tűzfalszabályok. Az Azure Machine Learning szolgáltatás a tárfiók korlátlan hozzáférést igényel.

Ha nem biztos hogy módosította ezeket a beállításokat vagy nem, olvassa el a __módosítsa az alapértelmezett hálózati hozzáférési szabály__ szakaszában a [konfigurálása az Azure Storage-tűzfalak és virtuális hálózatok](https://docs.microsoft.com/azure/storage/common/storage-network-security) dokumentum- és használata a lépéseket _hozzáférést_ a __minden hálózatból elérhető__.

## <a name="use-machine-learning-compute"></a>Machine Learning Compute használata

Használja a Machine Learning Compute a virtuális hálózatban, hálózati követelményeinek megismeréséhez használja a következő információkat:

- A virtuális hálózat az Azure Machine Learning szolgáltatás munkaterület azonos előfizetésben és régióban kell lennie.

- Az alhálózat megadva, a Machine Learning COMPUTE számítási fürt kell elegendő hozzá nem rendelt IP-címek a virtuális gépek számának megfelelően célzott a fürt számára. Ha az alhálózaton nincs elegendő hozzá nem rendelt IP-címek, a fürt részlegesen oszt ki.

- Ha azt tervezi, a virtuális hálózat védelme érdekében a forgalom korlátozásával, kell hagyja bizonyos portokat nyissa meg a Machine Learning Compute szolgáltatás. További információkért lásd: a [szükséges portok](#mlcports) szakaszban.

- Ellenőrizze, hogy a biztonsági házirendek vagy a virtuális hálózat előfizetésekhez vagy erőforráscsoportokhoz a zárolását korlátozza a virtuális hálózat engedélyeit.

- Ha egy virtuális hálózaton belüli több Machine Learning COMPUTE számítási fürtöt helyezi, szükség lehet egy vagy több erőforrást kvóta növelésére.

    Machine Learning Compute automatikusan foglalja le a további hálózati erőforrások az erőforráscsoportban, amely tartalmazza a virtuális hálózat. Minden egyes Machine Learning COMPUTE számítási fürt az Azure Machine Learning szolgáltatás a következő erőforrásokat foglal le: 

    - Egy hálózati biztonsági csoport (NSG)

    - One public IP address

    - Egy terheléselosztó

    Ezekre az erőforrásokra az előfizetésben meghatározott [erőforráskvóták](https://docs.microsoft.com/azure/azure-subscription-service-limits) vonatkoznak. 

### <a id="mlcports"></a> Szükséges portok

Machine Learning Compute jelenleg használja a megadott virtuális hálózatban lévő virtuális gépek kiépítése az Azure Batch szolgáltatást. Az alhálózatnak engedélyeznie kell a Batch szolgáltatás a bejövő kommunikációhoz. Ehhez a kommunikációhoz használatos ütemezni futtatja, a Machine Learning COMPUTE számítási csomópontokon, és kommunikál az Azure Storage és egyéb erőforrásokat. A Batch NSG-k hozzáadása a virtuális gépekhez csatolt hálózati adapterek (NIC) szintjén. Ezek az NSG-k automatikusan konfigurálnak bejövő és kimenő szabályokat a következő forgalom engedélyezéséhez:

- A Batch szolgáltatási szerepkör IP-címeiről érkező bejövő TCP-forgalom a 29876-os és a 29877-es portokon keresztül. 
 
- A bejövő TCP-forgalom engedélyezéséhez a távelérés 22-es port.
 
- Kimenő forgalom bármilyen porton keresztül a virtuális hálózathoz.

- Kimenő forgalom bármilyen porton keresztül az internetre.

Körültekintéssel, ha módosítja vagy bejövő/kimenő szabályok hozzáadása a Batch-konfigurált NSG-ket. Ha egy NSG blokkolja a számítási csomópontoknak kommunikációs, majd a Machine Learning COMPUTE számítási szolgáltatások állítja a számítási csomópontok állapotát használhatatlan lesz.

Nem kell megadnia NSG-t az alhálózat szintjén, mert a Batch konfigurálja a saját NSG-it. Azonban ha a megadott alhálózati NSG-k és/vagy tűzfal vannak társítva, konfigurálja a bejövő és kimenő biztonsági szabályok a korábban leírtaknak. A következő képernyőfelvételek bemutatják, hogyan néz ki a szabály konfigurálását az Azure Portalon:

![A Machine Learning Compute szabályok NSG bejövő képernyőképe](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Képernyőkép a kimenő NSG-t a Machine Learning Compute szabályok](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="create-machine-learning-compute-in-a-virtual-network"></a>Hozzon létre a Machine Learning Compute a virtuális hálózaton

A Machine Learning COMPUTE számítási fürt létrehozása a **az Azure portal**, kövesse az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com), válassza ki az Azure Machine Learning szolgáltatás munkaterületet.

1. Az a __alkalmazás__ szakaszban jelölje be __számítási__. Válassza ki __számítás hozzáadása__. 

    ![Az Azure Machine Learning szolgáltatás egy számítási hozzáadása](./media/how-to-enable-virtual-network/add-compute.png)

1. A számítási erőforrásokat egy virtuális hálózatot használja, használja az alábbi beállításokat konfigurálhatja:

    - __Hálózati konfiguráció__: Válassza az __Advanced__ (Speciális) lehetőséget.

    - __Erőforráscsoport__: Válassza ki az erőforráscsoportot, amely tartalmazza a virtuális hálózat.

    - __Virtuális hálózat__: Válassza ki a virtuális hálózat, amely tartalmazza az alhálózat.

    - __Alhálózat__: Válassza ki azt az alhálózatot, használja.

    ![A machine learning compute a virtuális hálózati beállításait ábrázoló képernyőfelvétel](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

A Machine Learning COMPUTE számítási fürt használatával is létrehozhat a **Azure Machine Learning SDK**. Az alábbi kód létrehoz egy új Machine Learning COMPUTE számítási fürtöt az a `default` nevű virtuális hálózat alhálózati `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure Virtual Network name, subnet, and resource group
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

A létrehozási folyamat befejezése után a modellt, a fürt segítségével betaníthatja. További információkért lásd: a [válassza ki, és a egy számítási célnak használata képzéshez](how-to-set-up-training-targets.md) dokumentumot.

## <a name="use-a-virtual-machine-or-hdinsight"></a>A virtuális gép vagy HDInsight

Virtuális gép vagy HDInsight-fürt használata a munkaterület egy virtuális hálózaton, használja az alábbi lépéseket:

> [!IMPORTANT]
> Az Azure Machine Learning szolgáltatás csak az Ubuntu rendszert futtató virtuális gépeket támogatja.

1. Hozzon létre egy virtuális gép vagy HDInsight-fürt Azure Portallal, Azure CLI-stb., és egy Azure virtuális hálózaton. További információkért tekintse meg a következő dokumentumokat:
    * [Hozzon létre, és a Linux rendszerű virtuális gépek Azure-beli virtuális hálózatok kezelése](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Azure-beli virtuális hálózatok használatával HDInsight kiterjesztése](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network) 

1. Ahhoz, hogy a virtuális gép vagy a fürt SSH-port kommunikálni az Azure Machine Learning szolgáltatáshoz, konfigurálnia kell az adatforrás-bejegyzés az NSG. Az SSH-port általában a 22-es portot. Ebből a forrásból érkező forgalom engedélyezéséhez használja a következő információkat:

    * __forrás__: Válassza a __Service Tag__ lehetőséget.

    * __Forrás szolgáltatáscímkéje__: Select __AzureMachineLearning__

    * __Porttartományok forrás__: Válassza ki __*__

    * __Cél__: Válassza ki __bármely__

    * __Célporttartományok__: Válassza ki __22-es__

    * __Protokoll__: Válassza ki __bármely__

    * __A művelet__: Válassza ki __engedélyezése__

   ![Képernyőkép a virtuális hálózatban lévő virtuális gép vagy HDInsight Kísérletezési adatelemzésre bejövő szabályok](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Tartsa meg az alapértelmezett kimenő szabályokat az NSG. További információkért tekintse meg az alapértelmezett biztonsági szabályok szakaszában a [biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules) dokumentációját.
    
1. Csatlakoztassa a virtuális gép vagy HDInsight-fürt az Azure Machine Learning szolgáltatás munkaterületén. További információkért lásd: a [állítsa be a modell betanítása és számítási célnak](how-to-set-up-training-targets.md) dokumentumot.

## <a name="use-azure-kubernetes-service-aks"></a>Az Azure Kubernetes Service (AKS) használatához

> [!IMPORTANT]
> Ellenőrizze az előfeltételeket, és megtervezése IP-címkezelés használhat saját fürtjében, mielőtt az alábbiakban leírt lépések folytatása. Olvassa el [speciális konfigurálása az Azure Kubernetes Service (AKS) hálózat](https://docs.microsoft.com/azure/aks/configure-advanced-networking)
> 
> Tartsa meg az alapértelmezett kimenő szabályokat az NSG. További információkért tekintse meg az alapértelmezett biztonsági szabályok szakaszában a [biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules) dokumentációját.
>
> Az Azure Kubernetes Service és az Azure virtuális hálózat ugyanabban a régióban kell lennie.

Azure Kubernetes Service hozzáadásához a virtuális hálózatban a munkaterülethez használja az alábbi lépéseket a a __az Azure portal__:

1. Az a [az Azure portal](https://portal.azure.com), válassza ki az Azure Machine Learning szolgáltatás munkaterületet.

1. Az a __alkalmazás__ szakaszban jelölje be __számítási__. Válassza ki __számítás hozzáadása__. 

    ![Az Azure Machine Learning szolgáltatás egy számítási hozzáadása](./media/how-to-enable-virtual-network/add-compute.png)

1. A számítási erőforrásokat egy virtuális hálózatot használja, használja az alábbi beállításokat konfigurálhatja:

    - __Hálózati konfiguráció__: Válassza az __Advanced__ (Speciális) lehetőséget.

    - __Erőforráscsoport__: Válassza ki az erőforráscsoportot, amely tartalmazza a virtuális hálózat.

    - __Virtuális hálózat__: Válassza ki a virtuális hálózat, amely tartalmazza az alhálózat.

    - __Alhálózat__: Válassza ki az alhálózatot.

    - __Kubernetes-szolgáltatást címtartomány__: Válassza ki a Kubernetes-szolgáltatás-címtartományt. Ez a címtartomány CIDR jelölésrendszerben IP-címtartomány használja a fürtcsomópont számára elérhető IP-címek definiálásához. Az összes alhálózat IP-címtartományok nem lehetnek átfedésben. Példa: 10.0.0.0/16.

    - __Kubernetes DNS szolgáltatás IP-cím__: Válassza ki a Kubernetes DNS szolgáltatás IP-címe. Az IP-cím van rendelve a Kubernetes DNS-szolgáltatás. A Kubernetes-szolgáltatást címtartományán belül kell lennie. Példa: 10.0.0.10.

    - __Docker híd cím__: Válassza ki a Docker híd címet. Az IP-címet a Docker híd van hozzárendelve. Nem lehet minden olyan alhálózat IP-címtartományok vagy a Kubernetes-szolgáltatás-címtartományt. Példa: 172.17.0.1/16

   ![Azure Machine Learning service: A Machine Learning Compute a virtuális hálózati beállítások](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

    > [!TIP]
    > Ha már rendelkezik egy AKS-fürt virtuális hálózaton, a munkaterületet is csatlakoztatható. További információkért lásd: [hogyan helyezhet üzembe az aks](how-to-deploy-to-aks.md).

Is használhatja a **Azure Machine Learning SDK** Azure-beli Kubernetes-szolgáltatás hozzáadása egy virtuális hálózaton. Az alábbi kód létrehoz egy új Azure Kubernetes Service a a `default` nevű virtuális hálózat alhálózati `mynetwork`:

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

A létrehozási folyamat befejezése után az AKS-fürt virtuális hálózat mögött következtetési teheti meg. További információkért lásd: [hogyan helyezhet üzembe az aks](how-to-deploy-to-aks.md).

## <a name="next-steps"></a>További lépések

* [Képzési környezetek beállítása](how-to-set-up-training-targets.md)
* [Az üzembe helyezés modellek](how-to-deploy-and-where.md)
* [Biztonságos helyezhet üzembe modelleket SSL-lel](how-to-secure-web-service.md)


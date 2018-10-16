---
title: Az Azure Linux rendszerű virtuális gép mérete – HPC |} A Microsoft Docs
description: A nagy teljesítményű Linux rendszerű virtuális gépek az Azure-ban elérhető különböző méretű sorolja fel. Tartalmazza a vcpu-k, az adatlemezeket és a hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati sávszélesség-sorozat méretei száma.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 70dca655d5300fcd34b4198093e136f6a971963b
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344489"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Nagy teljesítményű számítási virtuálisgép-méretek

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Csak az Intel MPI 5.x verziók támogatottak. Újabb verzió (2017-es, 2018.) az Intel MPI futásidejű kódtár nem kompatibilisek az Azure Linux RDMA-illesztőprogramokat.


### <a name="distributions"></a>Felosztások
 
Számításigényes virtuális gép üzembe helyezése az Azure Marketplace-en, amely támogatja az RDMA-kapcsolattal a lemezképek egyikéből:
  
* **Ubuntu** – Ubuntu Server 16.04 LTS. RDMA-illesztőprogramok a virtuális gép konfigurálása és regisztrálása az Intel MPI letöltéséhez Intel:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server** -SLES 12 HPC, SLES 12 SP3 HPC (Premium), SLES 12 SP3 SP1 HPC, SLES 12 SP1 HPC (prémium szintű). RDMA-illesztőprogramok lesznek telepítve, és az Intel MPI csomagok oszlanak meg a virtuális gépen. MPI telepítse a következő parancs futtatásával:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **CentOS-alapú HPC** -6.5-ös HPC CentOS-alapú vagy újabb verzióját (a H-sorozatú, 7.1-es vagy újabb verzió ajánlott). RDMA-illesztőprogramok és az Intel MPI 5.1 telepítve a virtuális gépen.  
 
  > [!NOTE]
  > A CentOS-alapú HPC-képekhez kernelfrissítés le vannak tiltva a **yum** konfigurációs fájlt. Ez azért, mert az RPM csomag oszlanak meg a Linux RDMA-illesztőprogramok és illesztőprogram-frissítések előfordulhat, hogy nem működik, ha a kernel frissül.
  > 
 
### <a name="cluster-configuration-options"></a>Fürt konfigurációs lehetőségek

Az Azure Linuxos HPC virtuális gépeken, amely az RDMA hálózati használatával kommunikálhatnak fürtöket számos lehetőséget kínál a többek között: 

* **Virtuális gépek** – az RDMA-kompatibilis HPC virtuális gépek ugyanazon rendelkezésre állási csoportjának (Ha használja az Azure Resource Manager üzemi modell) üzembe helyezése. Ha a klasszikus üzemi modellt használja, az ugyanazon a felhőszolgáltatáson virtuális gépek üzembe helyezése. 

* **A Virtual machine scale sets** – a Virtuálisgép-méretezési csoport állítsa be, győződjön meg arról, hogy korlátozza az üzembe helyezés az elhelyezési csoportból. Például egy Resource Manager-sablon beállítása az `singlePlacementGroup` tulajdonságot `true`. 

* **Az Azure CycleCloud** – a HPC-fürt létrehozása [Azure CycleCloud](/azure/cyclecloud/) MPI-feladatok futtatása a Linux-csomópontokat.

* **Az Azure Batch** – hozzon létre egy [Azure Batch](/azure/batch/) MPI számítási feladatok futtatása Linux-készlet számítási csomópontjain. További információkért lásd: [használata RDMA-kompatibilis vagy a GPU-kompatibilis példányok a Batch-készletek](../../batch/batch-pool-compute-intensive-sizes.md). További tájékoztatás a [Batch hajógyárnak](https://github.com/Azure/batch-shipyard) tárolóalapú számítási feladatok futtatásához a Batch-projektben.

* **A Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) számos Linux-disztribúció futtatását számítási csomópontokra RDMA-kompatibilis Azure-beli virtuális gépeken, támogatja a Windows Server fő csomópontot kezeli. Telepítését bemutató példát lásd: [HPC Pack Linux RDMA-fürt létrehozása az Azure-ban](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

Fürtkezelő válaszaitól függően szükség lehet további rendszerkonfiguráció MPI-feladatok futtatásához. Például a virtuális gépek fürtjeit, szükség lehet a fürt csomópontjai közötti bizalmi kapcsolat létrehozásához, az SSH-kulcsok létrehozásakor, vagy beállításának SSH megbízhatósági kapcsolat létrehozása.

### <a name="network-topology-considerations"></a>Hálózati topológia szempontjai
* Az RDMA-kompatibilis Linuxos virtuális gépek az Azure-ban az Eth1 RDMA hálózati forgalom számára van fenntartva. Ne módosítsa bármely Eth1 beállításai vagy a konfigurációs fájl hivatkozó ezen a hálózaton található bármely információ. Eth0 rendszeres Azure hálózati forgalom számára van fenntartva.

* Az Azure-ban az RDMA hálózati fenntartja a cím terület 172.16.0.0/16. 




## <a name="other-sizes"></a>További méretek
- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Előző generációs szoftvereknél jobban](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [Azure számítási egységek (ACU)](acu.md) Azure-termékváltozatok hasonlítsa össze a számítási teljesítményt nyújt segítséget.





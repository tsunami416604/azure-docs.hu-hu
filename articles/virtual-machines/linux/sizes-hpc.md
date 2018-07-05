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
ms.date: 03/15/2018
ms.author: jonbeck
ms.openlocfilehash: 441e99b86e9560d47af8ea18a2633e3f37a05e94
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2018
ms.locfileid: "34653841"
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
 
### <a name="cluster-configuration"></a>Fürtkonfiguráció 
    
MPI-feladatok futtatása a fürtözött virtuális gépeken további system konfigurációs van szükség. Például a virtuális gépek fürtjeit, szüksége a számítási csomópontok közötti bizalmi kapcsolat létrehozásához. Tipikus beállítások megtekintéséhez [Linux RDMA-fürt beállítása MPI-alkalmazások futtatására](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

### <a name="network-topology-considerations"></a>Hálózati topológia szempontjai
* Az RDMA-kompatibilis Linuxos virtuális gépek az Azure-ban az Eth1 RDMA hálózati forgalom számára van fenntartva. Ne módosítsa bármely Eth1 beállításai vagy a konfigurációs fájl hivatkozó ezen a hálózaton található bármely információ. Eth0 rendszeres Azure hálózati forgalom számára van fenntartva.

* Az Azure-ban az RDMA hálózati fenntartja a cím terület 172.16.0.0/16. 


## <a name="using-hpc-pack"></a>HPC Pack használatával
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), a Microsoft ingyenes HPC-fürt és a feladat felügyeleti megoldás, az egyik lehetőség, hogy a nagy számítási igényű példányok használata Linuxszal. HPC Pack már támogatja a legújabb verzióiban a számos Linux-disztribúció futtatását számítási csomópontokra, az Azure virtuális gépeken, egy Windows Server átjárócsomópont kezeli. Az Intel MPI-t futtató az RDMA-kompatibilis Linuxos számítási csomópontok a HPC Pack is ütemezése és futtatása Linux MPI alkalmazást, hogy az RDMA-hálózatot. Lásd: [Linuxos számítási csomópontok HPC Pack-fürtökben, az Azure-ban – első lépések](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>További méretek
- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Előző generációs szoftvereknél jobban](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

- Első lépések, telepítését és használatát a számításigényes méretek és a Linux RDMA-t, tekintse meg [Linux RDMA-fürt beállítása MPI-alkalmazások futtatására](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

- Tudjon meg többet [Azure számítási egységek (ACU)](acu.md) Azure-termékváltozatok hasonlítsa össze a számítási teljesítményt nyújt segítséget.





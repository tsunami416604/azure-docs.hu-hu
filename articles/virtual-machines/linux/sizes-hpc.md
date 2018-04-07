---
title: Azure Linux virtuális gép méretének - HPC |} Microsoft Docs
description: Felsorolja a különböző méretű Linux nagy teljesítményű számítástechnikai virtuális gépek Azure-ban érhető el. Vcpu, adatlemezek és hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati sávszélesség az a sorozat-méretek számát tartalmazza.
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
ms.openlocfilehash: a24cb03cd30b212650a36cd5ac40977de5eea11e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Nagy teljesítményű számítási virtuálisgép-méretek

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Csak Intel MPI 5.x verziókat támogatja. Újabb verzió (2017, 2018), az Intel MPI futásidejű kódtár nem kompatibilisek az Azure Linux RDMA-illesztőprogramokat.


### <a name="distributions"></a>Felosztások
 
A számítási igényű a virtuális gép telepíthető az Azure piactéren, amely támogatja az RDMA-kapcsolatot a képek egyikét:
  
* **Ubuntu** -Ubuntu Server 16.04 LTS. RDMA-illesztőprogramok konfigurálja a virtuális Gépre, és regisztrálhatja az Intel Intel MPI letöltése:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server** -SLES 12 a HPC, SLES 12 SP3 SP3 a SLES 12 (prémium) HPC HPC, SLES 12 SP1 SP1 HPC (prémium). RDMA illesztőprogramjait, és Intel MPI-csomagok terjesztése a virtuális Gépen. MPI telepítse a következő parancs futtatásával:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **CentOS-alapú HPC** -6.5 HPC CentOS-alapú vagy újabb verzió (H-adatsorozathoz, 7.1-es vagy újabb verzió javasolt). RDMA-illesztőprogramok és Intel MPI 5.1 telepítve vannak a virtuális Gépet.  
 
  > [!NOTE]
  > A CentOS-alapú HPC-lemezképek, a kernel frissítések le vannak tiltva a **yum** konfigurációs fájlt. Ennek az az oka egy RPM-csomag terjesztése a Linux RDMA-illesztőprogramok és illesztőprogram-frissítést nem lehet, hogy működik, ha a kernel frissül.
  > 
 
### <a name="cluster-configuration"></a>Fürtkonfiguráció 
    
További rendszerkonfiguráció MPI-feladatok futtatása a fürtözött virtuális gépeken van szükség. Például egy fürtön található virtuális gépek szeretné a számítási csomópontok közötti megbízhatósági kapcsolat létrehozása. Tipikus beállítások, lásd: [MPI alkalmazások futtatásához hozzon létre egy Linux RDMA fürt](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

### <a name="network-topology-considerations"></a>Hálózati topológia szempontjai
* Az RDMA-kompatibilis Linux virtuális gépeken futó Azure-ban Eth1 RDMA hálózati forgalom számára van fenntartva. Ne módosítsa a Eth1 beállításokat vagy a konfigurációs fájl hivatkozó ezen a hálózaton található bármely információ. Eth0 rendszeres Azure hálózati forgalom számára van fenntartva.

* Az Azure-ban az RDMA hálózati fenntartja a cím terület 172.16.0.0/16. 


## <a name="using-hpc-pack"></a>HPC Pack használatával
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), a Microsoft szabad HPC fürt és a feladat felügyeleti megoldás, egy a számítási igényű példányok használata a Linux és a lehetőség. HPC Pack támogatási legújabb kiadásaiban futtathatnak több Linux terjesztésekről számítási csomópontok telepítve az Azure virtuális gépeken, a Windows Server átjárócsomópont kezeli. Az RDMA-kompatibilis Linux számítási csomópontok Intel MPI fut, a HPC Pack is ütemezése és futtatása Linux MPI az RDMA hálózati elérhető alkalmazásokat. Lásd: [Ismerkedés az Azure-ban HPC Pack-fürtben lévő Linux számítási csomópontok](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Más méretek
- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)


## <a name="next-steps"></a>További lépések

- Első lépésként telepítésére és használatára számítási igényű méretek és rdma-t, Linux, lásd: [MPI alkalmazások futtatásához hozzon létre egy Linux RDMA fürt](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

- További tudnivalók [Azure számítási egység (ACU)](acu.md) segíthetnek a számítási teljesítmény összehasonlítása Azure termékváltozatok mentén.





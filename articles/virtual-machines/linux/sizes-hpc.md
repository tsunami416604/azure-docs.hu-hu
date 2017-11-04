---
title: "Azure Linux virtuális gép méretének - HPC |} Microsoft Docs"
description: "Felsorolja a különböző méretű Linux nagy teljesítményű számítástechnikai virtuális gépek Azure-ban érhető el."
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/10/2017
ms.author: jonbeck
ms.openlocfilehash: fecc0656264f1c1d44aa8ad3aea321aa8cc4a0c8
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="high-performance-compute-linux-vm-sizes"></a>Nagy teljesítményű számítási Linux Virtuálisgép-méretek

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="rdma-capable-instances"></a>Az RDMA-kompatibilis példányok
A számítási igényű példányok (H16r, H16mr, A8 és A9) egy részét a beállítást, a távoli közvetlen memória-hozzáférés (RDMA) hálózati kapcsolatot a hálózati adaptert. Ez az interfész mellett más Virtuálisgép-méretek elérhető szabványos Azure hálózati kapcsolat van. 
  
Ez az interfész lehetővé teszi az RDMA-kompatibilis példányok egy InfiniBand hálózati FDR sebességét H16r és H16mr virtuális gépek és a gyorsító A8 és A9 virtuális gépek QDR sebességet működő protokollt használó kommunikációra. E RDMA-képességeinek képes javítani, a méretezhetőség és teljesítmény Intel MPI alatt futó Message Passing Interface (MPI) alkalmazások 5.x-es vagy újabb verziója.

Telepítse az azonos rendelkezésre állási csoport (Ha használja az Azure Resource Manager telepítési modell) vagy felhő ugyanezt a RDMA-kompatibilisek-e virtuális gépek (Ha használja a klasszikus üzembe helyezési modellel). Hajtsa végre az RDMA-kompatibilis Linux virtuális gépet az Azure RDMA hálózati elérésére vonatkozó további követelményeket.

### <a name="distributions"></a>Azokat a terjesztéseket
 
A számítási igényű a virtuális gép telepíthető az Azure piactéren, amely támogatja az RDMA-kapcsolatot a képek egyikét:
  
* **Ubuntu** -Ubuntu Server 16.04 LTS. RDMA-illesztőprogramok konfigurálja a virtuális Gépre, és regisztrálhatja az Intel Intel MPI letöltése:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server** -SLES 12 a HPC, SLES 12 SP3 SP3 a SLES 12 (prémium) HPC HPC, SLES 12 SP1 SP1 HPC (prémium). RDMA illesztőprogramjait, és Intel MPI-csomagok terjesztése a virtuális Gépen. MPI telepítse a következő parancs futtatásával:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **CentOS-alapú HPC** -7.3 HPC CentOS-alapú, 7.1-es HPC CentOS-alapú, 6,8 HPC CentOS-alapú vagy 6.5 HPC CentOS-alapú (H-adatsorozathoz, 7.1-es vagy újabb verzió javasolt). RDMA-illesztőprogramok és Intel MPI 5.1 telepítve vannak a virtuális Gépet.  
 
  > [!NOTE]
  > A CentOS-alapú HPC-lemezképek, a kernel frissítések le vannak tiltva a **yum** konfigurációs fájlt. Ennek az az oka egy RPM-csomag terjesztése a Linux RDMA-illesztőprogramok és illesztőprogram-frissítést nem lehet, hogy működik, ha a kernel frissül.
  > 
 
### <a name="cluster-configuration"></a>Fürtkonfiguráció 
    
További rendszerkonfiguráció MPI-feladatok futtatása a fürtözött virtuális gépeken van szükség. Például egy fürtön található virtuális gépek szeretné a számítási csomópontok közötti megbízhatósági kapcsolat létrehozása. Tipikus beállítások, lásd: [MPI alkalmazások futtatásához hozzon létre egy Linux RDMA fürt](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

### <a name="network-topology-considerations"></a>Hálózati topológia szempontjai
* Az RDMA-kompatibilis Linux virtuális gépeken futó Azure-ban Eth1 RDMA hálózati forgalom számára van fenntartva. Ne módosítsa a Eth1 beállításokat vagy a konfigurációs fájl hivatkozó ezen a hálózaton található bármely információ. Eth0 rendszeres Azure hálózati forgalom számára van fenntartva.

* Az Azure IP keresztül InfiniBand (IB) nem támogatott. Csak IB feletti RDMA esetén támogatott.

## <a name="using-hpc-pack"></a>HPC Pack használatával
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), a Microsoft szabad HPC fürt és a feladat felügyeleti megoldás, egy a számítási igényű példányok használata a Linux és a lehetőség. HPC Pack támogatási legújabb kiadásaiban futtathatnak több Linux terjesztésekről számítási csomópontok telepítve az Azure virtuális gépeken, a Windows Server átjárócsomópont kezeli. Az RDMA-kompatibilis Linux számítási csomópontok Intel MPI fut, a HPC Pack is ütemezése és futtatása Linux MPI az RDMA hálózati elérhető alkalmazásokat. Lásd: [Ismerkedés az Azure-ban HPC Pack-fürtben lévő Linux számítási csomópontok](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Más méretek
- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)


## <a name="next-steps"></a>Következő lépések

- Első lépésként telepítésére és használatára számítási igényű méretek és rdma-t, Linux, lásd: [MPI alkalmazások futtatásához hozzon létre egy Linux RDMA fürt](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

- További tudnivalók [Azure számítási egység (ACU)](acu.md) segíthetnek a számítási teljesítmény összehasonlítása Azure termékváltozatok mentén.





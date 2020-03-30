---
title: H sorozat – Azure virtuális gépek
description: A H sorozatú virtuális gépek specifikációi.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 03/10/2020
ms.author: lahugh
ms.openlocfilehash: cb05a8c42c5710182f81d4bc1c76b78abe59c882
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79470503"
---
# <a name="h-series"></a>H-sorozat

A H sorozatú virtuális gépek a magas CPU-frekvenciák vagy az alapvető követelményekszerint nagy memória által vezérelt alkalmazásokhoz vannak optimalizálva. A H sorozatú virtuális gépek 8 vagy 16 Intel Xeon E5 2667 v3 processzormagot tartalmaznak, cpumagonként akár 14 GB RAM-ot is tartalmaznak, és nincs hyperthreading. A H sorozat 56 Gb/sec Mellanox FDR InfiniBand-et tartalmaz, amely nem blokkolja a zsírfa konfigurációt az egyenletes RDMA teljesítmény érdekében. A H sorozatú virtuális gépek támogatják az Intel MPI 5.x és MS-MPI-t.

ACU: 290–300

Prémium szintű tárhely: Nem támogatott

Prémium szintű tárolási gyorsítótárazás: Nem támogatott

Élő áttelepítés: Nem támogatott

Memóriamegőrzési frissítések: Nem támogatott

| Méret | vCPU | Processzor | Memória (GB) | Memória sávszélessége GB/s | Alap CPU-frekvencia (GHz) | All-cores frekvencia (GHz, csúcs) | Egymagos frekvencia (GHz, csúcs) | RDMA teljesítmény (Gb/s) | MPI-támogatás | Ideiglenes tárterület (GB) | Adatlemezek max. száma | Maximális Ethernet hálózati adapterek |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1.</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3,2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1.</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3,2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> MpI alkalmazások esetén a dedikált RDMA háttérhálózatot az FDR InfiniBand hálózat engedélyezi.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>Támogatott operációsrendszer-lemezképek (Linux)
 
Az Azure Piactér számos Linux-disztribúcióval rendelkezik, amelyek támogatják az RDMA-kapcsolatot:
  
* **CentOS-alapú HPC** - A nem SR-IOV-kompatibilis virtuális gépek, CentOS-alapú változat 6.5 HPC vagy újabb verzió, akár 7,5 alkalmasak. A H sorozatú virtuális gépek esetében a 7.1–7.5 verziók ajánlottak. RdMA-illesztőprogramok és Intel MPI 5.1 telepítve vannak a virtuális gépre.
  Az SR-IOV virtuális gépek hez a CentOS-HPC 7.6 optimalizált és előre telepített az RDMA illesztőprogramokkal és a telepített különböző MPI-csomagokkal.
  Más RHEL/CentOS virtuálisgép-lemezképek esetén adja hozzá az InfiniBand Linux bővítményt az InfiniBand engedélyezéséhez. Ez a Linux virtuális gép bővítmény telepíti a Mellanox OFED illesztőprogramokat (SR-IOV virtuális gépeken) az RDMA-kapcsolathoz. A következő PowerShell-parancsmag az InfiniBandDriverLinux bővítmény legújabb verzióját (1.0-s verzióját) telepíti egy meglévő RDMA-kompatibilis virtuális gépre. Az RDMA-kompatibilis virtuális gép neve *myVM,* és az USA nyugati *régiójában* a *myResourceGroup* nevű erőforráscsoportban van telepítve az alábbiak szerint:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  Másik lehetőségként a virtuálisgép-bővítmények is szerepelhetnek az Azure Resource Manager-sablonokban a következő JSON-elemmel való egyszerű üzembe helyezés érdekében:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  A következő parancs a legújabb 1.0-s verziójú InfiniBandDriverLinux kiterjesztést telepíti az összes RDMA-kompatibilis virtuális gépre egy *myVMSS* nevű, a *myResourceGroup*nevű erőforráscsoportban telepített meglévő virtuálisgép-méretezési csoportban:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > A CentOS-alapú HPC-lemezképeken a kernelfrissítések le vannak tiltva a **yum** konfigurációs fájlban. Ennek az az oka, hogy a Linux RDMA-illesztőprogramok RPM-csomagként kerülnek terjesztésre, és előfordulhat, hogy az illesztőprogram-frissítések nem működnek, ha a rendszermag frissül.
  >
  

* **SUSE Linux Enterprise Server** - SLES 12 SP3 HPC-hez, SLES 12 SP3 HPC-hez (Premium), SLES 12 SP1 HPC-hez, SLES 12 SP1 HPC-hez (Premium), SLES 12 SP4 és SLES 15. RdMA-illesztőprogramok vannak telepítve, és az Intel MPI csomagok a virtuális gépen vannak elosztva. Telepítse az MPI-t a következő parancs futtatásával:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** - Ubuntu Server 16.04 LTS, 18.04 LTS. Konfigurálja az RDMA-illesztőprogramokat a virtuális gépen, és regisztráljon az Intel MPI letöltéséhez:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Az InfiniBand engedélyezéséről, az MPI beállításáról az [InfiniBand engedélyezése témakörben talál](./workloads/hpc/enable-infiniband.md)további részleteket.

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

További információ arról, hogy [az Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek a számítási teljesítmény összehasonlításában az Azure-sKU-k között.

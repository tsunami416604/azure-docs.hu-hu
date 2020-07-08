---
title: H-sorozat – Azure Virtual Machines
description: A H-sorozatú virtuális gépek specifikációi.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 03/10/2020
ms.author: jushiman
ms.openlocfilehash: f79dcb8886985d60a1ed82e1a77d231cf7d3ad24
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84678681"
---
# <a name="h-series"></a>H-sorozat

A H-sorozatú virtuális gépeket nagy CPU-gyakorisággal vagy nagy memóriával rendelkező, alapvető követelményekkel rendelkező alkalmazások számára optimalizáltuk. A H-sorozatú virtuális gépek 8 vagy 16 Intel Xeon E5 2667 v3 processzorral rendelkeznek, CPU Core-onként akár 14 GB RAM-mal, és nem feleznie. A H-sorozat 56 GB/s Mellanox FDR InfiniBand a konzisztens RDMA teljesítményének nem blokkoló FAT-konfigurációjában. A H-sorozatú virtuális gépek támogatják az Intel MPI 5. x és az MS-MPI használatát.

ACU: 290–300

Premium Storage: nem támogatott

Premium Storage gyorsítótárazás: nem támogatott

Élő áttelepítés: nem támogatott

Memória-megőrzési frissítések: nem támogatott

| Méret | vCPU | Processzor | Memória (GB) | Memória sávszélessége GB/s | Alapszintű CPU-gyakoriság (GHz) | Teljes magok gyakorisága (GHz, csúcs) | Egymagos gyakoriság (GHz, csúcs) | RDMA teljesítmény (GB/s) | MPI-támogatás | Ideiglenes tárterület (GB) | Adatlemezek max. száma | Lemezek max. teljesítménye: IOPS | Ethernet hálózati adapterek maximális száma |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32x500 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64x500 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32x500 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64x500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3,6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64x500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3,6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64x500 | 4 |

<sup>1</sup> az MPI-alkalmazások esetében a dedikált RDMA háttérrendszer-hálózatot a FDR InfiniBand hálózata engedélyezte.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>Támogatott operációsrendszer-lemezképek (Linux)
 
Az Azure Marketplace számos Linux-disztribúcióval rendelkezik, amelyek támogatják a RDMA-kapcsolatot:
  
* **CentOS-alapú HPC** – nem SR-IOV-kompatibilis virtuális gépek, CentOS-alapú 6,5 HPC vagy újabb verzió, amely legfeljebb 7,5-ig használható. A H-sorozatú virtuális gépek esetében a 7,1 – 7,5 verzió ajánlott. A RDMA-illesztőprogramok és az Intel MPI 5,1 telepítve vannak a virtuális gépen.
  Az SR-IOV virtuális gépek esetében a CentOS-HPC 7,6 optimalizált és előre be van töltve a RDMA-illesztőprogramokkal és a különböző MPI-csomagokkal.
  Más RHEL/CentOS virtuálisgép-lemezképek esetén adja hozzá a InfiniBandLinux-bővítményt a InfiniBand engedélyezéséhez. Ez a linuxos virtuálisgép-bővítmény telepíti a Mellanox OFED-illesztőprogramokat (SR-IOV virtuális gépeken) a RDMA-kapcsolathoz. A következő PowerShell-parancsmag telepíti a InfiniBandDriverLinux-bővítmény legújabb verzióját (1,0-es verzió) egy meglévő RDMA-kompatibilis virtuális gépen. A RDMA-kompatibilis virtuális gép neve *myVM* , és az *USA nyugati* régiójában, a *myResourceGroup* nevű erőforráscsoporthoz van telepítve, a következőképpen:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  Alternatív megoldásként a virtuálisgép-bővítmények Azure Resource Manager sablonokban is elérhetők, így egyszerűen üzembe helyezhetők a következő JSON-elemmel:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  A következő parancs telepíti a legújabb 1,0 InfiniBandDriverLinux-bővítményt az összes RDMA-kompatibilis virtuális gépen egy *myVMSS* nevű meglévő virtuálisgép-méretezési csoportba, amelyet a *myResourceGroup*nevű erőforráscsoport telepít:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > A CentOS-alapú HPC-lemezképeken a kernel frissítései le vannak tiltva a **yum** konfigurációs fájlban. Ennek az az oka, hogy a Linux RDMA-illesztőprogramok RPM-csomagként vannak elosztva, és előfordulhat, hogy az illesztőprogram frissítései nem működnek, ha a kernel frissül.
  >
  

* **SUSE Linux Enterprise Server** -SLES 12 SP3 HPC, SLES 12 SP3 for HPC (prémium), SLES 12 SP1 for HPC, SLES 12 SP1 for HPC (prémium), SLES 12 SP4 és SLES 15. A RDMA-illesztőprogramok telepítve vannak, és az Intel MPI-csomagok el vannak osztva a virtuális gépen. Telepítse az MPI-t a következő parancs futtatásával:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** -ubuntu Server 16,04 lts, 18,04 LTS. Konfigurálja a RDMA-illesztőprogramokat a virtuális gépen, és regisztrálja az Intel-t az Intel MPI letöltéséhez:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  A InfiniBand engedélyezéséről, az MPI beállításáról további információt a [InfiniBand engedélyezése](./workloads/hpc/enable-infiniband.md)című témakörben talál.

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.

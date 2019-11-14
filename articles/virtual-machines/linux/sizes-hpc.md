---
title: Azure Linux VM-méretek – HPC
description: Felsorolja az Azure-ban a Linux nagy teljesítményű számítástechnikai virtuális gépekhez elérhető különböző méreteket. A vCPU, adatlemezek és hálózati adapterek számával, valamint az ebben a sorozatban lévő méretek sávszélességével kapcsolatos információkat sorolja fel.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 214ab48b6b0dca37eff3b3f155aaa92afc7fee16
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034894"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Nagy teljesítményű számítási virtuálisgép-méretek

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Az SR-IOV-kompatibilis VM-méretek az Azure-ban szinte bármilyen típusú MPI-t használhatnak.
A nem SR-IOV-kompatibilis virtuális gépeken csak az Intel MPI 5. x verziói támogatottak. Az Intel MPI runtime library újabb verziói (2017, 2018) vagy esetleg nem kompatibilisek az Azure Linux RDMA-illesztőprogramjaival.


### <a name="supported-os-images"></a>Támogatott operációsrendszer-lemezképek
 
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
  
  A következő parancs telepíti a legújabb 1,0 InfiniBandDriverLinux-bővítményt az összes RDMA-kompatibilis virtuális GÉPEN egy *myVMSS* nevű meglévő virtuálisgép-méretezési csoportba, amelyet a *myResourceGroup*nevű erőforráscsoport telepít:
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

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  A InfiniBand engedélyezéséről, az MPI beállításáról további információt a [InfiniBand engedélyezése](../workloads/hpc/enable-infiniband.md)című témakörben talál.


### <a name="cluster-configuration-options"></a>Fürt konfigurációs beállításai

Az Azure számos lehetőséget kínál a RDMA-hálózattal kommunikáló linuxos HPC virtuális gépekből álló fürtök létrehozására, beleértve a következőket: 

* **Virtual Machines** – helyezze üzembe a RDMA-kompatibilis HPC virtuális gépeket ugyanabban a rendelkezésre állási készletben (az Azure Resource Manager üzemi modell használatakor). Ha a klasszikus üzemi modellt használja, telepítse a virtuális gépeket ugyanabban a felhőalapú szolgáltatásban. 

* **Virtuálisgép-méretezési** csoportok – egy virtuálisgép-méretezési csoportban győződjön meg arról, hogy egyetlen elhelyezési csoportra korlátozza a központi telepítést. Például egy Resource Manager-sablonban állítsa a `singlePlacementGroup` tulajdonságot `true`re. 

* **MPI a virtuális gépek között** – ha a virtuális gépek (VM-EK) közötti MPI-kommunikáció szükséges, győződjön meg arról, hogy a virtuális gépek ugyanabban a rendelkezésre állási csoportba vagy a virtuális gépre ugyanazok a méretezési csoportba tartoznak.

* **Azure CycleCloud** – HPC-fürt létrehozása az [Azure CYCLECLOUD](/azure/cyclecloud/) -ben MPI-feladatok Linux-csomópontokon való futtatásához.

* **Azure batch** – hozzon létre egy [Azure batch](/azure/batch/) -készletet MPI-számítási feladatok futtatásához Linux rendszerű számítási csomópontokon. További információ: [RDMA-kompatibilis vagy GPU-kompatibilis példányok használata batch-készletekben](../../batch/batch-pool-compute-intensive-sizes.md). Tekintse meg a [Batch-hajógyár](https://github.com/Azure/batch-shipyard) projektjét, amely a tároló-alapú számítási feladatok futtatására használható a Batch szolgáltatásban.

* A **Microsoft HPC pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) számos Linux-disztribúciót támogat a RDMA-kompatibilis Azure-beli virtuális gépeken üzembe helyezett számítási csomópontokon való futtatáshoz, amelyeket egy Windows Server Head csomópont kezel. A központi telepítésre példát a [HPC Pack Linux RDMA-fürt létrehozása az Azure-ban](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam)című témakörben talál.


### <a name="network-considerations"></a>Hálózati megfontolások
* Az Azure-ban a RDMA-kompatibilis Linux rendszerű virtuális gépeken a eth1 a RDMA hálózati forgalom számára van fenntartva, nem SR-IOV. Ne módosítsa a eth1 vonatkozó beállításokat, illetve az erre a hálózatra hivatkozó konfigurációs fájlban lévő adatokat.
* Az SR-IOV-kompatibilis virtuális gépeken (HB és HC-sorozat) a ib0 a RDMA hálózati forgalom számára van fenntartva.
* Az Azure-beli RDMA-hálózat fenntartja a 172.16.0.0/16 címtartomány méretét. Ha az MPI-alkalmazásokat egy Azure-beli virtuális hálózaton üzembe helyezett példányokon szeretné futtatni, győződjön meg arról, hogy a virtuális hálózati címtartomány nem fedi át a RDMA-hálózatot.
* A fürtözési eszköztől függően további rendszerkonfigurációra lehet szükség az MPI-feladatok futtatásához. Előfordulhat például, hogy egy virtuális gépen lévő fürtön létre kell hoznia egy megbízhatósági kapcsolatot a fürtcsomópontok között SSH-kulcsok létrehozásával vagy jelszó nélkül SSH-bejelentkezések létrehozásával.


## <a name="other-sizes"></a>Egyéb méretek
- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>Következő lépések

- További információ a [HPC-munkaterhelések](../workloads/hpc/configure.md) beállításáról, optimalizálásáról és méretezéséről az Azure-ban.
- További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.

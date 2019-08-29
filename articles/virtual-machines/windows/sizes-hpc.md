---
title: Azure Windowsos VM-méretek – HPC | Microsoft Docs
description: Felsorolja az Azure-beli Windows nagy teljesítményű számítástechnikai virtuális gépekhez elérhető különböző méreteket. A vCPU, adatlemezek és hálózati adapterek számával, valamint az ebben a sorozatban lévő méretek sávszélességével kapcsolatos információkat sorolja fel.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: 0c5398caf85566f78c7ebe9e8ba114a53d2f6271
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088528"
---
# <a name="high-performance-compute-vm-sizes"></a>Nagy teljesítményű számítási VM-méretek

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **Operációs rendszer** – Windows Server 2016 a HPC sorozat összes virtuális gépe. A Windows Server 2012 R2, a Windows Server 2012 szintén támogatott a nem SR-IOV-kompatibilis virtuális gépeken (így a HB és a HC kivételével).

* **MPI** – az SR-IOV által engedélyezett virtuálisgép-méretek az Azure-ban (HB, HC) lehetővé teszik, hogy szinte bármilyen típusú MPI-t MELLANOX-OFED lehessen használni.
A nem SR-IOV-kompatibilis virtuális gépeken támogatott MPI-implementációk a Microsoft Network Direct (ND) felületet használják a példányok közötti kommunikációhoz. Ezért csak a Microsoft MPI (MS-MPI) 2012 R2 vagy újabb, illetve az Intel MPI 5. x verziói támogatottak. Az Intel MPI runtime library újabb verziói (2017, 2018) vagy esetleg nem kompatibilisek az Azure RDMA-illesztőprogramokkal.

* **InfiniBandDriverWindows** virtuálisgép-BŐVÍTMÉNY – RDMA-kompatibilis virtuális gépeken a InfiniBandDriverWindows-bővítmény hozzáadásával engedélyezheti a InfiniBand. Ez a Windowsos virtuálisgép-bővítmény telepíti a Windows Network Direct-illesztőprogramokat (nem SR-IOV virtuális gépeken) vagy a Mellanox OFED-illesztőprogramokat (SR-IOV virtuális gépeken) a RDMA-kapcsolathoz.
Az A8-as és A9-es példányok bizonyos telepítései esetében a HpcVmDrivers-bővítmény automatikusan hozzáadódik. Vegye figyelembe, hogy a HpcVmDrivers VM-bővítmény elavult; nem lesz frissítve. A virtuálisgép-bővítmény virtuális géphez való hozzáadásához [Azure PowerShell](/powershell/azure/overview) parancsmagokat használhat. 

  A következő parancs a 1,0-es InfiniBandDriverWindows-bővítményt telepíti egy meglévő RDMA-kompatibilis virtuális gépre, amelyet az *USA nyugati* régiójában, az *myResourceGroup* nevű erőforráscsoport helyez üzembe.

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```
  Alternatív megoldásként a virtuálisgép-bővítmények Azure Resource Manager sablonokban is elérhetők az egyszerű üzembe helyezéshez, a következő JSON-elemmel:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  A következő parancs telepíti a legújabb 1,0 InfiniBandDriverWindows-bővítményt az összes RDMA-kompatibilis virtuális GÉPEN egy *myVMSS* nevű meglévő virtuálisgép-méretezési csoportba, amelyet a *myResourceGroup*nevű erőforráscsoport telepít:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  További információ: [virtuálisgép-bővítmények és-szolgáltatások](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). A [klasszikus](classic/manage-extensions.md)üzemi modellben üzembe helyezett virtuális gépek bővítményei is használhatók.

* **RDMA hálózati címtartomány** – az Azure RDMA-hálózata fenntartja a 172.16.0.0/16 címtartomány méretét. Ha az MPI-alkalmazásokat egy Azure-beli virtuális hálózaton üzembe helyezett példányokon szeretné futtatni, győződjön meg arról, hogy a virtuális hálózati címtartomány nem fedi át a RDMA-hálózatot.


### <a name="cluster-configuration-options"></a>Fürt konfigurációs beállításai

Az Azure számos lehetőséget kínál a RDMA-hálózattal kommunikáló Windows HPC virtuális gépek fürtjének létrehozására, beleértve a következőket: 

* **Virtual Machines** – helyezze üzembe a RDMA-kompatibilis HPC virtuális gépeket ugyanabban a rendelkezésre állási készletben (az Azure Resource Manager üzemi modell használatakor). Ha a klasszikus üzemi modellt használja, telepítse a virtuális gépeket ugyanabban a felhőalapú szolgáltatásban. 

* **Virtuálisgép** -méretezési csoportok – egy virtuálisgép-méretezési csoportban győződjön meg arról, hogy egyetlen elhelyezési csoportra korlátozza a központi telepítést. Például egy Resource Manager-sablonban állítsa be a `singlePlacementGroup` `true`tulajdonságot a következőre:. 

* **MPI a virtuális gépek között** – ha a virtuális gépek (VM-EK) közötti MPI-kommunikáció szükséges, győződjön meg arról, hogy a virtuális gépek ugyanabban a rendelkezésre állási csoportba vagy a virtuális gépre ugyanazok a méretezési csoportba tartoznak.

* **Azure CycleCloud** – HPC-fürt létrehozása az [Azure CYCLECLOUD](/azure/cyclecloud/) -ben MPI-feladatok futtatásához Windows-csomópontokon.

* **Azure batch** – hozzon létre egy [Azure batch](/azure/batch/) -készletet MPI-számítási feladatok futtatásához Windows Server számítási csomópontokon. További információ: [RDMA-kompatibilis vagy GPU-kompatibilis példányok használata batch-készletekben](../../batch/batch-pool-compute-intensive-sizes.md). Tekintse meg a [Batch-hajógyár](https://github.com/Azure/batch-shipyard) projektjét, amely a tároló-alapú számítási feladatok futtatására használható a Batch szolgáltatásban.

* **A Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) olyan futásidejű környezetet tartalmaz az MS-MPI számára, amely az Azure RDMA-hálózatot használja RDMA-kompatibilis Windows rendszerű virtuális gépeken való üzembe helyezéskor. Például a központi telepítések esetében lásd: [Windows RDMA-fürt beállítása HPC-csomaggal MPI-alkalmazások futtatásához](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Egyéb méretek
- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](../virtual-machines-windows-sizes-memory.md)
- [Tárolásra optimalizált](../virtual-machines-windows-sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

- A Windows Serveren futó HPC Packtel rendelkező, nagy számítási igényű példányok használatával kapcsolatos feladatlistákat a [Windows RDMA-fürtök a HPC packtel való beállításával MPI-alkalmazások futtatásához](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)című témakörben tekintheti meg.

- Ha Azure Batch használatával MPI-alkalmazások futtatásához nagy számítási igényű példányokat szeretne használni, tekintse meg a többpéldányos [feladatok használata a Message Passing Interface (MPI) alkalmazások Azure batch-ben](../../batch/batch-mpi.md)való futtatásához című témakört.

- További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.

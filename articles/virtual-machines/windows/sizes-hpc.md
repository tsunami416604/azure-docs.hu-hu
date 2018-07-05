---
title: Az Azure Windows VM-méretek – HPC |} A Microsoft Docs
description: A nagy teljesítményű Windows virtuális gépek az Azure-ban elérhető különböző méretű sorolja fel. Tartalmazza a vcpu-k, az adatlemezeket és a hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati sávszélesség-sorozat méretei száma.
services: virtual-machines-windows
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: jonbeck
ms.openlocfilehash: d8ab1531c4e1fa33fbdba12a4ecbeb8908dd6a94
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2018
ms.locfileid: "34654351"
---
# <a name="high-performance-compute-vm-sizes"></a>Nagy teljesítményű számítási Virtuálisgép-méretek

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **Operációs rendszer** – Windows Server 2016, Windows Server 2012 R2, Windows Server 2012

* **MPI** – Microsoft MPI (MS-MPI) 2012 R2 vagy újabb, illetve az Intel MPI-kódtár 5.x

  Támogatott MPI-megvalósításokban az a Microsoft Network Directet felület példányok közötti kommunikáció során. 

* **RDMA hálózati címteret** – az Azure-ban az RDMA hálózati fenntartja a cím terület 172.16.0.0/16. MPI-alkalmazások egy Azure virtuális hálózaton üzembe helyezett példányokon futnak, győződjön meg róla, hogy a virtuális hálózat címtere nem fedi át az RDMA-hálózatot.

* **HpcVmDrivers Virtuálisgép-bővítmény** – az RDMA-kompatibilis virtuális gépek, a Windows hálózati RDMA-kapcsolattal az eszközillesztők telepítése HpcVmDrivers bővítmény hozzáadása. (A8 és A9 példányokon egyes példányainak a HpcVmDrivers bővítmény egészül ki automatikusan.) A Virtuálisgép-bővítmény hozzáadása egy virtuális Géphez, használható [Azure PowerShell-lel](/powershell/azure/overview) parancsmagok. 

  
  A következő parancsot a legújabb 1.1-es verziójának HpcVMDrivers bővítmény telepítését egy RDMA-kompatibilis nevű meglévő virtuális Géphez a *myVM* nevű erőforráscsoportban üzembe helyezett *myResourceGroup* a a  *USA nyugati RÉGIÓJA* régió:

  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  
  További információkért lásd: [virtuális gépi bővítmények és szolgáltatások](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Telepített virtuális gépek is dolgozhat bővítmények a [klasszikus üzemi modell](classic/manage-extensions.md).


## <a name="using-hpc-pack"></a>HPC Pack használatával

[A Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), a Microsoft ingyenes HPC-fürt és a feladat felügyeleti megoldás, az egyik lehetőség, hogy a számítási fürt létrehozása az Azure-ban Windows-alapú MPI-alkalmazások és az egyéb HPC számítási feladatok futtatásához. HPC Pack 2012 R2 és újabb verziók tartalmazzák a futtatási környezetet az RDMA-kompatibilis virtuális gépek üzembe helyezésekor az Azure RDMA-hálózatot használó MS MPI.



## <a name="other-sizes"></a>További méretek
- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](../virtual-machines-windows-sizes-memory.md)
- [Tárolásra optimalizált](../virtual-machines-windows-sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Előző generációs szoftvereknél jobban](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

- Ellenőrzőlista a nagy számítási igényű példányok használata a HPC Pack a Windows Server, lásd: [a HPC Pack segítségével Windows RDMA-fürt beállítása MPI-alkalmazások futtatására](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

- MPI-alkalmazások futtatása az Azure Batch számításigényes példányok használatához lásd: [többpéldányos tevékenységek használata Message Passing Interface (MPI) alkalmazások futtatásához az Azure Batchben](../../batch/batch-mpi.md).

- Tudjon meg többet [Azure számítási egységek (ACU)](acu.md) Azure-termékváltozatok hasonlítsa össze a számítási teljesítményt nyújt segítséget.





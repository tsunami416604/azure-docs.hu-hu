---
title: Az Azure Windows Virtuálisgép-méretek – HPC |} Microsoft Docs
description: A Windows Azure virtuális gépek számítási magas teljesítmény érhető el a különböző méretű sorolja fel. Vcpu, adatlemezek és hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati sávszélesség az a sorozat-méretek számát tartalmazza.
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
ms.openlocfilehash: e402fd3ac95cac4816b9442f7c08aeaf7c108886
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="high-performance-compute-vm-sizes"></a>Nagy teljesítményű számítási Virtuálisgép-méretek

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **Operációs rendszer** – Windows Server 2016-os, Windows Server 2012 R2, Windows Server 2012-ben

* **MPI** -Microsoft MPI (MS-MPI) 2012 R2 vagy újabb, illetve Intel MPI könyvtár 5.x

  Támogatott MPI-megvalósítások példányok közötti kommunikációra használja a Microsoft hálózati közvetlen felületét. 

* **RDMA hálózati címterüket** – az Azure-ban az RDMA hálózati fenntartja a cím terület 172.16.0.0/16. MPI alkalmazásokat futtasson egy Azure virtuális hálózatra-példányt, győződjön meg arról, hogy a virtuális hálózat címtere nem fedi át a RDMA hálózati.

* **HpcVmDrivers Virtuálisgép-bővítmény** - RDMA-kompatibilis virtuális gépeken, adja hozzá a Windows hálózati RDMA-kapcsolatot az eszközillesztők telepítéséhez HpcVmDrivers kiterjesztést. (Egyes telepítések A8 és A9 példányok, a HpcVmDrivers a kiterjesztés kerül automatikusan.) A Virtuálisgép-bővítmény hozzáadása a virtuális gépek, használhatja a [Azure PowerShell](/powershell/azure/overview) parancsmagok. 

  
  A következő parancsot a legújabb 1.1-es verzió HpcVMDrivers bővítményt telepít egy meglévő RDMA-kompatibilis nevű virtuális gép *myVM* nevű erőforráscsoportban telepített *myResourceGroup* a a  *USA nyugati régiója* régió:

  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  
  További információkért lásd: [virtuálisgép-bővítmények és a szolgáltatások](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). A telepített virtuális gépek is dolgozhat bővítmények a [klasszikus üzembe helyezési modellel](classic/manage-extensions.md).


## <a name="using-hpc-pack"></a>HPC Pack használatával

[A Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), a Microsoft szabad HPC fürt és a feladat felügyeleti megoldás, a beállítás egy a, hogy a számítási fürt létrehozása az Azure-ban MPI Windows-alapú alkalmazások és más HPC munkaterhelések futtatásához. HPC Pack 2012 R2 és újabb verziók például futtatókörnyezetének az MS-MPI, amely RDMA-kompatibilisek-e virtuális gépek az Azure RDMA hálózati telepítésekor használ.



## <a name="other-sizes"></a>Más méretek
- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](../virtual-machines-windows-sizes-memory.md)
- [Tárolásra optimalizált](../virtual-machines-windows-sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)

## <a name="next-steps"></a>További lépések

- Ellenőrzőlista a számítási igényű példányok használata HPC Pack a Windows Server, lásd: [MPI-alkalmazások futtatására HPC Pack Windows RDMA fürt beállítása](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

- MPI-alkalmazások futtatása az Azure Batch számítási igényű példányok használatához tekintse meg a [többpéldányos feladatok segítségével futtassa a Message Passing Interface (MPI) alkalmazások az Azure Batch](../../batch/batch-mpi.md).

- További tudnivalók [Azure számítási egység (ACU)](acu.md) segíthetnek a számítási teljesítmény összehasonlítása Azure termékváltozatok mentén.





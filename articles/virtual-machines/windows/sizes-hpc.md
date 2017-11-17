---
title: "Az Azure Windows Virtuálisgép-méretek – HPC |} Microsoft Docs"
description: "A Windows Azure virtuális gépek számítási magas teljesítmény érhető el a különböző méretű sorolja fel. Vcpu, adatlemezek és hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati sávszélesség az a sorozat-méretek számát tartalmazza."
services: virtual-machines-windows
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/28/2017
ms.author: jonbeck
ms.openlocfilehash: fc2cae8208baa211db2166c9d66a83153fa7b445
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="high-performance-compute-vm-sizes"></a>Nagy teljesítményű számítási Virtuálisgép-méretek

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="rdma-capable-instances"></a>Az RDMA-kompatibilis példányok
A számítási igényű példányok (H16r, H16mr, A8 és A9) egy részét a beállítást, a távoli közvetlen memória-hozzáférés (RDMA) hálózati kapcsolatot a hálózati adaptert. Ez az interfész mellett más Virtuálisgép-méretek elérhető szabványos Azure hálózati kapcsolat van. 
  
Ez az interfész lehetővé teszi az RDMA-kompatibilis példányok egy InfiniBand hálózati FDR sebességét H16r és H16mr virtuális gépek és a gyorsító A8 és A9 virtuális gépek QDR sebességet működő protokollt használó kommunikációra. E RDMA-képességeinek képes javítani, a méretezhetőség és a Message Passing Interface (MPI) alkalmazások teljesítményét.

Az RDMA-kompatibilis Windows virtuális gépek, az Azure RDMA hálózati elérésére vonatkozó követelmények a következők: 

* **Operációs rendszer**
  
  Windows Server 2012 R2, Windows Server 2012-ben
  
  > [!NOTE]
  > Jelenleg a Windows Server 2016 nem támogatja az Azure-ban RDMA-kapcsolatot.
  >

* **Rendelkezésre állási csoportot, vagy a felhőalapú szolgáltatás** – RDMA-kompatibilisek-e a virtuális gépek, az azonos rendelkezésre állási csoport (Ha használja az Azure Resource Manager telepítési modell) vagy az ugyanazon a felhőalapú szolgáltatás telepítése (Ha használja a klasszikus üzembe helyezési modellel). Azure Batch használja, ha az RDMA-kompatibilisek-e virtuális gépek ugyanabban a készletben kell lennie.

* **MPI** -Microsoft MPI (MS-MPI) 2012 R2 vagy újabb, illetve Intel MPI könyvtár 5.x

  Támogatott MPI-megvalósítások példányok közötti kommunikációra használja a Microsoft hálózati közvetlen felületét. 

* **RDMA hálózati címterüket** – az Azure-ban az RDMA hálózati fenntartja a cím terület 172.16.0.0/16. MPI alkalmazásokat futtasson egy Azure virtuális hálózatra-példányt, győződjön meg arról, hogy a virtuális hálózat címtere nem fedi át a RDMA hálózati.

* **HpcVmDrivers Virtuálisgép-bővítmény** -RDMA-kompatibilis virtuális gépeken, hozzá kell adni a HpcVmDrivers bővítmény telepítése a Windows hálózati eszköz-illesztőprogramok a RDMA hálózati kapcsolatot. (Egyes telepítések A8 és A9 példányok, a HpcVmDrivers a kiterjesztés kerül automatikusan.) A Virtuálisgép-bővítmény hozzáadása a virtuális gépek, használhatja a [Azure PowerShell](/powershell/azure/overview) parancsmagok. 

  
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

## <a name="next-steps"></a>Következő lépések

- Ellenőrzőlista a számítási igényű példányok használata HPC Pack a Windows Server, lásd: [MPI-alkalmazások futtatására HPC Pack Windows RDMA fürt beállítása](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

- MPI-alkalmazások futtatása az Azure Batch számítási igényű példányok használatához tekintse meg a [többpéldányos feladatok segítségével futtassa a Message Passing Interface (MPI) alkalmazások az Azure Batch](../../batch/batch-mpi.md).

- További tudnivalók [Azure számítási egység (ACU)](acu.md) segíthetnek a számítási teljesítmény összehasonlítása Azure termékváltozatok mentén.





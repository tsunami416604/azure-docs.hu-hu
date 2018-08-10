---
title: az Azure-ban vCPU-kvóták |} A Microsoft Docs
description: További információ a vCPU-kvóták az Azure-hoz.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 991deed871594efd310cfd24c5e9fc6a369b1a75
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628698"
---
# <a name="virtual-machine-vcpu-quotas"></a>Virtuális gép vCPU-kvóták

A virtuális gépek és a virtual machine scale sets vCPU-kvóták minden egyes előfizetés, az egyes régiókban két szinten vannak rendezve. Az első szinten az összes regionális vcpu-k, a második rétegű pedig a különböző virtuális gép mérete családba tartozó magok például a D-sorozat vcpu-k. A virtuális gép nem haladhatja meg a vCPU-kvóta virtuálisgépméret-családhoz tartozó vagy a teljes regionális vCPU-kvóta bármikor az új virtuális gép van telepítve a vcpu-k. Ha ezeket a kvótákat valamelyikét túllépése esetén a virtuális gép üzembe helyezése nem engedélyezett lesz. Emellett van egy régióban található virtuális gépek a teljes számára vonatkozó kvótát. Az egyes kvóta részletei láthatók a **használat + kvóták** szakaszában a **előfizetés** lap a [az Azure portal](https://portal.azure.com), vagy lekérdezheti az értékek használatával PowerShell.

 
## <a name="check-usage"></a>Használat ellenőrzése

Használhatja a [Get-AzureRmVMUsage](/powershell/module/azurerm.compute/get-azurermvmusage) parancsmag használatával ellenőrizheti a kvótahasználat.

```azurepowershell-interactive
Get-AzureRmVMUsage -Location "East US"
```

A kimenet ehhez hasonlóan néz ki:

```
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional vCPUs                         4   260 Count
Virtual Machines                             4 10000 Count
Virtual Machine Scale Sets                   1  2000 Count
Standard B Family vCPUs                      1    10 Count
Standard DSv2 Family vCPUs                   1   100 Count
Standard Dv2 Family vCPUs                    2   100 Count
Basic A Family vCPUs                         0   100 Count
Standard A0-A7 Family vCPUs                  0   250 Count
Standard A8-A11 Family vCPUs                 0   100 Count
Standard D Family vCPUs                      0   100 Count
Standard G Family vCPUs                      0   100 Count
Standard DS Family vCPUs                     0   100 Count
Standard GS Family vCPUs                     0   100 Count
Standard F Family vCPUs                      0   100 Count
Standard FS Family vCPUs                     0   100 Count
Standard NV Family vCPUs                     0    24 Count
Standard NC Family vCPUs                     0    48 Count
Standard H Family vCPUs                      0     8 Count
Standard Av2 Family vCPUs                    0   100 Count
Standard LS Family vCPUs                     0   100 Count
Standard Dv2 Promo Family vCPUs              0   100 Count
Standard DSv2 Promo Family vCPUs             0   100 Count
Standard MS Family vCPUs                     0     0 Count
Standard Dv3 Family vCPUs                    0   100 Count
Standard DSv3 Family vCPUs                   0   100 Count
Standard Ev3 Family vCPUs                    0   100 Count
Standard ESv3 Family vCPUs                   0   100 Count
Standard FSv2 Family vCPUs                   0   100 Count
Standard ND Family vCPUs                     0     0 Count
Standard NCv2 Family vCPUs                   0     0 Count
Standard NCv3 Family vCPUs                   0     0 Count
Standard LSv2 Family vCPUs                   0     0 Count
Standard Storage Managed Disks               2 10000 Count
Premium Storage Managed Disks                1 10000 Count
```


## <a name="reserved-vm-instances"></a>Reserved VM Instances
Fenntartott VM-példányok, amelyek hatóköre egy előfizetés, egy új aspektus adnak hozzá a vCPU-kvóták. Ezeket az értékeket a kell lennie az előfizetésben üzembe helyezhető, rögzített méretű példányok ismertetik. A kvóta rendszert, a kvóta annak biztosítása érdekében, fenntartott VM-példányok üzembe helyezhető az előfizetésben foglalt dolgoznak helyettesíti. Például ha egy adott előfizetésben 10 Standard_D1 fenntartott VM-példánya a tanúsítványalgoritmusok maximális Standard_D1 fenntartott Virtuálisgép-példányok 10 lesz. Ez azt eredményezi, hogy mindig is elérhető a teljes regionális Vcpu-kvóta Standard_D1-példányokhoz használandó legalább 10 vcpu-k, és legalább 10 vcpu-k nincsenek elérhető, a Standard D család vCPU-kvóta Standard_D1-példányokhoz használandó Azure.

Ha egy egyetlen előfizetés fenntartott példányok vásárlása szükséges egy kvótájának növelését, [a kvóta növelésére](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) az előfizetésén.

## <a name="next-steps"></a>További lépések

Számlázás és a kvóták kapcsolatos további információkért lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).

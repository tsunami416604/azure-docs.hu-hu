---
title: vCPU-kvóták az Azure-hoz
description: További információ az Azure-beli vCPU-kvótákról.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 20017f42031e126bbdae40f66bf4c36efd12f17c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75893465"
---
# <a name="virtual-machine-vcpu-quotas"></a>Virtuális gépek vCPU-kvótái

A virtuális gépek és a virtuálisgép-méretezési csoportok vCPU-kvóták két rétegben vannak elrendezve minden egyes előfizetéshez, minden régióban. Az első szint a teljes regionális vCPU-k, a második szint pedig a különböző virtuálisgép-méret-család magok, például a D-sorozatú vCPU-k. Minden alkalommal, amikor egy új virtuális gép telepítve van a virtuális gép vCPU-i nem haladhatja meg a vCPU-kvótát a virtuálisgép méretcsalád vagy a teljes regionális vCPU-kvóta. Ha ezek közül bármelyik a kvóták túllépése, a virtuális gép telepítése nem lesz engedélyezve. A régióban található virtuális gépek teljes számára vonatkozó kvóta is szerepel. Az egyes kvóták részletei az [Azure Portal](https://portal.azure.com) **Előfizetés** lapjának Használat **+ kvóták** szakaszában láthatók, vagy lekérdezheti az értékeket a PowerShell használatával.

   
 
## <a name="check-usage"></a>Használat ellenőrzése

A [Get-AzVMUsage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmusage) parancsmag segítségével ellenőrizheti a kvótahasználat.

```azurepowershell-interactive
Get-AzVMUsage -Location "East US"
```

A kimenet így fog kinézni:

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
Fenntartott virtuálisgép-példányok, amelyek hatóköre egyetlen előfizetés virtuális gép méretének rugalmassága nélkül, új szempontot avCPU-kvóták. Ezek az értékek a megadott méretű példányok számát írják le, amelyeknek az előfizetésben telepíthetőnek kell lenniük. Helyőrzőként dolgoznak a kvótarendszerben annak érdekében, hogy a kvóta le legyen foglalva annak biztosítása érdekében, hogy a fenntartott virtuálisgép-példányok telepíthetők legyenek az előfizetésben. Például ha egy adott előfizetés 10 Standard_D1 fenntartott virtuálisgép-példányok a használati korlát Standard_D1 fenntartott virtuálisgép-példányok lesz 10. Ez azt eredményezi, hogy az Azure biztosítja, hogy mindig legalább 10 vCPU érhető el a teljes regionális vCPU-k kvótát kell használni Standard_D1 példányok, és legalább 10 vCPU érhető el a standard D család vCPU kvótát kell használni Standard_D1 példányok.

Ha az egyetlen előfizetéses fenntartott rendszer megvásárlásához kvótanövelésre van szükség, kérheti az előfizetés [kvótanövelését.](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)

## <a name="next-steps"></a>További lépések

A számlázásról és a kvótákról az [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits?toc=/azure/billing/TOC.json)című témakörben talál további információt.

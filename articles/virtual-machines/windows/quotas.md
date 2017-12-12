---
title: "Azure vCPU kvótái |} Microsoft Docs"
description: "További információk a vCPU kvóták az Azure-bA."
keywords: 
services: virtual-machines-windows
documentationcenter: 
author: Drewm3
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: drewm
ms.openlocfilehash: b481299b62d452bc306c1f9c1fa2cdccd49b818e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="virtual-machine-vcpu-quotas"></a>Virtuális gép vCPU kvóták

A virtuális gépek és virtuálisgép-méretezési csoportok vCPU kvótái két helyezkednek el az egyes előfizetésekhez minden régióban. Az első szint a teljes regionális Vcpu, a második rétegű pedig különböző virtuális gép mérete termékcsalád mag például a Standard D termékcsalád Vcpu. Az újonnan telepített virtuális gép nem haladhatja meg az adott virtuális gép mérete termékcsalád vCPU kvótát vagy a teljes regionális vCPU kvóta bármikor egy új virtuális gép van telepítve a Vcpu. Ha ezek a kvóták vagy számát, majd a virtuális gép üzembe helyezéséhez nem engedélyezett. A kvóta a régióban található virtuális gépek összesített számának is van. Egyes ezek mely százalékértékénél kéri részletei láthatók a **használati + kvóták** szakasza a **előfizetés** lapját a [Azure-portálon](https://portal.azure.com), vagy az értékek használatával kérdezheti PowerShell.

 
## <a name="check-usage"></a>Ellenőrizze használatát

Használhatja a [Get-AzureRmVMUsage](/powershell/module/azurerm.compute/get-azurermvmusage) parancsmagot, hogy a kvótahasználat ellenőrzése.

```azurepowershell-interactive
Get-AzureRmVMUsage -Location "East US"
```

A kimenet ehhez hasonlóan fog kinézni:

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
Fenntartott virtuális gép olyan példányt, amelyek egy-egy előfizetéshez hatóköre, egy új aspektus adja hozzá a vCPU kvótákat. Ezeket az értékeket a megadott méret kell lennie az előfizetéshez telepíthető példányok száma ismertetik. A kvóta rendszer annak érdekében, hogy a kvóta van fenntartva, győződjön meg róla, fenntartott példányai is telepíthető az előfizetésben való használhatók helyőrzőként. Például egy adott előfizetésnek 10 standard szintű, D1 fenntartott standard szintű, D1 fenntartott példányok korlátozza a tanúsítványalgoritmusok példányok 10 lesz. Ez azt eredményezi, annak érdekében, hogy nincsenek mindig legalább 10 Vcpu elérhető a teljes regionális Vcpu kvótájának standard szintű, D1-példányok használható, és nincsenek elérhető, a Standard D termékcsalád vCPU kvótájának standard szintű, D1-példányok használandó legalább 10 Vcpu Azure.

Ha a kvóta növelését vagy vásároljon egy egyetlen előfizetés RI szükséges, akkor [a kvóta növelését](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) előfizetéséhez.

## <a name="next-steps"></a>Következő lépések

Számlázási és a kvóták kapcsolatos további információkért lásd: [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
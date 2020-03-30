---
title: vCPU-kvóták az Azure-hoz
description: További információ az Azure-beli vCPU-kvótákról.
keywords: ''
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: c194dbeb0183e64535342f8aaf9a770a93b3e332
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896187"
---
# <a name="virtual-machine-vcpu-quotas"></a>Virtuális gépek vCPU-kvótái

A virtuális gépek és a virtuálisgép-méretezési csoportok vCPU-kvóták két rétegben vannak elrendezve minden egyes előfizetéshez, minden régióban. Az első szint a teljes regionális vCPU-k, a második szint pedig a különböző virtuálisgép-méret-család magok, például a D-sorozatú vCPU-k. Minden alkalommal, amikor egy új virtuális gép telepítve van a virtuális gép vCPU-i nem haladhatja meg a vCPU-kvótát a virtuálisgép méretcsalád vagy a teljes regionális vCPU-kvóta. Ha ezek közül bármelyik a kvóták túllépése, a virtuális gép telepítése nem lesz engedélyezve. A régióban található virtuális gépek teljes számára vonatkozó kvóta is szerepel. Az egyes kvóták részletei az [Azure Portal](https://portal.azure.com) **Előfizetés** lapjának Használat **+ kvóták** szakaszában láthatók, vagy lekérdezheti az értékeket az Azure CLI használatával.


## <a name="check-usage"></a>Használat ellenőrzése

A kvótahasználatot az [az vm-listahasználatával](/cli/azure/vm)ellenőrizheti.

```azurecli-interactive
az vm list-usage --location "East US" -o table
```

Ezután a következőhöz hasonló eredményt kell kapnia:


```
Name                                CurrentValue    Limit
--------------------------------  --------------  -------
Availability Sets                              0     2000
Total Regional vCPUs                          29      100
Virtual Machines                               7    10000
Virtual Machine Scale Sets                     0     2000
Standard DSv3 Family vCPUs                     8      100
Standard DSv2 Family vCPUs                     3      100
Standard Dv3 Family vCPUs                      2      100
Standard D Family vCPUs                        8      100
Standard Dv2 Family vCPUs                      8      100
Basic A Family vCPUs                           0      100
Standard A0-A7 Family vCPUs                    0      100
Standard A8-A11 Family vCPUs                   0      100
Standard DS Family vCPUs                       0      100
Standard G Family vCPUs                        0      100
Standard GS Family vCPUs                       0      100
Standard F Family vCPUs                        0      100
Standard FS Family vCPUs                       0      100
Standard Storage Managed Disks                 5    10000
Premium Storage Managed Disks                  5    10000
```

## <a name="reserved-vm-instances"></a>Reserved VM Instances
Fenntartott virtuálisgép-példányok, amelyek hatóköre egyetlen előfizetés virtuális gép méretének rugalmassága nélkül, új szempontot avCPU-kvóták. Ezek az értékek a megadott méretű példányok számát írják le, amelyeknek az előfizetésben telepíthetőnek kell lenniük. Helyőrzőként dolgoznak a kvótarendszerben annak érdekében, hogy a kvóta le legyen foglalva annak biztosítására, hogy az Azure-foglalások telepíthetők legyenek az előfizetésben. Ha például egy adott előfizetés 10 Standard_D1 foglalással rendelkezik, a Standard_D1 foglalások ra vonatkozó használati korlát 10 lesz. Ez azt eredményezi, hogy az Azure biztosítja, hogy mindig legalább 10 vCPU érhető el a teljes regionális vCPU-k kvótát kell használni Standard_D1 példányok, és legalább 10 vCPU érhető el a standard D család vCPU kvótát kell használni Standard_D1 példányok.

Ha kvótanövelésre van szükség az egyetlen előfizetéses fenntartott rendszer megvásárlásához, kérheti az előfizetés [kvótanövelését.](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)

## <a name="next-steps"></a>További lépések

A számlázásról és a kvótákról az [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits?toc=/azure/billing/TOC.json)című témakörben talál további információt.

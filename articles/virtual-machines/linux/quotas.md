---
title: az Azure-ban vCPU-kvóták |} A Microsoft Docs
description: További információ a vCPU-kvóták az Azure-hoz.
keywords: ''
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: b7ec3a6919a27633fc36ba7fb1551ead10e06ffe
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55769446"
---
# <a name="virtual-machine-vcpu-quotas"></a>Virtuális gép vCPU-kvóták

A virtuális gépek és a virtual machine scale sets vCPU-kvóták minden egyes előfizetés, az egyes régiókban két szinten vannak rendezve. Az első szinten az összes regionális vcpu-k, a második rétegű pedig a különböző virtuális gép mérete családba tartozó magok például a D-sorozat vcpu-k. A virtuális gép nem haladhatja meg a vCPU-kvóta virtuálisgépméret-családhoz tartozó vagy a teljes regionális vCPU-kvóta bármikor az új virtuális gép van telepítve a vcpu-k. Ha ezeket a kvótákat valamelyikét túllépése esetén a virtuális gép üzembe helyezése nem engedélyezett lesz. Emellett van egy régióban található virtuális gépek a teljes számára vonatkozó kvótát. Az egyes kvóta részletei láthatók a **használat + kvóták** szakaszában a **előfizetés** lapját a [az Azure portal](https://portal.azure.com), vagy lekérdezheti az Azure az értékek PARANCSSORI FELÜLET.


## <a name="check-usage"></a>Használat ellenőrzése

Ellenőrizheti a kvóta használati [az vm list-usage](/cli/azure/vm).

```azurecli-interactive
az vm list-usage --location "East US" -o table
```

A kimenet következőhöz hasonlóan kell kinéznie:


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
Fenntartott VM-példányok, amelyek hatóköre egy előfizetés nélkül a virtuális gép mérete rugalmasan, egy új aspektus adnak hozzá a vCPU-kvóták. Ezeket az értékeket a kell lennie az előfizetésben üzembe helyezhető, rögzített méretű példányok ismertetik. Fognak működni ezek helyettesíti a kvóta rendszert, a kvóta annak érdekében, az előfizetésben üzembe helyezhető Azure foglalások van fenntartva. Például ha egy adott előfizetésben 10 Standard_D1 foglalások Standard_D1 lefoglalási a tanúsítványalgoritmusok korlát 10 lesz. Ez azt eredményezi, hogy mindig is elérhető a teljes regionális Vcpu-kvóta Standard_D1-példányokhoz használandó legalább 10 vcpu-k, és legalább 10 vcpu-k nincsenek elérhető, a Standard D család vCPU-kvóta Standard_D1-példányokhoz használandó Azure.

Ha kvótanövelést szükség, vagy vásároljon egy egyetlen előfizetés RI, [a kvóta növelésére](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) az előfizetésén.

## <a name="next-steps"></a>További lépések

Számlázás és a kvóták kapcsolatos további információkért lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).

---
title: Azure vCPU kvótái |} Microsoft Docs
description: További információk a vCPU kvóták az Azure-bA.
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
ms.openlocfilehash: a880ee18bb13b2cd8471cc58157469555397b872
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716516"
---
# <a name="virtual-machine-vcpu-quotas"></a>Virtuális gép vCPU kvóták

A virtuális gépek és virtuálisgép-méretezési csoportok vCPU kvótái két helyezkednek el az egyes előfizetésekhez minden régióban. Az első szint a teljes regionális Vcpu, a második rétegű pedig különböző virtuális gép mérete termékcsalád mag például a D sorozat Vcpu. A virtuális gép nem haladhatja meg a virtuális gép mérete termékcsalád vCPU kvótát vagy a teljes regionális vCPU kvóta bármikor egy új virtuális gép van telepítve a Vcpu. Ha ezek a kvóták vagy számát, a virtuális gép üzembe helyezéséhez nem engedélyezett. A kvóta a régióban található virtuális gépek összesített számának is van. Egyes ezek mely százalékértékénél kéri részletei láthatók a **használati + kvóták** szakasza a **előfizetés** lapját a [Azure-portálon](https://portal.azure.com), vagy alapján is kereshet az értékeket, az Azure használatával PARANCSSORI FELÜLET.


## <a name="check-usage"></a>Ellenőrizze használatát

A kvóta használati használatával ellenőrizheti [az vm lista-használati](/cli/azure/vm#az_vm_list_usage).

```azurecli-interactive
az vm list-usage --location "East US" -o table
```

A kimeneti kell kinéznie:


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
Fenntartott virtuális gép olyan példányt, amelyek egy-egy előfizetéshez hatóköre, egy új aspektus adja hozzá a vCPU kvótákat. Ezeket az értékeket a megadott méret kell lennie az előfizetéshez telepíthető példányok száma ismertetik. A kvóta rendszer annak érdekében, hogy a kvóta van fenntartva, győződjön meg róla, fenntartott példányai is telepíthető az előfizetésben való használhatók helyőrzőként. Például egy adott előfizetésnek 10 standard szintű, D1 fenntartott standard szintű, D1 fenntartott példányok korlátozza a tanúsítványalgoritmusok példányok 10 lesz. Ez azt eredményezi, annak érdekében, hogy nincsenek mindig legalább 10 Vcpu elérhető a teljes regionális Vcpu kvótájának standard szintű, D1-példányok használható, és nincsenek elérhető, a Standard D termékcsalád vCPU kvótájának standard szintű, D1-példányok használandó legalább 10 Vcpu Azure.

Ha a kvóta növelését vagy vásároljon egy egyetlen előfizetés RI szükséges, akkor [a kvóta növelését](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) előfizetéséhez.

## <a name="next-steps"></a>További lépések

Számlázási és a kvóták kapcsolatos további információkért lásd: [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).

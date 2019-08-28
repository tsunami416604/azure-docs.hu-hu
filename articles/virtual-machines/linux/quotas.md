---
title: vCPU-kvóták az Azure-hoz | Microsoft Docs
description: Ismerje meg az Azure-hoz készült vCPU-kvótákat.
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
ms.openlocfilehash: add0170c016b1a8226424ccf9b25cfde3a4c196f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091434"
---
# <a name="virtual-machine-vcpu-quotas"></a>Virtuális gépek vCPU-kvótái

A virtuális gépekhez és a virtuálisgép-méretezési csoportokhoz tartozó vCPU-kvóták minden egyes előfizetéshez két rétegben vannak rendezve. Az első réteg a teljes regionális vCPU, a második pedig a virtuális gépek különböző méretű, például a D sorozatú vCPU. Amikor új virtuális gépet telepít, a virtuális gép vCPU nem lépheti túl a virtuális gép vCPU-kvótáját vagy a teljes regionális vCPU-kvótát. Ha túllépi a kvótákat, a virtuális gép üzembe helyezése nem lesz engedélyezett. A régión belül a virtuális gépek teljes száma is rendelkezik kvótával. Az egyes kvóták részletei a [Azure Portal](https://portal.azure.com) **előfizetés** lapjának **használat + kvóták** szakaszában láthatók, vagy az Azure CLI használatával is lekérdezheti az értékeket.


## <a name="check-usage"></a>Használat ellenőrzése

A kvóta használatát az [az VM List-használat](/cli/azure/vm)használatával tekintheti meg.

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
A fenntartott VM-példányok, amelyek a virtuális gépek méretének rugalmassága nélkül egyetlen előfizetésre vannak korlátozva, új aspektust adhatnak hozzá a vCPU-kvótához. Ezek az értékek leírják, hogy a megadott méret hány példánya legyen üzembe helyezhető az előfizetésben. A kvótarendszer helyőrzőként működnek, így biztosítható, hogy a kvóta le legyen foglalva ahhoz, hogy az Azure-foglalások elérhetők legyenek az előfizetésben. Ha például egy adott előfizetés 10 Standard_D1 foglal le, a Standard_D1-foglalások használati korlátja 10 lesz. Ez azt eredményezi, hogy az Azure gondoskodik arról, hogy a Standard_D1-példányok esetében a teljes regionális vCPU kvótán belül legalább 10 vCPU legyen elérhető, és a VCPU-példányokhoz használt standard D család Standard_D1-kvótában legalább 10 vCPU elérhető.

Ha egy előfizetési RI megvásárlásához kvóta-növelésre van szükség, akkor az előfizetésre vonatkozó [kvóta növelését kérheti](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) .

## <a name="next-steps"></a>További lépések

A számlázással és a kvótákkal kapcsolatos további információkért lásd: Azure-előfizetések [és-szolgáltatások korlátai, kvótái és](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json)megkötései.

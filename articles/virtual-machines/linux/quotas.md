---
title: "Azure vCPU kvótái |} Microsoft Docs"
description: "További információk a vCPU kvóták az Azure-bA."
keywords: 
services: virtual-machines-linux
documentationcenter: 
author: Drewm3
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: drewm
ms.openlocfilehash: 8b99fd92d9031b7172e698cf5db1f776387bdfb9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="virtual-machine-vcpu-quotas"></a>Virtuális gép vCPU kvóták

A virtuális gépek és virtuálisgép-méretezési csoportok vCPU kvótái két helyezkednek el az egyes előfizetésekhez minden régióban. Az első szint a teljes regionális Vcpu, a második rétegű pedig különböző virtuális gép mérete termékcsalád mag például a Standard D termékcsalád Vcpu. Az újonnan telepített virtuális gép nem haladhatja meg az adott virtuális gép mérete termékcsalád vCPU kvótát vagy a teljes regionális vCPU kvóta bármikor egy új virtuális gép van telepítve a Vcpu. Ha ezek a kvóták vagy számát, majd a virtuális gép üzembe helyezéséhez nem engedélyezett. A kvóta a régióban található virtuális gépek összesített számának is van. Egyes ezek mely százalékértékénél kéri részletei láthatók a **használati + kvóták** szakasza a **előfizetés** lapját a [Azure-portálon](https://portal.azure.com), vagy az Azure parancssori felület használatával értékek .


## <a name="check-usage"></a>Ellenőrizze használatát

A kvóta használati használatával ellenőrizheti [az vm lista-használati](/cli/azure/vm#az_vm_list_usage).

```azurecli-interactive
az vm list-usage --location "East US"
[
  …
  {
    "currentValue": 4,
    "limit": 260,
    "name": {
      "localizedValue": "Total Regional vCPUs",
      "value": "cores"
    }
  },
  {
    "currentValue": 4,
    "limit": 10000,
    "name": {
      "localizedValue": "Virtual Machines",
      "value": "virtualMachines"
    }
  },
  {
    "currentValue": 1,
    "limit": 2000,
    "name": {
      "localizedValue": "Virtual Machine Scale Sets",
      "value": "virtualMachineScaleSets"
    }
  },
  {
    "currentValue": 1,
    "limit": 10,
    "name": {
      "localizedValue": "Standard B Family vCPUs",
      "value": "standardBFamily"
    }
  },
```
## <a name="reserved-vm-instances"></a>Reserved VM Instances
Fenntartott virtuális gép olyan példányt, amelyek egy-egy előfizetéshez hatóköre, egy új aspektus adja hozzá a vCPU kvótákat. Ezeket az értékeket a megadott méret kell lennie az előfizetéshez telepíthető példányok száma ismertetik. A kvóta rendszer annak érdekében, hogy a kvóta van fenntartva, győződjön meg róla, fenntartott példányai is telepíthető az előfizetésben való használhatók helyőrzőként. Például egy adott előfizetésnek 10 standard szintű, D1 fenntartott standard szintű, D1 fenntartott példányok korlátozza a tanúsítványalgoritmusok példányok 10 lesz. Ez azt eredményezi, annak érdekében, hogy nincsenek mindig legalább 10 Vcpu elérhető a teljes regionális Vcpu kvótájának standard szintű, D1-példányok használható, és nincsenek elérhető, a Standard D termékcsalád vCPU kvótájának standard szintű, D1-példányok használandó legalább 10 Vcpu Azure.

Ha a kvóta növelését vagy vásároljon egy egyetlen előfizetés RI szükséges, akkor [a kvóta növelését](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) előfizetéséhez.

## <a name="next-steps"></a>Következő lépések

Számlázási és a kvóták kapcsolatos további információkért lásd: [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).

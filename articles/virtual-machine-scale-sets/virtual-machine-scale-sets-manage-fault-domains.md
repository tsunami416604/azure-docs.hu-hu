---
title: Tartalék tartományok az Azure-beli virtuálisgép-méretezési csoportok kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan válassza ki a megfelelő számú tartalék tartományok között, a virtuálisgép-méretezési csoport létrehozása közben.
services: virtual-machine-scale-sets
documentationcenter: ''
author: rajsqr
manager: drewm
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/18
ms.author: rajraj
ms.openlocfilehash: 3b5568e0eed06684667b8aeefa389b6d0aab2a5f
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53636171"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>A megfelelő számú tartalék tartomány a virtuális gép méretezési csoportjának kiválasztása
A Virtual machine scale sets jönnek létre öt tartalék tartományok az Azure-régióban zónák nélkül alapértelmezés szerint. A régiók, amelyek támogatják a virtuálisgép-méretezési csoportok a zónaszintű telepítését az alapértelmezett érték a tartalék tartományok száma az egyes zónák 1. FD = 1 Ebben az esetben is, hogy a méretezési csoportba tartozó Virtuálisgép-példányok az elérhető legjobb lehetőség alapján számos állványt között lehetnek elosztva.

Igazítás, a méretezési csoport tartalék tartományok a felügyelt lemezek tartalék tartományok számát száma is használni. Ez a kiosztás segíthet a kvórum az adatvesztés elkerülése érdekében, ha leáll egy teljes felügyelt lemezek tartalék tartományt. Az FD száma állítható kisebb vagy egyenlő a felügyelt lemezek számát a tartalék tartományok az egyes régiókban érhető el. Ebben [dokumentum](../virtual-machines/windows/manage-availability.md) kapcsolatos régiónként felügyelt lemezek tartalék tartományok számát.

## <a name="rest-api"></a>REST API
Beállíthatja a tulajdonságot `properties.platformFaultDomainCount` , 1, 2 vagy 3 (alapértelmezés szerint 5, ha nincs megadva). A REST API dokumentációjában [Itt](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="azure-cli"></a>Azure CLI
Beállíthatja, hogy a paraméter `--platform-fault-domain-count` , 1, 2 vagy 3 (alapértelmezés szerint 5, ha nincs megadva). Az Azure CLI dokumentációjában [Itt](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create).

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --platform-fault-domain-count 3\
  --generate-ssh-keys
```

A méretezési csoport erőforrásainak és virtuális gépeinek létrehozása és konfigurálása néhány percet vesz igénybe.

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [rendelkezésre állási és redundanciával kapcsolatos szolgáltatásairól](../virtual-machines/windows/regions-and-availability.md) Azure környezetben.

---
title: Tartalék tartományok kezelése az Azure virtuálisgép-méretezési készleteiben
description: Ismerje meg, hogyan választhatja ki a megfelelő számú FD-k létrehozása közben egy virtuális gép méretezési készlet.
author: rajsqr
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: drewm
ms.openlocfilehash: 297837354cea4bb5ccdcc03261810dcffd144243
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275724"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>A tartalék tartományok számának megválasztása a virtuálisgép-méretezési csoportnak megfelelően
Virtuálisgép-méretezési készletek jönnek létre öt tartalék tartományok alapértelmezés szerint az Azure-régiókban zónák nélkül. A virtuálisgép-méretezési csoportok zónaszintű telepítését támogató és ez a beállítás beállítás esetén a tartaléktartomány-szám alapértelmezett értéke 1 az egyes zónákesetében. FD=1 ebben az esetben azt jelenti, hogy a méretezési csoporthoz tartozó virtuálisgép-példányok lesznek elosztva számos állványok a legjobb erőfeszítés alapján.

A méretezési csoport tartalék tartományainak számát a felügyelt lemezek tartalék tartományainak számához igazíthatja is. Ez az igazítás segíthet megelőzni a kvórum elvesztését, ha egy teljes felügyelt lemez tartaléktartomány a rendszer leáll. Az FD-szám az egyes régiókban elérhető felügyelt lemezek tartaléktartományainak számával kisebb vagy egyenlő. Ebből a [dokumentumból](../virtual-machines/windows/manage-availability.md) megtudhatja, hogy a felügyelt lemezek tartalék tartományai száma régiónként.

## <a name="rest-api"></a>REST API
A tulajdonságot `properties.platformFaultDomainCount` beállíthatja 1, 2 vagy 3 értékre (alapértelmezett 5, ha nincs megadva). A REST API dokumentációját [itt](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)találja.

## <a name="azure-cli"></a>Azure CLI
A paramétert `--platform-fault-domain-count` 1, 2 vagy 3 értékre állíthatja (alapértelmezett 5, ha nincs megadva). Az Azure CLI dokumentációját [itt találja.](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)

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
- További információ az Azure-környezetek [rendelkezésre állási és redundanciai](../virtual-machines/windows/availability.md) funkcióiról.

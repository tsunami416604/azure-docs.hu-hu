---
title: Karbantartási értesítések beszereznie a CLI használatával
description: Tekintse meg az Azure-ban futó virtuális gépek karbantartási értesítéseit, és indítsa el az önkiszolgáló karbantartást az Azure CLI használatával.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 4ad57c1c71a51f948bd405a5487a1e27e36bfff7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920892"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Tervezett karbantartási értesítések kezelése az Azure CLI használatával

**Ez a cikk linuxos és Windows-os virtuális gépekre vonatkozik.**

A CLI segítségével megtekintheti, hogy a virtuális gépek mikor vannak ütemezve [karbantartásra.](maintenance-notifications.md) A tervezett karbantartási információk az [az vm get-instance-view nézetből](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view)érhetők el.
 
A karbantartási adatokat csak akkor adja vissza, ha karbantartást terveznek. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>Karbantartás megkezdése

A következő hívás elindítja a `IsCustomerInitiatedMaintenanceAllowed` karbantartást a virtuális gépen, ha igaz értékre van állítva.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Klasszikus üzembe helyezések

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Ha továbbra is rendelkezik a klasszikus üzembe helyezési modell használatával üzembe helyezett örökölt virtuális gépek, használhatja az Azure klasszikus CLI lekérdezése a virtuális gépek és karbantartás kezdeményezése.

Győződjön meg arról, hogy a megfelelő módban működik a klasszikus virtuális gép beírásával:

```
azure config mode asm
```

A *myVM*nevű virtuális gép karbantartási állapotának leigazolásához írja be a következőt:

```
azure vm show myVM 
``` 

A karbantartás elindításához a myService szolgáltatásban és a myDeployment üzembe *helyezésében* a *myService* szolgáltatásban és a myDeployment üzembe helyezésében *myVM* nevű klasszikus virtuális gépen a következőt kell beírni:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>További lépések

A tervezett karbantartást az [Azure PowerShell](maintenance-notifications-powershell.md) vagy a [portál](maintenance-notifications-portal.md)használatával is kezelheti.

---
title: Az Azure Serial Console engedélyezése és letiltása | Microsoft dokumentumok
description: Az Azure Serial Console szolgáltatás engedélyezése és letiltása
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: e09e08f8ba36cf576bc27551254225adee3bb0fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451302"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>Az Azure soros konzol engedélyezése és letiltása

Csakúgy, mint bármely más erőforrás, az Azure Serial Console is engedélyezhető és letiltható. A Serial Console alapértelmezés szerint engedélyezve van a globális Azure összes előfizetéséhez. A Soros konzol letiltása jelenleg letiltja a szolgáltatást a teljes előfizetéshez. A Soros konzol letiltása vagy újbóli engedélyezése egy előfizetéshez közreműködői szintű vagy annál magasabb szintű hozzáférést igényel az előfizetésben.

A rendszerindítási diagnosztika letiltásával letilthatja a soros konzolt egy virtuális gép vagy a virtuálisgép-méretezési példány számára. A virtuális gép/virtuálisgép-méretezési csoport és a rendszerindító diagnosztikai tárfiók közreműködői szintű vagy annál magasabb szintű hozzáférést igényel.

## <a name="vm-level-disable"></a>Virtuálisgép-szintű letiltás
A soros konzol letiltható egy adott virtuális gép vagy virtuálisgép-méretezési készlethez a rendszerindítási diagnosztikai beállítás letiltásával. Kapcsolja ki a rendszerindítási diagnosztikát az Azure Portalon a virtuális gép vagy a virtuálisgép-méretezési készlet soros konzoljának letiltásához. Ha a soros konzol t használja a virtuális gép méretezési készlet, győződjön meg arról, hogy frissítse a virtuális gép méretezési készlet példányait a legújabb modellre.


## <a name="subscription-level-enabledisable"></a>Előfizetés-szintű engedélyezés/letiltás

> [!NOTE]
> A parancs futtatása előtt győződjön meg arról, hogy a megfelelő felhőben (Azure Public Cloud, Azure US Government Cloud) található. A segítségével `az cloud list` ellenőrizheti és `az cloud set -n <Name of cloud>`beállíthatja a felhőt.

### <a name="azure-cli"></a>Azure CLI

A soros konzol letiltható és újra engedélyezhető egy teljes előfizetéshez az Azure CLI következő parancsaival (a "Try it" gombbal elindíthatja az Azure Cloud Shell egy példányát, amelyben futtathatja a parancsokat):

Ha le szeretné tiltani egy előfizetés soros konzolját, használja a következő parancsokat:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Ha engedélyezni szeretné a soros konzolt egy előfizetéshez, használja a következő parancsokat:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

A soros konzol aktuális engedélyezett/letiltott állapotának leminősítéséhez használja a következő parancsokat:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --output=json --api-version="2018-05-01" | jq .properties
```

### <a name="powershell"></a>PowerShell

Soros konzol is engedélyezhető és letiltható a PowerShell használatával.

Ha le szeretné tiltani egy előfizetés soros konzolját, használja a következő parancsokat:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

Ha engedélyezni szeretné a soros konzolt egy előfizetéshez, használja a következő parancsokat:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>További lépések
* További információ az [Azure Serial Console for Linux virtuális gépekről](./serial-console-linux.md)
* További információ az [Azure Serial Console for Windows virtuális gépekről](./serial-console-windows.md)
* További információ [az Azure soros konzolenergia-kezelési lehetőségekről](./serial-console-power-options.md)
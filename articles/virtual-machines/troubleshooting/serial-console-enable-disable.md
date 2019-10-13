---
title: Az Azure soros konzol engedélyezése és letiltása | Microsoft Docs
description: Az Azure soros konzol szolgáltatás engedélyezése és letiltása
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
ms.openlocfilehash: f48fe94504d8012affb77c4fd5d39df2537d72b3
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300124"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>Az Azure soros konzol engedélyezése és letiltása

A többi erőforráshoz hasonlóan az Azure soros konzol is engedélyezhető és letiltható. A soros konzol alapértelmezés szerint engedélyezve van az összes előfizetéshez a globális Azure-ban. Jelenleg a soros konzol letiltása letiltja a szolgáltatást a teljes előfizetéshez. Az előfizetéshez tartozó soros konzol letiltásához vagy újbóli engedélyezéséhez közreműködő vagy magasabb szintű hozzáférés szükséges az előfizetéshez.

A rendszerindítási diagnosztika letiltásával letilthatja a soros konzolt egy adott virtuális gép vagy virtuálisgép-méretezési csoport példányai esetében is. A virtuális gép/virtuálisgép-méretezési csoport és a rendszerindítási diagnosztika Storage-fiókja esetében a közreműködői szintű hozzáférés vagy annál újabb rendszer szükséges.

## <a name="vm-level-disable"></a>VM-szintű letiltás
A soros konzol le lehet tiltani egy adott virtuális gép vagy virtuálisgép-méretezési csoport számára a rendszerindítási diagnosztika beállítás letiltásával. Kapcsolja ki a rendszerindítási diagnosztikát az Azure Portalról a virtuális gép vagy a virtuálisgép-méretezési csoport soros konzoljának letiltásához. Ha a soros konzolt egy virtuálisgép-méretezési csoporton használja, a virtuálisgép-méretezési csoport példányait a legújabb modellre kell frissítenie.


## <a name="subscription-level-disable"></a>Előfizetés szintű letiltás

### <a name="azure-cli"></a>Azure parancssori felület (CLI)

A Serial console letiltható és újra engedélyezhető a teljes előfizetéshez az Azure CLI következő parancsaival:

Az előfizetéshez tartozó soros konzol letiltásához használja a következő parancsokat:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Az előfizetéshez tartozó soros konzol engedélyezéséhez használja a következő parancsokat:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

A soros konzol aktuálisan engedélyezett/letiltott állapotának lekéréséhez használja az alábbi parancsokat:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --output=json --api-version="2018-05-01" | jq .properties
```

### <a name="powershell"></a>PowerShell

A Serial console a PowerShell használatával is engedélyezhető és letiltható.

Az előfizetéshez tartozó soros konzol letiltásához használja a következő parancsokat:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

Az előfizetéshez tartozó soros konzol engedélyezéséhez használja a következő parancsokat:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>Következő lépések
* További információ a [Linux rendszerű virtuális gépekhez készült Azure soros konzolról](./serial-console-linux.md)
* További információ a [Windows rendszerű virtuális gépekhez készült Azure soros konzolról](./serial-console-windows.md)
* [Az energiagazdálkodási lehetőségek megismerése az Azure soros konzolján](./serial-console-power-options.md)
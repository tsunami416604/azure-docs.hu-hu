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
ms.openlocfilehash: e09e08f8ba36cf576bc27551254225adee3bb0fd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75451302"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>Az Azure soros konzol engedélyezése és letiltása

A többi erőforráshoz hasonlóan az Azure soros konzol is engedélyezhető és letiltható. A soros konzol alapértelmezés szerint engedélyezve van az összes előfizetéshez a globális Azure-ban. Jelenleg a soros konzol letiltása letiltja a szolgáltatást a teljes előfizetéshez. Az előfizetéshez tartozó soros konzol letiltásához vagy újbóli engedélyezéséhez közreműködő vagy magasabb szintű hozzáférés szükséges az előfizetéshez.

A rendszerindítási diagnosztika letiltásával letilthatja a soros konzolt egy adott virtuális gép vagy virtuálisgép-méretezési csoport példányai esetében is. A virtuális gép/virtuálisgép-méretezési csoport és a rendszerindítási diagnosztika Storage-fiókja esetében a közreműködői szintű hozzáférés vagy annál újabb rendszer szükséges.

## <a name="vm-level-disable"></a>VM-szintű letiltás
A soros konzol le lehet tiltani egy adott virtuális gép vagy virtuálisgép-méretezési csoport számára a rendszerindítási diagnosztika beállítás letiltásával. Kapcsolja ki a rendszerindítási diagnosztikát a Azure Portal a virtuális gép vagy a virtuálisgép-méretezési csoport soros konzoljának letiltásához. Ha a soros konzolt egy virtuálisgép-méretezési csoporton használja, a virtuálisgép-méretezési csoport példányait a legújabb modellre kell frissítenie.


## <a name="subscription-level-enabledisable"></a>Előfizetés szintű engedélyezés/letiltás

> [!NOTE]
> A parancs futtatása előtt győződjön meg arról, hogy a megfelelő felhőben (Azure Public Cloud, Azure USA Government Cloud) van. Megtekintheti a `az cloud list` -t, és beállíthatja a felhőt a használatával `az cloud set -n <Name of cloud>` .

### <a name="azure-cli"></a>Azure CLI

Serial console letiltható és újraengedélyezhető a teljes előfizetéshez az Azure CLI következő parancsaival (a "kipróbálás" gombra kattintva elindíthatja a Azure Cloud Shell azon példányát, amelyben futtathatja a parancsokat):

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

## <a name="next-steps"></a>További lépések
* További információ a [Linux rendszerű virtuális gépekhez készült Azure soros konzolról](./serial-console-linux.md)
* További információ a [Windows rendszerű virtuális gépekhez készült Azure soros konzolról](./serial-console-windows.md)
* [Az energiagazdálkodási lehetőségek megismerése az Azure soros konzolján](./serial-console-power-options.md)
---
title: Azure PowerShell-példaszkript – Feliratkozás erőforráscsoportra és szűrés erőforrás alapján | Microsoft Docs
description: Azure PowerShell-példaszkript – Feliratkozás erőforráscsoportra és szűrés erőforrás alapján
services: event-grid
documentationcenter: na
author: tfitzmac
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2018
ms.author: tomfitz
ms.openlocfilehash: ed77e8f09af841a1414212d7df6b60655ac158cd
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39482583"
---
# <a name="subscribe-to-events-for-a-resource-group-and-filter-for-a-resource-with-powershell"></a>Feliratkozás egy erőforráscsoport eseményeire és szűrés erőforrásra a PowerShell-lel

Ez a szkript létrehoz egy Event Grid-előfizetést egy erőforráscsoport eseményeihez. Szűrőt használ az erőforráscsoport adott erőforrásához tartozó események lekéréséhez.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

```powershell
# Provide an endpoint for handling the events.
$myEndpoint = "<endpoint URL>"

# Select the Azure subscription that contains the resource group.
Set-AzureRmContext -Subscription "Contoso Subscription"

# Get the resource ID to filter events
$resourceId = (Get-AzureRmResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

# Subscribe to the resource group. Provide the name of the resource group you want to subscribe to.
New-AzureRmEventGridSubscription `
  -Endpoint $myEndpoint `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsot használja az esemény-előfizetés létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzureRmEventGridSubscription](https://docs.microsoft.com/powershell/module/azurerm.eventgrid/new-azurermeventgridsubscription) | Event Grid-előfizetés létrehozása. |

## <a name="next-steps"></a>További lépések

* A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](../overview.md) cikk ismerteti.
* A PowerShell-lel kapcsolatos további tudnivalókért tekintse meg az [Azure PowerShell dokumentációját](https://docs.microsoft.com/powershell/azure/get-started-azureps).

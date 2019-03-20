---
title: Az Azure Event Grid-előfizetések lekérdezése
description: Ismerteti, hogyan lehet az Azure Event Grid-előfizetések listázásához.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: spelluru
ms.openlocfilehash: ad9c2d492f70a697ef0e7dc3b7ed03b9938f2468
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181690"
---
# <a name="query-event-grid-subscriptions"></a>Event Grid-előfizetések lekérdezése 

Ez a cikk ismerteti, hogyan kell felsorolni az Event Grid-előfizetések az Azure-előfizetésében. A meglévő Event Grid-előfizetések lekérdezése, esetén fontos tudni, hogy a különböző típusú előfizetések. A lekérdezni kívánt előfizetés alapján különböző paraméterek adnia.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-groups-and-azure-subscriptions"></a>Erőforráscsoport- és Azure-előfizetések

Az Azure-előfizetések és -erőforráscsoportok nem Azure-erőforrások. Ezért event grid-előfizetések, erőforráscsoportok vagy Azure-előfizetés nem rendelkezik azonos tulajdonságokkal event grid-előfizetések az Azure-erőforrásokhoz. Event grid-előfizetések, erőforráscsoportok vagy Azure-előfizetések globális minősülnek.

Event grid-előfizetések Azure-előfizetés és a hozzá tartozó erőforráscsoportok lekéréséhez, nem kell megadnia a paramétereket. Ellenőrizze, hogy kiválasztotta a lekérdezni kívánt Azure-előfizetést. Az alábbi példák az event grid-előfizetések egyéni témaköröket és az Azure-erőforrások nem kap.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az account set -s "My Azure Subscription"
az eventgrid event-subscription list
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Set-AzContext -Subscription "My Azure Subscription"
Get-AzEventGridSubscription
```

Event grid-előfizetések Azure-előfizetés beszerzéséhez adja meg a témakörtípus **Microsoft.Resources.Subscriptions**.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions"
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Event grid-előfizetések az Azure-előfizetésen belüli összes erőforráscsoport lekéréséhez adja meg a témakörtípus **Microsoft.Resources.ResourceGroups**.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups"
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

Event grid-előfizetések a meghatározott erőforráscsoportban lekéréséhez adja meg az erőforráscsoport nevét paraméterként.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>Egyéni témaköröket és az Azure-erőforrások

Event grid-egyéni témakörök olyan Azure-erőforrások. Event grid-előfizetések egyéni témaköröket és más erőforrások, például Blob storage-fiók, ezért ugyanolyan módon lekérdezése. Event grid-előfizetések egyéni témakörök lekéréséhez, hogy az erőforrás azonosítása, vagy adja meg az erőforrás helyét paramétereket kell megadnia. Nincs lehetőség a széles körben lekérdezés event grid-előfizetések az erőforrások az Azure-előfizetése között.

Az egyéni témaköröket és más erőforrások event grid-előfizetések lépjen be egy olyan helyre, adja meg a hely nevét.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzEventGridSubscription -Location westus2
```

Egyéni témakörök, előfizetések beolvasása egy adott helyre vonatkozóan, adja meg a hely és a témakörtípus **Microsoft.EventGrid.Topics**.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

Storage-fiókok, előfizetések beolvasása egy adott helyre vonatkozóan, adja meg a hely és a témakörtípus **Microsoft.Storage.StorageAccounts**.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

Event grid-előfizetések egy egyéni témakör lekéréséhez adja meg az egyéni témakör nevét és az erőforráscsoport nevét.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

Event grid-előfizetések egy adott erőforráshoz lekéréséhez adja meg az erőforrás-azonosítója.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
resourceid=$(az resource show -n mystorage -g myResourceGroup --resource-type "Microsoft.Storage/storageaccounts" --query id --output tsv)
az eventgrid event-subscription list --resource-id $resourceid
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
$resourceid = (Get-AzResource -Name mystorage -ResourceGroupName myResourceGroup).ResourceId
Get-AzEventGridSubscription -ResourceId $resourceid
```

## <a name="next-steps"></a>További lépések

* Eseménykézbesítés és újrapróbálkozás [Event Grid az üzenetek kézbesítését, és ismételje meg](delivery-and-retry.md).
* Az Event Grid megismeréséhez tekintse meg [az Event Grid bevezetőjét](overview.md).
* Tekintse meg a gyors kezdéshez Event Grid használatával [az Azure Event Griddel egyéni események létrehozása és útvonal](custom-event-quickstart.md).

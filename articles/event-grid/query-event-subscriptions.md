---
title: Lekérdezés IM–1999
description: Ez a cikk bemutatja, hogyan sorolhatja fel az Event Grid-előfizetéseket az Azure-előfizetésben. Az előfizetés típusától függően különböző paramétereket ad meg.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2da4ba5946f16092c1d0918aec8dc3109b5fd4a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721540"
---
# <a name="query-event-grid-subscriptions"></a>Lekérdezési eseményrács-előfizetések 

Ez a cikk ismerteti, hogyan sorolhatja fel az Event Grid-előfizetések az Azure-előfizetésben. A meglévő Event Grid-előfizetések lekérdezésekénél fontos, hogy megértse a különböző típusú előfizetéseket. Különböző paramétereket adhat meg a beszerezni kívánt előfizetés típusától függően.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-groups-and-azure-subscriptions"></a>Erőforráscsoportok és Azure-előfizetések

Az Azure-előfizetések és erőforráscsoportok nem Azure-erőforrások. Ezért az erőforráscsoportok vagy az Azure-előfizetések eseményrács-előfizetések nem rendelkeznek ugyanolyan tulajdonságokkal, mint az Azure-erőforrások eseményrács-előfizetések. Eseményrács-előfizetések erőforráscsoportok vagy Az Azure-előfizetések globálisnak minősülnek.

Eseményrács-előfizetések lekérni egy Azure-előfizetés és az erőforrás-csoportok, nem kell paramétereket megadnia. Győződjön meg arról, hogy kiválasztotta a lekérdezni kívánt Azure-előfizetést. A következő példák nem kap eseményrács-előfizetések egyéni témakörök höz vagy Az Azure-erőforrások.

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

Ha egy Azure-előfizetéshez szeretne eseményrács-előfizetéseket beszerezni, adja meg a **Microsoft.Resources.Subscriptions**témakörtípusát.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions" --location global
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Ha egy Azure-előfizetésen belül az összes erőforráscsoporthoz szeretne eseményrács-előfizetéseket beszerezni, adja meg a **Microsoft.Resources.ResourceGroups**témakörtípusát.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups" --location global
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

Egy adott erőforráscsoport eseményrács-előfizetéseinek lekérnie, adja meg az erőforráscsoport nevét paraméterként.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup --location global
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>Egyéni témakörök és Azure-erőforrások

Az eseményrács egyéni témakörei Azure-erőforrások. Ezért lekérdezi az eseményrács-előfizetéseket az egyéni témakörökhöz és más erőforrásokhoz, például a Blob storage-fiókhoz, ugyanúgy. Az egyéni témakörök eseményrács-előfizetéseinek lekérnie szükséges paramétereket, amelyek azonosítják az erőforrást vagy az erőforrás helyét. Nem lehet széles körben lekérdezni az azure-előfizetéserőforrások eseményrács-előfizetéseit.

Egyéni témakörök höz és más erőforrásokhoz való eseményrács-előfizetések lekérnie egy helyen, adja meg a hely nevét.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzEventGridSubscription -Location westus2
```

Ha egyéni témakörökre szeretne feliratkozni egy adott helyhez, adja meg a **Microsoft.EventGrid.Topics**hely- és témakörtípusát.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

Egy hely tárfiókjainak előfizetéseihez adja meg a **Microsoft.Storage.StorageAccounts**helyét és témakörtípusát.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

Egyéni témakör eseményrács-előfizetéseinek lekérnie, adja meg az egyéni témakör nevét és az erőforráscsoport nevét.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

Egy adott erőforrás eseményrács-előfizetések lekérnie, adja meg az erőforrás-azonosítót.

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

* Az eseménykézbesítésről és az újrapróbálkozásokról az [Event Grid üzenetkézbesítésével és újrapróbálkozásával](delivery-and-retry.md)kapcsolatos információkért kattintson.
* Az Event Grid ismertetése: [Az Event Grid bemutatása](overview.md).
* Az Event Grid használatának gyors megkezdéséhez olvassa el az [Egyéni események létrehozása és irányítása az Azure Event Griddel című témakört.](custom-event-quickstart.md)

---
title: Lekérdezés Azure esemény rács előfizetések
description: Ismerteti, hogyan lehet Azure esemény rács előfizetések elemet.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 04/04/2018
ms.author: tomfitz
ms.openlocfilehash: ce89e1caefbf73512c4ad3c2ce57ebc6f03f2616
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="query-event-grid-subscriptions"></a>Esemény rács előfizetések lekérdezése 

A cikkből megtudhatja, hogyan listázhat a rács esemény-előfizetések az Azure-előfizetéshez. A meglévő esemény rács előfizetések lekérdezésekor fontos ismerni a különböző előfizetések. Az előfizetés le szeretné kérdezni a típusától függően különböző paramétereket megadnia.

## <a name="resource-groups-and-azure-subscriptions"></a>Erőforrás-csoportok és az Azure-előfizetések

Azure-előfizetések és -erőforráscsoportok nem Azure-erőforrások. Ezért esemény rács előfizetések erőforráscsoportokkal vagy Azure-előfizetések nem rendelkezik esemény rács előfizetések az Azure-erőforrások tulajdonságai azonosak. Esemény rács előfizetések erőforráscsoportokkal vagy az Azure-előfizetések globális minősülnek.

Ahhoz, hogy a rács esemény-előfizetések az Azure-előfizetés és az erőforráscsoportok, nem kell adja meg a paramétereket. Győződjön meg arról, hogy le szeretné kérdezni az Azure-előfizetés választott. Az alábbi példák nem jelenik meg esemény rács előfizetések egyéni témaköröket és az Azure-erőforrások.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az account set -s "My Azure Subscription"
az eventgrid event-subscription list
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Set-AzureRmContext -Subscription "My Azure Subscription"
Get-AzureRmEventGridSubscription
```

Ahhoz, hogy az esemény rács előfizetések az Azure-előfizetéssel, adja meg a témakörtípus **Microsoft.Resources.Subscriptions**.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions"
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Ahhoz, hogy az esemény rács előfizetések az Azure-előfizetés belül az összes erőforráscsoportra vonatkozóan, adja meg a témakörtípus **Microsoft.Resources.ResourceGroups**.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups"
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

Ahhoz, hogy a megadott erőforráscsoport esemény rács előfizetések, adja meg paraméterként az erőforráscsoport nevét.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>Egyéni témakörök és az Azure-erőforrások

Esemény rács egyéni témakörök Azure-erőforrások. Esemény rács előfizetések egyéni témakörök és más erőforrások, például a Blob storage-fiók, ezért ugyanolyan módon lekérdezése. Ahhoz, hogy a rács előfizetések esemény egyéni témaköröket, azonosíthatja az erőforrás vagy azonosíthatja az erőforrás helye paramétereket kell megadnia. Nincs lehetőség körben lekérdezés esemény rács előfizetésekhez erőforrások az Azure-előfizetés között.

Ahhoz, hogy a rács előfizetések esemény egyéni témakörök és egyéb erőforrások helyen, adja meg a hely nevét.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -Location westus2
```

Ahhoz, hogy egyéni témakörök előfizetések helyét, adja meg a hely és a témakörtípus **Microsoft.EventGrid.Topics**.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

Ahhoz, hogy a storage-fiókokra előfizetések helyét, adja meg a hely és a témakörtípus **Microsoft.Storage.StorageAccounts**.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

Ahhoz, hogy a rács előfizetések esemény egyéni témakör, adja meg az egyéni témakör nevét és az erőforráscsoport nevét.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

Ahhoz, hogy a rács előfizetések esemény egy adott erőforráshoz, adja meg az erőforrás-azonosító.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
resourceid=$(az resource show -n mystorage -g myResourceGroup --resource-type "Microsoft.Storage/storageaccounts" --query id --output tsv)
az eventgrid event-subscription list --resource-id $resourceid
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
$resourceid = (Get-AzureRmResource -Name mystorage -ResourceGroupName myResourceGroup).ResourceId
Get-AzureRmEventGridSubscription -ResourceId $resourceid
```

## <a name="next-steps"></a>További lépések

* Esemény kézbesítési és, az újrapróbálkozásokat információt [esemény rács üzenetkézbesítést, és próbálkozzon újra](delivery-and-retry.md).
* Esemény rácshoz ismertetőért lásd: [esemény rács](overview.md).
* Ha gyorsan esemény rács segítségével, lásd: [Azure esemény rácshoz hozza létre és útvonal egyéni események](custom-event-quickstart.md).

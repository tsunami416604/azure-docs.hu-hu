---
title: Kézbesítetlen levelek és újrapróbálkozási szabályzatok Azure Event Grid előfizetésekhez
description: Leírja, hogyan lehet testre szabni a Event Grid esemény-kézbesítési beállításait. Állítsa be a kézbesítetlen levél célját, és adja meg, hogy mennyi ideig próbálkozzon a kézbesítéssel.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/06/2019
ms.author: spelluru
ms.openlocfilehash: 63bae62ed89bd0bbc167a88274002d1fa1e9b86d
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933372"
---
# <a name="dead-letter-and-retry-policies"></a>Kézbesítetlen levelek és újrapróbálkozási szabályzatok

Esemény-előfizetés létrehozásakor testreszabhatja az események kézbesítésének beállításait. Ebből a cikkből megtudhatja, hogyan állíthatja be a kézbesítetlen levelek helyét, és hogyan szabhatja testre az újrapróbálkozási beállításokat. További információ ezekről a funkciókról: [Event Grid üzenet kézbesítése és újrapróbálkozás](delivery-and-retry.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-dead-letter-location"></a>Kézbesítetlen levelek helyének beállítása

A kézbesítetlen levelek helyének beállításához olyan tárolási fiókra van szükség, amely nem kézbesíthető végpontnak. A példák egy meglévő Storage-fiók erőforrás-AZONOSÍTÓját kapják meg. Létrehoznak egy olyan esemény-előfizetést, amely egy tárolót használ az adott Storage-fiókban a kézbesítetlen levelek végpontja számára.

> [!NOTE]
> - A cikkben szereplő parancsok futtatása előtt hozzon létre egy Storage-fiókot és egy BLOB-tárolót a tárolóban.
> - A Event Grid szolgáltatás blobokat hoz létre ebben a tárolóban. A Blobok nevei a Event Grid-előfizetés nevét fogják tartalmazni a nagybetűvel rendelkező összes betűvel. Ha például az előfizetés neve a My-blob-előfizetés, a kézbesítetlen levelek blobjának neve a saját BLOB-előfizetés (myblobcontainer/MY-BLOB-előfizetés/2019/8/8/5/111111111-1111-1111-1111 -111111111111. JSON) lesz. Ez a viselkedés a különböző Azure-szolgáltatások közötti adatfeldolgozások elleni védelem.


### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
containername=testcontainer

topicid=$(az eventgrid topic show --name demoTopic -g gridResourceGroup --query id --output tsv)
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

A kézbesítetlen levelek kikapcsolásához futtassa újra a parancsot az esemény-előfizetés létrehozásához, de ne adjon meg `deadletter-endpoint`értéket a következőhöz:. Nem kell törölnie az esemény-előfizetést.

> [!NOTE]
> Ha az Azure CLI-t használja a helyi gépen, használja az Azure CLI 2.0.56 vagy újabb verzióját. Az Azure CLI legújabb verziójának telepítésével kapcsolatos utasításokért lásd: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$containername = "testcontainer"

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

A kézbesítetlen levelek kikapcsolásához futtassa újra a parancsot az esemény-előfizetés létrehozásához, de ne adjon meg `DeadLetterEndpoint`értéket a következőhöz:. Nem kell törölnie az esemény-előfizetést.

> [!NOTE]
> Ha az Azure bővítményén-t használja a helyi gépen, használja a Azure PowerShell 1.1.0-es vagy újabb verzióját. Töltse le és telepítse a legújabb Azure PowerShell az [Azure downloads](https://azure.microsoft.com/downloads/)szolgáltatásból.

## <a name="set-retry-policy"></a>Újrapróbálkozási szabályzat beállítása

Event Grid-előfizetés létrehozásakor megadhatja, hogy mennyi ideig Event Grid próbálkozzon az esemény kézbesítésével. Alapértelmezés szerint a Event Grid 24 órát (1440 percet) vagy 30 alkalommal próbál meg. Ezen értékek bármelyikét megadhatja az Event Grid-előfizetéshez. Az esemény élettartama értékének 1 és 1440 közötti egész számnak kell lennie. A maximális újrapróbálkozások értékének 1 és 30 közötti egész számnak kell lennie.

Nem lehet konfigurálni az [](delivery-and-retry.md#retry-schedule-and-duration)újrapróbálkozási ütemtervet.

### <a name="azure-cli"></a>Azure CLI

Ha az esemény élettartamát 1440 perctől eltérő értékre szeretné beállítani, használja a következőt:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

Ha a maximális újrapróbálkozást a 30-tól eltérő értékre szeretné beállítani, használja a következőt:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Ha a és `max-deliver-attempts`a `event-ttl` lehetőséget is beállítja, Event Grid az elsővel jár le, hogy meghatározza, mikor kell leállítani az események kézbesítését.

### <a name="powershell"></a>PowerShell

Ha az esemény élettartamát 1440 perctől eltérő értékre szeretné beállítani, használja a következőt:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

Ha a maximális újrapróbálkozást a 30-tól eltérő értékre szeretné beállítani, használja a következőt:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

Ha a és `MaxDeliveryAttempt`a `EventTtl` lehetőséget is beállítja, Event Grid az elsővel jár le, hogy meghatározza, mikor kell leállítani az események kézbesítését.

## <a name="next-steps"></a>További lépések

* A kézbesítetlen levelek eseményeinek feldolgozására szolgáló Azure Function alkalmazást használó minta alkalmazáshoz lásd: [Azure Event Grid a .net-hez készült kézbesítetlen levelek mintáit](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* Eseménykézbesítés és újrapróbálkozás [Event Grid az üzenetek kézbesítését, és ismételje meg](delivery-and-retry.md).
* Az Event Grid megismeréséhez tekintse meg [az Event Grid bevezetőjét](overview.md).
* Tekintse meg a gyors kezdéshez Event Grid használatával [az Azure Event Griddel egyéni események létrehozása és útvonal](custom-event-quickstart.md).

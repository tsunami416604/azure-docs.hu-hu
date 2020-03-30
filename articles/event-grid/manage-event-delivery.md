---
title: Kézbesítésben visszaírt levél és újrapróbálkozási szabályzatok – Azure Event Grid
description: Ez a témakör az Event Grid eseménykézbesítési beállításainak testreszabását ismerteti. Állítson be egy kézbesítési célhelyet, és adja meg, hogy mennyi ideig kell újrapróbálkoznia a kézbesítéssel.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: spelluru
ms.openlocfilehash: caed3c077b4df5da5fd8541b2f7e85ef119604b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794034"
---
# <a name="dead-letter-and-retry-policies"></a>Kézbesítésben nem elhunyt és újrapróbálkozási házirendek

Esemény-előfizetés létrehozásakor testreszabhatja az eseménykézbesítés beállításait. Ez a cikk bemutatja, hogyan állíthat be egy kézbesítési levelet helyet, és testre szabhatja az újrapróbálkozási beállításokat. Ezekről a szolgáltatásokról az [Event Grid üzenetkézbesítési és újrapróbálkozási témakörben](delivery-and-retry.md)talál.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-dead-letter-location"></a>Kézbesítési levél helyének beállítása

Egy kézbesített levél helyét, szüksége van egy tárfiók események, amelyek nem kézbesíthetők a végpontra. A példák egy meglévő tárfiók erőforrás-azonosítóját kapják. Létrehoznak egy esemény-előfizetést, amely egy tárolót használ a lekézbesítési címvégponthoz a tárfiókban.

> [!NOTE]
> - Hozzon létre egy tárfiókot és egy blob tárolót a tárolóban, mielőtt parancsokat futtatna ebben a cikkben.
> - Az Event Grid szolgáltatás blobokat hoz létre ebben a tárolóban. A blobok neve lesz az Event Grid-előfizetés neve az összes betű nagybetűs. Ha például az előfizetés neve My-Blob-Subscription, a kézbesített levélblobok neve my-BLOB-SUBSCRIPTION (myblobcontainer/MY-BLOB-SUBSCRIPTION/2019/8/8/5/111111111-11111-1111-11111-1111111111111.json). Ez a viselkedés az Azure-szolgáltatások közötti esetkezelés közötti különbségek elleni védelem.


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

A holtbetűk kikapcsolásához futtassa újra a parancsot az esemény-előfizetés `deadletter-endpoint`létrehozásához, de ne adjon meg értéket a számára. Nem kell törölnie az esemény-előfizetést.

> [!NOTE]
> Ha az Azure CLI-t használja a helyi gépen, használja az Azure CLI 2.0.56-os vagy újabb verzióját. Az Azure CLI legújabb verziójának telepítéséről [az Azure CLI telepítése című](/cli/azure/install-azure-cli)témakörben talál.

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

A holtbetűk kikapcsolásához futtassa újra a parancsot az esemény-előfizetés `DeadLetterEndpoint`létrehozásához, de ne adjon meg értéket a számára. Nem kell törölnie az esemény-előfizetést.

> [!NOTE]
> Ha az Azure Poweshellt használja a helyi gépen, használja az Azure PowerShell 1.1.0-s vagy újabb verzióját. Töltse le és telepítse a legújabb Azure PowerShellt az [Azure letöltéseiből.](https://azure.microsoft.com/downloads/)

## <a name="set-retry-policy"></a>Újrapróbálkozási házirend beállítása

Event Grid-előfizetés létrehozásakor értékeket állíthat be arra vonatkozóan, hogy az Event Grid mennyi ideig próbálja meg az esemény kézbesítésére. Alapértelmezés szerint az Event Grid 24 órán (1440 percig) vagy 30 alkalommal próbálkozik. Ezeket az értékeket az eseményrács-előfizetéshez állíthatja be. Az esemény-idő-az-élni érték értékének 1 és 1440 között egész számnak kell lennie. A maximális újrapróbálkozások értékének 1 és 30 között kell egész számnak lennie.

Az [újrapróbálkozási ütemezés](delivery-and-retry.md#retry-schedule-and-duration)nem konfigurálható.

### <a name="azure-cli"></a>Azure CLI

Ha az esemény 1440 percnél eltérő értékre szeretné beállítani az eseményt, használja a következőket:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

Ha a maximális újrapróbálkozásokat 30-tól eltérő értékre szeretné állítani, használja a következőket:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Ha a `event-ttl` mindkettőt állítja be, és `max-deliver-attempts`az Event Grid az első lejárati idővel határozza meg, hogy mikor állítsa le az eseménykézbesítést.

### <a name="powershell"></a>PowerShell

Ha az esemény 1440 percnél eltérő értékre szeretné beállítani az eseményt, használja a következőket:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

Ha a maximális újrapróbálkozásokat 30-tól eltérő értékre szeretné állítani, használja a következőket:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

Ha a `EventTtl` mindkettőt állítja be, és `MaxDeliveryAttempt`az Event Grid az első lejárati idővel határozza meg, hogy mikor állítsa le az eseménykézbesítést.

## <a name="next-steps"></a>További lépések

* Egy mintaalkalmazás, amely egy Azure Function alkalmazást használ a kézbesítésben ellátható kézbesítési események feldolgozásához, lásd: [Azure Event Grid Holtév minták a .NET.](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/)
* Az eseménykézbesítésről és az újrapróbálkozásokról az [Event Grid üzenetkézbesítésével és újrapróbálkozásával](delivery-and-retry.md)kapcsolatos információkért kattintson.
* Az Event Grid ismertetése: [Az Event Grid bemutatása](overview.md).
* Az Event Grid használatának gyors megkezdéséhez olvassa el az [Egyéni események létrehozása és irányítása az Azure Event Griddel című témakört.](custom-event-quickstart.md)

---
title: Kézbesítetlen levelek és újrapróbálkozási szabályzatok az Azure Event Grid-előfizetések
description: Az Event Griddel kapcsolatos esemény Kézbesítési beállítások testreszabásának módját ismerteti. A kézbesíthetetlen levelek célkiszolgálón állítsa be, és adja meg, mennyi ideig kézbesítési újra.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/06/2019
ms.author: spelluru
ms.openlocfilehash: 69c26ab522a925032c5a255d07489de0052756c0
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57340851"
---
# <a name="dead-letter-and-retry-policies"></a>Kézbesítetlen levelek és újrapróbálkozási szabályzatok

Egy esemény-előfizetés létrehozásakor testre szabható eseménykézbesítés beállításait. Ez a cikk bemutatja, hogyan állítsa be a kézbesítetlen levelek helyét, és az újrapróbálkozási beállítások testreszabása. További információt szeretne ezekről a funkciókról: [Event Grid az üzenetek kézbesítését, és ismételje meg](delivery-and-retry.md).

## <a name="set-dead-letter-location"></a>Kézbesíthetetlen levelek helyének beállítása

Állítsa be a kézbesítetlen levelek helyét, szükség van egy storage-fiók rendelkezés eseményeket, amelyek a végpont nem lehet kézbesíteni. A példák egy meglévő tárfiókot erőforrás Azonosítójának lekéréséhez. Akkor hozzon létre egy esemény-előfizetést, amely a tárolót használja a tárfiók a kézbesíthetetlen levelek végpont.

> [!NOTE]
> Hozzon létre egy tárfiókot és a egy blobtárolót a storage-ban Ez a cikk a parancsok futtatása előtt.

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

Kikapcsolja a kézbesítetlen levelek kezelése, futtassa újra a parancsot az esemény-előfizetés létrehozásához, de nem ad meg értéket `deadletter-endpoint`. Az Eseményelőfizetés törlése nincs szükségünk.

> [!NOTE]
> Azure CLI-vel használ a helyi gépén, ha használja az Azure CLI verziója 2.0.56 vagy nagyobb. Az Azure CLI legújabb verziójának telepítéséről útmutatásért lásd: [az Azure CLI telepítése](/cli/azure/install-azure-cli).

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$containername = "testcontainer"

$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzureRmStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

Kikapcsolja a kézbesítetlen levelek kezelése, futtassa újra a parancsot az esemény-előfizetés létrehozásához, de nem ad meg értéket `DeadLetterEndpoint`. Az Eseményelőfizetés törlése nincs szükségünk.

> [!NOTE]
> Az Azure-bővítményén használja a helyi gépén, ha az Azure PowerShell 1.1.0-s verzió vagy újabb. Töltse le és telepítse az Azure PowerShell legújabb verzióját a [Azure letöltések](https://azure.microsoft.com/downloads/).

## <a name="set-retry-policy"></a>Újrapróbálkozási szabályzat beállítása

Event Grid-előfizetés létrehozásakor beállíthatja mennyi ideig kell próbálja az Event Grid, hogy az esemény értékeit. Alapértelmezés szerint a Event Grid 24 óra (1440 perc), illetve 30 alkalommal próbálkozik. Ezek az értékek valamelyike beállíthatja az event grid-előfizetés számára. A time-to-live event értékét 1-től 1440 egész szám lehet. Az újrapróbálkozások maximális értékét 30 1 közötti egész számnak kell lennie.

Nem lehet konfigurálni a [újrapróbálkozási ütemezés](delivery-and-retry.md#retry-schedule-and-duration).

### <a name="azure-cli"></a>Azure CLI

Az esemény time-to-live 1440 perc értéktől létrehozásához használja:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

A maximális újrapróbálkozások 30-től eltérő érték beállításához használja:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Ha mind `event-ttl` és `max-deliver-attempts`, Event Grid használja az első, hogy mikor állítsa le a eseménykézbesítés lejár.

### <a name="powershell"></a>PowerShell

Az esemény time-to-live 1440 perc értéktől létrehozásához használja:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

A maximális újrapróbálkozások 30-től eltérő érték beállításához használja:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

Ha mind `EventTtl` és `MaxDeliveryAttempt`, Event Grid használja az első, hogy mikor állítsa le a eseménykézbesítés lejár.

## <a name="next-steps"></a>További lépések

* Egy mintaalkalmazás, amely egy Azure-függvényalkalmazást kézbesítetlen levelek események feldolgozásához használja, lásd: [Azure Event Grid kézbesítetlen levelek minták a .NET-hez](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* Eseménykézbesítés és újrapróbálkozás [Event Grid az üzenetek kézbesítését, és ismételje meg](delivery-and-retry.md).
* Az Event Grid megismeréséhez tekintse meg [az Event Grid bevezetőjét](overview.md).
* Tekintse meg a gyors kezdéshez Event Grid használatával [az Azure Event Griddel egyéni események létrehozása és útvonal](custom-event-quickstart.md).

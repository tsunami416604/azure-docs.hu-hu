---
title: Kézbesítetlen levelek és újrapróbálkozási szabályzatok az Azure Event Grid-előfizetések
description: Az Event Griddel kapcsolatos esemény Kézbesítési beállítások testreszabásának módját ismerteti. A kézbesíthetetlen levelek célkiszolgálón állítsa be, és adja meg, mennyi ideig kézbesítési újra.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: fcf3ecaff6e8ba1421496a96d01428946cf8ab8e
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077782"
---
# <a name="dead-letter-and-retry-policies"></a>Kézbesítetlen levelek és újrapróbálkozási szabályzatok

Egy esemény-előfizetés létrehozásakor testre szabható eseménykézbesítés beállításait. Ez a cikk bemutatja, hogyan állítsa be a kézbesítetlen levelek helyét, és az újrapróbálkozási beállítások testreszabása. További információt szeretne ezekről a funkciókról: [Event Grid az üzenetek kézbesítését, és ismételje meg](delivery-and-retry.md).

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-dead-letter-location"></a>Kézbesíthetetlen levelek helyének beállítása

Állítsa be a kézbesítetlen levelek helyét, szükség van egy storage-fiók rendelkezés eseményeket, amelyek a végpont nem lehet kézbesíteni. A következő parancsfájl beolvassa egy meglévő tárfiókot az erőforrás-Azonosítóját, és létrehoz egy esemény-előfizetést, amely a tárolót használja a tárfiók a kézbesíthetetlen levelek végpont.

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

storagename=demostorage
containername=testcontainer

storageid=$(az storage account show --name $storagename --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Kikapcsolja a kézbesítetlen levelek kezelése, futtassa újra a parancsot az esemény-előfizetés létrehozásához, de nem ad meg értéket `deadletter-endpoint`. Az Eseményelőfizetés törlése nincs szükségünk.

## <a name="set-retry-policy"></a>Újrapróbálkozási szabályzat beállítása

Event Grid-előfizetés létrehozásakor beállíthatja mennyi ideig kell próbálja az Event Grid, hogy az esemény értékeit. Alapértelmezés szerint az Event Grid kísérletek 24 óra (1440 perc), és legfeljebb 30 alkalommal próbálkozik. Ezek az értékek valamelyike beállíthatja az event grid-előfizetés számára. A time-to-live event értékét 1-től 1440 egész szám lehet. Kézbesítések maximális kísérletek értéke 30 1 közötti egész számnak kell lennie.

Nem lehet konfigurálni a [újrapróbálkozási ütemezés](delivery-and-retry.md#retry-schedule-and-duration).

Az esemény time-to-live 1440 perc értéktől létrehozásához használja:

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

A maximális újrapróbálkozási kísérletek 30-től eltérő érték beállításához használja:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Ha mind `event-ttl` és `max-deliver-attempts`, Event Grid használja az első az újrapróbálkozási kísérletek lejár.

## <a name="next-steps"></a>További lépések

* Egy mintaalkalmazás, amely egy Azure-függvényalkalmazást kézbesítetlen levelek események feldolgozásához használja, lásd: [Azure Event Grid kézbesítetlen levelek minták a .NET-hez](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* Eseménykézbesítés és újrapróbálkozás [Event Grid az üzenetek kézbesítését, és ismételje meg](delivery-and-retry.md).
* Az Event Grid megismeréséhez tekintse meg [az Event Grid bevezetőjét](overview.md).
* Tekintse meg a gyors kezdéshez Event Grid használatával [az Azure Event Griddel egyéni események létrehozása és útvonal](custom-event-quickstart.md).

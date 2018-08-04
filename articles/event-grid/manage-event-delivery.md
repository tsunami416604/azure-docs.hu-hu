---
title: Kézbesítetlen levelek és újrapróbálkozási szabályzatok az Azure Event Grid-előfizetések
description: Az Event Griddel kapcsolatos esemény Kézbesítési beállítások testreszabásának módját ismerteti. A kézbesíthetetlen levelek célkiszolgálón állítsa be, és adja meg, mennyi ideig kézbesítési újra.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: tomfitz
ms.openlocfilehash: 5a37fadc179157ba590b31a79fcd98f223cb1869
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39501949"
---
# <a name="dead-letter-and-retry-policies"></a>Kézbesítetlen levelek és újrapróbálkozási szabályzatok

Egy esemény-előfizetés létrehozásakor testre szabható eseménykézbesítés beállításait. Beállíthatja, hogy mennyi ideig Event Grid próbál kézbesíteni az üzenetet. Beállíthat egy eseményeket, amelyek nem sikerült kézbesíteni a végpont tárolására szolgáló tárfiókot.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-dead-letter-location"></a>Kézbesíthetetlen levelek helyének beállítása

Event Grid nem lehet kézbesíteni az egy eseményt, amikor azokat küldeni tudná, a kézbesítetlen esemény egy tárfiókba. Ez a folyamat az úgynevezett kézbesítetlen levelek kezelése. Alapértelmezés szerint a nem az Event Grid kapcsolja be a kézbesítetlen levelek kezelése. Az engedélyezéshez, egy storage-fiókot, amely tárolja a kézbesítetlen események az esemény-előfizetés létrehozásakor adjon meg. Ezt a tárfiókot, a szállítások megoldásához események lekéri.

Event Grid egy esemény Ha próbálta az újrapróbálkozási kísérletek mindegyikét, vagy kap olyan hibaüzenetet, amely azt jelzi, ha soha nem lesz sikeres, szállítási a kézbesíthetetlen levelek helyre küldi. Például ha Event Grid helytelen formátumú hibát kap, amikor egy eseményt továbbít, azt az esemény a kézbesíthetetlen levelek helyre küldi. Öt perces késleltetés van, hogy egy eseményt, és ha biztosítását a kézbesíthetetlen levelek helyre a legutóbbi kísérlet között. Ez a késleltetés csökkentése érdekében a Blob storage művelet célja. A kézbesíthetetlen levelek hely 4 órán keresztül nem érhető el, ha az eseményt a rendszer eldobja.

A kézbesíthetetlen levelek helyének beállítása, mielőtt egy tároló tárfiók kell rendelkeznie. A végpont megadása a tároló az esemény-előfizetés létrehozásakor. A végpont formátuma van: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

A következő parancsfájl beolvassa egy meglévő tárfiókot az erőforrás-Azonosítóját, és létrehoz egy esemény-előfizetést, amely a tárolót használja a tárfiók a kézbesíthetetlen levelek végpont.

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

Event Grid használatára kézbesítetlen válaszadására [esemény-előfizetés létrehozása](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) a kézbesíthetetlen levelek a blob Storage. Minden alkalommal, amikor a kézbesíthetetlen levelek blob storage-kézbesítetlen eseményt kap, az Event Grid értesíti a kezelő. A kezelő műveletekhez tartozó kézbesítetlen események összeegyezteti szeretné válaszol. 

Kikapcsolja a kézbesítetlen levelek kezelése, futtassa újra a parancsot az esemény-előfizetés létrehozásához, de nem ad meg értéket `deadletter-endpoint`. Az Eseményelőfizetés törlése nincs szükségünk.

## <a name="set-retry-policy"></a>Újrapróbálkozási szabályzat beállítása

Event Grid-előfizetés létrehozásakor beállíthatja mennyi ideig kell próbálja az Event Grid, hogy az esemény értékeit. Alapértelmezés szerint az Event Grid kísérletek 24 óra (1440 perc), és legfeljebb 30 alkalommal próbálkozik. Ezek az értékek valamelyike beállíthatja az event grid-előfizetés számára. A time-to-live event értékét 1-től 1440 egész szám lehet. Kézbesítések maximális kísérletek értéke 30 1 közötti egész számnak kell lennie.

Nem lehet konfigurálni a [újrapróbálkozási időköz](delivery-and-retry.md#retry-intervals-and-duration).

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

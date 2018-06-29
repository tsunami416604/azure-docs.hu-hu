---
title: Az Azure Event rács előfizetések Kézbesítési beállítások kezelése
description: Az esemény a Kézbesítési beállítások esemény rács testreszabásának módját ismerteti.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: tomfitz
ms.openlocfilehash: 65e79f492e96c418502e096b60992ba992868dd7
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036111"
---
# <a name="manage-event-grid-delivery-settings"></a>Esemény rács Kézbesítési beállítások kezelése

Egy esemény előfizetés létrehozásakor testre szabhatja az esemény kézbesítési beállításait. Beállíthatja, hogy mennyi ideig esemény rács megpróbálja kézbesíteni az üzenetet. A tárfiók eseményeket, amelyek a végpont nem kézbesíthető tárolására használandó állíthatja be.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-retry-policy"></a>Set újrapróbálkozási házirendje

Egy esemény rács előfizetés létrehozásakor megadhatja, hogy mennyi ideig esemény rács kísérelje meg az esemény biztosításához az értékeket. Alapértelmezés szerint az esemény rács kísérletek 24 óra (1440 perc), és legfeljebb 30 alkalommal próbálkozik. Az esemény rács előfizetéséhez beállíthat két érték.

Az esemény időpontja a működés közbeni értéktől eltérőre 1440 perc használja:

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
  --event-ttl 720
```

Az újrapróbálkozási kísérletek értéktől eltérőre 30 használja:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
  --max-delivery-attempts 18
```

Ha mind `event-ttl` és `max-deliver-attempts`, esemény rács használja az első az újrapróbálkozási kísérletek lejár.

## <a name="set-dead-letter-location"></a>Kézbesítetlen levelek hely beállítása

Esemény rács nem biztosítanak egy eseményt, amikor a kézbesítetlen esemény is küldheti tárfiókba. Ezt a folyamatot nevezik kézbesítetlen levelek kezelése. Alapértelmezés szerint a nem az esemény rács kapcsolja be a kézbesítetlen levelek kezelése. Az engedélyezéshez egy tárfiókot, tartsa kézbesítetlen események az esemény-előfizetés létrehozásakor adjon meg. Ez a tárfiók kézbesítések megoldásához származó események meg lekéréses.

Esemény rács a kézbesítetlen levelek helyre küld egy eseményt, ha megpróbálta az újrapróbálkozási kísérletek mindegyikét, vagy kap olyan hibaüzenetet, amely azt jelzi, ha kézbesítési soha nem fog sikerülni. Például ha esemény rács egy nem megfelelő formátumú hibaüzenetet kap, ha olyan esemény, azonnal küld adott eseményt a kézbesítetlen levelek helyre.

A kézbesítetlen levelek helyének beállítása előtt rendelkeznie kell a tárfiók egy tárolót. A végpont a tároló biztosítanak, amikor az esemény-előfizetés létrehozása. A végpont formátumban kell megadni: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

A következő parancsfájl lekérdezi az erőforrás-azonosítója, meglévő tárfiókot, és létrehoz olyan esemény-előfizetéssel, amely egy tárolót használja a tárfiókban lévő a kézbesítetlen levelek végpont.

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
  --endpoint <endpoint_URL>
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Kézbesítetlen események válaszolni esemény rács használandó [esemény előfizetéssel, hozzon létre](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) a kézbesítetlen levelek blob Storage. Minden alkalommal, amikor a kézbesítetlen levelek blob-tároló kézbesítetlen esemény érkezik, az esemény rács értesíti a kezelő. A kezelő válaszol, a kézbesítetlen események feloldása vállalja műveletek. 

## <a name="next-steps"></a>További lépések

* Esemény kézbesítési és, az újrapróbálkozásokat információt [esemény rács üzenetkézbesítést, és próbálkozzon újra](delivery-and-retry.md).
* Az Event Grid megismeréséhez tekintse meg [az Event Grid bevezetőjét](overview.md).
* Ha gyorsan esemény rács segítségével, lásd: [Azure esemény rácshoz hozza létre és útvonal egyéni események](custom-event-quickstart.md).

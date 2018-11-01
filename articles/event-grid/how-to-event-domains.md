---
title: Nagy adatkészleteken, az Azure Event Grid témakörök kezelése, és események közzététele megadásával az esemény-tartományok
description: Bemutatja, hogyan létrehozása és kezelése az Azure Event Grid témaköreiben és közzé őket az esemény tartományok eseményeket.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 48a5356b03e38e864ba76f048febdb0b040893f5
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634049"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Témakörök kezelése és az esemény tartományok használatával közzé az eseményeket

Ez a cikk bemutatja, hogyan lehet:

* Hozzon létre egy Event Grid-tartomány
* Témákra iratkozhat fel
* Kulcsok listázása
* Események közzététele egy tartományhoz

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Hozzon létre egy esemény-tartomány

Egy esemény-tartomány létrehozása keresztül lehetséges az `eventgrid` bővítmény [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Miután létrehozott egy tartományhoz, a témakörök nagyméretű adatkészletek kezeléséhez használhatja.

```azurecli-interactive
# if you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name>
  -l <location>
```

Sikeres létrehozás adja vissza a következő:

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

Megjegyzés: a `endpoint` és `id` módon kezelheti a tartományhoz, és közzé az eseményeket kell.

## <a name="create-topics-and-subscriptions"></a>Üzenettémák és előfizetések létrehozása

Az Event Grid szolgáltatás automatikusan létrehozza és kezeli az adott témakör egy tartomány alapján hozzon létre egy esemény-előfizetést egy tartományhoz a témakörben a hívást. Nincs semmilyen külön lépést a témakör létrehozásához egy tartományban. Hasonlóképpen egy adott üzenettémához az utolsó esemény-előfizetés törlése esetén a témakör törlődik is.

Ugyanaz, mint bármely más Azure-erőforrás előfizetés feliratkozik a témakörre tartományban:

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/<my-topic>" \
  --endpoint https://contoso.azurewebsites.net/api/f1?code=code
```

A megadott erőforrás-azonosító adja vissza, ha a tartományt korábban hoz létre ugyanazzal az Azonosítóval. Adja meg a kívánt feliratkozás témakörre, adjon hozzá `/topics/<my-topic>` végére az erőforrás-azonosítója.

A tartományi hatókör esemény-előfizetés létrehozása, amely a tartomány összes eseményt fogad, adjon a tartományhoz, mint a `resource-id` bármely, itt például megadása nélkül `/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>`.

Ha egy teszt végpont fizethet elő az eseményeket, mindig telepíthet egy [előre elkészített webalkalmazás](https://github.com/Azure-Samples/azure-event-grid-viewer) , amely a bejövő eseményeket jeleníti meg. Az eseményeket küldhet a teszt webhellyel `https://<your-site-name>.azurewebsites.net/api/updates`.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

Az engedélyeket, amelyek be vannak állítva a témakör az Azure Active Directoryban tárolják, és explicit módon kell törölni. Egy esemény-előfizetés törlése, nem egy esemény-előfizetések létrehozása, ha írási hozzáféréssel rendelkeznek a témakör a felhasználók hozzáférésének visszavonása.

## <a name="manage-access-to-topics"></a>Témakörök való hozzáférés kezelése

Témakörök való hozzáférés felügyelete meghatározhatják [szerepkör-hozzárendelés](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-cli). Szerepkör-hozzárendelést használ szerepkör alapú hozzáférés-ellenőrzési műveletek az Azure-erőforrások egy bizonyos hatókörben jogosult felhasználókra korlátozzák.

Event Grid használatával adott felhasználók hozzárendelése egy tartományon belüli különböző témakörök hozzáférést két beépített szerepkört tartalmaz. Ezek a szerepkörök felelnek `EventGrid EventSubscription Contributor (Preview)`, amely lehetővé teszi a létrehozása vagy törlése, előfizetések, a és `EventGrid EventSubscription Reader (Preview)`, amely csak lehetővé teszi az esemény-előfizetések listája.

A következő parancs korlátozza `alice@contoso.com` létrehozásához, és csak a témakör az esemény-előfizetések törlése `foo`:

```azurecli-interactive
az role assignment create --assignee alice@contoso.com --role "EventGrid EventSubscription Contributor (Preview)" --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/foo
```

Lásd: [Event Grid biztonsági és hitelesítési](./security-authentication.md) további információk:

* Felügyeleti hozzáférés-vezérlése
* Művelet típusa
* Egyéni szerepkör-definíciók létrehozása

## <a name="publish-events-to-an-event-grid-domain"></a>Események közzétételét egy Event Grid-tartomány

Események közzétételét egy tartomány pedig ugyanaz, mint [közzététele egy egyéni témakör](./post-to-custom-topic.md). Az egyetlen különbség, hogy meg kell adnia minden egyes esemény nyissa meg a kívánt témakör. Az alábbi tömböt események az esemény eredményezne `"id": "1111"` témakörbe `foo` esemény közben `"id": "2222"` küldi el a témakör `bar`:

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

A kulcsok egy tartomány használja:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

Majd a kedvenc módszer az, hogy egy HTTP POST-közzé az eseményeket az Event Grid tartomány.

## <a name="next-steps"></a>További lépések

* Az esemény-tartományok és az azok hasznos fogalmait további információkért lásd: a [esemény-tartomány fogalmi áttekintése](./event-domains.md).
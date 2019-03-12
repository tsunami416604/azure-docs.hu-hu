---
title: Nagyméretű adatkészletek Azure Event Grid témaköreiben az esemény tartományok kezelése
description: Bemutatja, hogyan Nagy adatkészleteken, az Azure Event Grid témakörök kezelése és események közzétételére esemény tartomány használatával.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 01/17/2019
ms.openlocfilehash: 63a71065fc168f5eb80f19b93c014bdacc5af7e6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542250"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Témakörök kezelése és az esemény tartományok használatával közzé az eseményeket

Ez a cikk bemutatja, hogyan lehet:

* Hozzon létre egy Event Grid-tartomány
* Fizessen elő az event grid-témakörök
* Kulcsok listázása
* Események közzététele egy tartományhoz

Esemény tartományok kapcsolatos további információkért lásd: [esemény tartományok Event Grid-témakörök felügyeletére megértéséhez](event-domains.md).

## <a name="install-preview-feature"></a>Előzetes verziójú funkció telepítése

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Hozzon létre egy esemény-tartomány

Témakörök nagy készleteinek kezelése, hozzon létre egy esemény-tartományhoz.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```

Sikeres létrehozás a következő értékeket adja vissza:

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

Megjegyzés: a `endpoint` és `id` módon kezelheti a tartományhoz, és közzé az eseményeket kell azokat.

## <a name="manage-access-to-topics"></a>Témakörök való hozzáférés kezelése

Témakörök való hozzáférés felügyelete meghatározhatják [szerepkör-hozzárendelés](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli). Szerepkör-hozzárendelés szerepköralapú hozzáférés-vezérlés használatával műveletek az Azure-erőforrások egy bizonyos hatókörben jogosult felhasználókra korlátozzák.

Event Grid két beépített szerepkörök, amelyek használatával adott felhasználók egy tartományon belüli különböző témakörök a hozzáférés hozzárendelése rendelkezik. Ezek a szerepkörök felelnek `EventGrid EventSubscription Contributor (Preview)`, amely lehetővé teszi a létrehozása vagy törlése, előfizetések, a és `EventGrid EventSubscription Reader (Preview)`, amely csak lehetővé teszi az esemény-előfizetések listája.

Az alábbi Azure CLI-parancs korlátok `alice@contoso.com` létrehozásához, és csak a témakör az esemény-előfizetések törlése `demotopic1`:

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

Az alábbi PowerShell-parancs korlátok `alice@contoso.com` létrehozásához, és csak a témakör az esemény-előfizetések törlése `demotopic1`:

```azurepowershell-interactive
New-AzureRmRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

Event Grid műveletek hozzáférés kezelésével kapcsolatos további információkért lásd: [Event Grid biztonsági és hitelesítési](./security-authentication.md).

## <a name="create-topics-and-subscriptions"></a>Üzenettémák és előfizetések létrehozása

Az Event Grid szolgáltatás automatikusan létrehozza és kezeli az adott témakör egy tartomány alapján hozzon létre egy esemény-előfizetést egy tartományhoz a témakörben a hívást. Nincs semmilyen külön lépést a témakör létrehozásához egy tartományban. Hasonlóképpen egy adott üzenettémához az utolsó esemény-előfizetés törlése esetén a témakör törlődik is.

Ugyanaz, mint bármely más Azure-erőforrás előfizetés feliratkozik a témakörre tartományban. Az adatforrás erőforrás-azonosító adja meg az eseményazonosító-tartományt adja vissza, ha a tartományt korábban hoz létre. Adja meg a kívánt feliratkozás témakörre, adjon hozzá `/topics/<my-topic>` végéig a erőforrás azonosítóját. A tartományi hatókör esemény-előfizetés létrehozása, amely a tartomány összes eseményt fogad, adja meg az eseményazonosító-tartomány bármely, itt megadása nélkül.

Általában a felhasználó, hozzáférést kap az az előző szakaszban az előfizetéshez kell létrehoznia. A cikk egyszerűsítése érdekében hozzon létre az előfizetést. 

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

Ha egy teszt végpont fizethet elő az eseményeket, mindig telepíthet egy [előre elkészített webalkalmazás](https://github.com/Azure-Samples/azure-event-grid-viewer) , amely a bejövő eseményeket jeleníti meg. Az eseményeket küldhet a teszt webhellyel `https://<your-site-name>.azurewebsites.net/api/updates`.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

Az engedélyeket, amelyek be vannak állítva a témakör az Azure Active Directoryban tárolják, és explicit módon kell törölni. Egy esemény-előfizetés törlése, nem egy esemény-előfizetések létrehozása, ha írási hozzáféréssel rendelkeznek a témakör a felhasználók hozzáférésének visszavonása.


## <a name="publish-events-to-an-event-grid-domain"></a>Események közzétételét egy Event Grid-tartomány

Események közzétételét egy tartomány pedig ugyanaz, mint [közzététele egy egyéni témakör](./post-to-custom-topic.md). A közzététel az egyéni témakörbe helyett az összes esemény közzététele a tartomány-végpontra. A JSON-esemény adatokat adja meg a témakör az eseményeket, nyissa meg a kívánt. Az alábbi tömböt események az esemény eredményezne `"id": "1111"` témakörbe `demotopic1` esemény közben `"id": "2222"` küldi el a témakör `demotopic2`:

```json
[{
  "topic": "demotopic1",
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
  "topic": "demotopic2",
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

Az Azure CLI-vel a tartomány végpontot használja

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

A kulcsokat a tartomány lekérdezéséhez használja:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

A PowerShell-lel a tartomány végpontot használja

```azurepowershell-interactive
Get-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

A kulcsokat a tartomány lekérdezéséhez használja:

```azurepowershell-interactive
Get-AzureRmEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

Majd a kedvenc módszer az, hogy egy HTTP POST-közzé az eseményeket az Event Grid-tartomány.

## <a name="next-steps"></a>További lépések

* Az esemény-tartományok és az azok hasznos fogalmait további információkért lásd: a [esemény-tartomány fogalmi áttekintése](event-domains.md).
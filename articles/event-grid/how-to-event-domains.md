---
title: Események közzététele eseménytartománnyal az Azure Event Griddel
description: Bemutatja, hogyan kezelheti a témakörök nagy készleteit az Azure Event Gridben, és hogyan tehet közzé eseményeket az eseménytartományok használatával.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: 1d07227249806b7d54523af66817a170c19354ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72786553"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Témakörök kezelése és események közzététele eseménytartományok használatával

Ez a cikk bemutatja, hogyan:

* Eseményrács tartomány létrehozása
* Feliratkozás az eseményrács témaköreire
* Billentyűk listázása
* Események közzététele tartományban

Az eseménytartományokról az [Eseményrács témakörök kezelésének ismertetése](event-domains.md)című témakörben olvashat.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Az előzetes verzió telepítése szolgáltatás

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Eseménytartomány létrehozása

Nagy témakörök kezeléséhez hozzon létre egy eseménytartományt.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

# <a name="powershell"></a>[Powershell](#tab/powershell)
```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```
---

A sikeres létrehozás a következő értékeket adja vissza:

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

Vegye `endpoint` figyelembe `id` a és a tartomány kezeléséhez és az események közzétételéhez szükséges.

## <a name="manage-access-to-topics"></a>Témakörökhöz való hozzáférés kezelése

A témakörökhöz való hozzáférés kezelése [szerepkör-hozzárendeléssel](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)történik. Szerepkör-hozzárendelés szerepköralapú hozzáférés-vezérlés segítségével korlátozza az Azure-erőforrások on-k on-t egy adott hatókörben jogosult felhasználók számára.

Az Event Grid két beépített szerepkörrel rendelkezik, amelyek segítségével adott felhasználók hoznak hozzáférést a tartomány különböző témaköreihez. Ezek a `EventGrid EventSubscription Contributor (Preview)`szerepkörök a , amely lehetővé teszi `EventGrid EventSubscription Reader (Preview)`az előfizetések létrehozását és törlését, és amelyek csak az esemény-előfizetések listázását teszik lehetővé.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)
A következő Azure CLI parancs csak `alice@contoso.com` a témában korlátozza `demotopic1`az esemény-előfizetések létrehozását és törlését:

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

# <a name="powershell"></a>[Powershell](#tab/powershell)
A következő PowerShell-parancs `alice@contoso.com` csak a témában korlátozza `demotopic1`az esemény-előfizetések létrehozását és törlését:

```azurepowershell-interactive
New-AzureRmRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```
---

Az Event Grid-műveletek hez való hozzáférés kezeléséről az [Event Grid biztonsága és hitelesítése](./security-authentication.md)című témakörben talál további információt.

## <a name="create-topics-and-subscriptions"></a>Témakörök és előfizetések létrehozása

Az Event Grid szolgáltatás automatikusan létrehozza és kezeli a megfelelő témakört egy tartományban a tartománytémakör esemény-előfizetésének létrehozásához szükséges hívás alapján. Nincs külön lépés a tartományban lévő témakör létrehozásához. Hasonlóképpen, ha egy témakör utolsó esemény-előfizetése törlődik, a témakör is törlődik.

A tartomány ban lévő témakörre való feliratkozás megegyezik egy másik Azure-erőforrásra való feliratkozással. A forráserőforrás-azonosítóhoz adja meg a tartomány korábbi létrehozásakor visszaadott eseménytartomány-azonosítót. Az előfizetni kívánt témakör megadásához `/topics/<my-topic>` adja hozzá a forráserőforrás-azonosító végéhez. Ha olyan tartományhatókör-esemény-előfizetést szeretne létrehozni, amely a tartomány összes eseményét fogadja, adja meg az eseménytartomány-azonosítót anélkül, hogy bármilyen témakört megadna.

Általában az előző szakaszban megadott felhasználó hozza létre az előfizetést. A cikk egyszerűsítése érdekében hozza létre az előfizetést. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

---

Ha szüksége van egy tesztvégpontra az események előjegyzéséhez, bármikor üzembe helyezhet egy [előre elkészített webalkalmazást,](https://github.com/Azure-Samples/azure-event-grid-viewer) amely megjeleníti a bejövő eseményeket. Az eseményeket elküldheti a `https://<your-site-name>.azurewebsites.net/api/updates`tesztwebhelyére a .

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

A témakörhöz beállított engedélyek et az Azure Active Directory tárolja, és explicit módon törölni kell. Az esemény-előfizetés törlése nem vonja vissza a felhasználók hozzáférését az esemény-előfizetések létrehozásához, ha írási hozzáféréssel rendelkeznek egy témához.


## <a name="publish-events-to-an-event-grid-domain"></a>Események közzététele eseményrács-tartományban

Az események tartományban való közzététele megegyezik az [egyéni témakörben való közzététellel.](./post-to-custom-topic.md) Az egyéni témakörben való közzététel helyett azonban az összes eseményt közzéteszi a tartomány végpontjára. A JSON-eseményadatokban megadhatja azt a témakört, amelyhez az eseményeket meg szeretné adni. A következő tömb az események `"id": "1111"` azt `demotopic1` eredményezné, `"id": "2222"` hogy az `demotopic2`esemény a téma, míg az esemény lenne küldeni a téma:

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)
A tartományvégpont azure CLI-vel való beszerezéséhez használja a

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

Tartomány kulcsainak lekérnie, használja a következőket:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

# <a name="powershell"></a>[Powershell](#tab/powershell)
A tartományvégpont powershell-lel való lefelvételéhez használja a

```azurepowershell-interactive
Get-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

Tartomány kulcsainak lekérnie, használja a következőket:

```azurepowershell-interactive
Get-AzureRmEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```
---

Ezután a http-posta rendszer készítésének kedvenc módszerével tegye közzé az eseményeket az Event Grid tartományban.

## <a name="next-steps"></a>További lépések

* Az Eseménytartományok magas szintű fogalmairól és azok hasznosságáról az [Eseménytartományok fogalmi áttekintésében olvashat bővebben.](event-domains.md)

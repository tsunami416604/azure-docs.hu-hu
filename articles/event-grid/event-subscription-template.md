---
title: "Az Azure rács Eseményelőfizetés-sablonhoz"
description: "Egy esemény rács előfizetés létrehozása a Resource Manager sablonnal."
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/05/2018
ms.author: tomfitz
ms.openlocfilehash: 2b9f55f8e944d688b622e30a773e1a34698f22ec
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
---
# <a name="use-resource-manager-template-for-event-grid-subscription"></a>Esemény rács az előfizetéshez használatban Resource Manager sablon

Ez a cikk bemutatja, hogyan esemény rács előfizetések létrehozása az Azure Resource Manager-sablon használatával. A formátum használata eltér e előfizetés, az erőforrás-események csoportosítása, vagy egy adott erőforrástípusra események alapján. Ez a cikk mindkét formátumot láthatók.

## <a name="subscribe-to-resource-group-events"></a>Fizessen elő a erőforrás események csoportosítása

Előfizetés erőforrás események csoportosítása, ha `Microsoft.EventGrid/eventSubscriptions` az erőforrástípus. Esemény terjesztésipont-típus, mindkét `WebHook` vagy `EventHub`.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "type": "Microsoft.EventGrid/eventSubscriptions",
            "name": "mySubscription",
            "apiVersion": "2017-09-15-preview",
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "https://requestb.in/ynboxiyn"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false,
                    "includedEventTypes": ["All"]
                }
            }
        }
    ]
}
```

Ez a sablon telepítésekor az erőforráscsoporthoz előfizet események erőforráscsoporthoz.

## <a name="subscribe-to-resource-events"></a>Fizessen elő a erőforrás-események

Amikor az erőforrás-események előfizetés, rendelje hozzá az előfizetést, hogy a helyes erőforrás az beleértve az előfizetés-definícióban az erőforrás típusa és neve. Az erőforrás típusára vonatkozó használja `<provider-namespace>/<resource-type>/providers/eventSubscriptions`. A nevet használja `<resource-name>/Microsoft.EventGrid/<subscription-name>`. Esemény terjesztésipont-típus, mindkét `WebHook` vagy `EventHub`.

A következő példa bemutatja, hogyan szeretne előfizetni a Blob storage-események.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
            "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/myStorageSubscription')]",
            "apiVersion": "2017-09-15-preview",
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "https://requestb.in/ynboxiyn"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>További lépések

* Esemény rácshoz ismertetőért lásd: [esemény rács](overview.md).
* Az erőforrás-kezelő ismertetőért lásd: [Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md).
* Első lépésként esemény rácshoz, lásd: [Azure esemény rácshoz hozza létre és útvonal egyéni események](custom-event-quickstart.md).
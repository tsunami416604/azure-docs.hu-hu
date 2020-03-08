---
title: Az Azure Event Grid biztonsági és hitelesítés
description: Az Azure Event Gridet és a vele kapcsolatos fogalmakat ismerteti.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: babanisa
ms.openlocfilehash: 03bc2f9de6f50f08c9f62f86a3d1791a067cecd0
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899281"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Event Grid erőforrásokhoz való hozzáférés engedélyezése
Az Azure Event Grid lehetővé teszi a különböző felügyeleti műveleteket, például az esemény-előfizetések listája, újakat hoz létre, és hozzon létre kulcsokat, a különböző felhasználókhoz megadott hozzáférési szintet szabályozza. Event Grid használja az Azure szerepköralapú hozzáférés-vezérlés (RBAC).

## <a name="operation-types"></a>Művelet típusa

Event Grid támogatja a következő műveleteket:

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

Az utolsó három műveletek potenciálisan titkos információt, amely lekérdezi a normál olvasási műveletek szűrt adja vissza. Javasoljuk, hogy korlátozza a hozzáférést ezeket a műveleteket. 

## <a name="built-in-roles"></a>Beépített szerepkörök

Event Grid két beépített szerepkörrel biztosít esemény-előfizetések kezeléséhez. Fontosak az esemény- [tartományok](event-domains.md) megvalósításakor, mert a felhasználóknak az adott esemény tartományában lévő témakörökre való előfizetéshez szükséges engedélyeket adják meg. Ezek a szerepkörök eseményelőfizetések összpontosítanak, és ne adjon hozzáférést műveleteket, mint például a témakörök létrehozására.

[Ezeket a szerepköröket hozzárendelheti egy felhasználóhoz vagy csoportjához](../role-based-access-control/quickstart-assign-role-user-portal.md).

**EventGrid EventSubscription közreműködő**: Event Grid-előfizetési műveletek kezelése

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

**EventGrid EventSubscription-olvasó**: olvasás Event Grid előfizetések

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

## <a name="custom-roles"></a>Egyéni szerepkörök

Ha adja meg az engedélyeket, amelyek eltérnek a beépített szerepkörök van szüksége, létrehozhat egyéni szerepköröket is.

Az alábbiakban a minta Event Grid szerepkör-definíciók, amelyek lehetővé teszik a felhasználók számára a különböző műveleteket. Ezek egyéni szerepkörök nem azonosak a beépített szerepkörök, mivel azok csak az esemény-előfizetések, mint szélesebb körű hozzáférés biztosítása.

**EventGridReadOnlyRole. JSON**: csak olvasási műveletek engedélyezése.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole. JSON**: a korlátozás utáni műveletek engedélyezése, de a törlési műveletek letiltása.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole. JSON**: engedélyezi az összes Event Grid-műveletet.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

Egyéni szerepköröket a [PowerShell](../role-based-access-control/custom-roles-powershell.md), az [Azure CLI](../role-based-access-control/custom-roles-cli.md)és a [Rest](../role-based-access-control/custom-roles-rest.md)használatával hozhat létre.



### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

A Event Grid szolgáltatás által lemezre írt összes eseményt vagy adatfájlt egy Microsoft által felügyelt kulcs titkosítja, amely biztosítja, hogy az inaktív állapotban legyen titkosítva. Emellett az események vagy az adatmegőrzési időszak maximális időtartama 24 óra az [Event Grid újrapróbálkozási házirend](delivery-and-retry.md)betartásával. A Event Grid 24 óra elteltével automatikusan törli az összes eseményt vagy az adatmennyiséget, vagy az esemény élettartama, attól függően, hogy melyik a kisebb.

## <a name="next-steps"></a>További lépések

* A Event Grid bevezetését lásd: [About Event Grid](overview.md)

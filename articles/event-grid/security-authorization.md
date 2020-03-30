---
title: Az Azure Event Grid biztonsága és hitelesítése
description: Az Azure Event Gridet és a vele kapcsolatos fogalmakat ismerteti.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: babanisa
ms.openlocfilehash: 03bc2f9de6f50f08c9f62f86a3d1791a067cecd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899281"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Hozzáférés engedélyezése az Eseményrács erőforrásaihoz
Az Azure Event Grid lehetővé teszi, hogy szabályozhatja a különböző felhasználók számára adott hozzáférési szintet a különböző felügyeleti műveletek, például az esemény-előfizetések listázásához, újak létrehozásához és kulcsok létrehozásához. Az Event Grid az Azure szerepköralapú hozzáférés-vezérlését (RBAC) használja.

## <a name="operation-types"></a>Művelettípusok

Az Event Grid a következő műveleteket támogatja:

* Microsoft.EventGrid/*/olvasás
* Microsoft.EventGrid/*/írás
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

Az utolsó három művelet potenciálisan titkos információkat ad vissza, amelyek kiszűrve lesznek a normál olvasási műveletekből. Javasoljuk, hogy korlátozza a hozzáférést ezekhez a műveletekhez. 

## <a name="built-in-roles"></a>Beépített szerepkörök

Az Event Grid két beépített szerepkört biztosít az esemény-előfizetések kezeléséhez. Fontos, hogy [eseménytartományokat valósítson](event-domains.md) meg, mert megadja a felhasználóknak az eseménytartomány témaköreire való feliratkozáshoz szükséges engedélyeket. Ezek a szerepkörök az esemény-előfizetésekre összpontosítanak, és nem biztosítanak hozzáférést az olyan műveletekhez, mint például a témakörök létrehozása.

[Ezeket a szerepköröket hozzárendelheti egy felhasználóhoz vagy csoporthoz.](../role-based-access-control/quickstart-assign-role-user-portal.md)

**EventGrid EventSubscription Közreműködő**: Event Grid-előfizetési műveletek kezelése

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

**EventGrid EventSubscription-olvasó**: Event Grid-előfizetések olvasása

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

Ha a beépített szerepköröktől eltérő engedélyeket kell megadnia, létrehozhat egyéni szerepköröket.

Az alábbiakban példa Eseményrács szerepkör-definíciók, amelyek lehetővé teszik a felhasználók számára, hogy különböző műveleteket. Ezek az egyéni szerepkörök különböznek a beépített szerepköröktől, mivel szélesebb körű hozzáférést biztosítanak, mint az esemény-előfizetések.

**EventGridReadOnlyRole.json**: Csak írásvédett műveletek engedélyezése.

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

**EventGridNoDeleteListKeysRole.json**: Korlátozott utóműveletek engedélyezése, de a törlési műveletek lehetővé teszi.

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

**EventGridContributorRole.json**: Lehetővé teszi az összes eseményrács-műveletet.

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

Egyéni szerepköröket hozhat létre a [PowerShell,](../role-based-access-control/custom-roles-powershell.md) [az Azure CLI](../role-based-access-control/custom-roles-cli.md)és a [REST](../role-based-access-control/custom-roles-rest.md)használatával.



### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

Az Event Grid szolgáltatás által lemezre írt összes eseményt vagy adatot egy Microsoft által felügyelt kulcs titkosítja, amely biztosítja, hogy az inkban van titkosítva. Ezenkívül az események vagy adatok által megőrzött maximális időtartam 24 óra az [Event Grid újrapróbálkozási házirendjének](delivery-and-retry.md)betartásával. Az Event Grid 24 óra elteltével automatikusan törli az összes eseményt vagy adatot, vagy az esemény az élő adást, attól függően, hogy melyik a kisebb.

## <a name="next-steps"></a>További lépések

* Az Eseményrács bemutatása az [Eseményrács – bemutatkozása:](overview.md)

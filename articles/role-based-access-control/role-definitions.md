---
title: Az Azure RBAC szerepkör-definíciók megértése |} Microsoft Docs
description: Információ a szerepköralapú hozzáférés-vezérlést (RBAC) szerepkör-definíciók és hogyan adhat meg a részletes hozzáféréskezelést az erőforrások egyéni szerepkörök az Azure-ban.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: ''
ms.openlocfilehash: 7a9e257d445ff7dadfe27d1c75cde6f58a393397
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="understand-role-definitions"></a>Szerepkör-definíciók ismertetése

Ha el egy szerepkört működésének vagy létrehozásakor a saját [egyéni szerepkör](custom-roles.md), hasznos lehet megérteni a szerepkörök definiálásának módját. Ez a cikk ismerteti a szerepkör-definíciók részleteit és néhány példa.

## <a name="role-definition-structure"></a>Szerepkör-definíció struktúra

A *szerepkör-definíció* engedélyek gyűjteménye. Nevezik néha csak egy *szerepkör*. Egy szerepkör-definíció végezhető, ilyen például az olvasási műveletek sorolja fel, írási és törlési. Nem hajtható végre, műveletek is tud megjeleníteni. Egy szerepkör-definíciót az alábbi szerkezettel rendelkezik:

```
assignableScopes []
description
id
name
permissions []
  actions []
  notActions []
roleName
roleType
type
```

Műveletek vannak megadva, karakterláncok, amelyek formátuma a következő:

- `Microsoft.{ProviderName}/{ChildResourceType}/{action}`

A `{action}` egy művelet karakterlánc részének erőforrástípus végezhető műveletek típusát határozza meg. Például láthatja a következő karakterláncrészletek `{action}`:

| A művelet substring    | Leírás         |
| ------------------- | ------------------- |
| `*` | A helyettesítő karakter engedélyezi a hozzáférést a karakterláncnak megfelelő összes művelethez. |
| `read` | Lehetővé teszi, hogy olvasási műveletek (GET). |
| `write` | Lehetővé teszi, hogy írási műveletek (PUT, POST és javítás). |
| `delete` | Lehetővé teszi, hogy törli a műveleteket (Törlés). |

Itt a [közreműködő](built-in-roles.md#contributor) szerepkör-definíció JSON formátumban. A helyettesítő karakter (`*`) műveletet `actions` azt jelzi, hogy a rendszerbiztonsági tag ehhez a szerepkörhöz rendelt minden művelet végrehajtására, vagy ez azt jelenti, hogy mindent felügyelhetnek. Ez magában foglalja a jövőben definiált műveletek, az Azure ad hozzá az új erőforrástípusok esetében. A műveletek `notActions` vannak-e vonni `actions`. A következőket a [közreműködő](built-in-roles.md#contributor) szerepkör, `notActions` eltávolítja ezt a szerepkört képes erőforrásokhoz való hozzáférés kezelése és is hozzárendelhetők erőforrásokhoz való hozzáférést.

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="management-operations"></a>Felügyeleti műveletek

Felügyeleti műveletek szerepköralapú hozzáférés-vezérlés van megadva a `actions` és `notActions` szerepkör-definíció szakaszait. Íme néhány példa a felügyeleti műveleteket az Azure-ban:

- A tárfiók való hozzáférés kezelése
- Létrehozására, frissítésére, és egy blob-tároló törlése
- Törölje az erőforráscsoportot és az összes erőforrást

A kezelési hozzáférés nem örökli az adatait. Ez az elkülönítés megakadályozza, hogy a szerepkört és a helyettesítő karakterek (`*`) az adatok korlátlan hozzáféréssel rendelkezik a. Például, ha a felhasználó rendelkezik-e egy [olvasó](built-in-roles.md#reader) szerepkört az előfizetés, majd is megtekinthetik a tárfiókot, de nem tekintheti meg az alapul szolgáló adatokat.

## <a name="actions"></a>műveletek

A `actions` engedélyt megadja a kezelési műveletek, amelyre a szerepkört engedélyezi a hozzáférést. Művelet karakterláncok, amelyek azonosítják az Azure erőforrás-szolgáltatók biztonságos műveletek gyűjteménye. Néhány példa a használt felügyeleti műveletek `actions`.

| A művelet karakterlánc    | Leírás         |
| ------------------- | ------------------- |
| `*/read` | biztosít hozzáférést az olvasási műveletek az összes Azure-erőforrás-szolgáltató minden erőforrástípus esetén.|
| `Microsoft.Compute/*` | engedélyezi a hozzáférést a Microsoft.Compute erőforrás-szolgáltató az összes erőforrástípus összes műveletet.|
| `Microsoft.Network/*/read` | Olvasási műveletek a Microsoft.Network erőforrás-szolgáltató az összes erőforrástípus biztosít hozzáférést.|
| `Microsoft.Compute/virtualMachines/*` | minden műveletet a virtuális gépek és a gyermek típusú erőforrások hozzáférést biztosít.|
| `microsoft.web/sites/restart/Action` | A webalkalmazás újraindítása biztosít hozzáférést.|

## <a name="notactions"></a>NotActions

A `notActions` engedélyt megadja a felügyeleti műveleteket, amelyek ki lettek zárva az engedélyezett a `actions`. Használja a `notActions` engedélyt, ha az engedélyezni kívánt műveletek készletét könnyebben definiált tiltott operatív kizárásával. Egy szerepkör (hatályos engedélyek) által biztosított hozzáférést számított kivonja a `notActions` műveletek a `actions` műveletek.

> [!NOTE]
> Ha egy felhasználó tartozik, amely nem tartalmazza egy műveletet a szerepkör `notActions`, és hozzá van rendelve egy második szerepkör, amely hozzáférést biztosít a műveletet, hogy a felhasználó számára engedélyezett a művelet elvégzéséhez. `notActions` Nincs megtagadási szabály – egyszerűen csak egy kényelmes módot nyújt az engedélyezett műveletek készlet létrehozása, ha az adott műveletek ki lesznek zárva.
>

## <a name="assignablescopes"></a>AssignableScopes

A `assignableScopes` szakaszban határozza meg, hogy a szerepkör érhető el a hozzárendelési hatókör (felügyeleti csoport (jelenleg előzetes verzió), előfizetések, erőforráscsoport-sablonok vagy erőforrások). Elérhetővé teheti a szerepkör hozzárendelés csak a előfizetésekben, vagy azt, és nem a zsúfoltságát felhasználó igénylő erőforráscsoportok élményt a többi részének az előfizetések vagy erőforráscsoportok. Segítségével kell legalább egy felügyeleti csoport, az előfizetés, erőforráscsoport vagy erőforrás-azonosító.

Beépített szerepkörök `assignableScopes` értéke a gyökérszintű hatókörben (`"/"`). A gyökérszintű hatókörben azt jelzi, hogy a szerepkör az összes hatókör kiosztására használható. A saját egyéni szerepkörök nem használhatja a gyökérszintű hatókörben. Ha, egy engedélyezési hiba jelenik meg.

Érvényes hozzárendelhető hatókörök például:

| Eset | Példa |
|----------|---------|
| Szerepkör-hozzárendelést egy-egy előfizetéshez tartozó érhető el | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| Szerepkör hozzárendelés két előfizetésekben érhető el | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| Szerepkör esetén csak a hálózati erőforrás csoport-hozzárendelést érhető el | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| Szerepkör érhető el az összes hatókör hozzárendelése | `"/"` |

## <a name="assignablescopes-and-custom-roles"></a>assignableScopes és egyéni szerepkörök

A `assignableScopes` szakasz egy egyéni biztonsági szerepkört is vezérlők, akik létrehozása, törlése, módosítása vagy megtekintése az egyéni biztonsági szerepkört.

| Feladat | Művelet | Leírás |
| --- | --- | --- |
| Egyéni szerepkör létrehozása vagy törlése | `Microsoft.Authorization/ roleDefinition/write` | Ez a művelet az összes engedéllyel rendelkező felhasználók a `assignableScopes` az egyéni szerepkör létrehozhat (vagy törölhet) egyéni szerepkörök ezeket használható. Például [tulajdonosok](built-in-roles.md#owner) és [felhasználói rendszergazdák](built-in-roles.md#user-access-administrator) előfizetések, erőforráscsoport-sablonok és erőforrások. |
| Egyéni szerepkör módosítása | `Microsoft.Authorization/ roleDefinition/write` | Ez a művelet az összes engedéllyel rendelkező felhasználók a `assignableScopes` az egyéni szerepkör módosíthatja ezeket az egyéni szerepkörök. Például [tulajdonosok](built-in-roles.md#owner) és [felhasználói rendszergazdák](built-in-roles.md#user-access-administrator) előfizetések, erőforráscsoport-sablonok és erőforrások. |
| Egy egyéni biztonsági szerepkört megtekintése | `Microsoft.Authorization/ roleDefinition/read` | Ez a művelet egy hatókörhöz engedéllyel rendelkező felhasználók megtekinthetik az adott hatókörben kiosztására használható egyéni szerepköröket. Az összes beépített szerepkör engedélyezése egyéni szerepkörök hozzárendelés elérhető legyen. |

## <a name="role-definition-examples"></a>Szerepkör-definíció példák

Az alábbi példa azt mutatja meg a [olvasó](built-in-roles.md#reader) szerepkör-definíció jelenik meg, az Azure parancssori felület használatával:

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "permissions": [
      {
        "actions": [
          "*/read"
        ],
        "additionalProperties": {},
        "notActions": [],
      }
    ],
    "roleName": "Reader",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

A következő példa bemutatja egy egyéni biztonsági szerepkört figyelési és virtuális gépek újraindításával megjelenített Azure PowerShell használatával:

```json
{
  "Name":  "Virtual Machine Operator",
  "Id":  "88888888-8888-8888-8888-888888888888",
  "IsCustom":  true,
  "Description":  "Can monitor and restart virtual machines.",
  "Actions":  [
                  "Microsoft.Storage/*/read",
                  "Microsoft.Network/*/read",
                  "Microsoft.Compute/*/read",
                  "Microsoft.Compute/virtualMachines/start/action",
                  "Microsoft.Compute/virtualMachines/restart/action",
                  "Microsoft.Authorization/*/read",
                  "Microsoft.Resources/subscriptions/resourceGroups/read",
                  "Microsoft.Insights/alertRules/*",
                  "Microsoft.Insights/diagnosticSettings/*",
                  "Microsoft.Support/*"
  ],
  "NotActions":  [

                 ],
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

## <a name="see-also"></a>Lásd még

* [Beépített szerepkörök](built-in-roles.md)
* [Egyéni szerepkörök](custom-roles.md)
* [Az Azure Resource Manager erőforrás-szolgáltatói műveletekhez](resource-provider-operations.md)

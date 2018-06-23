---
title: Egyéni szerepkörök az Azure-ban |} Microsoft Docs
description: Útmutató az Azure szerepköralapú hozzáférés-vezérlés (RBAC) egyéni szerepkörök definiálása a részletes hozzáféréskezelést az Azure-erőforrások.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 074c305cb15bc1fb25dfa5cfc52dcce53b661a7e
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321177"
---
# <a name="custom-roles-in-azure"></a>Egyéni szerepkörök az Azure-ban

Ha a [beépített szerepkörök](built-in-roles.md) nem felelnek meg a szervezet igényeinek, saját egyéni szerepköröket is létrehozhat. Hasonlóan a beépített szerepkörök egyéni szerepkörök hozzárendelése felhasználók, csoportok és szolgáltatásnevekről előfizetés, erőforráscsoport és erőforrás-hatókörök. Egyéni szerepkörök az Azure Active Directory (Azure AD) bérlő tárolódnak, és előfizetések között megosztható legyen. Mindegyik bérlő legfeljebb 2000 egyéni szerepkörök is rendelkezhetnek. Egyéni szerepkörök az Azure PowerShell, az Azure parancssori felület vagy a REST API használatával hozhatók létre.

## <a name="custom-role-example"></a>Egyéni szerepkör – példa

Az alábbiakban látható egy egyéni biztonsági szerepkört figyelési és virtuális gépek újraindításával megjelenített Azure PowerShell használatával:

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
  "DataActions":  [

                  ],
  "NotDataActions":  [

                     ],
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

Miután létrehozott egy egyéni biztonsági szerepkört, az Azure portál egy narancssárga erőforrás ikon jelenik meg.

![Egyéni szerepkör ikon](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Egy egyéni biztonsági szerepkört létrehozásának lépései

1. Határozza meg a szükséges engedélyekkel

    Amikor létrehoz egy egyéni biztonsági szerepkört, meg kell tudni, hogy az erőforrás elérhető a engedélyek meghatározásához szolgáltatói műveletekhez. A műveletek listájának megtekintéséhez használja a [Get-AzureRMProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) vagy [az szolgáltató műveletlista](/cli/azure/provider/operation#az-provider-operation-list) parancsok.
    Az egyéni szerepkör engedélyeinek megadásához adja hozzá a műveleteket, így a `actions` vagy `notActions` tulajdonságainak a [szerepkör-definíció](role-definitions.md). Ha az műveleteket, akkor adja hozzá ezeket a `dataActions` vagy `notDataActions` tulajdonságait.

2. Az egyéni szerepkör létrehozása

    Azure PowerShell vagy Azure CLI hozhat létre az egyéni biztonsági szerepkört. Általában meglévő beépített szerepkör kezdődhet, majd módosítsa az igényeinek. Akkor használja a [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) vagy [az szerepkör-definíció létrehozása](/cli/azure/role/definition#az-role-definition-create) parancsok futtatásával hozza létre az egyéni biztonsági szerepkört. Hozzon létre egy egyéni biztonsági szerepkört, kell rendelkeznie a `Microsoft.Authorization/roleDefinitions/write` engedély az összes `assignableScopes`, például a [tulajdonos](built-in-roles.md#owner) vagy [felhasználói hozzáférés adminisztrátora](built-in-roles.md#user-access-administrator).

3. Az egyéni biztonsági szerepkört tesztelése

    Miután az egyéni szerepkör, úgy, hogy ellenőrizze, hogy működik a várt módon tesztelni kell. Ha módosítani kell tenni, frissítheti az egyéni biztonsági szerepkört.

## <a name="custom-role-properties"></a>Egyéni szerepkör tulajdonságai

Egy egyéni biztonsági szerepkört a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Szükséges | Típus | Leírás |
| --- | --- | --- | --- |
| `Name` | Igen | Sztring | Az egyéni biztonsági szerepkört megjelenített neve. A bérlő egyedinek kell lennie. Tartalmazhatnak betűket, számokat, szóközöket és speciális karaktereket. Karakterek maximális száma 128. |
| `Id` | Igen | Sztring | Az egyéni biztonsági szerepkört egyedi azonosítója. Az Azure PowerShell és az Azure parancssori felület ezt az Azonosítót automatikusan létrejön, amikor létrehoz egy új szerepkört. |
| `IsCustom` | Igen | Sztring | Azt jelzi, hogy ez egy egyéni biztonsági szerepkört. Beállítása `true` egyéni szerepkörök. |
| `Description` | Igen | Sztring | Az egyéni szerepkör leírása Tartalmazhatnak betűket, számokat, szóközöket és speciális karaktereket. Karakterek maximális száma: 1024. |
| `Actions` | Igen | String] | Karakterláncok tömbje, amely meghatározza a felügyeleti műveleteket, amely a szerepkör lehetővé teszi a végrehajtását. További információkért lásd: [műveletek](role-definitions.md#actions). |
| `NotActions` | Nem | String] | Karakterláncok tömbje, amely meghatározza a felügyeleti műveleteket, amelyek ki lettek zárva az engedélyezett a `actions`. További információkért lásd: [notActions](role-definitions.md#notactions). |
| `DataActions` | Nem | String] | A szerepkör lehetővé teszi az adatok az objektumon belül végrehajtandó műveletek a adatok megadó karakterláncokból álló tömb. További információkért lásd: [dataActions (előzetes verzió)](role-definitions.md#dataactions-preview). |
| `NotDataActions` | Nem | String] | Karakterláncok tömbje, amely meghatározza az adatok műveleteket, amelyek ki lettek zárva az engedélyezett a `dataActions`. További információkért lásd: [notDataActions (előzetes verzió)](role-definitions.md#notdataactions-preview). |
| `AssignableScopes` | Igen | String] | Karakterláncok tömbje, amely meghatározza, hogy rendelhető hozzá-e az egyéni biztonsági szerepkört a hatókörök. Nem állítható be a gyökérszintű hatókörben (`"/"`). További információkért lásd: [assignableScopes](role-definitions.md#assignablescopes). |

## <a name="assignablescopes-for-custom-roles"></a>egyéni szerepkörök assignableScopes

Akárcsak a beépített szerepkörök a `assignableScopes` tulajdonság határozza meg a hatókörök, hogy a szerepkör érhető el a hozzárendeléshez. Nem használhatja azonban a gyökérszintű hatókörben (`"/"`) saját egyéni szerepkört. Ha, egy engedélyezési hiba jelenik meg. A `assignableScopes` tulajdonsága egy egyéni biztonsági szerepkört is vezérli, akik létrehozása, törlése, módosítása vagy megtekintése az egyéni biztonsági szerepkört.

| Tevékenység | Művelet | Leírás |
| --- | --- | --- |
| Egyéni szerepkör létrehozása vagy törlése | `Microsoft.Authorization/ roleDefinition/write` | Ez a művelet az összes engedéllyel rendelkező felhasználók a `assignableScopes` az egyéni szerepkör létrehozhat (vagy törölhet) egyéni szerepkörök ezeket használható. Például [tulajdonosok](built-in-roles.md#owner) és [felhasználói rendszergazdák](built-in-roles.md#user-access-administrator) előfizetések, erőforráscsoport-sablonok és erőforrások. |
| Egyéni szerepkör módosítása | `Microsoft.Authorization/ roleDefinition/write` | Ez a művelet az összes engedéllyel rendelkező felhasználók a `assignableScopes` az egyéni szerepkör módosíthatja ezeket az egyéni szerepkörök. Például [tulajdonosok](built-in-roles.md#owner) és [felhasználói rendszergazdák](built-in-roles.md#user-access-administrator) előfizetések, erőforráscsoport-sablonok és erőforrások. |
| Egy egyéni biztonsági szerepkört megtekintése | `Microsoft.Authorization/ roleDefinition/read` | Ez a művelet egy hatókörhöz engedéllyel rendelkező felhasználók megtekinthetik az adott hatókörben kiosztására használható egyéni szerepköröket. Az összes beépített szerepkör engedélyezése egyéni szerepkörök hozzárendelés elérhető legyen. |

## <a name="next-steps"></a>További lépések
- [Hozzon létre egyéni szerepkörök az Azure PowerShell](custom-roles-powershell.md)
- [Hozzon létre egyéni szerepkörök az Azure parancssori felület használatával](custom-roles-cli.md)
- [Szerepkör-definíciók ismertetése](role-definitions.md)

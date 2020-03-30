---
title: Egyéni szerepkörök az Azure-erőforrásokhoz | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre egyéni szerepkörökszerepkörökszerepkör-alapú hozzáférés-vezérléssel (RBAC) az Azure-erőforrások részletes hozzáférés-kezeléséhez.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9454962e210781559f2fdceb1c36f499c4ae8ff7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062170"
---
# <a name="custom-roles-for-azure-resources"></a>Egyéni szerepkörök Azure-erőforrásokhoz

> [!IMPORTANT]
> Felügyeleti csoport hozzáadása `AssignableScopes` jelenleg előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Ha az [Azure-erőforrások beépített szerepkörei](built-in-roles.md) nem felelnek meg a szervezet egyedi igényeinek, létrehozhat saját egyéni szerepköröket. A beépített szerepkörökhöz hasonlóan egyéni szerepköröket is hozzárendelhet a felhasználókhoz, csoportokhoz és egyszerű szolgáltatáscsoportokhoz a felügyeleti csoport, az előfizetés és az erőforráscsoport hatókörében.

Egyéni szerepkörök megoszthatók az azonos Azure AD-címtárban megbízó előfizetések között. Könyvtáronként **legfeljebb 5000** egyéni szerepkör lehet. (Az Azure Germany és az Azure China 21Vianet esetében a korlát 2000 egyéni szerepkör.) Egyéni szerepkörök az Azure Portalon (előzetes verzió), az Azure PowerShell, az Azure CLI vagy a REST API használatával hozhatók létre.

## <a name="custom-role-example"></a>Példa egyéni szerepkörre

Az alábbiakban bemutatjuk, hogyan néz ki egy egyéni szerepkör json formátumban. Ez az egyéni szerepkör a virtuális gépek figyelésére és újraindítására használható.

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

Amikor létrehoz egy egyéni szerepkört, az megjelenik az Azure Portalon egy narancssárga erőforrás ikonnal.

![Egyéni szerepkör ikon](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Egyéni szerepkör létrehozásának lépései

1. Döntse el, hogyan szeretné létrehozni az egyéni szerepkört

    Egyéni szerepköröket hozhat létre az [Azure Portalon](custom-roles-portal.md) (előzetes verzió), [az Azure PowerShell,](custom-roles-powershell.md) [az Azure CLI](custom-roles-cli.md)vagy a [REST API használatával.](custom-roles-rest.md)

1. A szükséges engedélyek meghatározása

    Amikor egyéni szerepkört hoz létre, ismernie kell az engedélyek meghatározásához rendelkezésre álló erőforrás-szolgáltatói műveleteket. A műveletek listájának megtekintéséhez tekintse meg az [Azure Resource Manager erőforrás-szolgáltató műveleteit.](resource-provider-operations.md) A műveleteket hozzá adja `Actions` `NotActions` a [szerepkör-definíció](role-definitions.md)vagy tulajdonságaihoz. Ha rendelkezik adatműveletek, akkor hozzá `DataActions` kell `NotDataActions` adnia ezeket a vagy tulajdonságokat.

1. Egyéni szerepkör létrehozása

    Általában egy meglévő beépített szerepkörrel kezdi, majd módosítja azt az igényeinek megfelelően. Ezután a [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) vagy [az szerepkör-definíció létrehozása](/cli/azure/role/definition#az-role-definition-create) parancsokat az egyéni szerepkör létrehozásához használja. Egyéni szerepkör létrehozásához `Microsoft.Authorization/roleDefinitions/write` minden `AssignableScopes`szerepkörhöz , például a [Tulajdonoshoz](built-in-roles.md#owner) vagy a Felhasználói hozzáférés rendszergazdájához engedéllyel kell [rendelkeznie.](built-in-roles.md#user-access-administrator)

1. Az egyéni szerepkör tesztelése

    Miután rendelkezik az egyéni szerepkör, tesztelnie kell, hogy ellenőrizze, hogy működik,ahogy várta. Ha később módosításokat kell végrehajtania, frissítheti az egyéni szerepkört.

Az egyéni szerepkör létrehozásáról részletes oktatóanyagról az [Oktatóanyag: Egyéni szerepkör létrehozása az Azure PowerShell vagy](tutorial-custom-role-powershell.md) [az Oktatóanyag használatával: Egyéni szerepkör létrehozása az Azure CLI használatával](tutorial-custom-role-cli.md)című témakörben.

## <a name="custom-role-properties"></a>Egyéni szerepkör tulajdonságai

Az egyéni szerepkör a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Kötelező | Típus | Leírás |
| --- | --- | --- | --- |
| `Name` | Igen | Sztring | Az egyéni szerepkör megjelenítendő neve. Míg a szerepkör-definíció egy felügyeleti csoport vagy előfizetés-szintű erőforrás, a szerepkör-definíció használható több előfizetések, amelyek ugyanazt az Azure AD-címtárban. Ennek a megjelenítendő névnek egyedinek kell lennie az Azure AD-címtár hatókörében. Tartalmazhat betűket, számokat, szóközöket és speciális karaktereket. A karakterek maximális száma 128. |
| `Id` | Igen | Sztring | Az egyéni szerepkör egyedi azonosítója. Az Azure PowerShell és az Azure CLI esetében ez az azonosító automatikusan létrejön, amikor új szerepkört hoz létre. |
| `IsCustom` | Igen | Sztring | Azt jelzi, hogy ez egyéni szerepkör-e. Egyéni `true` szerepkörökhöz. |
| `Description` | Igen | Sztring | Az egyéni szerepkör leírása. Tartalmazhat betűket, számokat, szóközöket és speciális karaktereket. A karakterek maximális száma 1024. |
| `Actions` | Igen | Karakterlánc[] | Karakterláncok tömbje, amely megadja a szerepkör által lehetővé tesz felügyeleti műveleteket. További információt a Műveletek című [témakörben talál.](role-definitions.md#actions) |
| `NotActions` | Nem | Karakterlánc[] | Karakterláncok tömbje, amely megadja azengedélyezett rendszerből kizárt `Actions`felügyeleti műveleteket. További információ: [NotActions](role-definitions.md#notactions). |
| `DataActions` | Nem | Karakterlánc[] | Karakterláncok tömbje, amely megadja azokat az adatműveleteket, amelyeket a szerepkör lehetővé tesz az objektumon belüli adatokon. Ha egyéni szerepkört `DataActions`hoz létre a alkalmazással, akkor a szerepkör nem rendelhető hozzá a felügyeleti csoport hatóköréhez. További információ: [DataActions](role-definitions.md#dataactions). |
| `NotDataActions` | Nem | Karakterlánc[] | Karakterláncok tömbje, amely megadja az engedélyezett ből kizárt `DataActions`adatműveleteket. További információ: [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes` | Igen | Karakterlánc[] | Karakterláncok tömbje, amely megadja azokat a hatóköröket, amelyekhez az egyéni szerepkör rendelkezésre áll a hozzárendeléshez. Egyéni szerepkörben `AssignableScopes` csak egy felügyeleti csoportot határozhat meg. Felügyeleti csoport hozzáadása `AssignableScopes` jelenleg előzetes verzióban érhető el. További információ: [AssignableScopes](role-definitions.md#assignablescopes). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Ki hozhat létre, törölhet, frissíthet vagy tekinthet meg egyéni szerepkört?

A beépített szerepkörökhöz `AssignableScopes` hasonlóan a tulajdonság is meghatározza azokat a hatóköröket, amelyeket a szerepkör hozzárendelésre rendelkezésre bocsát. Az `AssignableScopes` egyéni szerepkör tulajdonsága azt is szabályozza, hogy ki hozhat létre, törölhet, frissíthet vagy tekinthet meg az egyéni szerepkört.

| Tevékenység | Művelet | Leírás |
| --- | --- | --- |
| Egyéni szerepkör létrehozása/törlése | `Microsoft.Authorization/ roleDefinitions/write` | Azok a felhasználók, akik `AssignableScopes` az egyéni szerepkör összesen ezt a műveletet kapják, létrehozhatnak (vagy törölhetnek) egyéni szerepköröket ezekben a hatókörökben való használatra. Például a felügyeleti csoportok, előfizetések és erőforráscsoportok [tulajdonosai](built-in-roles.md#owner) és [felhasználói hozzáférés rendszergazdái.](built-in-roles.md#user-access-administrator) |
| Egyéni szerepkörök frissítése | `Microsoft.Authorization/ roleDefinitions/write` | Azok a felhasználók, akik `AssignableScopes` az egyéni szerepkör összes en kapják ezt a műveletet, frissíthetik az egyéni szerepköröket ezekben a hatókörökben. Például a felügyeleti csoportok, előfizetések és erőforráscsoportok [tulajdonosai](built-in-roles.md#owner) és [felhasználói hozzáférés rendszergazdái.](built-in-roles.md#user-access-administrator) |
| Egyéni szerepkör megtekintése | `Microsoft.Authorization/ roleDefinitions/read` | Azok a felhasználók, akik ezt a műveletet egy hatókörben kapják, megtekinthetik az adott hatókörben hozzárendeléshez rendelkezésre álló egyéni szerepköröket. Minden beépített szerepkör lehetővé teszi, hogy egyéni szerepkörök legyenek elérhetők a hozzárendeléshez. |

## <a name="custom-role-limits"></a>Egyéni szerepkörkorlátok

Az alábbi lista az egyéni szerepkörök reklisek határait ismerteti.

- Minden könyvtár legfeljebb **5000** egyéni szerepkörrel rendelkezhet.
- Az Azure Germany és az Azure China 21Vianet könyvtárakhoz legfeljebb 2000 egyéni szerepkört használhat.
- A gyökérhatókör ( `AssignableScopes` )`"/"`nem állítható be.
- Egyéni szerepkörben `AssignableScopes` csak egy felügyeleti csoportot határozhat meg. Felügyeleti csoport hozzáadása `AssignableScopes` jelenleg előzetes verzióban érhető el.
- A felügyeleti `DataActions` csoport hatókörén nem rendelhető hozzá egyéni szerepkörök.
- Az Azure Resource Manager nem ellenőrzi a felügyeleti csoport létezését a szerepkör-definíció hozzárendelhető hatókörében.

Az egyéni szerepkörökről és felügyeleti csoportokról az [Erőforrások rendszerezése az Azure felügyeleti csoportokkal](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések
- [Egyéni Azure-szerepkörök létrehozása vagy frissítése az Azure Portal használatával (előzetes verzió)](custom-roles-portal.md)
- [Az Azure-erőforrások szerepkör-definícióinak ismertetése](role-definitions.md)
- [Az RBAC hibáiaz Azure-erőforrásokhoz](troubleshooting.md)

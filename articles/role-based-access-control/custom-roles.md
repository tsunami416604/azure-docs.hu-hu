---
title: Egyéni szerepkörök az Azure-erőforrásokhoz | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egyéni szerepköröket szerepköralapú hozzáférés-vezérléssel (RBAC) az Azure-erőforrások részletes hozzáférés-kezeléséhez.
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
ms.date: 06/07/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fbea0567ec125ce12acd8f757b32df723876fe09
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338573"
---
# <a name="custom-roles-for-azure-resources"></a>Egyéni szerepkörök az Azure-erőforrásokhoz

Ha az [Azure-erőforrások beépített szerepkörei](built-in-roles.md) nem felelnek meg a szervezet konkrét igényeinek, létrehozhat saját egyéni szerepköröket is. A beépített szerepkörökhöz hasonlóan egyéni szerepköröket is hozzárendelhet a felhasználókhoz, csoportokhoz és egyszerű szolgáltatásokhoz az előfizetés, az erőforráscsoport és az erőforrás-hatókörök esetében.

Az egyéni szerepkörök egy Azure Active Directory (Azure AD) könyvtárban tárolódnak, és az előfizetések között megoszthatók. Minden címtárhoz legfeljebb **5000** egyéni szerepkör tartozhat. (A speciális felhőkben, mint az Azure Government, az Azure Germany és az Azure China 21Vianet, az egyéni szerepkörök száma 2000-re van korlátozva.) Az egyéni szerepkörök a Azure PowerShell, az Azure CLI vagy a REST API használatával hozhatók létre.

## <a name="custom-role-example"></a>Példa egyéni szerepkörre

Az alábbi ábrán látható, hogyan néz ki egy egyéni szerepkör JSON formátumban látható módon. Ezt az egyéni szerepkört a virtuális gépek figyelésére és újraindítására használhatja.

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
    "/subscriptions/{subscriptionId3}"
  ]
}
```

Ha egyéni szerepkört hoz létre, az Azure Portal narancssárga erőforrás ikonnal jelenik meg.

![Egyéni szerepkör ikonja](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Egyéni szerepkör létrehozásának lépései

1. Döntse el, hogyan szeretné létrehozni az egyéni szerepkört

    A [Azure PowerShell](custom-roles-powershell.md), az [Azure CLI](custom-roles-cli.md)vagy a [REST API](custom-roles-rest.md)használatával hozhat létre egyéni szerepköröket.

1. A szükséges engedélyek meghatározása

    Egyéni szerepkör létrehozásakor tudnia kell, hogy milyen erőforrás-szolgáltatói műveletek érhetők el az engedélyek definiálásához. A műveletek listájának megtekintéséhez tekintse meg a [Azure Resource Manager erőforrás-szolgáltatói műveletek](resource-provider-operations.md)című témakört. A műveletek a [szerepkör-definíció](role-definitions.md)`Actions` vagy `NotActions` tulajdonságához lesznek hozzáadva. Ha adatműveletekkel rendelkezik, ezeket a `DataActions` vagy a `NotDataActions` tulajdonsághoz adja hozzá.

1. Az egyéni szerepkör létrehozása

    Általában egy meglévő beépített szerepkörrel kell kezdenie, majd módosítania kell az igényeinek megfelelően. Ezután a [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) vagy [az az szerepkör-definíció létrehozása](/cli/azure/role/definition#az-role-definition-create) paranccsal hozza létre az egyéni szerepkört. Egyéni szerepkör létrehozásához a `Microsoft.Authorization/roleDefinitions/write` engedéllyel kell rendelkeznie az összes `AssignableScopes`, például a [tulajdonos](built-in-roles.md#owner) vagy a felhasználó- [hozzáférési rendszergazda](built-in-roles.md#user-access-administrator)számára.

1. Az egyéni szerepkör tesztelése

    Ha már rendelkezik az egyéni szerepkörrel, tesztelje, hogy a várt módon működik-e. Ha később módosítania kell a módosításokat, akkor frissítheti az egyéni szerepkört.

Az egyéni szerepkörök létrehozásával kapcsolatos részletes oktatóanyagért lásd: [Tutorial: Egyéni szerepkör létrehozása Azure PowerShell @ no__t-0 vagy [Tutorial használatával: Hozzon létre egy egyéni szerepkört az Azure CLI @ no__t-0 használatával.

## <a name="custom-role-properties"></a>Egyéni szerepkör tulajdonságai

Az egyéni szerepkör a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Szükséges | Típus | Leírás |
| --- | --- | --- | --- |
| `Name` | Igen | Sztring | Az egyéni szerepkör megjelenített neve. Míg a szerepkör-definíció egy előfizetési szintű erőforrás, a szerepkör-definíció több előfizetésben is használható, amelyek ugyanazt az Azure AD-címtárat használják. Ennek a megjelenítendő névnek egyedinek kell lennie az Azure AD-címtár hatókörében. Tartalmazhat betűket, számokat, szóközöket és speciális karaktereket is. A karakterek maximális száma 128. |
| `Id` | Igen | Sztring | Az egyéni szerepkör egyedi azonosítója. A Azure PowerShell és az Azure CLI esetében ez az azonosító automatikusan létrejön, amikor új szerepkört hoz létre. |
| `IsCustom` | Igen | Sztring | Azt jelzi, hogy ez egy egyéni szerepkör-e. Egyéni szerepkörök esetében állítsa `true` értékre. |
| `Description` | Igen | Sztring | Az egyéni szerepkör leírása. Tartalmazhat betűket, számokat, szóközöket és speciális karaktereket is. A karakterek maximális száma 1024. |
| `Actions` | Igen | Karakterlánc [] | Karakterláncok tömbje, amely meghatározza, hogy a szerepkör milyen kezelési műveleteket hajtson végre. További információ: [műveletek](role-definitions.md#actions). |
| `NotActions` | Nem | Karakterlánc [] | Karakterláncok tömbje, amely meghatározza az engedélyezett @no__t – 0 által kizárt felügyeleti műveleteket. További információkért lásd: a [](role-definitions.md#notactions)nem-tapintatok. |
| `DataActions` | Nem | Karakterlánc [] | Karakterláncok tömbje, amely meghatározza azokat az adatműveleteket, amelyeket a szerepkör engedélyez az adott objektumon belüli adatokon való végrehajtáshoz. További információ: [DataActions](role-definitions.md#dataactions). |
| `NotDataActions` | Nem | Karakterlánc [] | Karakterláncok tömbje, amely meghatározza az engedélyezett @no__t – 0 értékből kizárt adatműveleteket. További információ: [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes` | Igen | Karakterlánc [] | Karakterláncok tömbje, amely meghatározza az egyéni szerepkör hozzárendeléshez elérhető hatóköreit. Egyéni szerepkörök esetében jelenleg nem lehet `AssignableScopes` értéket beállítani a gyökérszintű hatókörhöz (`"/"`) vagy egy felügyeleti csoport hatóköréhez. További információ: az erőforrások [AssignableScopes](role-definitions.md#assignablescopes) és [rendszerezése az Azure felügyeleti csoportjaival](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Kik hozhatnak létre, törölhetnek, frissíthetnek vagy tekinthetnek meg egyéni szerepköröket

A beépített szerepkörökhöz hasonlóan a `AssignableScopes` tulajdonság határozza meg azokat a hatóköröket, amelyekhez a szerepkör elérhető a hozzárendeléshez. Az egyéni szerepkör `AssignableScopes` tulajdonsága azt is meghatározza, hogy ki hozhat létre, törölhet, frissíthet vagy tekinthet meg egyéni szerepkört.

| Tevékenység | Művelet | Leírás |
| --- | --- | --- |
| Egyéni szerepkör létrehozása/törlése | `Microsoft.Authorization/ roleDefinitions/write` | Azok a felhasználók, akik az egyéni szerepkör összes `AssignableScopes` értékében engedélyezik ezt a műveletet, létrehozhatnak (vagy törölhetnek) egyéni szerepköröket a hatókörökben való használatra. Például az előfizetések, erőforráscsoportok és erőforrások [tulajdonosai](built-in-roles.md#owner) és [felhasználói hozzáférés-rendszergazdái](built-in-roles.md#user-access-administrator) . |
| Egyéni szerepkörök frissítése | `Microsoft.Authorization/ roleDefinitions/write` | Azok a felhasználók, akik az egyéni szerepkör összes `AssignableScopes` értékében engedélyezik ezt a műveletet, frissíthetik a hatókörökben lévő egyéni szerepköröket. Például az előfizetések, erőforráscsoportok és erőforrások [tulajdonosai](built-in-roles.md#owner) és [felhasználói hozzáférés-rendszergazdái](built-in-roles.md#user-access-administrator) . |
| Egyéni szerepkör megtekintése | `Microsoft.Authorization/ roleDefinitions/read` | Azok a felhasználók, akik egy hatókörben engedélyezik ezt a műveletet, megtekinthetik az adott hatókörben való hozzárendeléshez elérhető egyéni szerepköröket. Az összes beépített szerepkör lehetővé teszi, hogy az egyéni szerepkörök elérhetők legyenek a hozzárendeléshez. |

## <a name="next-steps"></a>További lépések
- [Egyéni szerepkörök létrehozása Azure-erőforrásokhoz az Azure PowerShell használatával](custom-roles-powershell.md)
- [Egyéni szerepkörök létrehozása Azure-erőforrásokhoz az Azure CLI használatával](custom-roles-cli.md)
- [Az Azure-erőforrások szerepkör-definícióinak megismerése](role-definitions.md)
- [Azure-erőforrások RBAC kapcsolatos hibák](troubleshooting.md)

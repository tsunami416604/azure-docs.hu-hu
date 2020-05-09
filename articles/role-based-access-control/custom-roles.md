---
title: Egyéni Azure-szerepkörök – Azure RBAC
description: Ismerje meg, hogyan hozhat létre Azure-beli egyéni szerepköröket az Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC) az Azure-erőforrások részletes hozzáférés-kezeléséhez.
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
ms.date: 04/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5030fb50313e1db2173990c55930c22fdf58f559
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734790"
---
# <a name="azure-custom-roles"></a>Egyéni Azure-szerepkörök

> [!IMPORTANT]
> A felügyeleti csoport hozzáadása a `AssignableScopes` jelenleg előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha az [Azure beépített szerepkörei](built-in-roles.md) nem felelnek meg a szervezet konkrét igényeinek, létrehozhat saját egyéni szerepköröket is. A beépített szerepkörökhöz hasonlóan egyéni szerepköröket is hozzárendelhet a felhasználókhoz, csoportokhoz és egyszerű szolgáltatásokhoz a felügyeleti csoport, az előfizetés és az erőforráscsoport hatókörében.

Az egyéni szerepkörök megoszthatók az azonos Azure AD-címtárban megbízható előfizetések között. A címtárban legfeljebb **5 000** egyéni szerepkör lehet. (Az Azure Germany és az Azure China 21Vianet esetében a korlát 2 000 egyéni szerepkör.) Egyéni szerepkörök hozhatók létre a Azure Portal, a Azure PowerShell, az Azure CLI vagy a REST API használatával.

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
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

Ha egyéni szerepkört hoz létre, az Azure Portal narancssárga erőforrás ikonnal jelenik meg.

![Egyéni szerepkör ikonja](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Egyéni szerepkör létrehozásának lépései

1. Döntse el, hogyan szeretné létrehozni az egyéni szerepkört

    Egyéni szerepköröket [Azure Portal](custom-roles-portal.md), [Azure POWERSHELL](custom-roles-powershell.md), [Azure CLI](custom-roles-cli.md)vagy a [REST API](custom-roles-rest.md)használatával hozhat létre.

1. A szükséges engedélyek meghatározása

    Egyéni szerepkör létrehozásakor tudnia kell, hogy milyen erőforrás-szolgáltatói műveletek érhetők el az engedélyek definiálásához. A műveletek listájának megtekintéséhez tekintse meg a [Azure Resource Manager erőforrás-szolgáltatói műveletek](resource-provider-operations.md)című témakört. Adja hozzá a műveleteket a `Actions` szerepkör- `NotActions` [definíció](role-definitions.md)vagy tulajdonságaihoz. Ha rendelkezik adatműveletekkel, ezeket a `DataActions` vagy `NotDataActions` tulajdonságokat adja hozzá.

1. Az egyéni szerepkör létrehozása

    Általában egy meglévő beépített szerepkörrel kell kezdenie, majd módosítania kell az igényeinek megfelelően. Ezután a [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) vagy [az az szerepkör-definíció létrehozása](/cli/azure/role/definition#az-role-definition-create) paranccsal hozza létre az egyéni szerepkört. `Microsoft.Authorization/roleDefinitions/write` Egyéni szerepkör létrehozásához az engedéllyel kell rendelkeznie `AssignableScopes`, például a [tulajdonos](built-in-roles.md#owner) vagy a [felhasználó hozzáférési rendszergazdája](built-in-roles.md#user-access-administrator)számára.

1. Az egyéni szerepkör tesztelése

    Ha már rendelkezik az egyéni szerepkörrel, tesztelje, hogy a várt módon működik-e. Ha később módosítania kell a módosításokat, akkor frissítheti az egyéni szerepkört.

Az egyéni szerepkörök létrehozásával kapcsolatos részletes oktatóanyagért lásd [: oktatóanyag: egyéni Azure-szerepkör létrehozása Azure PowerShell](tutorial-custom-role-powershell.md) vagy [oktatóanyag használatával: Azure-beli egyéni szerepkör létrehozása az Azure CLI használatával](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Egyéni szerepkör tulajdonságai

Az egyéni szerepkör a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Kötelező | Típus | Leírás |
| --- | --- | --- | --- |
| `Name` | Igen | Sztring | Az egyéni szerepkör megjelenített neve. Habár a szerepkör-definíció egy felügyeleti csoport vagy előfizetési szintű erőforrás, a szerepkör-definíció több előfizetésben is használható, amelyek ugyanazt az Azure AD-címtárral rendelkeznek. Ennek a megjelenítendő névnek egyedinek kell lennie az Azure AD-címtár hatókörében. Tartalmazhat betűket, számokat, szóközöket és speciális karaktereket is. A karakterek maximális száma 128. |
| `Id` | Igen | Sztring | Az egyéni szerepkör egyedi azonosítója. A Azure PowerShell és az Azure CLI esetében ez az azonosító automatikusan létrejön, amikor új szerepkört hoz létre. |
| `IsCustom` | Igen | Sztring | Azt jelzi, hogy ez egy egyéni szerepkör-e. `true` Egyéni szerepkörökre beállítva. |
| `Description` | Igen | Sztring | Az egyéni szerepkör leírása. Tartalmazhat betűket, számokat, szóközöket és speciális karaktereket is. A karakterek maximális száma 1024. |
| `Actions` | Igen | Karakterlánc [] | Karakterláncok tömbje, amely meghatározza, hogy a szerepkör milyen kezelési műveleteket hajtson végre. További információ: [műveletek](role-definitions.md#actions). |
| `NotActions` | No | Karakterlánc [] | Karakterláncok tömbje, amely meghatározza az engedélyezetttől `Actions`kizárt felügyeleti műveleteket. További információkért lásd: a nem- [tapintatok](role-definitions.md#notactions). |
| `DataActions` | No | Karakterlánc [] | Karakterláncok tömbje, amely meghatározza azokat az adatműveleteket, amelyeket a szerepkör engedélyez az adott objektumon belüli adatokon való végrehajtáshoz. Ha egyéni szerepkört hoz létre a `DataActions`alkalmazással, a szerepkör nem rendelhető hozzá a felügyeleti csoport hatóköréhez. További információ: [DataActions](role-definitions.md#dataactions). |
| `NotDataActions` | No | Karakterlánc [] | Karakterláncok tömbje, amely az engedélyezetttől `DataActions`kizárt adatműveleteket határozza meg. További információ: [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes` | Igen | Karakterlánc [] | Karakterláncok tömbje, amely meghatározza az egyéni szerepkör hozzárendeléshez elérhető hatóköreit. Egyéni szerepkörben csak egyetlen felügyeleti csoportot `AssignableScopes` lehet definiálni. A felügyeleti csoport hozzáadása a `AssignableScopes` jelenleg előzetes verzióban érhető el. További információ: [AssignableScopes](role-definitions.md#assignablescopes). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Kik hozhatnak létre, törölhetnek, frissíthetnek vagy tekinthetnek meg egyéni szerepköröket

A beépített szerepkörökhöz hasonlóan a `AssignableScopes` tulajdonság határozza meg azokat a hatóköröket, amelyekhez a szerepkör elérhető a hozzárendeléshez. Az `AssignableScopes` egyéni szerepkör tulajdonsága azt is meghatározza, hogy ki hozhatja létre, törölheti, frissítheti vagy megtekintheti az egyéni szerepkört.

| Tevékenység | Művelet | Leírás |
| --- | --- | --- |
| Egyéni szerepkör létrehozása/törlése | `Microsoft.Authorization/ roleDefinitions/write` | Azok a felhasználók, akik ezt a műveletet `AssignableScopes` az egyéni szerepkörön keresztül kaptak, létrehozhatnak (vagy törölhetnek) egyéni szerepköröket az adott hatókörökben való használatra. Például a felügyeleti csoportok, előfizetések és erőforráscsoportok [tulajdonosai](built-in-roles.md#owner) és [felhasználói hozzáférési rendszergazdái](built-in-roles.md#user-access-administrator) . |
| Egyéni szerepkörök frissítése | `Microsoft.Authorization/ roleDefinitions/write` | Azok a felhasználók, akik ezt a műveletet az `AssignableScopes` egyéni szerepkör mindegyikén megkapják, frissíthetik az egyéni szerepköröket a hatókörökben. Például a felügyeleti csoportok, előfizetések és erőforráscsoportok [tulajdonosai](built-in-roles.md#owner) és [felhasználói hozzáférési rendszergazdái](built-in-roles.md#user-access-administrator) . |
| Egyéni szerepkör megtekintése | `Microsoft.Authorization/ roleDefinitions/read` | Azok a felhasználók, akik egy hatókörben engedélyezik ezt a műveletet, megtekinthetik az adott hatókörben való hozzárendeléshez elérhető egyéni szerepköröket. Az összes beépített szerepkör lehetővé teszi, hogy az egyéni szerepkörök elérhetők legyenek a hozzárendeléshez. |

## <a name="custom-role-limits"></a>Egyéni szerepkör korlátai

Az alábbi lista az egyéni szerepkörökre vonatkozó korlátozásokat ismerteti.

- Minden címtárhoz legfeljebb **5000** egyéni szerepkör tartozhat.
- Az Azure Germany és az Azure China 21Vianet az egyes könyvtárak esetében akár 2000 egyéni szerepkört is tartalmazhat.
- A gyökérszintű hatókör `AssignableScopes` (`"/"`) nem állítható be.
- Egyéni szerepkörben csak egyetlen felügyeleti csoportot `AssignableScopes` lehet definiálni. A felügyeleti csoport hozzáadása a `AssignableScopes` jelenleg előzetes verzióban érhető el.
- A felügyeleti csoport `DataActions` hatókörében nem lehet hozzárendelni egyéni szerepköröket.
- Azure Resource Manager nem ellenőrzi a felügyeleti csoport létezését a szerepkör-definíció hozzárendelhető hatókörében.

Az egyéni szerepkörökkel és felügyeleti csoportokkal kapcsolatos további információkért lásd: [erőforrások rendszerezése az Azure felügyeleti csoportjaival](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment).

## <a name="next-steps"></a>További lépések
- [Egyéni Azure-szerepkörök létrehozása vagy frissítése a Azure Portal használatával](custom-roles-portal.md)
- [Az Azure szerepkör-definíciók ismertetése](role-definitions.md)
- [Az Azure RBAC hibáinak megoldása](troubleshooting.md)

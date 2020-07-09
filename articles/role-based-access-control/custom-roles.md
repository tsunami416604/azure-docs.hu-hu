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
ms.date: 05/08/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3a30ea70c623c8456ae97c8ca9475e4989784edf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82995851"
---
# <a name="azure-custom-roles"></a>Egyéni Azure-szerepkörök

> [!IMPORTANT]
> A felügyeleti csoport hozzáadása a `AssignableScopes` jelenleg előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha az [Azure beépített szerepkörei](built-in-roles.md) nem felelnek meg a szervezet konkrét igényeinek, létrehozhat saját egyéni szerepköröket is. A beépített szerepkörökhöz hasonlóan egyéni szerepköröket is hozzárendelhet a felhasználókhoz, csoportokhoz és egyszerű szolgáltatásokhoz a felügyeleti csoport, az előfizetés és az erőforráscsoport hatókörében.

Az egyéni szerepkörök megoszthatók az azonos Azure AD-címtárban megbízható előfizetések között. A címtárban legfeljebb **5 000** egyéni szerepkör lehet. (Az Azure Germany és az Azure China 21Vianet esetében a korlát 2 000 egyéni szerepkör.) Egyéni szerepkörök hozhatók létre a Azure Portal, a Azure PowerShell, az Azure CLI vagy a REST API használatával.

## <a name="custom-role-example"></a>Példa egyéni szerepkörre

Az alábbi ábrán látható, hogyan néz ki egy egyéni szerepkör a Azure PowerShell JSON formátumban történő megjelenítéséhez. Ezt az egyéni szerepkört a virtuális gépek figyelésére és újraindítására használhatja.

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

A következő az Azure CLI használatával megjelenő egyéni szerepkört jeleníti meg.

```json
[
  {
    "assignableScopes": [
      "/subscriptions/{subscriptionId1}",
      "/subscriptions/{subscriptionId2}",
      "/providers/Microsoft.Management/managementGroups/{groupId1}"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/88888888-8888-8888-8888-888888888888",
    "name": "88888888-8888-8888-8888-888888888888",
    "permissions": [
      {
        "actions": [
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
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Ha egyéni szerepkört hoz létre, az Azure Portal narancssárga erőforrás ikonnal jelenik meg.

![Egyéni szerepkör ikonja](./media/custom-roles/roles-custom-role-icon.png)

## <a name="custom-role-properties"></a>Egyéni szerepkör tulajdonságai

A következő táblázat leírja, hogy az egyéni szerepkör tulajdonságai mit jelentenek.

| Tulajdonság | Kötelező | Típus | Description |
| --- | --- | --- | --- |
| `Name`</br>`roleName` | Yes | Sztring | Az egyéni szerepkör megjelenített neve. Habár a szerepkör-definíció egy felügyeleti csoport vagy előfizetési szintű erőforrás, a szerepkör-definíció több előfizetésben is használható, amelyek ugyanazt az Azure AD-címtárral rendelkeznek. Ennek a megjelenítendő névnek egyedinek kell lennie az Azure AD-címtár hatókörében. Tartalmazhat betűket, számokat, szóközöket és speciális karaktereket is. A karakterek maximális száma 128. |
| `Id`</br>`name` | Yes | Sztring | Az egyéni szerepkör egyedi azonosítója. A Azure PowerShell és az Azure CLI esetében ez az azonosító automatikusan létrejön, amikor új szerepkört hoz létre. |
| `IsCustom`</br>`roleType` | Yes | Sztring | Azt jelzi, hogy ez egy egyéni szerepkör-e. `true` `CustomRole` Egyéni szerepkörökhöz vagy értékhez. `false` `BuiltInRole` Beépített szerepkörökhöz vagy értékhez. |
| `Description`</br>`description` | Yes | Sztring | Az egyéni szerepkör leírása. Tartalmazhat betűket, számokat, szóközöket és speciális karaktereket is. A karakterek maximális száma 1024. |
| `Actions`</br>`actions` | Yes | Karakterlánc [] | Karakterláncok tömbje, amely meghatározza, hogy a szerepkör milyen kezelési műveleteket hajtson végre. További információ: [műveletek](role-definitions.md#actions). |
| `NotActions`</br>`notActions` | No | Karakterlánc [] | Karakterláncok tömbje, amely meghatározza az engedélyezetttől kizárt felügyeleti műveleteket `Actions` . További információkért lásd: a nem- [tapintatok](role-definitions.md#notactions). |
| `DataActions`</br>`dataActions` | No | Karakterlánc [] | Karakterláncok tömbje, amely meghatározza azokat az adatműveleteket, amelyeket a szerepkör engedélyez az adott objektumon belüli adatokon való végrehajtáshoz. Ha egyéni szerepkört hoz létre a `DataActions` alkalmazással, a szerepkör nem rendelhető hozzá a felügyeleti csoport hatóköréhez. További információ: [DataActions](role-definitions.md#dataactions). |
| `NotDataActions`</br>`notDataActions` | No | Karakterlánc [] | Karakterláncok tömbje, amely az engedélyezetttől kizárt adatműveleteket határozza meg `DataActions` . További információ: [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes`</br>`assignableScopes` | Yes | Karakterlánc [] | Karakterláncok tömbje, amely meghatározza az egyéni szerepkör hozzárendeléshez elérhető hatóköreit. Egyéni szerepkörben csak egyetlen felügyeleti csoportot lehet definiálni `AssignableScopes` . A felügyeleti csoport hozzáadása a `AssignableScopes` jelenleg előzetes verzióban érhető el. További információ: [AssignableScopes](role-definitions.md#assignablescopes). |

## <a name="steps-to-create-a-custom-role"></a>Egyéni szerepkör létrehozásának lépései

Egyéni szerepkör létrehozásához kövesse az alábbi alapvető lépéseket.

1. Döntse el, hogyan szeretné létrehozni az egyéni szerepkört.

    Egyéni szerepköröket Azure Portal, Azure PowerShell, Azure CLI vagy a REST API használatával hozhat létre.

1. Határozza meg a szükséges engedélyeket.

    Egyéni szerepkör létrehozásakor ismernie kell az engedélyek definiálásához elérhető műveleteket. A műveletek listájának megtekintéséhez tekintse meg a [Azure Resource Manager erőforrás-szolgáltatói műveletek](resource-provider-operations.md)című témakört. Adja hozzá a műveleteket a `Actions` `NotActions` szerepkör- [definíció](role-definitions.md)vagy tulajdonságaihoz. Ha rendelkezik adatműveletekkel, ezeket a vagy tulajdonságokat adja hozzá `DataActions` `NotDataActions` .

1. Hozza létre az egyéni szerepkört.

    Általában egy meglévő beépített szerepkörrel kell kezdenie, majd módosítania kell az igényeinek megfelelően. A legegyszerűbb módszer a Azure Portal használata. Az egyéni szerepkörök a Azure Portal használatával történő létrehozásával kapcsolatos lépéseiért lásd: [Azure-beli egyéni szerepkörök létrehozása vagy frissítése a Azure Portal használatával](custom-roles-portal.md).

1. Tesztelje az egyéni szerepkört.

    Ha már rendelkezik az egyéni szerepkörrel, tesztelje, hogy a várt módon működik-e. Ha később módosítania kell a módosításokat, akkor frissítheti az egyéni szerepkört.

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Kik hozhatnak létre, törölhetnek, frissíthetnek vagy tekinthetnek meg egyéni szerepköröket

A beépített szerepkörökhöz hasonlóan a `AssignableScopes` tulajdonság határozza meg azokat a hatóköröket, amelyekhez a szerepkör elérhető a hozzárendeléshez. Az `AssignableScopes` Egyéni szerepkör tulajdonsága azt is meghatározza, hogy ki hozhatja létre, törölheti, frissítheti vagy megtekintheti az egyéni szerepkört.

| Tevékenység | Művelet | Description |
| --- | --- | --- |
| Egyéni szerepkör létrehozása/törlése | `Microsoft.Authorization/ roleDefinitions/write` | Azok a felhasználók, akik ezt a műveletet az `AssignableScopes` Egyéni szerepkörön keresztül kaptak, létrehozhatnak (vagy törölhetnek) egyéni szerepköröket az adott hatókörökben való használatra. Például a felügyeleti csoportok, előfizetések és erőforráscsoportok [tulajdonosai](built-in-roles.md#owner) és [felhasználói hozzáférési rendszergazdái](built-in-roles.md#user-access-administrator) . |
| Egyéni szerepkörök frissítése | `Microsoft.Authorization/ roleDefinitions/write` | Azok a felhasználók, akik ezt a műveletet az egyéni szerepkör mindegyikén megkapják, `AssignableScopes` frissíthetik az egyéni szerepköröket a hatókörökben. Például a felügyeleti csoportok, előfizetések és erőforráscsoportok [tulajdonosai](built-in-roles.md#owner) és [felhasználói hozzáférési rendszergazdái](built-in-roles.md#user-access-administrator) . |
| Egyéni szerepkör megtekintése | `Microsoft.Authorization/ roleDefinitions/read` | Azok a felhasználók, akik egy hatókörben engedélyezik ezt a műveletet, megtekinthetik az adott hatókörben való hozzárendeléshez elérhető egyéni szerepköröket. Az összes beépített szerepkör lehetővé teszi, hogy az egyéni szerepkörök elérhetők legyenek a hozzárendeléshez. |

## <a name="custom-role-limits"></a>Egyéni szerepkör korlátai

Az alábbi lista az egyéni szerepkörökre vonatkozó korlátozásokat ismerteti.

- Minden címtárhoz legfeljebb **5000** egyéni szerepkör tartozhat.
- Az Azure Germany és az Azure China 21Vianet az egyes könyvtárak esetében akár 2000 egyéni szerepkört is tartalmazhat.
- `AssignableScopes`A gyökérszintű hatókör () nem állítható be `"/"` .
- Egyéni szerepkörben csak egyetlen felügyeleti csoportot lehet definiálni `AssignableScopes` . A felügyeleti csoport hozzáadása a `AssignableScopes` jelenleg előzetes verzióban érhető el.
- `DataActions`A felügyeleti csoport hatókörében nem lehet hozzárendelni egyéni szerepköröket.
- Azure Resource Manager nem ellenőrzi a felügyeleti csoport létezését a szerepkör-definíció hozzárendelhető hatókörében.

Az egyéni szerepkörökkel és felügyeleti csoportokkal kapcsolatos további információkért lásd: [erőforrások rendszerezése az Azure felügyeleti csoportjaival](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment).

## <a name="input-and-output-formats"></a>Bemeneti és kimeneti formátumok

Ha a parancssorból szeretne egyéni szerepkört létrehozni, általában a JSON használatával határozza meg az egyéni szerepkörhöz használni kívánt tulajdonságokat. A használt eszközöktől függően a bemeneti és a kimeneti formátumok némileg eltérőek lesznek. Ez a szakasz az eszköztől függően megadja a bemeneti és kimeneti formátumokat.

### <a name="azure-powershell"></a>Azure PowerShell

Ha Azure PowerShell használatával szeretne egyéni szerepkört létrehozni, a következő adatokat kell megadnia.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Ha Azure PowerShell használatával szeretne egyéni szerepkört frissíteni, meg kell adnia a következő adatokat. Vegye figyelembe, hogy a `Id` tulajdonság hozzá lett adva. 

```json
{
  "Name": "",
  "Id": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Az alábbi ábrán egy példa látható a kimenetre, amikor Azure PowerShell és a [ConvertTo-JSON](/powershell/module/microsoft.powershell.utility/convertto-json) parancs használatával egyéni szerepkört listáz. 

```json
{
  "Name": "",
  "Id": "",
  "IsCustom": true,
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

### <a name="azure-cli"></a>Azure CLI

Ha egyéni szerepkört szeretne létrehozni vagy frissíteni az Azure CLI használatával, meg kell adnia a következő bemenetet. Ez a formátum ugyanaz, amikor egyéni szerepkört hoz létre Azure PowerShell használatával.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Az alábbi példa bemutatja a kimenetet, amikor egyéni szerepkört listáz az Azure CLI használatával.

```json
[
  {
    "assignableScopes": [],
    "description": "",
    "id": "",
    "name": "",
    "permissions": [
      {
        "actions": [],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="rest-api"></a>REST API

Egyéni szerepkör a REST API használatával történő létrehozásához vagy frissítéséhez a következő adatokat kell megadnia. Ez a formátum ugyanaz a formátum, amely akkor jön létre, amikor létrehoz egy egyéni szerepkört a Azure Portal használatával.

```json
{
  "properties": {
    "roleName": "",
    "description": "",
    "assignableScopes": [],
    "permissions": [
      {
        "actions": [],
        "notActions": [],
        "dataActions": [],
        "notDataActions": []
      }
    ]
  }
}
```

Az alábbi példa bemutatja a kimenetet, amikor egyéni szerepkört listáz a REST API használatával.

```json
{
    "properties": {
        "roleName": "",
        "type": "CustomRole",
        "description": "",
        "assignableScopes": [],
        "permissions": [
            {
                "actions": [],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ],
        "createdOn": "",
        "updatedOn": "",
        "createdBy": "",
        "updatedBy": ""
    },
    "id": "",
    "type": "Microsoft.Authorization/roleDefinitions",
    "name": ""
}
```

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: egyéni Azure-szerepkör létrehozása Azure PowerShell használatával](tutorial-custom-role-powershell.md)
- [Oktatóanyag: egyéni Azure-szerepkör létrehozása az Azure CLI használatával](tutorial-custom-role-cli.md)
- [Az Azure szerepkör-definíciók ismertetése](role-definitions.md)
- [Az Azure RBAC hibáinak megoldása](troubleshooting.md)

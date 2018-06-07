---
title: Hozzon létre egyéni szerepkörök az Azure RBAC |} Microsoft Docs
description: Megtudhatja, hogyan pontosabb identitáskezeléshez átruházásához hozzáférés-vezérléssel egyéni szerepkörök definiálása az Azure-előfizetésben.
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
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e75f2cceed83d577730cd29490f80011bdb0a508
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640982"
---
# <a name="create-custom-roles-in-azure"></a>Hozzon létre egyéni szerepkörök az Azure-ban

Ha a [beépített szerepkörök](built-in-roles.md) nem felelnek meg a kívánt hozzáférés igényeinek, saját egyéni szerepköröket is létrehozhat. Hasonlóan a beépített szerepkörök egyéni szerepkörök hozzárendelése felhasználók, csoportok és szolgáltatásnevekről előfizetés, erőforráscsoport és erőforrás-hatókörök. Egyéni szerepkörök az Azure Active Directory (Azure AD) bérlő tárolódnak, és előfizetések között megosztható legyen. Mindegyik bérlő legfeljebb 2000 egyéni szerepkörök is rendelkezhetnek. Egyéni szerepkörök az Azure PowerShell, az Azure parancssori felület vagy a REST API használatával hozhatók létre.

Ez a cikk ismerteti a példa bemutatja, hogyan való PowerShell és az Azure parancssori felület használatával egyéni szerepkörök létrehozásához.

## <a name="create-a-custom-role-to-open-support-requests-using-powershell"></a>Hozzon létre egy egyéni biztonsági szerepkört megnyitásához támogatási kérelmek PowerShell használatával

Szeretne létrehozni egy egyéni biztonsági szerepkört, egy beépített szerepkör kezdődnie, szerkesztheti, és ezután hozzon létre egy új szerepkört. Az ebben a példában a beépített [olvasó](built-in-roles.md#reader) szerepkör testre szabott "olvasó támogatási jegyek hozzáférési szint" nevű egyéni szerepkör létrehozása. Ez lehetővé teszi a felhasználónak az előfizetés, valamint a Megnyitás támogatási kérelmek mindent megtekinthetnek.

> [!NOTE]
> A csak két beépített szerepkörök, amelyek lehetővé teszik a felhasználót, hogy nyissa meg a támogatási kérelmek [tulajdonos](built-in-roles.md#owner) és [közreműködő](built-in-roles.md#contributor). A felhasználó megnyithatja a támogatási kérelmek ő szerepkört kell hozzárendelni egy az előfizetési hatókört, mert összes támogatási kérelmek létrehozása az Azure-előfizetés alapján.

A PowerShellben használja a [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) parancs használatával exportálja a [olvasó](built-in-roles.md#reader) szerepkör JSON formátumban.

```azurepowershell
Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json
```

A következő példa a JSON-kimenetét a [olvasó](built-in-roles.md#reader) szerepkör. Egy tipikus szerepkör áll három fő szakasz `Actions`, `NotActions`, és `AssignableScopes`. A `Actions` a szakasz a megengedett műveletek a szerepkörhöz. Műveletek kizárása `Actions`, hozzáadhatja őket a `NotActions`. A hatályos engedélyek számított kivonja a `NotActions` műveletek a `Actions` műveletek.

```json
{
    "Name":  "Reader",
    "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "IsCustom":  false,
    "Description":  "Lets you view everything, but not make any changes.",
    "Actions":  [
                    "*/read"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

A következő szerkeszti a JSON kimeneti az egyéni szerepkör létrehozásához. Ebben az esetben létrehozása támogatási jegyek, a `Microsoft.Support/*` műveletet hozzá kell adni. Minden műveletet olyan erőforrás-szolgáltató legyen elérhető. Ahhoz, hogy az erőforrás-szolgáltató műveletek listáját, használhatja a [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) parancsot, vagy tekintse meg [Azure Resource Manager erőforrás-szolgáltatói műveletekhez](resource-provider-operations.md).

Fontos, hogy a szerepkör tartalmazza a explicit előfizetési azonosítók felhasználási kötelező. Az előfizetési azonosítók a `AssignableScopes`, ellenkező esetben Ön nem jogosult lesz importálnia kell a szerepkört az előfizetés.

Végezetül be kell állítani a `IsCustom` tulajdonságot `true` adhatja meg, hogy ez az egy egyéni biztonsági szerepkört.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/subscriptions/11111111-1111-1111-1111-111111111111"
                         ]
}
```

Az új egyéni szerepkör létrehozásához használja a [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) parancsot, és adja meg a JSON szerepkör frissített definíciós fájl.

```azurepowershell
New-AzureRmRoleDefinition -InputFile "C:\rbacrole2.json"
```

Futtatása után [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition), az új egyéni szerepkör érhető el az Azure portálon, és a felhasználók számára is hozzárendelhető.

![Képernyőkép a egyéni biztonsági szerepkört importálni az Azure-portálon](./media/custom-roles/18.png)

![Képernyőkép a egyéni importált szerepkör hozzárendelése felhasználóhoz ugyanabban a könyvtárban](./media/custom-roles/19.png)

![egyéni importált szerepkör engedélyeinek képernyőképe](./media/custom-roles/20.png)

Egyéni szerepkörrel rendelkező felhasználók hozhatnak létre új támogatási kérelmek.

![Képernyőkép a támogatási kérelmek létrehozása egyéni szerepkör](./media/custom-roles/21.png)

Egyéni szerepkörrel rendelkező felhasználók nem egyéb műveleteket hajthat végre, például a virtuális gépek létrehozása vagy erőforráscsoportokat létrehozni.

![egyéni szerepkör nem sikerült létrehozni a virtuális gépek képernyőképe](./media/custom-roles/22.png)

![egyéni szerepkör nem hozhat létre új RGs képernyőképe](./media/custom-roles/23.png)

## <a name="create-a-custom-role-to-open-support-requests-using-azure-cli"></a>Hozzon létre egy egyéni biztonsági szerepkört megnyitásához támogatási kérelmek Azure parancssori felület használatával

Azure parancssori felület használatával egyéni szerepkör létrehozásának lépései hasonlóak powershellel, azzal a különbséggel, hogy a JSON-kimenetét nem egyezik.

Például elindíthatja a beépített [olvasó](built-in-roles.md#reader) szerepkör. A műveletek a listát a [olvasó](built-in-roles.md#reader) szerepkör, használja a [szerepkör-definíció listán az](/cli/azure/role/definition#az_role_definition_list) parancs.

```azurecli
az role definition list --name "Reader" --output json
```

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
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

Hozzon létre egy JSON-fájl a következő formátumban. A `Microsoft.Support/*` művelet hozzá lett adva a `Actions` részek, hogy a felhasználói támogatási kérelmek is megnyithatja, miközben továbbra is egy olvasó lehet. Hozzá kell adni az előfizetés-azonosító, amelyeken ezt a szerepkört a kell használni a `AssignableScopes` szakasz.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes": [
                            "/subscriptions/11111111-1111-1111-1111-111111111111"
                        ]
}
```

Az új egyéni szerepkör létrehozásához használja a [az szerepkör-definíció létrehozása](/cli/azure/role/definition#az_role_definition_create) parancsot.

```azurecli
az role definition create --role-definition ~/roles/rbacrole1.json
```

Az új egyéni szerepkör már elérhető az Azure portálon, és ez a szerepkör folyamata megegyezik az előző PowerShell szakaszban.

![Parancssori felület 1.0 használatával létrehozott egyéni biztonsági szerepkört az Azure portál képernyőképe](./media/custom-roles/26.png)


## <a name="see-also"></a>Lásd még
- [Szerepkör-definíciók ismertetése](role-definitions.md)
- [Szerepköralapú hozzáférés-vezérlés AzurePowerShell kezelése](role-assignments-powershell.md)
- [Az Azure parancssori felület szerepköralapú hozzáférés-vezérlés kezelése](role-assignments-cli.md)
- [A REST API szerepköralapú hozzáférés-vezérlés kezelése](role-assignments-rest.md)

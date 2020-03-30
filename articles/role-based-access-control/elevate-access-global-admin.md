---
title: Hozzáférési jogosultságszint emelése az összes Azure-előfizetés és felügyeleti csoport kezeléséhez
description: Ez a témakör ismerteti, hogyan emelheti a globális rendszergazda hozzáférését az Azure Active Directory összes előfizetésének és felügyeleti csoportjának kezeléséhez az Azure Portal vagy a REST API használatával.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: bagovind
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/03/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1731c630cb98ac542ebcdc7aedf07f7bb63eaec0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137461"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Az összes Azure-előfizetés és felügyeleti csoport kezeléséhez való hozzáférés magasabb szintű ítése

Az Azure Active Directory (Azure AD) globális rendszergazdájaként előfordulhat, hogy nem fér hozzá a címtárban lévő összes előfizetéshez és felügyeleti csoporthoz. Ez a cikk bemutatja, hogyan emelheti a hozzáférést az összes előfizetéshez és felügyeleti csoporthoz.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Miért kellene magasabbra emelned a hozzáférésed?

Ha Ön globális rendszergazda, előfordulhat, hogy a következőket szeretné tenni:

- Hozzáférés visszaszerzése egy Azure-előfizetéshez vagy felügyeleti csoporthoz, ha egy felhasználó elvesztette a hozzáférést
- Azure-előfizetéshez vagy felügyeleti csoporthoz való hozzáférés engedélyezése más felhasználónak vagy saját részre
- A szervezet összes Azure-előfizetésének vagy felügyeleti csoportjának megtekintése
- Az összes Azure-előfizetés vagy felügyeleti csoport elérésének engedélyezése egy automatizálási alkalmazásnak (például számlázási vagy naplózási alkalmazásnak)

## <a name="how-does-elevated-access-work"></a>Hogyan működik az emelt szintű hozzáférés?

Az Azure AD és az Azure-erőforrások egymástól függetlenül vannak biztosítva. Ez azt jelenti, hogy az Azure AD szerepkör-hozzárendelések nem biztosítanak hozzáférést az Azure-erőforrásokhoz, és az Azure-szerepkör-hozzárendelések nem biztosítanak hozzáférést az Azure AD-hez. Ha azonban az Azure AD [globális rendszergazdája,](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) saját maga is hozzárendelheti a hozzáférést a címtárban lévő összes Azure-előfizetéshez és felügyeleti csoporthoz. Akkor használja ezt a funkciót, ha nem fér hozzá az Azure-előfizetés erőforrásaihoz, például a virtuális gépekhez vagy a tárfiókokhoz, és a globális rendszergazdai jogosultsággal szeretné elérni ezeket az erőforrásokat.

A hozzáférés magasabb szintű ítésekén a rendszer a [felhasználói hozzáférés-rendszergazdai](built-in-roles.md#user-access-administrator) szerepkört kapja az Azure-ban a gyökérhatókörben (`/`.Ez lehetővé teszi az összes erőforrás megtekintését és a hozzáférést a címtár bármely előfizetési vagy felügyeleti csoportjában. A Felhasználói hozzáférés rendszergazdája szerepkör-hozzárendelések eltávolíthatók az Azure PowerShell, az Azure CLI vagy a REST API használatával.

A emelt szintű hozzáférést távolítsa el, miután elkészítette a legfelső szintű hatókörben elvégzett módosításokat.

![Hozzáférési szint emelése](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure portál

### <a name="elevate-access-for-a-global-administrator"></a>Globális rendszergazda hozzáférése magasabb rakoncátsokban

Az alábbi lépésekkel magasabb szintű hozzáférést biztosít egy globális rendszergazda számára az Azure Portalhasználatával.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) vagy az [Azure Active Directory felügyeleti központjába](https://aad.portal.azure.com) globális rendszergazdaként.

1. Keresse meg és válassza ki az **Azure Active Directoryt**.

   ![Az Azure Active Directory kiválasztása – képernyőkép](./media/elevate-access-global-admin/search-for-azure-active-directory.png)

1. A **Kezelés csoportban**válassza a **Tulajdonságok lehetőséget.**

   ![Az Azure Active Directory tulajdonságainak tulajdonságainak kiválasztása – képernyőkép](./media/elevate-access-global-admin/azure-active-directory-properties.png)

1. Az **Azure-erőforrások Hozzáférés-kezelése**csoportban állítsa a kapcsolót **Igen**beállításra.

   ![Hozzáférés-kezelés az Azure-erőforrásokhoz – képernyőkép](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Ha a kapcsolót **Igen**értékre állítja, a felhasználói hozzáférés-rendszergazdai szerepkör az Azure RBAC-ban a gyökérhatókörben (/) van hozzárendelve. Ez engedélyt ad szerepkörök hozzárendelésére az Azure AD-címtárhoz társított összes Azure-előfizetésben és felügyeleti csoportban. Ez a kapcsoló csak azok nak a felhasználókszámára érhető el, akik az Azure AD globális rendszergazdai szerepkörrel vannak elrendelve.

   Ha a kapcsolót **Nem,** a felhasználói hozzáférés rendszergazda szerepkör az Azure RBAC törlődik a felhasználói fiókból. Az Azure AD-címtárhoz társított összes Azure-előfizetésben és felügyeleti csoportban már nem rendelhet hozzá szerepköröket. Csak azokat az Azure-előfizetéseket és felügyeleti csoportokat tekintheti meg és kezelheti, amelyekhez hozzáférést kapott.

    > [!NOTE]
    > Ha [az Azure AD privilegizált identitáskezelés (PIM)](../active-directory/privileged-identity-management/pim-configure.md)használatával, a szerepkör-hozzárendelés inaktiválása nem módosítja ezt a kapcsolót **Nem**értékre. A legkisebb jogosultsági szintű hozzáférés fenntartása érdekében azt javasoljuk, hogy állítsa ezt a kapcsolót **Nem,** mielőtt inaktiválja a szerepkör-hozzárendelés.
    
1. A beállítás mentéséhez kattintson a **Mentés** gombra.

   Ez a beállítás nem globális tulajdonság, és csak az aktuálisan bejelentkezett felhasználóra vonatkozik. A globális rendszergazdai szerepkör minden tagjának hozzáférése nem emelhető.

1. Jelentkezzen ki, majd jelentkezzen be újra a hozzáférés frissítéséhez.

    Most már hozzáférhet a címtárban lévő összes előfizetéshez és felügyeleti csoporthoz. A hozzáférés-vezérlési (IAM) ablaktábla megtekintésekor észre fogja venni, hogy a felhasználói hozzáférés-rendszergazdaszerepkör t kapott a gyökérhatókörhöz.

   ![Előfizetési szerepkör-hozzárendelések gyökérhatókörrel – képernyőkép](./media/elevate-access-global-admin/iam-root.png)

1. Hajtsa végre a szükséges módosításokat a emelt szintű hozzáféréssel.

    A szerepkörök hozzárendeléséről az [Access kezelése az RBAC és az Azure Portal használatával című témakörben](role-assignments-portal.md)talál. Ha az Azure AD privilegizált identitáskezelés (PIM) használatával, [lásd: Azure-erőforrások felderítése a PIM-ben,](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) vagy [az Azure-erőforrás-szerepkörök hozzárendelése a PIM-ben.](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md)

### <a name="remove-elevated-access"></a>Emelt szintű hozzáférés eltávolítása

A Felhasználói hozzáférés rendszergazdája szerepkör-hozzárendelés`/`eltávolításához a gyökérhatókörben ( ) hajtsa végre az alábbi lépéseket.

1. Jelentkezzen be ugyanazzal a felhasználóval, amivel magasabb szintre emelheti a hozzáférést.

1. A navigációs listában kattintson az **Azure Active Directory** elemre, majd a **Tulajdonságok**gombra.

1. Állítsa az **Azure-erőforrások Hozzáférés-kezelését,** és állítsa vissza a **Nem kapcsolót.** Mivel ez egy felhasználónkénti beállítás, akkor ugyanazzal a felhasználóval kell bejelentkeznie, mint amilyen a hozzáférés magasabb szintre állításához használt.

    Ha megpróbálja eltávolítani a Felhasználói hozzáférés-rendszergazda szerepkör-hozzárendelést a Hozzáférés-vezérlési (IAM) ablaktáblán, a következő üzenet jelenik meg. A szerepkör-hozzárendelés eltávolításához állítsa vissza a kapcsolót **Nem-** re, vagy az Azure PowerShell, az Azure CLI vagy a REST API használatát.

    ![Szerepkör-hozzárendelések eltávolítása gyökérhatókörrel](./media/elevate-access-global-admin/iam-root-remove.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-root-scope-"></a>Szerepkör-hozzárendelés listázása a gyökérhatókörben (/)

A Felhasználói hozzáférés rendszergazda szerepkör-hozzárendelésének listázásához használja`/`a [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) parancsot.

```azurepowershell
Get-AzRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : User
CanDelegate        : False
```

### <a name="remove-elevated-access"></a>Emelt szintű hozzáférés eltávolítása

Ha el szeretné távolítani a Felhasználói hozzáférés rendszergazdája`/`szerepkör-hozzárendelést saját magának vagy egy másik felhasználónak a gyökérhatókörben ( ) , kövesse az alábbi lépéseket.

1. Jelentkezzen be olyan felhasználóként, aki eltávolíthatja az emelt szintű hozzáférést. Ez lehet ugyanaz a felhasználó, amelyet a hozzáférés magasabb szintű eléréséhez használt, vagy egy másik globális rendszergazda, akinek emelt szintű hozzáférése van a gyökérhatókörben.

1. A Felhasználói hozzáférés rendszergazdája szerepkör-hozzárendelés eltávolításához használja az [Eltávolítás-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) parancsot.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="azure-cli"></a>Azure CLI

### <a name="list-role-assignment-at-root-scope-"></a>Szerepkör-hozzárendelés listázása a gyökérhatókörben (/)

A felhasználói hozzáférés rendszergazdája szerepkör-hozzárendelés listázásához használja a legfelső szintű`/` [szerepkör-hozzárendelési lista](/cli/azure/role/assignment#az-role-assignment-list) parancsát.

```azurecli
az role assignment list --role "User Access Administrator" --scope "/"
```

```Example
[
  {
    "canDelegate": null,
    "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
    "name": "11111111-1111-1111-1111-111111111111",
    "principalId": "22222222-2222-2222-2222-222222222222",
    "principalName": "username@example.com",
    "principalType": "User",
    "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "roleDefinitionName": "User Access Administrator",
    "scope": "/",
    "type": "Microsoft.Authorization/roleAssignments"
  }
]

```

### <a name="remove-elevated-access"></a>Emelt szintű hozzáférés eltávolítása

Ha el szeretné távolítani a Felhasználói hozzáférés rendszergazdája`/`szerepkör-hozzárendelést saját magának vagy egy másik felhasználónak a gyökérhatókörben ( ) , kövesse az alábbi lépéseket.

1. Jelentkezzen be olyan felhasználóként, aki eltávolíthatja az emelt szintű hozzáférést. Ez lehet ugyanaz a felhasználó, amelyet a hozzáférés magasabb szintű eléréséhez használt, vagy egy másik globális rendszergazda, akinek emelt szintű hozzáférése van a gyökérhatókörben.

1. A Felhasználói hozzáférés rendszergazdája szerepkör-hozzárendelés eltávolításához használja az [az szerepkör-hozzárendelés törlésparancsát.](/cli/azure/role/assignment#az-role-assignment-delete)

    ```azurecli
    az role assignment delete --assignee username@example.com --role "User Access Administrator" --scope "/"
    ```

## <a name="rest-api"></a>REST API

### <a name="elevate-access-for-a-global-administrator"></a>Globális rendszergazda hozzáférése magasabb rakoncátsokban

Az alábbi alapvető lépésekkel magasabb szintű hozzáférést biztosít hatni a REST API-t használó globális rendszergazdák számára.

1. A REST `elevateAccess`használatával hívja meg a hívást, amely`/`a felhasználói hozzáférés rendszergazdája szerepkört biztosítja a gyökérhatókörben ( ).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Hozzon létre egy [szerepkör-hozzárendelést](/rest/api/authorization/roleassignments) bármely hatókör bármely szerepkörének hozzárendeléséhez. A következő példa a {roleDefinitionID} szerepkör root hatókörben`/`( ) való hozzárendelésének tulajdonságait mutatja be:

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "11111111-1111-1111-1111-111111111111"
   }
   ```

1. Felhasználó-hozzáférési rendszergazdaként a szerepkör-hozzárendeléseket a gyökérhatókörben (`/`is eltávolíthatja.

1. Távolítsa el a felhasználói hozzáférés-rendszergazdai jogosultságokat, amíg újra szükség nem lesz rájuk.

### <a name="list-role-assignments-at-root-scope-"></a>Szerepkör-hozzárendelések listázása a gyökérhatókörben (/)

A felhasználó összes szerepkör-hozzárendelését a gyökérhatókörben`/`( listázhatja .

- Hívja meg a `{objectIdOfUser}` [GET szerepkör-hozzárendeléseket,](/rest/api/authorization/roleassignments/listforscope) ahol annak a felhasználónak az objektumazonosítója, akinek a szerepkör-hozzárendeléseit be szeretné olvasni.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-root-scope-"></a>Megtagadási hozzárendelések listázása a gyökérhatókörben (/)

A felhasználó összes megtagadási hozzárendelését a gyökérhatókörben (`/`listázhatja .

- Hívja meg a `{objectIdOfUser}` GET denyAssignments-eket, ahol annak a felhasználónak az objektumazonosítója van, akinek a megtagadási hozzárendeléseit be szeretné olvasni.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Emelt szintű hozzáférés eltávolítása

A híváskor `elevateAccess`saját magának hoz létre szerepkör-hozzárendelést, ezért a jogosultságok visszavonásához el kell távolítania a Felhasználói hozzáférés-rendszergazda szerepkör-hozzárendelést saját maga a gyökérhatókörben (`/`)

1. Hívja meg a `roleName` [GET szerepkördefiníciókat,](/rest/api/authorization/roledefinitions/get) ahol a felhasználói hozzáférés rendszergazdája határozza meg a Felhasználói hozzáférés-rendszergazda szerepkör névazonosítóját.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
      "roleName": "User Access Administrator",
      "type": "BuiltInRole",
      "description": "Lets you manage user access to Azure resources.",
      "assignableScopes": [
        "/"
      ],
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Authorization/*",
            "Microsoft.Support/*"
          ],
          "notActions": []
        }
      ],
      "createdOn": "0001-01-01T08:00:00.0000000Z",
      "updatedOn": "2016-05-31T23:14:04.6964687Z",
      "createdBy": null,
      "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    Mentse az azonosítót `name` a paraméterből, ebben az esetben `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`.

1. A címtár-rendszergazda szerepkör-hozzárendelését is fel kell sorolnia a címtárhatókörben. Sorolja fel az összes `principalId` hozzárendelést a címtárhatókörben annak a címtár-rendszergazdának, aki a hozzáférési hívást kezdeményezte. Ez felsorolja az objectid könyvtárában lévő összes hozzárendelést.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
        
    >[!NOTE] 
    >A címtárrendszergazdának nem lehet sok hozzárendelése, ha az előző lekérdezés túl sok hozzárendelést ad vissza, az összes hozzárendelést csak a címtárhatókör szintjén is lekérdezheti, majd szűrheti az eredményeket:`GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
            
1. Az előző hívások a szerepkör-hozzárendelések listáját adják vissza. Keresse meg a szerepkör-hozzárendelést, ahol a hatókör van, `"/"` és a `roleDefinitionId` végén az 1. `principalId` 
    
    Mintaszerepkör-hozzárendelés:
    
    ```json
    {
      "value": [
        {
          "properties": {
            "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
            "principalId": "{objectID}",
            "scope": "/",
            "createdOn": "2016-08-17T19:21:16.3422480Z",
            "updatedOn": "2016-08-17T19:21:16.3422480Z",
            "createdBy": "22222222-2222-2222-2222-222222222222",
            "updatedBy": "22222222-2222-2222-2222-222222222222"
          },
          "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
          "type": "Microsoft.Authorization/roleAssignments",
          "name": "11111111-1111-1111-1111-111111111111"
        }
      ],
      "nextLink": null
    }
    ```
    
    Ismét mentse az azonosítót `name` a paraméter, ebben az esetben 11111111-1111-1111-11111111111111111111111.

1. Végül a szerepkör-hozzárendelésazonosító segítségével távolítsa el `elevateAccess`a következő höz hozzáadott hozzárendelést:

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111?api-version=2015-07-01
    ```

## <a name="next-steps"></a>További lépések

- [A különböző Azure-beli szerepkörök ismertetése](rbac-and-directory-admin-roles.md)
- [Azure-erőforrásokhoz való hozzáférés kezelése az RBAC és a REST API használatával](role-assignments-rest.md)

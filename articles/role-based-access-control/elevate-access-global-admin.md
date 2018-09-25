---
title: Az Azure Active Directoryban egy globális rendszergazda hozzáférési szintjének emelése |} A Microsoft Docs
description: Ismerteti, hogyan lehet az Azure Active Directoryban az Azure portal vagy a REST API használatával egy globális rendszergazda hozzáférési szintjének emelése.
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
ms.date: 09/24/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: fb0fb4e0f23413cb56b1bb5ec419c44dfc52e7b6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996842"
---
# <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Az Azure Active Directoryban egy globális rendszergazda hozzáférési szintjének emelése

Ha Ön egy [globális rendszergazdai](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator) az Azure Active Directoryban (Azure AD), előfordulhat, azt szeretné, tegye a következőket:

- Egy Azure-előfizetéshez való hozzáférés visszaszerzéséhez, amikor a felhasználó elveszíti a hozzáférést
- Adja meg egy másik felhasználó, és saját kezűleg el az Azure-előfizetéshez
- Tekintse meg a szervezeten belüli összes Azure-előfizetések
- Minden Azure-előfizetés eléréséhez egy automation-alkalmazást (például számlázási vagy naplózási alkalmazás) engedélyezése

Alapértelmezés szerint az Azure AD-rendszergazdai szerepköröket és az Azure szerepköralapú hozzáférés-vezérlést (RBAC) szerepkörök do nem span az Azure AD és az Azure. Azonban ha az Azure AD globális rendszergazda, szintre emelhet, az Azure-előfizetések és a felügyeleti csoportok kezelése a hozzáférést. Ha Ön a hozzáférési szintjének emelése, akkor kapnak a [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) szerepkört (RBAC szerepkör) egy adott bérlő összes előfizetés. A felhasználói hozzáférés rendszergazdájának szerepköre lehetővé teszi, hogy hozzáférést biztosítani más felhasználók Azure-erőforrások a gyökérszintű hatókörben (`/`).

A jogosultságszint-emelés a ideiglenes és csak szükség esetén kész kell lennie.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="azure-portal"></a>Azure Portal

Kövesse az alábbi lépéseket az Azure portal használatával egy globális rendszergazda hozzáférési szintjének emelése.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) vagy a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com).

1. Kattintson a navigációs lista **Azure Active Directory** majd **tulajdonságok**.

   ![Az Azure AD tulajdonságok – képernyőkép](./media/elevate-access-global-admin/aad-properties.png)

1. A **globális rendszergazda kezelheti az Azure-előfizetéseket és a felügyeleti csoportok**, a kapcsoló beállítása **Igen**.

   ![Globális rendszergazda kezelheti az Azure-előfizetéseket és a felügyeleti csoport – képernyőkép](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Ha beállította a kapcsoló **Igen**, a globális rendszergazdai fiókjával (jelenleg bejelentkezett felhasználó) kerül a felhasználói hozzáférés rendszergazdájának szerepköre az Azure RBAC a gyökérszintű hatókörben (`/`), mely engedélyezi a nézet és a jelentés eléréséhez az összes Azure-előfizetések az Azure AD-bérlőhöz társított.

   Ha beállította a kapcsoló **nem**, a globális rendszergazdai fiókjával (jelenleg bejelentkezett felhasználó) eltávolítják az Azure RBAC-ben a felhasználói hozzáférés rendszergazdája szerepkör. Az összes Azure-előfizetések az Azure AD-bérlőhöz társított nem fogja látni, és megtekintheti, és csak az Azure-előfizetések kezelése, amelyhez rendelkezik hozzáféréssel.

1. Kattintson a **mentése** a beállítás mentéséhez.

   Ez a beállítás a globális tulajdonság nem, és csak a bejelentkezett felhasználó vonatkozik.

1. Végre kell hajtania az emelt szintű hozzáférési feladatokat. Amikor elkészült, a kapcsoló állítsa vissza a **nem**.

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-role-assignment-at-the-root-scope-"></a>A gyökérszintű hatókörben (/) szerepkör-hozzárendelés listája

A gyökérszintű hatókörben egy felhasználó a felhasználói hozzáférés rendszergazdája szerepkör-hozzárendelés felsorolása (`/`), használja a [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) parancsot.

```azurepowershell
Get-AzureRmRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc
ObjectType         : User
```

### <a name="remove-a-role-assignment-at-the-root-scope-"></a>A gyökérszintű hatókörben (/) szerepkör-hozzárendelés eltávolítása

A gyökérszintű hatókörben egy felhasználó a felhasználói hozzáférés rendszergazdája szerepkör-hozzárendelés eltávolítása (`/`), használja a [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment) parancsot.

```azurepowershell
Remove-AzureRmRoleAssignment -SignInName <username@example.com> `
  -RoleDefinitionName "User Access Administrator" -Scope "/"
```

## <a name="rest-api"></a>REST API

### <a name="elevate-access-for-a-global-administrator"></a>Egy globális rendszergazda hozzáférési szintjének emelése

Az alábbi alapvető lépéseket használatával jogosultságszintjének emelése egy globális rendszergazda, a REST API használatával.

1. REST használatával hívja `elevateAccess`, amely engedélyezi, hogy a felhasználói hozzáférés rendszergazdája szerepkör a gyökérszintű hatókörben (`/`).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Hozzon létre egy [szerepkör-hozzárendelés](/rest/api/authorization/roleassignments) bármely hatókörben bármely szerepkör hozzárendelése. Az alábbi példa bemutatja a gyökérszintű hatókörben a {roleDefinitionID} szerepkör hozzárendelése tulajdonságai (`/`):

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
   }
   ```

1. A felhasználói hozzáférés rendszergazdája közben is eltávolíthatja a gyökérszintű hatókörben szerepkör-hozzárendelések (`/`).

1. Távolítsa el a felhasználói hozzáférés rendszergazdai jogosultságokkal, amíg szükség van rájuk újra.

### <a name="list-role-assignments-at-the-root-scope-"></a>A gyökérszintű hatókörben (/) szerepkör-hozzárendelések listát

A szerepkör-hozzárendeléseket a gyökérszintű hatókörben egy felhasználó összes listázhatja (`/`).

- Hívás [GET Szerepkörkiosztások](/rest/api/authorization/roleassignments/listforscope) ahol `{objectIdOfUser}` a felhasználót, amelynek szeretné beolvasni a szerepkör-hozzárendelések objektum azonosítója.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-the-root-scope-"></a>Listában elutasítása hozzárendelések a gyökérszintű hatókörben (/)

Listázhatja az összes, a Megtagadás hozzárendelését az olyan a gyökérszintű hatókörben (`/`).

- Hívja a GET-denyAssignments ahol `{objectIdOfUser}` azon megtagadása hozzárendelések szeretné beolvasni a felhasználói objektum azonosítója.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Emelt szintű hozzáférés eltávolítása

Meghívásakor `elevateAccess`, szerepkör-hozzárendelés hoz létre a saját maga számára, így ezeket a jogosultságokat visszavonása el kell távolítania a hozzárendelést.

1. Hívás [GET roleDefinitions](/rest/api/authorization/roledefinitions/get) ahol `roleName` eredménye a felhasználói hozzáférés adminisztrátora, a felhasználói hozzáférés rendszergazdája szerepkör neve Azonosítóját határozza meg.

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

    Mentse az Azonosítót a a `name` paramétert, ebben az esetben `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`.

2. Fel is kell a bérlői hatókörben Bérlői rendszergazda szerepkör-hozzárendelést. Minden hozzárendelését a bérlői hatókörben listázása a `principalId` a bérlői rendszergazda azokat a jogosultságszint-emelés hozzáférés, hívja. Ez felsorolja az objektumazonosító a bérlő összes hozzárendelést.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >Bérlői rendszergazda nem rendelkezhet számos hozzárendelésnél, ha az előző lekérdezést a függvény túl sok hozzárendelések is lekérdezheti, ha az összes hozzárendelés csak bérlői hatókör szintjén, majd az eredmények szűréséhez: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
    2. Az előző hívás az szerepkör-hozzárendelések listáját adja vissza. Keresse meg a szerepkör-hozzárendelés, ahol a hatókör az `"/"` és a `roleDefinitionId` végződik a szerepkör nevének azonosítója, az 1. lépésben található és `principalId` objectid azonosítóját, a bérlői rendszergazda megegyezik. 
    
    Szerepkör-hozzárendelés minta:

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
                "createdBy": "93ce6722-3638-4222-b582-78b75c5c6d65",
                "updatedBy": "93ce6722-3638-4222-b582-78b75c5c6d65"
              },
              "id": "/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
              "type": "Microsoft.Authorization/roleAssignments",
              "name": "e7dd75bc-06f6-4e71-9014-ee96a929d099"
            }
          ],
          "nextLink": null
        }
        ```
        
    Ismét mentse az Azonosítót a a `name` paramétert, ebben az esetben e7dd75bc-06f6-4e71-9014-ee96a929d099.

    3. Végül a hozzáadta a hozzárendelés eltávolítása a szerepkör-hozzárendelés azonosítója `elevateAccess`:

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>További lépések

- [Szerepköralapú hozzáférés-vezérlés a REST segítségével](role-assignments-rest.md)
- [A Privileged Identity Management Azure-erőforrásokhoz való hozzáférés kezelése](pim-azure-resource.md)
- [A feltételes hozzáférés az Azure-felügyelet elérésének kezelése](conditional-access-azure-management.md)

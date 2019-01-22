---
title: Az Azure-előfizetések és a felügyeleti csoportok kezelése hozzáférési szintjének emelése |} A Microsoft Docs
description: Összes előfizetés és a felügyeleti csoportok az Azure Active Directoryban az Azure portal vagy a REST API használata kezelheti egy globális rendszergazda hozzáférési szintjének emelése módját ismerteti.
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
ms.date: 01/15/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 7552018c32078295c164023f909a604c6522c32f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54437470"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Az Azure-előfizetések és a felügyeleti csoportok kezelése hozzáférési szintjének emelése

Globális rendszergazdaként az Azure Active Directoryban (Azure AD) akkor nem tudja elérni az összes előfizetés és a felügyeleti csoportok a címtárban. Ez a cikk leírja, hogyan, hogy szintre emelhet, a hozzáférés az összes előfizetés és a felügyeleti csoportok.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Miért érdemes lenne szükség van a hozzáférési szintjének emelése?

Ha egy globális rendszergazdai, előfordulhat, hogy azt szeretné, a következő lehet:

- Azure-előfizetés vagy a felügyeleti csoportok való hozzáférés visszaszerzéséhez, amikor a felhasználó elveszíti a hozzáférést
- Adja meg egy másik felhasználó, vagy saját kezűleg elérni az Azure-előfizetés vagy a felügyeleti csoportok
- Tekintse meg az összes Azure-előfizetések vagy a szervezet felügyeleti csoportok
- Lehetővé teszi egy automation-alkalmazást (például számlázási vagy naplózási alkalmazás) eléréséhez az Azure-előfizetések vagy a felügyeleti csoportok

## <a name="how-does-elevate-access-work"></a>Hogyan szintjének emelése a hozzáférés munkahelyi?

Az Azure AD és az Azure-erőforrások egymástól függetlenül biztonságosak. Azt jelenti az Azure AD szerepkör-hozzárendelések nem biztosíthat hozzáférést az Azure-erőforrásokhoz, és Azure szerepkör-hozzárendelések nem biztosíthat hozzáférést az Azure ad-hez. Azonban ha Ön egy [globális rendszergazdai](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator) az Azure AD-ben rendelhet saját kezűleg hozzáférés az Azure-előfizetések és a felügyeleti csoportok a címtárban. Akkor használja ezt a funkciót, ha nem rendelkezik hozzáféréssel az Azure-előfizetések erőforrásaihoz, például virtuális gépeket vagy a storage-fiókok, és ezek az erőforrások eléréséhez a globális rendszergazda jogosultságot használni kívánt.

Ha Ön a hozzáférési szintjének emelése, akkor hozzá lesz rendelve a [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) gyökérszintű hatókörben Azure-ban szerepet (`/`). Ez lehetővé teszi, hogy az összes erőforrás megtekintéséhez és hozzárendeléséhez a hozzáférést minden olyan előfizetés vagy a felügyeleti csoport a címtárban. Felhasználói hozzáférés rendszergazdája szerepkör-hozzárendeléseit a PowerShell használatával eltávolítható.

Miután elvégezte a módosításokat, végre kell hajtania a gyökérszintű hatókörben, el kell távolítania az emelt szintű hozzáférés.

![Hozzáférési szintjének emelése](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure Portal

Kövesse az alábbi lépéseket az Azure portal használatával egy globális rendszergazda hozzáférési szintjének emelése.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) vagy a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) globális rendszergazdaként.

1. Kattintson a navigációs lista **Azure Active Directory** majd **tulajdonságok**.

   ![Az Azure AD tulajdonságok – képernyőkép](./media/elevate-access-global-admin/aad-properties.png)

1. A **Access management az Azure-erőforrások**, a váltógomb beállítása **Igen**.

   ![Hozzáférés-kezelés az Azure-erőforrásokhoz – képernyőkép](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Ha beállította a váltógomb **Igen**, az Azure RBAC a gyökérszintű hatókörben (/) a felhasználói hozzáférés rendszergazdája szerepkör van rendelve. Ezzel engedélyt ad meg szerepkörök hozzárendelése az összes Azure-előfizetések és az Azure AD-címtárhoz társított felügyeleti csoportokat. Ezzel a kapcsolóval csak az Azure AD globális rendszergazdai szerepkörrel felruházott felhasználók számára érhető el.

   Ha beállította a váltógomb **nem**, az Azure RBAC-ben a felhasználói hozzáférés rendszergazdája szerepkör törlődik a felhasználói fiók. Szerepkörök az összes Azure-előfizetések és az Azure AD-címtárhoz társított felügyeleti csoport már nem rendelhet hozzá. Megtekintheti, és kezelheti a csak az Azure-előfizetések és a felügyeleti csoportot, amelyhez rendelkezik hozzáféréssel.

1. Kattintson a **mentése** a beállítás mentéséhez.

   Ez a beállítás a globális tulajdonság nem, és csak a jelenleg bejelentkezett felhasználóra vonatkozik. A globális rendszergazdai szerepkör összes tagja számára nem jogosultságszintjének emelése.

1. Jelentkezzen ki, és jelentkezzen be újra a frissítés a hozzáférést.

    Akkor most már elérhetők az összes előfizetés és a felügyeleti csoportok a címtárban. Láthatja, hogy Önhöz rendelve a felhasználói hozzáférés rendszergazdája szerepkör gyökérszintű hatókörben.

   ![Előfizetés szerepkör-hozzárendeléseit a legfelső szintű hatóköre – képernyőkép](./media/elevate-access-global-admin/iam-root.png)

1. Győződjön meg a kívánt módosításokat, ha szeretné tenni az emelt szintű hozzáférés.

    További információ a szerepkörök hozzárendelése: [rbac-RÓL és az Azure portal-hozzáférés kezelése](role-assignments-portal.md). Ha az Azure AD Privileged Identity Management (PIM) használ, tekintse meg [Fedezze fel az Azure erőforrások kezeléséhez a PIM](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) vagy [erőforrás-szerepkörök hozzárendelése az Azure PIM](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md).

1. Amikor elkészült, állítsa be a **Access management az Azure-erőforrások** válthat vissza a **nem**. Mivel ez egy felhasználói beállítás, akkor be kell jelentkeznie ugyanazon felhasználóként, hozzáférési szintjének emelése használt.

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
CanDelegate        : False
```

### <a name="remove-a-role-assignment-at-the-root-scope-"></a>A gyökérszintű hatókörben (/) szerepkör-hozzárendelés eltávolítása

A gyökérszintű hatókörben egy felhasználó a felhasználói hozzáférés rendszergazdája szerepkör-hozzárendelés eltávolítása (`/`), kövesse az alábbi lépéseket.

1. Jelentkezzen be, eltávolíthatja az emelt szintű hozzáférés. Ez lehet ugyanaz a felhasználó hozzáférést és a egy másik globális rendszergazda emelt szintű hozzáféréssel a gyökérszintű hatókörben jogosultságszintjének emeléséhez használt.


1. Használja a [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment) parancsot a felhasználói hozzáférés rendszergazdája szerepkör-hozzárendelés eltávolítása.

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

2. Fel is kell a szerepkör-hozzárendelést a directory-rendszergazda directory hatókörben. Összes hozzárendelés directory hatókörben, a listában a `principalId` a directory-rendszergazda azokat a jogosultságszint-emelés hozzáférés, hívja. Ez felsorolja az összes hozzárendelés az objectid könyvtárában.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >A directory-rendszergazda nem rendelkezhet számos hozzárendelésnél, ha az előző lekérdezést ad vissza túl sok hozzárendelések is lekérdezheti az összes hozzárendelés csak könyvtár hatókör szintjén, majd az eredmények szűréséhez: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
    2. Az előző hívás az szerepkör-hozzárendelések listáját adja vissza. Keresse meg a szerepkör-hozzárendelés, ahol a hatókör az `"/"` és a `roleDefinitionId` végződik a szerepkör nevének azonosítója, az 1. lépésben található és `principalId` objectid azonosítóját, a directory-rendszergazda megegyezik. 
    
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

- [A különféle szerepkörök ismertetése](rbac-and-directory-admin-roles.md)
- [Szerepköralapú hozzáférés-vezérlés a REST segítségével](role-assignments-rest.md)

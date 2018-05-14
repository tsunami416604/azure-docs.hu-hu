---
title: Egy globális rendszergazda az Azure Active Directoryban jogosultságszintjének emelése |} Microsoft Docs
description: Útmutatás jogosultságszintjének emelése egy globális rendszergazda az Azure Active Directoryban az Azure portálon vagy REST API használatával.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/11/2018
ms.author: rolyon
ms.openlocfilehash: b671ff6b473093e59bce18c7bf98b32e9849bbb0
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
---
# <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Egy globális rendszergazda az Azure Active Directoryban jogosultságszintjének emelése

Ha Ön egy [globális rendszergazda](../active-directory/active-directory-assign-admin-roles-azure-portal.md#global-administrator) az Azure Active Directory (Azure AD), előfordulhat, ha azt szeretné, a következő alkalommal:

- Újra hozzáférést nyerni egy Azure-előfizetés, amikor a felhasználó elveszíti hozzáférés
- Adja meg egy másik felhasználó vagy saját maga által elérhető Azure-előfizetés
- Tekintse meg a szervezeten belüli összes Azure-előfizetések
- Az automatizálási alkalmazások (például a számlázási vagy naplózási alkalmazás) engedélyezése az összes Azure-előfizetések eléréséhez

Alapértelmezés szerint az Azure AD rendszergazdai szerepköröket és az Azure szerepköralapú hozzáférés-vezérlő (RBAC) szerepkörök tegye nem span az Azure AD és az Azure. Azonban ha egy globális rendszergazda Azure AD-ben, emelhet a hozzáférését használva kezelheti az Azure-előfizetések és a felügyeleti csoportok. Amikor emelheti a hozzáférést, mostantól a [felhasználói hozzáférés adminisztrátora](built-in-roles.md#user-access-administrator) szerepkört (RBAC szerepet) egy adott bérlő előfizetéseket. A felhasználói hozzáférés adminisztrátora szerepkör lehetővé teszi a többi felhasználónak hozzáférést biztosít a gyökérszintű hatókörben Azure-erőforrások (`/`).

A jogosultságszint-emelés kell ideiglenes, és csak végre, ha szükséges.

## <a name="elevate-access-for-a-global-administrator-using-the-azure-portal"></a>Egy globális rendszergazda az Azure portál használatával jogosultságszintjének emelése

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) vagy a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com).

1. Kattintson a navigációs lista **Azure Active Directory** majd **tulajdonságok**.

   ![Az Azure AD tulajdonságai – képernyőkép](./media/elevate-access-global-admin/aad-properties.png)

1. A **globális rendszergazdák által kezelhető, Azure-előfizetések és a felügyeleti csoportok**, a kapcsoló beállítása **Igen**.

   ![Globális rendszergazdák által kezelhető, Azure-előfizetések és a felügyeleti csoport – képernyőkép](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Ha beállította a kapcsoló **Igen**, a globális rendszergazdai fiókjával (jelenleg bejelentkezett felhasználó) kerül a felhasználói hozzáférés adminisztrátora szerepkört az Azure RBAC a gyökérszintű hatókörben (`/`), mely biztosít hozzáférést nézet és a jelentés a az összes Azure-előfizetések az Azure AD bérlőhöz társított.

   Ha beállította a kapcsoló **nem**, a globális rendszergazdai fiókjával (jelenleg bejelentkezett felhasználó) a felhasználói hozzáférés adminisztrátora szerepkört az Azure RBAC törlődik. Az Azure AD-bérlő társított összes Azure előfizetések nem jelennek meg, és megtekintheti, és csak az Azure-előfizetések kezeléséhez, amelyhez Ön hozzáférést kapott.

1. Kattintson a **mentése** a beállítás mentéséhez.

   Ez a beállítás globális tulajdonság, és csak a bejelentkezett felhasználó vonatkozik.

1. A feladatokat az emelt szintű hozzáférés szükséges. Amikor elkészült, a kapcsoló állítsa vissza a **nem**.

## <a name="list-role-assignment-at-the-root-scope--using-powershell"></a>Szerepkör-hozzárendelés lista a legfelső szintű hatókörében (/) PowerShell használatával

A gyökérszintű hatókörben egy felhasználó a felhasználói hozzáférés adminisztrátora szerepkör-hozzárendelés elemet (`/`), használja a [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) parancsot.

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

## <a name="delete-a-role-assignment-at-the-root-scope--using-powershell"></a>PowerShell-lel a gyökérszintű hatókörben (/) szerepkör-hozzárendelés törlése

A gyökérszintű hatókörben egy felhasználó a felhasználói hozzáférés adminisztrátora szerepkör-hozzárendelés törlése (`/`), használja a [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment) parancsot.

```azurepowershell
Remove-AzureRmRoleAssignment -SignInName <username@example.com> `
  -RoleDefinitionName "User Access Administrator" -Scope "/"
```

## <a name="elevate-access-for-a-global-administrator-using-the-rest-api"></a>Egy globális rendszergazda, a REST API használatával jogosultságszintjének emelése

A következő alapvető lépések segítségével jogosultságszintjének emelése egy globális rendszergazda, a REST API használatával.

1. Használja a többi, hívja `elevateAccess`, amely engedélyezi a felhasználói hozzáférés adminisztrátora szerepkör a gyökérszintű hatókörben (`/`).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Hozzon létre egy [szerepkör-hozzárendelés](/rest/api/authorization/roleassignments) bármely hatókörből szerepköröket hozzárendelni. A következő példa bemutatja a gyökérszintű hatókörben a {roledefinitionid-értékkel} szerepkör hozzárendelése tulajdonságait (`/`):

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

1. A felhasználói hozzáférés adminisztrátora közben törölheti is a gyökérszintű hatókörben szerepkör-hozzárendelések (`/`).

1. Vonja vissza a felhasználói hozzáférés rendszergazdai jogosultságokkal, amíg azokat újra van szükség.


## <a name="how-to-undo-the-elevateaccess-action-with-the-rest-api"></a>A REST API-val elevateAccess művelet visszavonása

A hívás esetén `elevateAccess`, szerepkör-hozzárendelés létrehozása a szolgáltatást, így ezek a jogosultságok visszavonásához kell a hozzárendelést.

1. Hívás [GET roleDefinitions](/rest/api/authorization/roledefinitions/get) ahol `roleName` egyenlő a felhasználói hozzáférés adminisztrátora a felhasználói hozzáférés adminisztrátora szerepkör neve azonosító meghatározására.

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

    Mentse az Azonosítót a `name` paraméter, ebben az esetben `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`.

2. Is fel kell sorolnia bérlői hatókörből Bérlői rendszergazda szerepkör-hozzárendelés. A bérlői hatókörben vonatkozó összes hozzárendelést listában a `principalId` a bérlői rendszergazda végző a jogosultságszint-emelés hozzáférés hívható meg. Ez felsorolja az ObjectId azonosító a bérlő vonatkozó összes hozzárendelést.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >A bérlői rendszergazda nem rendelkezhet sok hozzárendelések, ha az előző lekérdezés túl sok hozzárendelések is lekérheti vonatkozó összes hozzárendelést csak bérlői hatókör szintjén, majd az eredmények szűréséhez: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
    2. Az előző hívások szerepkör-hozzárendelések listáját adja vissza. A szerepkör-hozzárendelés található, ahol a hatókör az "/" és a `roleDefinitionId` karakterlánccal végződik-e a szerepkör neve azonosítójú meg az 1. lépésben található és `principalId` megegyezik a bérlői rendszergazda objektumazonosító. 
    
    A minta szerepkör-hozzárendelést:

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
        
    Ebben az esetben mentse az Azonosítót a a `name` paraméter, ebben az esetben e7dd75bc-06f6-4e71-9014-ee96a929d099.

    3. Végül, használja a szerepkör-hozzárendelés azonosítója a hozzárendelést által hozzáadott `elevateAccess`:

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>További lépések

- [REST szerepköralapú hozzáférés-vezérlés](role-assignments-rest.md)
- [Hozzáférés-hozzárendelések kezelése](role-assignments-users.md)

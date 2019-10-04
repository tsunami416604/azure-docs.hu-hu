---
title: Jogosultságszint-emelési hozzáférés az összes Azure-előfizetés és-felügyeleti csoport kezeléséhez | Microsoft Docs
description: Ismerteti, hogyan lehet a globális rendszergazda hozzáférését a Azure Portal vagy REST API használatával a Azure Active Directory összes előfizetésének és felügyeleti csoportjának kezeléséhez.
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
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 984284fa185d4d8454b1689a62ca9e08c342e33b
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195121"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Jogosultságszint-emelési hozzáférés az összes Azure-előfizetés és-felügyeleti csoport kezeléséhez

Azure Active Directory (Azure AD) globális rendszergazdájaként előfordulhat, hogy nem fér hozzá a címtár összes előfizetéséhez és felügyeleti csoportjához. Ez a cikk bemutatja, hogyan emelheti ki az összes előfizetéshez és felügyeleti csoporthoz való hozzáférést.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Miért kell megemelni a hozzáférést?

Ha Ön globális rendszergazda, akkor előfordulhat, hogy az alábbi lépéseket szeretné elvégezni:

- Azure-előfizetéshez vagy felügyeleti csoporthoz való hozzáférés visszanyerése, ha egy felhasználó elveszítette a hozzáférést
- Egy másik felhasználó vagy egy Azure-előfizetéshez vagy egy felügyeleti csoporthoz való hozzáférés biztosítása
- A szervezet összes Azure-előfizetésének vagy felügyeleti csoportjának megjelenítése
- Az Azure-előfizetések és-felügyeleti csoportok elérésének engedélyezése az Automation-alkalmazások számára (például számlázási vagy naplózási alkalmazás)

## <a name="how-does-elevate-access-work"></a>Hogyan működik a jogosultságszint-emelési hozzáférés?

Az Azure AD és az Azure-erőforrások egymástól függetlenül biztonságosak. Az Azure AD szerepkör-hozzárendelések nem biztosítanak hozzáférést az Azure-erőforrásokhoz, és az Azure-szerepkör-hozzárendelések nem biztosítanak hozzáférést az Azure AD-hoz. Ha azonban Ön [globális rendszergazda](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) az Azure ad-ben, saját maga is kioszthatja az összes Azure-előfizetést és felügyeleti csoportot a címtárában. Akkor használja ezt a funkciót, ha nem rendelkezik hozzáféréssel az Azure-előfizetési erőforrásokhoz, például a virtuális gépekhez vagy a Storage-fiókokhoz, és a globális rendszergazdai jogosultságot szeretné használni az erőforrásokhoz való hozzáféréshez.

Ha emeli a hozzáférést, a [felhasználói hozzáférés rendszergazdai](built-in-roles.md#user-access-administrator) szerepköre lesz hozzárendelve az Azure-ban a root scope (`/`) elemnél. Ez lehetővé teszi az összes erőforrás megtekintését és a hozzáférés hozzárendelését bármely előfizetésben vagy felügyeleti csoportban a címtárban. A felhasználói hozzáférés rendszergazdai szerepkör-hozzárendelései eltávolíthatók a PowerShell használatával.

Ezt a emelt szintű hozzáférést csak akkor távolíthatja el, ha elvégezte a szükséges módosításokat a gyökérszintű hatókörben.

![Jogosultságszint-emelési hozzáférés](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure Portal

Kövesse az alábbi lépéseket a globális rendszergazda hozzáférésének a Azure Portal használatával történő jogosultságszint-emeléséhez.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) vagy a [Azure Active Directory felügyeleti](https://aad.portal.azure.com) központba globális rendszergazdaként.

1. A navigációs listában kattintson a **Azure Active Directory** elemre, majd a **Tulajdonságok**elemre.

   ![Azure AD-tulajdonságok – képernyőfelvétel](./media/elevate-access-global-admin/aad-properties.png)

1. Az **Azure-erőforrások hozzáférés-kezelés**területén állítsa a kapcsolót **Igen**értékre.

   ![Hozzáférés-kezelés az Azure-erőforrásokhoz – képernyőfelvétel](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Ha a váltás az **Igen**értékre van állítva, akkor a root (/) hatókörben az Azure RBAC felhasználói hozzáférés rendszergazdai szerepköre lesz hozzárendelve. Ez lehetővé teszi, hogy az ehhez az Azure AD-címtárhoz társított összes Azure-előfizetéshez és felügyeleti csoporthoz rendeljen szerepköröket. Ez a váltógomb csak a globális rendszergazdai szerepkörrel rendelkező felhasználók számára érhető el az Azure AD-ben.

   Ha a **nem**értékre állítja a kapcsolót, a rendszer eltávolítja a felhasználói fiókból az Azure RBAC felhasználói hozzáférés rendszergazdai szerepkörét. Az ehhez az Azure AD-címtárhoz társított összes Azure-előfizetéshez és felügyeleti csoporthoz már nem lehet szerepköröket hozzárendelni. Csak azokat az Azure-előfizetéseket és felügyeleti csoportokat tekintheti meg és kezelheti, amelyekhez hozzáférést kapott.

1. A beállítás mentéséhez kattintson a **Save (Mentés** ) gombra.

   Ez a beállítás nem globális tulajdonság, és csak az aktuálisan bejelentkezett felhasználóra vonatkozik. A globális rendszergazdai szerepkör összes tagjához nem lehet hozzáférni.

1. Jelentkezzen ki, majd jelentkezzen be újra, és frissítse a hozzáférését.

    Most már hozzáféréssel kell rendelkeznie a címtár összes előfizetéséhez és felügyeleti csoportjához. Megfigyelheti, hogy a felhasználói hozzáférés rendszergazdai szerepköre a gyökérszintű hatókörben van hozzárendelve.

   ![Előfizetési szerepkör-hozzárendelések a gyökérszintű hatókörrel – képernyőfelvétel](./media/elevate-access-global-admin/iam-root.png)

1. Végezze el a szükséges módosításokat a emelt szintű hozzáféréshez.

    A szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [hozzáférés kezelése a RBAC és a Azure Portal használatával](role-assignments-portal.md). Azure AD Privileged Identity Management (PIM) használata esetén olvassa el a következő témakört: [Azure-erőforrások kezelése a PIM-ban](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) , illetve [Azure-erőforrás-szerepkörök kiosztása a PIM-ben](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md).

1. Ha elkészült, állítsa be a **hozzáférés-kezelés az Azure** -erőforrásokhoz visszaváltás a **nem**értékre. Mivel ez egy felhasználónkénti beállítás, a hozzáférés megemeléséhez használt felhasználóként kell bejelentkeznie.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-the-root-scope-"></a>Szerepkör-hozzárendelés listázása a gyökérszintű hatókörben (/)

A felhasználói hozzáférés rendszergazdai szerepkör-hozzárendelésének listázásához a gyökérszintű hatókörben (`/`) használja a [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) parancsot.

```azurepowershell
Get-AzRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
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

### <a name="remove-a-role-assignment-at-the-root-scope-"></a>Szerepkör-hozzárendelés eltávolítása a gyökérszintű hatókörben (/)

Az alábbi lépéseket követve távolíthatja el a felhasználói hozzáférés rendszergazdai szerepkörének hozzárendelését`/`egy felhasználóhoz a gyökérszintű hatókörben ().

1. Jelentkezzen be olyan felhasználóként, aki el tudja távolítani a emelt szintű hozzáférést. Ez lehet ugyanaz a felhasználó, aki a hozzáférés vagy egy másik globális rendszergazda számára emelt szintű hozzáféréssel rendelkezik a gyökérszintű hatókörben.


1. A [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) parancs használatával távolítsa el a felhasználói hozzáférés rendszergazdai szerepkörének hozzárendelését.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="rest-api"></a>REST API

### <a name="elevate-access-for-a-global-administrator"></a>Globális rendszergazda hozzáférésének megemelése

A következő alapvető lépésekkel emelheti ki a globális rendszergazda hozzáférését a REST API használatával.

1. A REST, a `elevateAccess`Call, amellyel a felhasználói hozzáférés rendszergazdai szerepkört a gyökérszintű hatókör (`/`) segítségével érheti el.

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Hozzon létre egy [szerepkör](/rest/api/authorization/roleassignments) -hozzárendelést, amely bármely hatókörhöz társítható. Az alábbi példa a {roleDefinitionID} szerepkör a gyökérszintű hatókörben (`/`) való hozzárendelésének tulajdonságait mutatja be:

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

1. A felhasználói hozzáférés rendszergazdája a gyökérszintű hatókör (`/`) segítségével is eltávolíthatja a szerepkör-hozzárendeléseket.

1. Távolítsa el a felhasználói hozzáférés rendszergazdai jogosultságait, amíg újra nem szükségesek.

### <a name="list-role-assignments-at-the-root-scope-"></a>Szerepkör-hozzárendelések listázása a gyökérszintű hatókörben (/)

Egy felhasználóhoz tartozó összes szerepkör-hozzárendelést listázhatja a gyökérszintű hatókör (`/`) használatával.

- Hívja le a [Get roleAssignments](/rest/api/authorization/roleassignments/listforscope) -t, ahol a a lekérdezni kívánt szerepkör-hozzárendeléseket tartalmazó `{objectIdOfUser}` felhasználó objektumazonosító.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-the-root-scope-"></a>Megtagadási hozzárendelések listázása a gyökérszintű hatókörben (/)

Egy felhasználó összes megtagadási hozzárendelését listázhatja a gyökérszintű hatókörben (`/`).

- Hívja le a Get `{objectIdOfUser}` denyAssignments, ahol a a lekérdezni kívánt megtagadási hozzárendeléseket kérő felhasználó objektumazonosító.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Emelt szintű hozzáférés eltávolítása

A hívásakor `elevateAccess`létre kell hoznia egy szerepkör-hozzárendelést a saját maga számára, hogy visszavonja ezeket a jogosultságokat a hozzárendelés eltávolításához.

1. Hívja meg a [Get roleDefinitions](/rest/api/authorization/roledefinitions/get) , ahol `roleName` a felhasználó-hozzáférési rendszergazda határozza meg a felhasználói hozzáférés rendszergazdai szerepkörének a nevét.

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

    Mentse az azonosítót a `name` paraméterből, ebben az esetben. `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`

2. A címtár-hatókörben a címtár-rendszergazda szerepkör-hozzárendelését is fel kell sorolnia. Sorolja fel az összes hozzárendelést a címtár `principalId` hatókörében azon címtár-rendszergazda számára, aki az emelt szintű hozzáférési hívást kezdeményezte. Ez a lista a címtárban lévő összes hozzárendelést listázza a ObjectId.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >A címtár-rendszergazda nem rendelkezhet sok hozzárendeléssel, ha az előző lekérdezés túl sok hozzárendelést ad vissza, az összes hozzárendelés lekérdezése csak a címtár hatóköri szintjén végezhető el, majd szűrheti az eredményeket:`GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
   1. Az előző hívások a szerepkör-hozzárendelések listáját adják vissza. Keresse meg azt a szerepkör-hozzárendelést `"/"` , amelyben `roleDefinitionId` a hatókör szerepel, és az 1. lépésben megtalált `principalId` szerepkör-név és a címtár-rendszergazda objectId. 
    
      Példa szerepkör-hozzárendelésre:

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
        
      Ismét mentse az azonosítót a `name` paraméterből, ebben az esetben a e7dd75bc-06f6-4e71-9014-ee96a929d099.

   1. Végül a szerepkör-hozzárendelési azonosító használatával távolítsa el a következő `elevateAccess`által hozzáadott hozzárendelést:

      ```http
      DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
      ```

## <a name="next-steps"></a>További lépések

- [A különböző Azure-beli szerepkörök ismertetése](rbac-and-directory-admin-roles.md)
- [Az Azure-erőforrásokhoz való hozzáférés kezelése a RBAC és a REST API használatával](role-assignments-rest.md)

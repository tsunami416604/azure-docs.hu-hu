---
title: "Bérlői rendszergazda jogosultságszintjének emelése – az Azure AD |} Microsoft Docs"
description: "Ez a témakör ismerteti a beépített szerepkörök szerepköralapú hozzáférés-vezérlés (RBAC)."
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2017
ms.author: rolyon
ms.openlocfilehash: dff3a26201507f974d52de3fe6dcb23945cd900f
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2018
---
# <a name="elevate-access-as-a-tenant-admin-with-role-based-access-control"></a>Bérlői rendszergazdaként a szerepköralapú hozzáférés-vezérlés jogosultságszintjének emelése

Szerepköralapú hozzáférés-vezérlés segít a bérlői rendszergazdák úgy, hogy azok engedélyeket adhat magasabb, mint a normál ideiglenes jogosultságszint-emelései beolvasása a hozzáférést. Bérlői rendszergazda emelhet magukat a felhasználói hozzáférés adminisztrátora szerepkörhöz szükség esetén. Ez a szerepkör lehetőséget ad a bérlői rendszergazda engedélyeket az önmagukat vagy más szerepkörök a "/" hatókörben.

Ez a funkció fontos, mert lehetővé teszi a bérlői rendszergazda szerepel a szervezet összes előfizetés megtekintéséhez. Azt is lehetővé teszi, hogy automation alkalmazásokhoz, például a számlázással és a naplózás elérni az összes olyan előfizetést, és adjon meg egy pontos képet a szervezet állapotának számlázással vagy az eszközök kezelése.  

## <a name="use-elevateaccess-for-tenant-access-with-azure-ad-admin-center"></a>Az Azure AD felügyeleti központban bérlő eléréséhez elevateAccess használata

1. Lépjen a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) és jelentkezzen be a hitelesítő adatait.

2. Válasszon **tulajdonságok** az Azure ad-bal oldali menüben.

3. Található **globális rendszergazdák által kezelhető az Azure-előfizetések**, válassza a **Igen**, majd **mentése**.
    > [!IMPORTANT] 
    > Ha úgy dönt, **Igen**, hozzárendeli a **felhasználói hozzáférés adminisztrátora** gyökerében szerepkör "/" (gyökérszintű hatókörben), amellyel jelenleg bejelentkezett a portál a használatra. **Ez lehetővé teszi a felhasználó számára a más Azure-előfizetésekhez.**
    
    > [!NOTE] 
    > Ha úgy dönt, **nem**, eltávolítja a **felhasználói hozzáférés adminisztrátora** gyökerében szerepkör "/" (gyökérszintű hatókörben), amellyel jelenleg bejelentkezett a portál a használatra.

> [!TIP] 
> A benyomást, hogy ez az Azure Active Directory globális tulajdonság, azonban a bejelentkezett felhasználó felhasználónkénti alapon működik. Ha a globális rendszergazdai jogosultságokkal rendelkezik az Azure Active Directoryban, hívhat meg a elevateAccess funkció, amellyel jelenleg bejelentkezett az Azure Active Directory felügyeleti központ a felhasználó számára.

![Az Azure AD felügyeleti központban - tulajdonságok – globális rendszergazdák által kezelhető Azure-előfizetés – képernyőkép](./media/role-based-access-control-tenant-admin-access/aad-azure-portal-global-admin-can-manage-azure-subscriptions.png)

## <a name="view-role-assignments-at-the--scope-using-powershell"></a>A PowerShell használatával "/" hatókörből szerepkör-hozzárendelések megtekintése
Megtekintéséhez a **felhasználói hozzáférés adminisztrátora** -hozzárendelés a  **/**  hatókörét, használja a `Get-AzureRmRoleAssignment` PowerShell-parancsmagot.
    
```powershell
Get-AzureRmRoleAssignment* | where {$_.RoleDefinitionName -eq "User Access Administrator" -and $_SignInName -eq "<username@somedomain.com>" -and $_.Scope -eq "/"}
```

**Példa a kimenetre**:
```
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0    
Scope              : /    
DisplayName        : username    
SignInName         : username@somedomain.com    
RoleDefinitionName : User Access Administrator    
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9    
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc    
ObjectType         : User    
```

## <a name="delete-the-role-assignment-at--scope-using-powershell"></a>A szerepkör-hozzárendelés törlése "/" hatókör a Powershell használatával:
A következő PowerShell-parancsmag használatával-hozzárendelés törlése:

```powershell
Remove-AzureRmRoleAssignment -SignInName <username@somedomain.com> -RoleDefinitionName "User Access Administrator" -Scope "/" 
```

## <a name="use-elevateaccess-to-give-tenant-access-with-the-rest-api"></a>A bérlői hozzáférésének REST API-val elevateAccess használata

A folyamat alapvetően együttműködik az alábbi lépéseket:

1. Használja a többi, hívja *elevateAccess*, amely engedélyezi a felhasználói hozzáférés adminisztrátora szerepkör a(z) "/" hatókör.

    ```
    POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
    ```

2. Hozzon létre egy [szerepkör-hozzárendelés](/rest/api/authorization/roleassignments) bármely hatókörből szerepköröket hozzárendelni. A következő példa bemutatja a tulajdonságait: az olvasó szerepkört rendelni "/" hatóköre:

    ```json
    { 
      "properties": {
        "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/acdd72a7338548efbd42f606fba81ae7",
        "principalId": "cbc5e050-d7cd-4310-813b-4870be8ef5bb",
        "scope": "/"
      },
      "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
    }
    ```

3. Egy felhasználó hozzáférési rendszergazdai közben meg is is, szerepkör-hozzárendelések törlése "/" hatókör.

4. Vonja vissza a felhasználói hozzáférés rendszergazdai jogosultságokkal, amíg azokat újra van szükség.


## <a name="how-to-undo-the-elevateaccess-action-with-the-rest-api"></a>A REST API-val elevateAccess művelet visszavonása

A hívás esetén *elevateAccess* , szerepkör-hozzárendelés létrehozása a szolgáltatást, ezért ezek a jogosultságok visszavonásához szüksége lesz a hozzárendelést.

1.  GET roleDefinitions hívás ahol roleName = a felhasználói hozzáférés adminisztrátora a felhasználói hozzáférés adminisztrátora szerepkör GUID nevének megállapítása érdekében.
    ```
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User+Access+Administrator
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

    Mentse a GUID-Azonosítójának a *neve* paraméter, ebben az esetben **18d7d88d-d35e-4fb5-a5c3-7773c20a72d9**.

2. Is fel kell sorolnia bérlői hatókörből Bérlői rendszergazda szerepkör-hozzárendelést. A listában, a PrincipalId a tenantadmin rendszergazdát. a jogosultságszint-emelés hozzáférés hívás leadó a bérlői hatókört vonatkozó összes hozzárendelést. Ez felsorolja az ObjectId azonosító a bérlő vonatkozó összes hozzárendelést.

    ```
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >Egy Bérlői rendszergazda nem rendelkezhet több hozzárendelések, ha az előző lekérdezés túl sok hozzárendelések is lekérheti vonatkozó összes hozzárendelést csak bérlői hatókör szintjén, majd az eredmények szűréséhez: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
    
        
    2. Az előző hívások szerepkör-hozzárendelések listáját adja vissza. A szerepkör-hozzárendelés található, ahol a hatókör az "/" és a roledefinitionid-értékkel végződik-e a szerepkör nevét, az 1. lépésben található GUID Azonosítót és a PrincipalId megegyezik a bérlői rendszergazda ObjectId 
    
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
        
    Ebben az esetben a GUID-Azonosítójának mentése a *neve* paraméter, ebben az esetben **e7dd75bc-06f6-4e71-9014-ee96a929d099**.

    3. Végül, használja a kijelölt **RoleAssignment azonosító** a hozzárendelést jogosultságszint-emelés hozzáférési által hozzáadott:

    ```
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>További lépések

- További információ [szerepköralapú hozzáférés-vezérlés többi kezelése](role-based-access-control-manage-access-rest.md)

- [Hozzáférés-hozzárendelések kezelése](role-based-access-control-manage-assignments.md) az Azure-portálon

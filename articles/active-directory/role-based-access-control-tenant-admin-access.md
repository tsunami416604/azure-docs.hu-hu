---
title: "Bérlői rendszergazda jogosultságszintjének emelése – az Azure AD |} Microsoft Docs"
description: "Ez a témakör ismerteti a beépített szerepkörök szerepköralapú hozzáférés-vezérlés (RBAC)."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andredm
ms.openlocfilehash: 22b62be1773c5042ecf6ee078e68a4ffdf791d53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="elevate-access-as-a-tenant-admin-with-role-based-access-control"></a>Bérlői rendszergazdaként a szerepköralapú hozzáférés-vezérlés jogosultságszintjének emelése

Szerepköralapú hozzáférés-vezérlés segít a bérlői rendszergazdák úgy, hogy azok engedélyeket adhat magasabb, mint a normál ideiglenes jogosultságszint-emelései beolvasása a hozzáférést. A bérlői rendszergazda emelhet saját magát, a felhasználói hozzáférés adminisztrátora szerepkörhöz szükség esetén. Ez a szerepkör biztosítja a bérlői rendszergazdai engedélyeket az saját magát vagy más szerepkörök a "/" hatókörben.

Ez a funkció fontos, mert lehetővé teszi a bérlői rendszergazda szerepel a szervezet összes előfizetés megtekintéséhez. Automation-alkalmazások (például a számlázással és a naplózás) is lehetővé teszi az előfizetések eléréséhez, és adjon meg egy pontos képet a szervezet a számlázással vagy az eszköz felügyeleti állapotát.  

## <a name="how-to-use-elevateaccess-for-tenant-access-with-azure-ad-admin-center"></a>Az Azure AD felügyeleti központban bérlő eléréséhez elevateAccess használata

Az a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) hívhat meg ez a funkció a **tulajdonságok**.
A szolgáltatás neve **globális rendszergazdák által kezelhető az Azure-előfizetések**. A benyomást, hogy ez az Azure Active Directory globális tulajdonság, azonban a bejelentkezett felhasználó felhasználónkénti alapon működik. Ha a globális rendszergazdai jogosultságokkal rendelkezik az Azure Active Directoryban, hívhat meg a elevateAccess funkció, amely Azure Active Directory felügyeleti központ jelenleg bejelentkezett felhasználó.

Kiválasztása **Igen** , majd **mentése**: Ez **hozzárendeli** a **felhasználói hozzáférés adminisztrátora** gyökerében szerepkör "/" (gyökérszintű hatókörben) a felhasználó számára amely a portál jelenleg bejelentkezett.

Kiválasztása **nem** , majd **mentése**: Ez **eltávolítja** a **felhasználói hozzáférés adminisztrátora** gyökerében szerepkör "/" (gyökérszintű hatókörben) a felhasználó számára amely a portál jelenleg bejelentkezett.

![Az Azure AD - tulajdonságok – felügyeleti központot Globalisrendszergazda kezelheti az Azure-előfizetés – képernyőkép](./media/role-based-access-control-tenant-admin-access/aad-azure-portal-global-admin-can-manage-azure-subscriptions.png)

## <a name="how-to-use-elevateaccess-to-give-tenant-access-with-the-rest-api"></a>A bérlői hozzáférésének REST API-val elevateAccess használata

A folyamat alapvetően együttműködik az alábbi lépéseket:

1. Használja a többi, hívja *elevateAccess*, amely engedélyezi a felhasználói hozzáférés adminisztrátora szerepkör a(z) "/" hatókör.

    ```
    POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
    ```

2. Hozzon létre egy [szerepkör-hozzárendelés](/rest/api/authorization/roleassignments) bármely hatókörből szerepköröket hozzárendelni. A következő példa bemutatja a tulajdonságait: az olvasó szerepkört rendelni "/" hatóköre:

    ```
    { "properties":{
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

1.  Hívás [GET roleDefinitions](/rest/api/authorization/roledefinitions#RoleDefinitions_Get) ahol roleName = a felhasználói hozzáférés adminisztrátora a felhasználói hozzáférés adminisztrátora szerepkör GUID nevének megállapítása érdekében. A válasz kell kinéznie:

    ```
    {"value":[{"properties":{
    "roleName":"User Access Administrator",
    "type":"BuiltInRole",
    "description":"Lets you manage user access to Azure resources.",
    "assignableScopes":["/"],
    "permissions":[{"actions":["*/read","Microsoft.Authorization/*","Microsoft.Support/*"],"notActions":[]}],
    "createdOn":"0001-01-01T08:00:00.0000000Z",
    "updatedOn":"2016-05-31T23:14:04.6964687Z",
    "createdBy":null,
    "updatedBy":null},
    "id":"/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "type":"Microsoft.Authorization/roleDefinitions",
    "name":"18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"}],
    "nextLink":null}
    ```

    Mentse a GUID-Azonosítójának a *neve* paraméter, ebben az esetben **18d7d88d-d35e-4fb5-a5c3-7773c20a72d9**.

2. Hívás [GET roleAssignments](/rest/api/authorization/roleassignments#RoleAssignments_Get) ahol principalId saját ObjectId =. A parancs megjeleníti az összes hozzárendelésének a bérlő. A keres, ahol a hatókör az "/", és a roledefinitionid-értékkel végződik-e a szerepkör nevét, az 1. lépésben található GUID. A szerepkör-hozzárendelés kell kinéznie:

    ```
    {"value":[{"properties":{
    "roleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "principalId":"{objectID}",
    "scope":"/",
    "createdOn":"2016-08-17T19:21:16.3422480Z",
    "updatedOn":"2016-08-17T19:21:16.3422480Z",
    "createdBy":"93ce6722-3638-4222-b582-78b75c5c6d65",
    "updatedBy":"93ce6722-3638-4222-b582-78b75c5c6d65"},
    "id":"/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
    "type":"Microsoft.Authorization/roleAssignments",
    "name":"e7dd75bc-06f6-4e71-9014-ee96a929d099"}],
    "nextLink":null}
    ```

    Ebben az esetben a GUID-Azonosítójának mentése a *neve* paraméter, ebben az esetben **e7dd75bc-06f6-4e71-9014-ee96a929d099**.

3. Végezetül hívás [DELETE roleAssignments](/rest/api/authorization/roleassignments#RoleAssignments_DeleteById) ahol roleAssignmentId = a neve, a 2. lépésben található GUID.

## <a name="next-steps"></a>Következő lépések

- További információ [szerepköralapú hozzáférés-vezérlés többi kezelése](role-based-access-control-manage-access-rest.md)

- [Hozzáférés-hozzárendelések kezelése](role-based-access-control-manage-assignments.md) az Azure-portálon

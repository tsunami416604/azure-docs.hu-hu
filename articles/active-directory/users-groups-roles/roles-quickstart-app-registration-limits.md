---
title: Az alkalmazásregisztrációk létrehozására vonatkozó korlátozások megszüntetése - Azure AD | Microsoft dokumentumok
description: Egyéni szerepkör hozzárendelése az Azure AD Active Directoryban való korlátlan alkalmazásregisztrációk engedélyezéséhez
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7acd76ff45f783f614b2a1d3f0d5c10d800a1ea9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77559045"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Rövid útmutató: Engedély megadása korlátlan alkalmazásregisztrációk létrehozásához

Ebben a rövid útmutatóban hozzon létre egy egyéni szerepkört, amely korlátlan számú alkalmazásregisztráció t szeretne létrehozni, majd hozzárendeli a szerepkört egy felhasználóhoz. A hozzárendelt felhasználó ezután használhatja az Azure AD portál, az Azure AD PowerShell vagy a Microsoft Graph API alkalmazásregisztrációk létrehozásához. A beépített alkalmazásfejlesztői szerepkörrel ellentétben ez az egyéni szerepkör korlátlan számú alkalmazásregisztráció létrehozását teszi lehetővé. Az Alkalmazásfejlesztő szerepkör megadja a lehetőséget, de a létrehozott objektumok teljes száma legfeljebb 250 lehet, hogy ne ütközjön [a címtárszintű objektumkvótába.](directory-service-limits-restrictions.md)

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisite"></a>Előfeltétel

Az Azure AD egyéni szerepkörök létrehozásához és hozzárendeléséhez szükséges legkevésbé kiemelt szerepkör a kiemelt szerepkör rendszergazdája.

## <a name="create-a-new-custom-role-using-the-azure-ad-portal"></a>Új egyéni szerepkör létrehozása az Azure AD portál használatával

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) kiemelt szereppel rendelkező rendszergazdai vagy globális rendszergazdai engedélyekkel az Azure AD-szervezetben.
1. Válassza az **Azure Active Directory**, válassza a **Szerepkörök és rendszergazdák**lehetőséget, majd az Új egyéni **szerepkör**lehetőséget.

    ![Szerepkörök létrehozása és szerkesztése a Szerepkörök és rendszergazdák lapon](./media/roles-create-custom/new-custom-role.png)

1. Az **Alapok** lapon adja meg az "Alkalmazásregisztrációs készítő" lehetőséget a szerepkör nevéhez, és "Korlátlan számú alkalmazásregisztrációt hozhat létre" a szerepkör leírásához, majd válassza a **Tovább**gombot.

    ![egyéni szerepkör nevének és leírásának megadása az Alapok lapon](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. Az **Engedélyek** lapon írja be a "microsoft.directory/applications/create" kifejezést a keresőmezőbe, majd jelölje be a kívánt engedélyek melletti jelölőnégyzeteket, majd kattintson a **Tovább gombra.**

    ![Egyéni szerepkör engedélyeinek kiválasztása az Engedélyek lapon](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. A **Véleményezés + létrehozás** lapon tekintse át az engedélyeket, és válassza a **Létrehozás gombot.**

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>A szerepkör hozzárendelése egy felhasználóhoz az Azure AD portál használatával

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) kiemelt szereppel rendelkező szerepkör-rendszergazdai vagy globális rendszergazdai engedélyekkel az Azure AD-szervezetben.
1. Válassza az **Azure Active Directory,** majd **a Szerepkörök és rendszergazdák**lehetőséget.
1. Jelölje ki az Alkalmazásregisztrációs készítőszerepkört, és válassza **a Hozzárendelés hozzáadása**lehetőséget.
1. Jelölje ki a kívánt **felhasználót,** és a Kijelölés gombra kattintva adja hozzá a felhasználót a szerepkörhöz.

Kész! Ebben a rövid útmutatóban sikeresen létrehozott egy egyéni szerepkört, amely korlátlan számú alkalmazásregisztráció létrehozására jogosult, majd hozzárendelte a szerepkört egy felhasználóhoz.

> [!TIP]
> Ha hozzá szeretné rendelni a szerepkört egy alkalmazáshoz az Azure AD portál használatával, írja be az alkalmazás nevét a hozzárendelési lap keresőmezőjébe. Az alkalmazások alapértelmezés szerint nem jelennek meg a listában, de a keresési eredmények között is megjelennek.

### <a name="app-registration-permissions"></a>Alkalmazásregisztrációs engedélyek

Két engedély áll rendelkezésre az alkalmazásregisztrációk létrehozásának engedélyezéséhez, amelyek mindegyike eltérő viselkedéssel rendelkezik.

- microsoft.directory/applications/createAsOwner: Az engedély hozzárendelése azt eredményezi, hogy a létrehozó a létrehozott alkalmazás regisztrációjának első tulajdonosaként kerül hozzáadásra, és a létrehozott alkalmazás regisztrációja beleszámít a létrehozó 250 létrehozott objektumkvótájába.
- microsoft.directory/applicationPolicies/create: Az engedély hozzárendelése azt eredményezi, hogy a létrehozó nem kerül hozzáadásra a létrehozott alkalmazásregisztráció első tulajdonosaként, és a létrehozott alkalmazás regisztrációja nem számít bele a létrehozó 250 létrehozott objektumkvótájába. Körültekintően használja ezt az engedélyt, mert semmi sem akadályozza meg a hozzárendeltt abban, hogy alkalmazásregisztrációkat hozzon létre a címtárszintű kvóta leütéséig. Ha mindkét engedély hozzá van rendelve, ez az engedély élvez elsőbbséget.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Egyéni szerepkör létrehozása az Azure AD PowerShell használatával

Hozzon létre egy új szerepkört a következő PowerShell-parancsfájl használatával:

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/createAsOwner"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Az egyéni szerepkör hozzárendelése az Azure AD PowerShell használatával

#### <a name="prepare-powershell"></a>PowerShell előkészítése

Először telepítse az Azure AD PowerShell-modult a [PowerShell-galériából.](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17) Ezután importálja az Azure AD PowerShell előzetes modulját a következő paranccsal:

```powershell
import-module azureadpreview
```

Annak ellenőrzéséhez, hogy a modul készen áll-e a használatra, egyezzen meg a következő parancs által visszaadott verzióval az itt felsoroltakkal:

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

#### <a name="assign-the-custom-role"></a>Egyéni szerepkör hozzárendelése

Rendelje hozzá a szerepkört az alábbi PowerShell-parancsfájl használatával:

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="create-a-custom-role-using-microsoft-graph-api"></a>Egyéni szerepkör létrehozása a Microsoft Graph API-val

HTTP-kérelem az egyéni szerepkör létrehozásához.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

Törzs

```HTTP
{
    "description":"Can create an unlimited number of application registrations.",
    "displayName":"Application Registration Creator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/applications/create"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Egyéni szerepkör hozzárendelése a Microsoft Graph API-val

A szerepkör-hozzárendelés egyesíti a rendszerbiztonsági tagazonosító (amely lehet egy felhasználó vagy egyszerű szolgáltatás), egy szerepkör-definíció (szerepkör) azonosító, és egy Azure AD erőforrás hatókörét.

HTTP-kérelem egyéni szerepkör hozzárendelésére.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Törzs

``` HTTP
{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>További lépések

- Nyugodtan ossza meg velünk az [Azure AD felügyeleti szerepkörök fórum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- A szerepkörökről és a Rendszergazdai szerepkör-hozzárendelésről a [Rendszergazdai szerepkörök hozzárendelése című témakörben](directory-assign-admin-roles.md)van további témakör.
- Az alapértelmezett felhasználói engedélyeket [az alapértelmezett vendég- és tagjogosultságok összehasonlítása](../fundamentals/users-default-permissions.md)című témakörben olvashatja.

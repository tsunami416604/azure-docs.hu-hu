---
title: Egyéni szerepkörök létrehozása az Azure AD szerepköralapú hozzáférés-vezérlésében | Microsoft dokumentumok
description: Hozzon létre és rendeljen hozzá egyéni Azure AD-szerepköröket erőforráshatókörrel az Azure Active Directory-erőforrásokon.
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
ms.openlocfilehash: c2cb19c82f8c19bf87eeef755adb5756b2452512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025275"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Egyéni szerepkör létrehozása és hozzárendelése az Azure Active Directoryban

Ez a cikk ismerteti, hogyan hozhat létre új egyéni szerepkörök az Azure Active Directoryban (Azure AD). Az egyéni szerepkörök alapjait az [egyéni szerepkörök áttekintése című témakörben találja.](roles-custom-overview.md) A szerepkör hozzárendelhető a címtárszintű hatókörhöz vagy csak egy alkalmazásregisztrációs erőforráshatókörhöz.

Egyéni szerepkörök hozhatók létre a [szerepkörök és a rendszergazdák](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) lapon az Azure AD áttekintése lap.

## <a name="create-a-role-in-the-azure-portal"></a>Szerepkör létrehozása az Azure Portalon

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Új egyéni szerepkör létrehozása az alkalmazásregisztrációk kezeléséhez való hozzáférés biztosításához

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) kiemelt szereppel rendelkező szerepkör-rendszergazdai vagy globális rendszergazdai engedélyekkel az Azure AD-szervezetben.
1. Válassza az **Azure Active Directory szerepkörök** > **és rendszergazdák** > **Új egyéni szerepkör**lehetőséget.

   ![Szerepkörök létrehozása és szerkesztése a Szerepkörök és rendszergazdák lapon](./media/roles-create-custom/new-custom-role.png)

1. Az **Alapok** lapon adja meg a szerepkör nevét és leírását, majd kattintson a **Tovább**gombra.

   ![egyéni szerepkör nevének és leírásának megadása az Alapok lapon](./media/roles-create-custom/basics-tab.png)

1. Az **Engedélyek** lapon válassza ki az alkalmazásregisztrációk alapvető tulajdonságainak és hitelesítő adatainak kezeléséhez szükséges engedélyeket. Az egyes engedélyek részletes leírását az [Azure Active Directory alkalmazásregisztrációs altípusai és engedélyei című témakörben tartalmazza.](./roles-custom-available-permissions.md)
   1. Először írja be a "hitelesítő adatok" `microsoft.directory/applications/credentials/update` szót a keresősávba, és válassza ki az engedélyt.

      ![Egyéni szerepkör engedélyeinek kiválasztása az Engedélyek lapon](./media/roles-create-custom/permissions-tab.png)

   1. Ezután írja be az "alap" `microsoft.directory/applications/basic/update` szót a keresősávba, válassza ki az engedélyt, majd kattintson a **Tovább**gombra.
1. A **Véleményezés + létrehozás** lapon tekintse át az engedélyeket, és válassza a **Létrehozás gombot.**

Az egyéni szerepkör megjelenik a hozzárendelhetővé a rendelkezésre álló szerepkörök listájában.

## <a name="create-a-role-using-powershell"></a>Szerepkör létrehozása a PowerShell használatával

### <a name="prepare-powershell"></a>PowerShell előkészítése

Először le kell [töltenie az Azure AD Preview PowerShell-modult.](https://www.powershellgallery.com/packages/AzureADPreview)

Az Azure AD PowerShell-modul telepítéséhez használja a következő parancsokat:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Annak ellenőrzéséhez, hogy a modul készen áll-e a használatra, használja a következő parancsot:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.2.31     azuread                      {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role"></a>Egyéni szerepkör létrehozása

Hozzon létre egy új szerepkört a következő PowerShell-parancsfájl használatával:

``` PowerShell
# Basic role information
$displayName = "Application Support Administrator"
$description = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid
 
# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
 
# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Az egyéni szerepkör hozzárendelése az Azure AD PowerShell használatával

Rendelje hozzá a szerepkört az alábbi PowerShell-parancsfájl használatával:

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="create-a-role-with-graph-api"></a>Szerepkör létrehozása a Graph API-val

1. Hozza létre a szerepkör-definíciót.

    HTTP-kérelem egyéni szerepkör-definíció létrehozására.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Törzs

    ``` HTTP
   {
       "description": "Can manage basic aspects of application registrations.",
       "displayName": "Application Support Administrator",
       "isEnabled": true,
       "templateId": "<GUID>",
       "rolePermissions": [
           {
               "allowedResourceActions": [
                   "microsoft.directory/applications/basic/update",
                   "microsoft.directory/applications/credentials/update"
               ]
           }
       ]
   }
    ```

1. A szerepkör-hozzárendelés létrehozása.

    HTTP-kérelem egyéni szerepkör-definíció létrehozására.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    Törzs

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>Egyéni szerepkör hatókörének hozzárendelése erőforráshoz

A beépített szerepkörökhöz hasonlóan az egyéni szerepkörök is alapértelmezés szerint az alapértelmezett szervezeti szintű hatókörhöz vannak rendelve, hogy hozzáférési engedélyeket adjanak a szervezet összes alkalmazásregisztrációjához. De a beépített szerepkörök, egyéni szerepkörök is hozzárendelhető egyetlen Azure AD-erőforrás hatókörén. Ez lehetővé teszi, hogy a felhasználó engedélyt adjon egyetlen alkalmazás hitelesítő adatainak és alapvető tulajdonságainak frissítésére anélkül, hogy egy második egyéni szerepkört kellene létrehoznia.

1. Jelentkezzen be az [Azure AD felügyeleti központ](https://aad.portal.azure.com) alkalmazásfejlesztői engedélyekkel az Azure AD-szervezetben.
1. Válassza **az Alkalmazásregisztrációk lehetőséget.**
1. Válassza ki azt az alkalmazásregisztrációt, amelyhez hozzáférést biztosít a kezeléshez. Előfordulhat, hogy az Azure AD-szervezetben az alkalmazásregisztrációk teljes listájának megtekintéséhez minden **alkalmazás** kiválasztásához válassza.

    ![Az alkalmazásregisztráció kiválasztása erőforráshatókörként egy szerepkör-hozzárendeléshez](./media/roles-create-custom/appreg-all-apps.png)

1. Az alkalmazásregisztrációban válassza a **Szerepkörök és rendszergazdák**lehetőséget. Ha még nem hozott létre egyet, az utasítások az [előző eljárásban](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations)találhatók.

1. Válassza ki a **Szerepkört** a Hozzárendelések lap megnyitásához.
1. Felhasználó hozzáadásához válassza a **Hozzárendelés hozzáadása** lehetőséget. A felhasználó csak a kijelölt alkalmazásregisztrációhoz kap engedélyt.

## <a name="next-steps"></a>További lépések

- Nyugodtan ossza meg velünk az [Azure AD felügyeleti szerepkörök fórum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- A szerepkörökről és a Rendszergazdai szerepkör-hozzárendelésről a [Rendszergazdai szerepkörök hozzárendelése című témakörben](directory-assign-admin-roles.md)van további témakör.
- Az alapértelmezett felhasználói engedélyeket [az alapértelmezett vendég- és tagjogosultságok összehasonlítása](../fundamentals/users-default-permissions.md)című témakörben olvashatja.

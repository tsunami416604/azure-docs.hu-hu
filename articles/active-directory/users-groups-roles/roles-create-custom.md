---
title: Egyéni szerepkör-definíció létrehozása az Azure AD szerepköralapú hozzáférés-vezérlésében – Azure Active Directory | Microsoft Docs
description: Hozzon létre egyéni Azure AD-szerepköröket erőforrás-hatókörrel Azure Active Directory erőforrásokon.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0e33cf9fa1c4661dd71fc41cb667b0373c9e955
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774823"
---
# <a name="create-a-custom-role-and-assign-at-resource-scope-in-azure-active-directory"></a>Hozzon létre egy egyéni szerepkört, és rendelje hozzá az erőforrás-hatókört Azure Active Directory

Ez a cikk bemutatja, hogyan hozhat létre új egyéni szerepköröket a Azure Active Directoryban (Azure AD). Az egyéni szerepkörök az Azure AD Áttekintés lapjának [szerepkörök és rendszergazdák](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) lapján hozhatók létre. A szerepkör csak a címtár szintű hatókörben vagy az alkalmazás regisztrációs erőforrásának hatókörében rendelhető hozzá.

További információ: egyéni szerepkörök [– Áttekintés](roles-custom-overview.md) az egyéni szerepkörök alapjairól.

## <a name="using-the-azure-ad-portal"></a>Az Azure AD-portál használata

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Új egyéni szerepkör létrehozása az alkalmazások regisztrálásához való hozzáférés biztosításához

1. Jelentkezzen be az [Azure ad felügyeleti](https://aad.portal.azure.com) központba a Kiemelt szerepkörű rendszergazda vagy a globális rendszergazdai engedélyekkel az Azure ad-szervezetben.
1. Válassza ki **Azure Active Directory** > **szerepkörök és rendszergazdák** > **új egyéni szerepkört**.

   ![Szerepkörök létrehozása vagy szerkesztése a szerepkörök és rendszergazdák lapról](./media/roles-create-custom/new-custom-role.png)

1. Az **alapok** lapon adja meg a szerepkör nevét és leírását, majd kattintson a **tovább**gombra.

   ![adja meg az egyéni szerepkör nevét és leírását az alapok lapon](./media/roles-create-custom/basics-tab.png)

1. Az **engedélyek** lapon válassza ki az alkalmazás-regisztrációk alapszintű tulajdonságainak és hitelesítő adatainak kezeléséhez szükséges engedélyeket. Az egyes engedélyek részletes ismertetését lásd: [alkalmazás-regisztrációs altípusok és engedélyek a Azure Active Directory](./roles-custom-available-permissions.md).
   1. Először írja be a "hitelesítő adatok" kifejezést a keresősáv mezőbe, `microsoft.directory/applications/credentials/update` és válassza ki az engedélyt.

      ![Egyéni szerepkör engedélyeinek kiválasztása az engedélyek lapon](./media/roles-create-custom/permissions-tab.png)

   1. Ezután írja be az "alapszintű" kifejezést a keresősávba `microsoft.directory/applications/basic/update` , válassza ki az engedélyt, majd kattintson a **tovább**gombra.
1. A **felülvizsgálat + létrehozás** lapon tekintse át az engedélyeket, és válassza a **Létrehozás**lehetőséget.

Az egyéni szerepkör megjelenik a Hozzárendelendő elérhető szerepkörök listájában.

## <a name="assign-a-role-scoped-to-a-resource"></a>Szerepkör hatókörének társítása erőforráshoz

A beépített szerepkörökhöz hasonlóan az egyéni szerepköröket a szervezetre kiterjedő hatókörrel rendelheti hozzá az összes alkalmazás regisztrációjának biztosításához. Az egyéni szerepkörök pedig az erőforrás hatókörében is hozzárendelhetők. Ez lehetővé teszi, hogy a megbízott jogosult legyen egy adott alkalmazás hitelesítő adatainak és alapvető tulajdonságainak frissítésére anélkül, hogy második egyéni szerepkört kellene létrehoznia.

1. Ha még nem tette meg, jelentkezzen be az [Azure ad felügyeleti](https://aad.portal.azure.com) központba az alkalmazás fejlesztői engedélyeivel az Azure ad-szervezetben.
1. Válassza az **Alkalmazásregisztrációk** elemet.
1. Válassza ki azt az alkalmazás-regisztrációt, amelyhez hozzáférést kíván adni a kezeléshez. Előfordulhat, hogy az **összes alkalmazás** lehetőséget kell választania az Azure ad-szervezetben az alkalmazások regisztrálásának teljes listájának megtekintéséhez.

    ![Válassza ki az alkalmazás regisztrációját erőforrás-hatókörként a szerepkör-hozzárendeléshez](./media/roles-create-custom/appreg-all-apps.png)

1. Az alkalmazás regisztrálása területen válassza a **szerepkörök és rendszergazdák**lehetőséget. Ha még nem hozott létre ilyet, az utasítások az [előző eljárásban](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations)találhatók.

1. Válassza ki a szerepkört a **hozzárendelések** lap megnyitásához.
1. Felhasználó hozzáadásához válassza a **hozzárendelés hozzáadása** lehetőséget. A felhasználó nem kap semmilyen jogosultságot az alkalmazás regisztrációján kívül, kivéve a kiválasztott elemet.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Egyéni szerepkör létrehozása az Azure AD PowerShell-lel

### <a name="prepare-powershell"></a>A PowerShell előkészítése

Először [le kell töltenie az Azure ad Preview PowerShell](https://www.powershellgallery.com/packages/AzureADPreview)-modult.

Az Azure AD PowerShell-modul telepítéséhez használja az alábbi parancsokat:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

A következő parancs használatával ellenőrizheti, hogy a modul használatra kész-e:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.2.31     azuread                      {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role"></a>Az egyéni szerepkör létrehozása

Hozzon létre egy új szerepkört a következő PowerShell-parancsfájl használatával:

``` PowerShell
# Basic role information
$description = "Application Support Administrator"
$displayName = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Egyéni szerepkör kiosztása az Azure AD PowerShell használatával

Rendelje hozzá a szerepkört az alábbi PowerShell-parancsfájl használatával:

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADRoleDefinition -Filter "displayName eq ' Application Registration Creator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScopes = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADRoleAssignment -ResourceScopes $resourceScopes -RoleDefinitionId $roleDefinition.objectId -PrincipalId $user.objectId
```

## <a name="create-a-custom-role-using-microsoft-graph-api"></a>Egyéni szerepkör létrehozása Microsoft Graph API használatával

1. Hozza létre a szerepkör-definíciót.

    HTTP-kérelem egyéni szerepkör-definíció létrehozásához.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Body

    ``` HTTP
    {
    "description":"Can manage basic aspects of application registrations.",
    "displayName":"Application Support Administrator",
    "isEnabled":true,
    "rolePermissions":
    [
        "microsoft.directory/applications/basic/update",
        "microsoft.directory/applications/credentials/update"
    ]
    }
    ```

1. Hozza létre a szerepkör-hozzárendelést.

    HTTP-kérelem egyéni szerepkör-definíció létrehozásához.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    Body

    ``` HTTP
    {
    "principalId":"<GUID OF USER>",
    "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
    "resourceScope":["/<GUID OF APPLICATION REGISTRATION>"]
    }
    ```

## <a name="next-steps"></a>További lépések

- Nyugodtan ossza meg velünk az [Azure ad rendszergazdai szerepkörökkel](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)foglalkozó fórumát.
- A szerepkörökkel és a rendszergazdai szerepkör-hozzárendeléssel kapcsolatos további információkért lásd: [rendszergazdai szerepkörök hozzárendelése](directory-assign-admin-roles.md).
- Az alapértelmezett felhasználói engedélyek összehasonlítását lásd: a [vendég és a tag alapértelmezett felhasználói engedélyeinek összehasonlítása](../fundamentals/users-default-permissions.md).

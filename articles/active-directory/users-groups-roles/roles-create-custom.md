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
ms.openlocfilehash: ae773bc1a4e1831dbe462149bb827c26b7e74b96
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722308"
---
# <a name="create-a-custom-role-and-assign-a-resource-scope-in-azure-active-directory"></a>Hozzon létre egy egyéni szerepkört, és rendeljen hozzá egy erőforrás-hatókört Azure Active Directory

Ez a cikk bemutatja, hogyan hozhat létre új egyéni szerepköröket a Azure Active Directoryban (Azure AD). Az egyéni szerepkörök az Azure AD áttekintés lapján vagy [az alkalmazás regisztrációs lapján](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)hozhatók létre a **szerepkörök és rendszergazdák** lapon. A szerepkör a címtár-szintű hatókörben vagy csak az alkalmazás-regisztrációhoz rendelhető hozzá.

További információ: egyéni szerepkörök [– Áttekintés](roles-custom-overview.md) az egyéni szerepkörök alapjairól.

## <a name="using-the-azure-ad-portal"></a>Az Azure AD-portál használata

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Új egyéni szerepkör létrehozása az alkalmazások regisztrálásához való hozzáférés biztosításához

1. Jelentkezzen be az [Azure ad felügyeleti](https://aad.portal.azure.com) központba a Kiemelt szerepkörű rendszergazda vagy a globális rendszergazdai engedélyekkel az Azure ad-szervezetben.
1. Válassza ki **Azure Active Directory** > **szerepkörök és rendszergazdák** > **új egyéni szerepkört**.

   ![Szerepkörök létrehozása vagy szerkesztése a szerepkörök és rendszergazdák lapról](./media/roles-create-custom/new-custom-role.png)

1. Az **alapok** lapon adja meg a szerepkör nevét és leírását.

   ![adja meg az egyéni szerepkör nevét és leírását az alapok lapon](./media/roles-create-custom/basics-tab.png)

1. Az alkalmazás-regisztrációs hitelesítő adatok és az alapszintű tulajdonságok, például a név kezeléséhez szükséges engedélyek kiválasztása:
   1. Adja meg a "hitelesítő adatok" kifejezést a keresősáv alatt `microsoft.directory/applications/credentials/update` , és válassza ki az engedélyt.

      ![Egyéni szerepkör engedélyeinek kiválasztása az engedélyek lapon](./media/roles-create-custom/permissions-tab.png)

   1. Írja be a "Basic" kifejezést a keresősávba, `microsoft.directory/applications/basic/update` válassza ki az engedélyt, majd kattintson a **tovább**gombra.
1. A **felülvizsgálat + létrehozás** lapon tekintse át az engedélyeket, és válassza a **Létrehozás**lehetőséget.

Az egyéni szerepkör megjelenik a Hozzárendelendő elérhető szerepkörök listájában.

## <a name="assign-a-role-scoped-to-a-resource"></a>Szerepkör hatókörének társítása erőforráshoz

A beépített szerepkörökhöz hasonlóan az egyéni szerepkörök is hozzárendelhetők az alapértelmezett szervezeti hatókörhöz, így hozzáférést biztosíthat az összes alkalmazás-regisztrációhoz. Az egyéni szerepkörök azonban az objektum hatókörében is hozzárendelhetők. Ez lehetővé teszi, hogy a megbízott jogosult legyen egy adott alkalmazás hitelesítő adatainak és alapvető tulajdonságainak frissítésére anélkül, hogy második egyéni szerepkört kellene létrehoznia.

1. Jelentkezzen be az [Azure ad felügyeleti](https://aad.portal.azure.com) központba az alkalmazás fejlesztői engedélyeivel az Azure ad-szervezetben.
1. Válassza az **Alkalmazásregisztrációk** elemet.
1. Válassza ki azt az alkalmazás-regisztrációt, amelyhez hozzáférést kíván adni a kezeléshez. Előfordulhat, hogy az **összes alkalmazás** lehetőséget kell választania az Azure ad-szervezetben az alkalmazások regisztrálásának teljes listájának megtekintéséhez.

    ![Válassza ki az alkalmazás regisztrációját erőforrás-hatókörként a szerepkör-hozzárendeléshez](./media/roles-create-custom/appreg-all-apps.png)

1. Az alkalmazás regisztrálása területen válassza a **szerepkörök és rendszergazdák**lehetőséget. Ha még nem hozott létre ilyet, az utasítások az [előző eljárásban](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations)találhatók.

    Ha ezt a szerepkört egy nyílt alkalmazás-regisztráció környezetében rendeli hozzá, akkor a hozzárendelt engedélyekkel rendelkezik az adott alkalmazás regisztrálásához. Az Ön által hozzárendelt szerepkör megjelenik a listában minden alkalmazás regisztrálásakor. Ez a hozzáférési modell, ahol a tulajdonos jogosultságot rendelhet bizonyos Azure AD-erőforrásokhoz szerepkörök szerint, hasonló az [Azure RBAC](../../role-based-access-control/overview.md) for Azure erőforrás-hozzáférés-vezérléshez használt modellhez.

1. Válassza ki a szerepkört a **hozzárendelések** lap megnyitásához.
1. Felhasználó hozzáadásához válassza a **hozzárendelés hozzáadása** lehetőséget. A felhasználó nem kap semmilyen jogosultságot az alkalmazás regisztrációján kívül, kivéve a kiválasztott elemet.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Egyéni szerepkör létrehozása az Azure AD PowerShell-lel

### <a name="prepare-powershell"></a>A PowerShell előkészítése

Először [le kell töltenie az Azure ad PowerShell](https://www.powershellgallery.com/packages/AzureAD/)-modult.

Az Azure AD PowerShell-modul telepítéséhez használja az alábbi parancsokat:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

A következő parancs használatával ellenőrizheti, hogy a modul használatra kész-e:

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
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
    "microsoft.directory/applications/allProperties/read",
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customAdmin = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
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
        {
            "allowedResourceActions": 
            [
                "microsoft.directory/applications/basic/read",
                "microsoft.directory/applications/credentials/update"
            ]
        }
    ]
    }
    ```

1. Hozza létre a szerepkör-hozzárendelést.

    HTTP-kérelem egyéni szerepkör-definíció létrehozásához.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions/roleAssignments
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

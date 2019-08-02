---
title: Rendszergazdai szerepkör engedélyeinek megtekintése a felügyeleti központban – Azure Active Directory | Microsoft Docs
description: Most már megtekintheti és kezelheti az Azure ad rendszergazdai szerepkör tagjait az Azure AD felügyeleti központban.
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
ms.openlocfilehash: bf45d45481712e30d40bacec9a3c4d80d1ed56b6
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707551"
---
# <a name="view-custom-role-assignments-in-azure-active-directory"></a>Egyéni szerepkör-hozzárendelések megtekintése Azure Active Directory

Ez a cikk azt ismerteti, hogyan lehet megtekinteni a Azure Active Directoryhoz rendelt egyéni szerepköröket (Azure AD). Azure Active Directory (Azure AD) esetében a szerepkörök a címtár szintjén vagy egyetlen alkalmazás hatókörével rendelhetők hozzá. A címtár hatókörében lévő szerepkör-hozzárendelések hozzá lesznek adva az Egyalkalmazásos szerepkör-hozzárendelések listájához, de az egyetlen alkalmazás hatókörében lévő szerepkör-hozzárendelések nem lesznek hozzáadva a címtári szintű hozzárendelések listájához.

## <a name="view-the-assignments-of-a-role-with-directory-scope-using-the-azure-ad-portal"></a>Címtár hatókörű szerepkör hozzárendeléseinek megtekintése az Azure AD-portál használatával

1. Jelentkezzen be az [Azure ad felügyeleti](https://aad.portal.azure.com) központba a Kiemelt szerepkörű rendszergazda vagy a globális rendszergazdai engedélyekkel az Azure ad-szervezetben.
1. Válassza a **Azure Active Directory**lehetőséget, válassza a **szerepkörök és rendszergazdák**lehetőséget, majd válasszon ki egy szerepkört a megnyitásához, és tekintse meg a tulajdonságait.
1. Válassza a hozzárendelések lehetőséget a szerepkör hozzárendeléseinek megtekintéséhez.

    ![Szerepkör-hozzárendelések és engedélyek megtekintése, amikor megnyit egy szerepkört a listából](./media/roles-view-assignments/role-assignments.png)

## <a name="view-the-assignments-of-a-role-with-directory-scope-using-azure-ad-powershell"></a>Címtár hatókörű szerepkör hozzárendeléseinek megtekintése az Azure AD PowerShell használatával

Automatizálhatja, hogyan rendeljen hozzá Azure AD-rendszergazdai szerepköröket a felhasználókhoz Azure PowerShell használatával. Ez a cikk a [Azure Active Directory PowerShell 2-es verziójának](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) modulját használja.

### <a name="prepare-powershell"></a>A PowerShell előkészítése

Először [le kell töltenie az Azure ad Preview PowerShell](https://www.powershellgallery.com/packages/AzureAD/)-modult.

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

### <a name="view-the-assignments-of-a-role"></a>Szerepkör hozzárendeléseinek megtekintése

Példa egy szerepkör hozzárendeléseinek megtekintésére.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-the-assignments-of-a-role-with-directory-scope-using-microsoft-graph-api"></a>Címtár-hatókörrel rendelkező szerepkör hozzárendeléseinek megtekintése Microsoft Graph API használatával

HTTP-kérelem egy adott szerepkör-definícióhoz tartozó szerepkör-hozzárendelés beszerzéséhez.

GET

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Válasz

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview"></a>Egy szerepkör hozzárendelésének megtekintése az Egyalkalmazásos hatókörrel az Azure AD portálon (előzetes verzió)

1. Jelentkezzen be az [Azure ad felügyeleti](https://aad.portal.azure.com) központba a Kiemelt szerepkörű rendszergazda vagy a globális rendszergazdai engedélyekkel az Azure ad-szervezetben.
1. Válassza a Azure Active Directory lehetőséget, válassza a **Alkalmazásregisztrációk**lehetőséget, majd válassza ki az alkalmazás regisztrációját a tulajdonságainak megtekintéséhez. Előfordulhat, hogy az **összes alkalmazás** lehetőséget kell választania az Azure ad-szervezetben az alkalmazások regisztrálásának teljes listájának megtekintéséhez.

    ![Alkalmazások regisztrálásának létrehozása vagy szerkesztése a Alkalmazásregisztrációk lapon](./media/roles-create-custom/appreg-all-apps.png)

1. Válassza a **szerepkörök és rendszergazdák**lehetőséget, majd válasszon egy szerepkört a tulajdonságainak megtekintéséhez.

    ![Alkalmazás-regisztrációs szerepkör-hozzárendelések megtekintése a Alkalmazásregisztrációk lapról](./media/roles-view-assignments/appreg-assignments.png)

1. Válassza a hozzárendelések lehetőséget a szerepkör hozzárendeléseinek megtekintéséhez.

    ![Alkalmazás-regisztrációs szerepkör-hozzárendelések megtekintése az alkalmazások regisztrációjának tulajdonságaiból](./media/roles-view-assignments/appreg-assignments-2.png)

## <a name="next-steps"></a>További lépések

* Nyugodtan ossza meg velünk az [Azure ad rendszergazdai szerepkörökkel](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)foglalkozó fórumát.
* A szerepkörökkel és a rendszergazdai szerepkör-hozzárendeléssel kapcsolatos további információkért lásd: [rendszergazdai szerepkörök hozzárendelése](directory-assign-admin-roles.md).
* Az alapértelmezett felhasználói engedélyek összehasonlítását lásd: a [vendég és a tag alapértelmezett felhasználói engedélyeinek összehasonlítása](../fundamentals/users-default-permissions.md).

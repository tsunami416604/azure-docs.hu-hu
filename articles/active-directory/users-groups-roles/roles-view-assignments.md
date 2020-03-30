---
title: Egyéni szerepkör-hozzárendelések megtekintése az Azure AD portálon | Microsoft dokumentumok
description: Most már megtekintheti és kezelheti az Azure AD-rendszergazdai szerepkör tagjait az Azure AD felügyeleti központban.
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
ms.openlocfilehash: f04bd7788a9cc9657e14aedfb153182d6e53eb95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259707"
---
# <a name="view-custom-role-assignments-in-azure-active-directory"></a>Egyéni szerepkör-hozzárendelések megtekintése az Azure Active Directoryban

Ez a cikk ismerteti, hogyan tekintheti meg az Azure Active Directoryban (Azure AD) hozzárendelt egyéni szerepköröket. Az Azure Active Directoryban (Azure AD) szerepkörök szervezeti szintű hatókörrel vagy egyalkalmazásos hatókörrel rendelhetők hozzá.

- A szervezeti szintű szerepkör-hozzárendelések hozzáadódnak az egyalkalmazásos szerepkör-hozzárendelések listájához, és láthatók azok között.
- Az egyetlen alkalmazáshatókörszerepkör-hozzárendelések nem kerülnek hozzáadásra, és nem láthatók a szervezeti szintű hatókör-hozzárendelések listájában.

## <a name="view-role-assignments-in-the-azure-portal"></a>Szerepkör-hozzárendelések megtekintése az Azure Portalon

Ez az eljárás egy szerepkör hozzárendeléseinek megtekintését ismerteti szervezeti szintű hatókörrel.

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) kiemelt szereppel rendelkező szerepkör-rendszergazdai vagy globális rendszergazdai engedélyekkel az Azure AD-szervezetben.
1. Válassza az **Azure Active Directory**lehetőséget, válassza a **Szerepkörök és rendszergazdák**lehetőséget, majd válasszon ki egy szerepkört a megnyitásához és a tulajdonságainak megtekintéséhez.
1. **Válassza a Hozzárendelések** lehetőséget a szerepkör hozzárendelései megtekintéséhez.

    ![Szerepkör-hozzárendelések és engedélyek megtekintése, amikor megnyit egy szerepkört a listából](./media/roles-view-assignments/role-assignments.png)

## <a name="view-role-assignments-using-azure-ad-powershell"></a>Szerepkör-hozzárendelések megtekintése az Azure AD PowerShell használatával

Ez a szakasz egy szerepkör hozzárendeléseinek megtekintését ismerteti szervezeti szintű hatókörrel. Ez a cikk az [Azure Active Directory PowerShell 2-es verziómodulját](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) használja. Az egyalkalmazásos hatókör-hozzárendelések PowerShell használatával történő megtekintéséhez használhatja az [egyéni szerepkörök hozzárendelése a PowerShell-lel](roles-assign-powershell.md)parancsmagokat.

### <a name="prepare-powershell"></a>PowerShell előkészítése

Először le kell [töltenie az Azure AD előzetes PowerShell-modult.](https://www.powershellgallery.com/packages/AzureAD/)

Az Azure AD PowerShell-modul telepítéséhez használja a következő parancsokat:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Annak ellenőrzéséhez, hogy a modul készen áll-e a használatra, használja a következő parancsot:

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

## <a name="view-role-assignments-using-microsoft-graph-api"></a>Szerepkör-hozzárendelések megtekintése a Microsoft Graph API használatával

Ez a szakasz egy szerepkör hozzárendeléseinek megtekintését ismerteti szervezeti szintű hatókörrel.  Az egyalkalmazásos hatókör-hozzárendelések Graph API használatával történő megtekintéséhez használhatja az [egyéni szerepkörök hozzárendelése a Graph API-val](roles-assign-graph.md)műveletet.

HTTP-kérelem egy adott szerepkör-definíció szerepkör-hozzárendelésének lekéréséhez.

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
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

## <a name="view-assignments-of-single-application-scope"></a>Egyalkalmazásos hatókör hozzárendeléseinek megtekintése

Ez a szakasz az egyalkalmazásos hatókörrel rendelkező szerepkör-hozzárendelések megtekintését ismerteti. Ez a funkció jelenleg nyilvános előzetes verzióban érhető el.

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) kiemelt szereppel rendelkező szerepkör-rendszergazdai vagy globális rendszergazdai engedélyekkel az Azure AD-szervezetben.
1. Válassza **az Alkalmazásregisztrációk**lehetőséget, majd az alkalmazásregisztráció t a tulajdonságainak megtekintéséhez. Előfordulhat, hogy az Azure AD-szervezetben az alkalmazásregisztrációk teljes listájának megtekintéséhez minden **alkalmazás** kiválasztásához válassza.

    ![Alkalmazásregisztrációk létrehozása vagy szerkesztése az Alkalmazásregisztrációk lapon](./media/roles-create-custom/appreg-all-apps.png)

1. Az alkalmazásregisztrációban válassza a **Szerepkörök és rendszergazdák**lehetőséget, majd jelöljön ki egy szerepkört a tulajdonságainak megtekintéséhez.

    ![Alkalmazásregisztrációs szerepkör-hozzárendelések megtekintése az Alkalmazásregisztrációk lapon](./media/roles-view-assignments/appreg-assignments.png)

1. **Válassza a Hozzárendelések** lehetőséget a szerepkör hozzárendelései megtekintéséhez. A hozzárendelések nézet megnyitása az alkalmazás regisztrációján belül megmutatja az Azure AD-erőforrás hatókörét.

    ![Alkalmazásregisztrációs szerepkör-hozzárendelések megtekintése az alkalmazásregisztráció tulajdonságaiból](./media/roles-view-assignments/appreg-assignments-2.png)

## <a name="next-steps"></a>További lépések

* Nyugodtan ossza meg velünk az [Azure AD felügyeleti szerepkörök fórum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* A szerepkörökről és a Rendszergazdai szerepkör-hozzárendelésről a [Rendszergazdai szerepkörök hozzárendelése című témakörben](directory-assign-admin-roles.md)van további témakör.
* Az alapértelmezett felhasználói engedélyeket [az alapértelmezett vendég- és tagjogosultságok összehasonlítása](../fundamentals/users-default-permissions.md)című témakörben olvashatja.

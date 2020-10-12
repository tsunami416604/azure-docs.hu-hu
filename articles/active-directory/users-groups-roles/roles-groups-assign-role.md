---
title: Szerepkör kiosztása egy felhőalapú csoporthoz Azure Active Directoryban | Microsoft Docs
description: Rendeljen hozzá egy Azure AD-szerepkört egy szerepkörhöz hozzárendelhető csoporthoz a Azure Portal, a PowerShellben vagy a Graph API.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f45b5709369dfc2025b55bc3acec69e9328ce403
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87513417"
---
# <a name="assign-a-role-to-a-cloud-group-in-azure-active-directory"></a>Szerepkör kiosztása egy felhőalapú csoporthoz Azure Active Directory

Ez a szakasz azt ismerteti, hogy a rendszergazda hogyan rendelhet hozzá Azure Active Directory (Azure AD) szerepkört egy Azure AD-csoporthoz.

## <a name="using-azure-ad-admin-center"></a>Az Azure AD felügyeleti központ használata

A csoportok Azure AD-szerepkörökhöz való hozzárendeléséhez hasonló a felhasználók és az egyszerű szolgáltatások hozzárendeléséhez, azzal a kivétellel, hogy csak a szerepkör-hozzárendelésre alkalmas csoportok használhatók. A Azure Portal csak a szerepkörhöz hozzárendelhető csoportok jelennek meg.

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) a Kiemelt szerepkörű rendszergazda vagy a globális rendszergazdai engedélyekkel az Azure ad-szervezetben.

1. Válassza ki **Azure Active Directory**  >  **szerepkörök és rendszergazdák**lehetőséget, majd válassza ki a hozzárendelni kívánt szerepkört.

1. A ***szerepkör neve*** lapon válassza ki > **hozzárendelés hozzáadása**elemet.

   ![Az új szerepkör-hozzárendelés hozzáadása](./media/roles-groups-assign-role/add-assignment.png)

1. Válassza ki a csoportot. Csak az Azure AD-szerepkörökhöz hozzárendelhető csoportok jelennek meg.

    [![Az új szerepkör-hozzárendeléshez csak a hozzárendelhető csoportok jelennek meg.](media/roles-groups-assign-role/eligible-groups.png "Az új szerepkör-hozzárendeléshez csak a hozzárendelhető csoportok jelennek meg.")](media/roles-groups-assign-role/eligible-groups.png#lightbox)

1. Válassza a **Hozzáadás** lehetőséget.

A szerepkör-engedélyek hozzárendelésével kapcsolatos további információkért lásd: [rendszergazdai és nem rendszergazdai szerepkörök kiosztása a felhasználók számára](../fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="using-powershell"></a>A PowerShell használata

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Szerepkörhöz hozzárendelhető csoport létrehozása

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true 
```

### <a name="get-the-role-definition-for-the-role-you-want-to-assign"></a>A hozzárendelni kívánt szerepkör szerepkör-definíciójának beolvasása

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'" 
```

### <a name="create-a-role-assignment"></a>Szerepkör-hozzárendelés létrehozása

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id 
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph API használata

### <a name="create-a-group-that-can-be-assigned-azure-ad-role"></a>Azure AD-szerepkörhöz hozzárendelhető csoport létrehozása

```
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

### <a name="get-the-role-definition"></a>Szerepkör-definíció beolvasása

```
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>Szerepkör-hozzárendelés létrehozása

```
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<ID of role definition>",
"directoryScopeId":"/"
}
```
## <a name="next-steps"></a>További lépések

- [Felhőalapú csoportok használata a szerepkör-hozzárendelések kezeléséhez](roles-groups-concept.md)
- [A felhőbeli csoportokhoz hozzárendelt szerepkörökkel kapcsolatos hibák elhárítása](roles-groups-faq-troubleshooting.md)

---
title: Csoporthoz rendelt szerepkörök megtekintése Azure Active Directoryban | Microsoft Docs
description: Az Identitáskezelés delegálásához egyéni Azure AD-szerepköröket tekinthet meg. Az Azure-szerepkörök kezelése a Azure Portal, a PowerShell vagy a Graph API használatával.
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
ms.openlocfilehash: 3b7613fb70299a70e4389b97c2647a26cb7c3374
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87476087"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Csoporthoz rendelt szerepkörök megtekintése Azure Active Directory

Ez a szakasz azt ismerteti, hogyan tekinthetők meg a csoportokhoz rendelt szerepkörök az Azure AD felügyeleti központban. A csoportok és a hozzárendelt szerepkörök megtekintése alapértelmezett felhasználói engedély.

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) a nem rendszergazdai vagy rendszergazdai hitelesítő adatokkal.

1. Válassza ki azt a csoportot, amelyre kíváncsi.

1. Válassza ki a **hozzárendelt szerepkörök**elemet. Most már megtekintheti a csoporthoz rendelt összes Azure AD-szerepkört.

   ![Egy kiválasztott csoporthoz rendelt összes szerepkör megtekintése](./media/roles-groups-view-assignments/view-assignments.png)

## <a name="using-powershell"></a>A PowerShell használata

### <a name="get-object-id-of-the-group"></a>Csoport objektumazonosítóának beolvasása

```powershell
Get-AzureADMSGroup -SearchString “Contoso_Helpdesk_Administrators”
```

### <a name="view-role-assignment-to-a-group"></a>Szerepkör-hozzárendelés megtekintése egy csoport számára

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph API használata

### <a name="get-object-id-of-the-group"></a>Csoport objektumazonosítóának beolvasása

```powershell
GET https://graph.microsoft.com/beta/groups?$filter displayName eq ‘Contoso_Helpdesk_Administrator’ 
```

### <a name="get-role-assignments-to-a-group"></a>Szerepkör-hozzárendelések beolvasása egy csoportba

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>További lépések

- [Felhőalapú csoportok használata a szerepkör-hozzárendelések kezeléséhez](roles-groups-concept.md)
- [Felhőalapú csoportokhoz rendelt szerepkörök hibaelhárítása](roles-groups-faq-troubleshooting.md)

---
title: Csoporthoz rendelt szerepkörök megtekintése Azure Active Directoryban | Microsoft Docs
description: Megtudhatja, hogyan tekinthetők meg a csoportokhoz rendelt szerepkörök az Azure AD felügyeleti központban. A csoportok és a hozzárendelt szerepkörök megtekintése alapértelmezett felhasználói engedély.
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
ms.openlocfilehash: 0c0a34b3861c82b3d2ef54a36108f9ea522d716d
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90983113"
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

## <a name="next-steps"></a>Következő lépések

- [Felhőalapú csoportok használata a szerepkör-hozzárendelések kezeléséhez](roles-groups-concept.md)
- [A felhőbeli csoportokhoz hozzárendelt szerepkörökkel kapcsolatos hibák elhárítása](roles-groups-faq-troubleshooting.md)

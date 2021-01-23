---
title: Csoporthoz rendelt szerepkörök megtekintése Azure Active Directoryban | Microsoft Docs
description: Megtudhatja, hogyan tekinthetők meg a csoportokhoz rendelt szerepkörök az Azure AD felügyeleti központban. A csoportok és a hozzárendelt szerepkörök megtekintése alapértelmezett felhasználói engedély.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a1939be42126606fdae261e60c890c71374c894
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2021
ms.locfileid: "98741825"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Csoporthoz rendelt szerepkörök megtekintése Azure Active Directory

Ez a szakasz azt ismerteti, hogyan tekinthetők meg a csoportokhoz rendelt szerepkörök az Azure AD felügyeleti központban. A csoportok és a hozzárendelt szerepkörök megtekintése alapértelmezett felhasználói engedély.

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) a nem rendszergazdai vagy rendszergazdai hitelesítő adatokkal.

1. Válassza ki azt a csoportot, amelyre kíváncsi.

1. Válassza ki a **hozzárendelt szerepkörök** elemet. Most már megtekintheti a csoporthoz rendelt összes Azure AD-szerepkört.

   ![Egy kiválasztott csoporthoz rendelt összes szerepkör megtekintése](./media/groups-view-assignments/view-assignments.png)

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

- [Felhőbeli csoportok használata a szerepkör-hozzárendelések kezeléséhez](groups-concept.md)
- [A felhőbeli csoportokhoz hozzárendelt szerepkörökkel kapcsolatos hibák elhárítása](groups-faq-troubleshooting.md)

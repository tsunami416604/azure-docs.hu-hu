---
title: A Microsoft 365 számítási feladatokhoz – az Azure AD rendszergazdai szerepkör tartalmat |} A Microsoft Docs
description: Tartalom és API-referenciák találhat a Microsoft 365 számítási feladatokhoz az Azure Active Directory rendszergazdai szerepkörök
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 9ef400a5c7b42f6782fefa28e2351b09d8667861
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55470246"
---
# <a name="administrator-roles-for-microsoft-365-workloads"></a>A Microsoft 365 számítási feladatokhoz a rendszergazdai szerepkörökről

Minden termék a Microsoft 365-ben az Azure AD felügyeleti szerepkörök is felügyelhetők. Egyes termékek termékekre vonatkozó további szerepköröket is biztosítanak. Az egyes termékek által támogatott szerepkörökkel kapcsolatos információkért lásd az alábbi táblázat. A delegálási problémák általános viták található [tervezése az Azure Active Directoryban a szerepkör-delegálás](roles-concept-delegation.md).

## <a name="where-to-find-content"></a>Hol található tartalom

A Microsoft 365 számítási feladatok | Obsah Role | API-tartalom
---------------------- | ------------------ | -----------------
Az Office 365 és a Microsoft 365 üzleti csomagjait rendszergazdai szerepkörök | [Office 365-rendszergazdai szerepkörök](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Nem érhető el
Az Azure Active Directory (Azure AD) és az Azure AD Identity Protection| [Az Azure AD felügyeleti szerepkörök](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Szerepkör-hozzárendelések beolvasása](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Exchange-szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell az Exchange-hez](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Szerepkör-hozzárendelések beolvasása](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Az Azure AD felügyeleti szerepkörök](directory-assign-admin-roles.md)<br>Emellett [a SharePoint-rendszergazdai szerepkör az Office 365-ben](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Szerepkör-hozzárendelések beolvasása](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Csoportok és Skype vállalati verzió | [Az Azure AD felügyeleti szerepkörök](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Szerepkör-hozzárendelések beolvasása](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Biztonsági és megfelelőségi központban (Office 365, a komplex veszélyforrások elleni védelem, az Exchange Online védelmi Information Protection) | [Office 365-rendszergazdai szerepkörök](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Szerepkör-hozzárendelések beolvasása](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Biztonságos pontszám | [Az Azure AD felügyeleti szerepkörök](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Szerepkör-hozzárendelések beolvasása](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Megfelelőségi Manager | [Megfelelőségi Manager szerepkörök](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Nem érhető el
Azure Information Protection | [Az Azure AD felügyeleti szerepkörök](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Szerepkör-hozzárendelések beolvasása](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/cloud-app-security/manage-admins) | [API-leírások](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Azure Komplex veszélyforrások elleni védelem | [Az Azure ATP szerepkörcsoportjai](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | Nem érhető el
Windows Defender Komplex veszélyforrások elleni védelem | [A Windows Defender ATP szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Nem érhető el
Privileged Identity Management | [Az Azure AD felügyeleti szerepkörök](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Szerepkör-hozzárendelések beolvasása](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Az Intune szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/intune/role-based-access-control) | [Graph API](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Szerepkör-hozzárendelések beolvasása](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Felügyelt asztali | [Az Azure AD felügyeleti szerepkörök](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Szerepkör-hozzárendelések beolvasása](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>További lépések

* [Rendelje hozzá, vagy távolítsa el az Azure AD-rendszergazdai szerepkörök](directory-manage-roles-portal.md)
* [Azure AD rendszergazdai szerepkörei hivatkozhat.](directory-assign-admin-roles.md)

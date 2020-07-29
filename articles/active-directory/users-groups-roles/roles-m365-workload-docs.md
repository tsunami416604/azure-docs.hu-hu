---
title: Rendszergazdai szerepkör docs Microsoft 365 szolgáltatások között – Azure AD | Microsoft Docs
description: Tartalom és API-referenciák keresése a Microsoft 365-szolgáltatások felügyeleti szerepköreihez Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79e3ed41ad3038cf66d61c25292ebad55c2b5e92
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84731697"
---
# <a name="administrator-roles-for-microsoft-365-services"></a>Microsoft 365 szolgáltatások rendszergazdai szerepkörei

A Microsoft 365ban található összes termék felügyelhető az Azure AD-ben rendszergazdai szerepkörökkel. Egyes termékek a termékre vonatkozó további szerepköröket is biztosítanak. Az egyes termékek által támogatott szerepkörökkel kapcsolatos információkért tekintse meg az alábbi táblázatot. A delegálással kapcsolatos problémák általános megbeszélései a Azure Active Directory-ben a [szerepkör-delegálás megtervezésében](roles-concept-delegation.md)találhatók.

## <a name="where-to-find-content"></a>Hol található a tartalom?

Microsoft 365 szolgáltatás | Szerepkör tartalma | API-tartalom
---------------------- | ------------------ | -----------------
Rendszergazdai szerepkörök az Office 365-ben és Microsoft 365 üzleti tervekben | [Office 365-rendszergazdai szerepkörök](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Nem érhető el
Azure Active Directory (Azure AD) és Azure AD Identity Protection| [Azure AD-rendszergazdai szerepkörök](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Szerepkör-hozzárendelések beolvasása](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Exchange szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell Exchange-hez](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Szerepkör-hozzárendelések beolvasása](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Azure AD-rendszergazdai szerepkörök](directory-assign-admin-roles.md)<br>[A SharePoint-rendszergazdai szerepkör az Office 365-ben](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) is | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Szerepkör-hozzárendelések beolvasása](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Csapatok/Skype vállalati verzió | [Azure AD-rendszergazdai szerepkörök](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Szerepkör-hozzárendelések beolvasása](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Biztonsági & megfelelőségi központ (Office 365 Advanced Threat Protection, Exchange Online Protection, Information Protection) | [Office 365-rendszergazdai szerepkörök](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Szerepkör-hozzárendelések beolvasása](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Biztonságos pontszám | [Azure AD-rendszergazdai szerepkörök](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Szerepkör-hozzárendelések beolvasása](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Compliance Manager | [A megfelelőségi kezelő szerepkörei](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Nem érhető el
Azure Information Protection | [Azure AD-rendszergazdai szerepkörök](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Szerepkör-hozzárendelések beolvasása](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/cloud-app-security/manage-admins) | [API-leírások](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Azure Komplex veszélyforrások elleni védelem | [Az Azure ATP szerepkörcsoportjai](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | Nem érhető el
Windows Defender Komplex veszélyforrások elleni védelem | [Windows Defender ATP szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Nem érhető el
Privileged Identity Management | [Azure AD-rendszergazdai szerepkörök](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Szerepkör-hozzárendelések beolvasása](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Intune szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/intune/role-based-access-control) | [Graph API](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Szerepkör-hozzárendelések beolvasása](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Felügyelt asztal | [Azure AD-rendszergazdai szerepkörök](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Szerepkör-hozzárendelések beolvasása](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>További lépések

* [Azure AD-rendszergazdai szerepkörök kiosztása vagy eltávolítása](directory-manage-roles-portal.md)
* [Az Azure AD rendszergazdai szerepköreinek referenciája](directory-assign-admin-roles.md)

---
title: Felügyeleti szerepkör-dokumentumok a Microsoft 365-szolgáltatásokban – Azure AD | Microsoft dokumentumok
description: Tartalom- és API-hivatkozások keresése a Microsoft 365-szolgáltatások rendszergazdai szerepköreihez az Azure Active Directoryban
services: active-directory
documentationcenter: ''
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
ms.openlocfilehash: 430b65217da33185cd3b5c235fb148f3350e1bfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024453"
---
# <a name="administrator-roles-for-microsoft-365-services"></a>Rendszergazdai szerepkörök a Microsoft 365 szolgáltatásokhoz

A Microsoft 365 összes terméke felügyeleti szerepkörökkel kezelhető az Azure AD-ben. Egyes termékek további, az adott termékre jellemző szerepköröket is biztosítanak. Az egyes termékek által támogatott szerepkörökkel kapcsolatos információkért tekintse meg az alábbi táblázatot. A delegálási problémák általános megvitatása az [Azure Active Directory szerepkör-delegálási tervezésében](roles-concept-delegation.md)található.

## <a name="where-to-find-content"></a>Hol található tartalom?

Microsoft 365 szolgáltatás | Szerepkör tartalma | API-tartalom
---------------------- | ------------------ | -----------------
Rendszergazdai szerepkörök az Office 365-ben és a Microsoft 365 vállalati csomagban | [Office 365-rendszergazdai szerepkörök](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Nem érhető el
Az Azure Active Directory (Azure AD) és az Azure AD-identitásvédelem| [Azure AD-rendszergazdai szerepkörök](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Szerepkör-hozzárendelések lehívása](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Szerepköralapú hozzáférés-vezérlés cseréje](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [Az Exchange-hez való PowerShell](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Szerepkör-hozzárendelések lehívása](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Azure AD-rendszergazdai szerepkörök](directory-assign-admin-roles.md)<br>[Az Office 365 SharePoint felügyeleti szerepköre](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) is | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Szerepkör-hozzárendelések lehívása](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Teams/Skype Vállalati verzió | [Azure AD-rendszergazdai szerepkörök](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Szerepkör-hozzárendelések lehívása](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Biztonsági & megfelelőségi központ (Office 365 komplex veszélyforrások elleni védelem, Exchange Online védelem, információvédelem) | [Office 365-rendszergazdai szerepkörök](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Szerepkör-hozzárendelések lehívása](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Biztonsági pontszám | [Azure AD-rendszergazdai szerepkörök](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Szerepkör-hozzárendelések lehívása](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Compliance Manager | [Megfelelőségi kezelői szerepkörök](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Nem érhető el
Azure Information Protection | [Azure AD-rendszergazdai szerepkörök](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Szerepkör-hozzárendelések lehívása](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/cloud-app-security/manage-admins) | [API-leírások](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Azure Komplex veszélyforrások elleni védelem | [Az Azure ATP szerepkörcsoportjai](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | Nem érhető el
Windows Defender Komplex veszélyforrások elleni védelem | [Windows Defender ATP szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Nem érhető el
Privileged Identity Management | [Azure AD-rendszergazdai szerepkörök](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Szerepkör-hozzárendelések lehívása](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Intune szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/intune/role-based-access-control) | [Graph API](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Szerepkör-hozzárendelések lehívása](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Felügyelt asztal | [Azure AD-rendszergazdai szerepkörök](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Szerepkör-hozzárendelések lehívása](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>További lépések

* [Azure AD rendszergazdai szerepkörök hozzárendelése és eltávolítása](directory-manage-roles-portal.md)
* [Az Azure AD rendszergazdai szerepkörök hivatkozása](directory-assign-admin-roles.md)

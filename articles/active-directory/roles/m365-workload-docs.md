---
title: Rendszergazdai szerepkör docs Microsoft 365 szolgáltatások között – Azure AD | Microsoft Docs
description: Tartalom és API-referenciák keresése a Microsoft 365-szolgáltatások felügyeleti szerepköreihez Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42b1798646281ef4e78c9950e9931332cde373ed
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499169"
---
# <a name="roles-for-microsoft-365-services-in-azure-active-directory"></a>A Azure Active Directory Microsoft 365 szolgáltatásainak szerepkörei

A Microsoft 365ban található összes termék felügyelhető a Azure Active Directory (Azure AD) felügyeleti szerepköreivel. Egyes termékek a termékre vonatkozó további szerepköröket is biztosítanak. Az egyes termékek által támogatott szerepkörökkel kapcsolatos információkért tekintse meg az alábbi táblázatot. A delegálással kapcsolatos problémák általános megbeszélései a Azure Active Directory-ben a [szerepkör-delegálás megtervezésében](concept-delegation.md)találhatók.

## <a name="where-to-find-content"></a>Hol található a tartalom?

Microsoft 365 szolgáltatás | Szerepkör tartalma | API-tartalom
---------------------- | ------------------ | -----------------
Rendszergazdai szerepkörök az Office 365-ben és Microsoft 365 üzleti tervekben | [Rendszergazdai szerepkörök Microsoft 365](/office365/admin/add-users/about-admin-roles?view=o365-worldwide&preserve-view=true) | Nem elérhető
Azure Active Directory (Azure AD) és Azure AD Identity Protection| [Azure AD-rendszergazdai szerepkörök](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true&preserve-view=true)<br>[Szerepkör-hozzárendelések beolvasása](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Exchange Online| [Exchange szerepköralapú hozzáférés-vezérlés](/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell Exchange-hez](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps&preserve-view=true)<br>[Szerepkör-hozzárendelések beolvasása](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps&preserve-view=true)
SharePoint Online | [Azure AD-rendszergazdai szerepkörök](permissions-reference.md)<br>További [információ a SharePoint-rendszergazdai szerepkörről Microsoft 365](/sharepoint/sharepoint-admin-role) | [Graph API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[Szerepkör-hozzárendelések beolvasása](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Csapatok/Skype vállalati verzió | [Azure AD-rendszergazdai szerepkörök](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[Szerepkör-hozzárendelések beolvasása](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Biztonsági & megfelelőségi központ (Office 365 Advanced Threat Protection, Exchange Online Protection, Information Protection) | [Office 365-rendszergazdai szerepkörök](/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps&preserve-view=true)<br>[Szerepkör-hozzárendelések beolvasása](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps&preserve-view=true)
Biztonságos pontszám | [Azure AD-rendszergazdai szerepkörök](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[Szerepkör-hozzárendelések beolvasása](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Compliance Manager | [A megfelelőségi kezelő szerepkörei](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Nem elérhető
Azure Information Protection | [Azure AD-rendszergazdai szerepkörök](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[Szerepkör-hozzárendelések beolvasása](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Microsoft Cloud App Security | [Szerepköralapú hozzáférés-vezérlés](/cloud-app-security/manage-admins) | [API-leírások](/cloud-app-security/api-tokens) 
Azure Komplex veszélyforrások elleni védelem | [Az Azure ATP szerepkörcsoportjai](/azure-advanced-threat-protection/atp-role-groups) | Nem elérhető
Windows Defender Komplex veszélyforrások elleni védelem | [Windows Defender ATP szerepköralapú hozzáférés-vezérlés](/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Nem elérhető
Privileged Identity Management | [Azure AD-rendszergazdai szerepkörök](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[Szerepkör-hozzárendelések beolvasása](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Intune | [Intune szerepköralapú hozzáférés-vezérlés](/intune/role-based-access-control) | [Graph API](/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta&preserve-view=true)<br>[Szerepkör-hozzárendelések beolvasása](/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta&preserve-view=true)
Felügyelt asztal | [Azure AD-rendszergazdai szerepkörök](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[Szerepkör-hozzárendelések beolvasása](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)

## <a name="next-steps"></a>További lépések

* [Azure AD-rendszergazdai szerepkörök kiosztása vagy eltávolítása](manage-roles-portal.md)
* [Az Azure AD rendszergazdai szerepköreinek referenciája](permissions-reference.md)
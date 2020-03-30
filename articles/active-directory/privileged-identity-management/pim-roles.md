---
title: A kiemelt identitáskezelésben nem kezelhető szerepkörök – Azure Active Directory | Microsoft dokumentumok
description: Ismerteti az Azure AD emelt szintű identitáskezelés (PIM) szerepkörök nem kezelhető.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fac7074cf85a585c93ece60be9eea8ffb9a6345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72895206"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>A kiemelt identitáskezelésben nem kezelhető szerepkörök

Az Azure Active Directory (Azure AD) privilegizált identitáskezelése (PIM) lehetővé teszi az összes [Azure AD-szerepkör](../users-groups-roles/directory-assign-admin-roles.md) és az összes [Azure-erőforrás-szerepkör](../../role-based-access-control/built-in-roles.md)kezelését. Ezek a szerepkörök magukban foglalják a felügyeleti csoportokhoz, előfizetésekhez, erőforráscsoportokhoz és erőforrásokhoz csatolt egyéni szerepköröket is. Azonban kevés olyan szerepkör van, amelyet nem tud kezelni. Ez a cikk ismerteti a szerepkörök nem kezelhető a kiemelt identitáskezelés.

## <a name="classic-subscription-administrator-roles"></a>A hagyományos előfizetés-rendszergazdai szerepkörök

A következő klasszikus előfizetés-rendszergazdai szerepkörök nem kezelhetők a Kiemelt identitáskezelésben:

- Fiókadminisztrátor
- Szolgáltatás-rendszergazda
- Társadminisztrátor

A klasszikus előfizetéses rendszergazdai szerepkörökről további információt a [Klasszikus előfizetéses rendszergazdai szerepkörök, az Azure RBAC-szerepkörök és az Azure AD-rendszergazdai szerepkörök című témakörben talál.](../../role-based-access-control/rbac-and-directory-admin-roles.md)

## <a name="what-about-office-365-admin-roles"></a>Mi a helyzet az Office 365 rendszergazdai szerepköreivel?

Az Exchange Online-ban vagy a SharePoint Online-on belüli szerepkörök – az Exchange-rendszergazda és a SharePoint-rendszergazda kivételével – nem jelennek meg az Azure AD-ben, így nem kezelhetők a Kiemelt identitáskezelés ben. Az Office 365-szolgáltatásokról az [Office 365 rendszergazdai szerepkörei című témakörben](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles)talál további információt.

> [!NOTE]
> A SharePoint-rendszergazda rendszergazdai hozzáféréssel rendelkezik a SharePoint Online-hoz a SharePoint Online Felügyeleti központon keresztül, és szinte bármilyen feladatot el tud végezni a SharePoint Online-ban. A jogosult felhasználók késésben lehetnek a SharePoint-on belüli szerepkör használatában, miután aktiválták a Kiemelt identitáskezelést.

## <a name="next-steps"></a>További lépések

- [Azure AD-szerepkörök hozzárendelése a kiemelt identitáskezelésben](pim-how-to-add-role-to-user.md)
- [Azure-erőforrás-szerepkörök hozzárendelése a kiemelt identitáskezelésben](pim-resource-roles-assign-roles.md)

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
ms.date: 03/31/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6eaa50f57dd8037ef0ad96b69284f565bd3558f
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607523"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>A kiemelt identitáskezelésben nem kezelhető szerepkörök

Az Azure Active Directory (Azure AD) privilegizált identitáskezelése (PIM) lehetővé teszi az összes [Azure AD-szerepkör](../users-groups-roles/directory-assign-admin-roles.md) és az összes [Azure-szerepkör](../../role-based-access-control/built-in-roles.md)kezelését. Az Azure-szerepkörök a felügyeleti csoportokhoz, előfizetésekhez, erőforráscsoportokhoz és erőforrásokhoz kapcsolódó egyéni szerepköröket is tartalmazhatnak. Azonban kevés olyan szerepkör van, amelyet nem tud kezelni. Ez a cikk ismerteti a szerepkörök nem kezelhető a kiemelt identitáskezelés.

## <a name="classic-subscription-administrator-roles"></a>A hagyományos előfizetés-rendszergazdai szerepkörök

A következő klasszikus előfizetés-rendszergazdai szerepkörök nem kezelhetők a Kiemelt identitáskezelésben:

- Fiókadminisztrátor
- Szolgáltatás-rendszergazda
- Társadminisztrátor

A klasszikus előfizetéses rendszergazdai szerepkörökről további információt a [Klasszikus előfizetéses rendszergazdai szerepkörök, az Azure RBAC-szerepkörök és az Azure AD-rendszergazdai szerepkörök című témakörben talál.](../../role-based-access-control/rbac-and-directory-admin-roles.md)

## <a name="what-about-office-365-admin-roles"></a>Mi a helyzet az Office 365 rendszergazdai szerepköreivel?

Az Azure AD-szerepkörök és rendszergazdák portálon található összes Office365-szerepkört támogatjuk, például az Exchange-rendszergazda és a SharePoint-rendszergazda szolgáltatást, de az Exchange RBAC-on vagy a SharePoint RBAC-on belül nem támogatunk bizonyos szerepköröket. Az Office 365-szolgáltatásokról az [Office 365 rendszergazdai szerepkörei című témakörben](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles)talál további információt.

> [!NOTE]
> A SharePoint-rendszergazdai szerepkörhöz jogosult felhasználók, valamint a Microsoft Biztonsági és Megfelelőségi központelérését próbáló szerepkörök szerepkörei a szerepkör aktiválása után akár néhány órás késéseket is tapasztalhatnak. Együtt dolgozunk ezekkel a csapatokkal, hogy megoldjuk a problémákat.

## <a name="next-steps"></a>További lépések

- [Azure AD-szerepkörök hozzárendelése a kiemelt identitáskezelésben](pim-how-to-add-role-to-user.md)
- [Azure-erőforrás-szerepkörök hozzárendelése a kiemelt identitáskezelésben](pim-resource-roles-assign-roles.md)

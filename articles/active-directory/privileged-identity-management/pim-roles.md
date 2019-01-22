---
title: A szerepkörök nem kezelheti a PIM – Azure |} A Microsoft Docs
description: Ismerteti az Azure AD Privileged Identity Management (PIM) nem kezelheti a szerepköröket.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 01/18/2019
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: e4c60c6ecdc586ba97a01fa42cd80b8ed541fa61
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434938"
---
# <a name="roles-you-cannot-manage-in-pim"></a>Nem kezelheti a PIM szerepkörök

Az Azure AD Privileged Identity Management (PIM) lehetővé teszi az összes kezelését [az Azure AD-címtárbeli szerepkörök](../users-groups-roles/directory-assign-admin-roles.md) és az összes [Azure-erőforrások szerepköreihez](../../role-based-access-control/built-in-roles.md). Ezek a szerepkörök az egyéni szerepkörök csatlakozik a felügyeleti csoportokhoz, előfizetések, erőforráscsoportok és erőforrásokat is tartalmaznak. Vannak azonban néhány olyan szerepköröket, nem tudja kezelni. Ez a cikk ismerteti a szerepköröket a PIM nem kezelheti.

## <a name="classic-subscription-administrator-roles"></a>A hagyományos előfizetés-rendszergazdai szerepkörök

A következő hagyományos előfizetés rendszergazdai szerepköröket a PIM nem kezelheti:

- Fiókadminisztrátor
- Szolgáltatás-rendszergazda
- Társadminisztrátor

A hagyományos előfizetés-rendszergazda szerepkörök kapcsolatos további információkért lásd: [klasszikus előfizetés rendszergazdai szerepköröket, az Azure RBAC-szerepkörök és az Azure AD-rendszergazdai szerepköröket](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Mi a helyzet az Office 365 rendszergazdai szerepköreinek?

Az Exchange Online vagy SharePoint online-hoz, kivéve az Exchange-rendszergazda és a SharePoint-rendszergazda szerepkörök nem szerepelnek az Azure AD-ben, és ezért nem lehet kezelni a PIM. A Office 365-szolgáltatásokkal kapcsolatos további információkért lásd: [Office 365 rendszergazdai szerepköreinek](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> SharePoint-rendszergazda a SharePoint online rendszergazdai hozzáféréssel rendelkezik a SharePoint Online felügyeleti központban, és szinte bármilyen feladatot végrehajthat a SharePoint online-ban. Jogosult felhasználók tapasztalhat az késleltetések a PIM aktiválása után a sharepointból szerepkör használatával.

## <a name="next-steps"></a>További lépések

- [Ismerkedés a PIM-mel](pim-getting-started.md)
- [A PIM az Azure AD-címtárbeli szerepkörök hozzárendelése](pim-how-to-add-role-to-user.md)
- [Rendelje hozzá a PIM az Azure-erőforrásszerepkörök](pim-resource-roles-assign-roles.md)

---
title: A nem felügyelhető szerepkörök Privileged Identity Management-Azure Active Directoryban | Microsoft Docs
description: Ismerteti azokat a szerepköröket, amelyeket nem kezelhet Azure AD Privileged Identity Management (PIM) szolgáltatásban.
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
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895206"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Nem felügyelhető szerepkörök Privileged Identity Management

A Azure Active Directory (Azure AD) Privileged Identity Management (PIM) segítségével kezelheti az összes [Azure ad-szerepkört](../users-groups-roles/directory-assign-admin-roles.md) és az összes [Azure-erőforrás-szerepkört](../../role-based-access-control/built-in-roles.md). Ezek a szerepkörök a felügyeleti csoportokhoz, előfizetésekhez, erőforrás-csoportokhoz és erőforrásokhoz csatolt egyéni szerepköröket is tartalmazzák. Vannak azonban olyan szerepkörök, amelyeket nem kezelhet. Ez a cikk azokat a szerepköröket ismerteti, amelyeket nem kezelhet Privileged Identity Managementban.

## <a name="classic-subscription-administrator-roles"></a>A hagyományos előfizetés-rendszergazdai szerepkörök

A következő klasszikus előfizetés-rendszergazdai szerepkörök nem kezelhetők Privileged Identity Managementban:

- Fiókadminisztrátor
- Szolgáltatás-rendszergazda
- Társadminisztrátor

A klasszikus előfizetés-rendszergazdai szerepkörökkel kapcsolatos további információkért lásd a [klasszikus előfizetés-rendszergazdai szerepköröket, az Azure RBAC-szerepköröket és az Azure ad rendszergazdai szerepköreit](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Mi a helyzet az Office 365 rendszergazdai szerepköreivel?

Az Exchange Online-ban vagy a SharePoint Online-ban lévő szerepkörök nem jelennek meg az Azure AD-ben, az Exchange-rendszergazda és a SharePoint-rendszergazda kivételével, ezért nem kezelhetők Privileged Identity Managementban. További információ ezekről az Office 365-szolgáltatásokról: [office 365 rendszergazdai szerepkörök](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> A SharePoint-rendszergazda rendszergazdai hozzáféréssel rendelkezik a SharePoint Online-hoz a SharePoint Online felügyeleti központban, és szinte bármilyen feladatot képes végrehajtani a SharePoint Online-ban. A jogosult felhasználók a Privileged Identity Management-ban való aktiválás után késést tapasztalhatnak a SharePointon belüli ezen szerepkör használatával.

## <a name="next-steps"></a>Következő lépések

- [Azure AD-szerepkörök kiosztása Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Azure-beli erőforrás-szerepkörök kiosztása Privileged Identity Management](pim-resource-roles-assign-roles.md)

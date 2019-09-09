---
title: A PIM-Azure Active Directoryban nem kezelhető szerepkörök | Microsoft Docs
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
ms.date: 01/18/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: d66d433d9de537358777e54e3c7d5489c25c849b
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804088"
---
# <a name="roles-you-cannot-manage-in-pim"></a>A PIM-ben nem felügyelhető szerepkörök

A Azure Active Directory (Azure AD) Privileged Identity Management (PIM) segítségével kezelheti az összes [Azure ad-szerepkört](../users-groups-roles/directory-assign-admin-roles.md) és az összes [Azure-erőforrás-szerepkört](../../role-based-access-control/built-in-roles.md). Ezek a szerepkörök a felügyeleti csoportokhoz, előfizetésekhez, erőforrás-csoportokhoz és erőforrásokhoz csatolt egyéni szerepköröket is tartalmazzák. Vannak azonban olyan szerepkörök, amelyeket nem kezelhet. Ez a cikk azokat a szerepköröket ismerteti, amelyeket nem kezelhet a PIM-ben.

## <a name="classic-subscription-administrator-roles"></a>Klasszikus előfizetés-adminisztrátori szerepkörök

A következő klasszikus előfizetés-rendszergazdai szerepkörök nem kezelhetők a PIM-ben:

- Fiókadminisztrátor
- Szolgáltatás-rendszergazda
- Társadminisztrátor

A klasszikus előfizetés-rendszergazdai szerepkörökkel kapcsolatos további információkért lásd a [klasszikus előfizetés-rendszergazdai szerepköröket, az Azure RBAC-szerepköröket és az Azure ad rendszergazdai szerepköreit](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Mi a helyzet az Office 365 rendszergazdai szerepköreivel?

Az Exchange Online-ban vagy a SharePoint Online-ban lévő szerepkörök az Exchange-rendszergazda és a SharePoint-rendszergazda kivételével nem jelennek meg az Azure AD-ben, ezért nem kezelhetők a PIM-ben. További információ ezekről az Office 365-szolgáltatásokról: [office 365 rendszergazdai szerepkörök](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> A SharePoint-rendszergazda rendszergazdai hozzáféréssel rendelkezik a SharePoint Online-hoz a SharePoint Online felügyeleti központban, és szinte bármilyen feladatot képes végrehajtani a SharePoint Online-ban. A jogosult felhasználók a PIM-ben történő aktiválás után késések jelentkezhetnek a SharePointon belül.

## <a name="next-steps"></a>További lépések

- [Azure AD-szerepkörök kiosztása a PIM-ben](pim-how-to-add-role-to-user.md)
- [Azure-beli erőforrás-szerepkörök kiosztása a PIM-ben](pim-resource-roles-assign-roles.md)

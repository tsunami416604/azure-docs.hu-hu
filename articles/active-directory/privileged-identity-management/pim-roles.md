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
ms.date: 05/11/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d1d7ad27c43a0f8b4bbb763a1eb933a63cec58e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83115642"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Nem felügyelhető szerepkörök Privileged Identity Management

A Azure Active Directory (Azure AD) Privileged Identity Management (PIM) segítségével kezelheti az összes [Azure ad-szerepkört](../users-groups-roles/directory-assign-admin-roles.md) és az összes [Azure-szerepkört](../../role-based-access-control/built-in-roles.md). Az Azure-szerepkörök magukban foglalhatják a felügyeleti csoportokhoz, előfizetésekhez, erőforrás-csoportokhoz és erőforrásokhoz csatolt egyéni szerepköröket is. Vannak azonban olyan szerepkörök, amelyeket nem kezelhet. Ez a cikk azokat a szerepköröket ismerteti, amelyeket nem kezelhet Privileged Identity Managementban.

## <a name="classic-subscription-administrator-roles"></a>A hagyományos előfizetés-rendszergazdai szerepkörök

A következő klasszikus előfizetés-rendszergazdai szerepkörök nem kezelhetők Privileged Identity Managementban:

- Fiókadminisztrátor
- Szolgáltatás-rendszergazda
- Társadminisztrátor

A klasszikus előfizetés-rendszergazdai szerepkörökkel kapcsolatos további információkért lásd a [klasszikus előfizetés-rendszergazdai szerepköröket, az Azure RBAC-szerepköröket és az Azure ad rendszergazdai szerepköreit](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Mi a helyzet az Office 365 rendszergazdai szerepköreivel?

Az Azure AD-szerepkörök és a rendszergazdák portálon az összes Office 365 szerepkört támogatjuk, például az Exchange-rendszergazdát és a SharePoint-rendszergazdát, de nem támogatjuk az Exchange RBAC vagy a SharePoint RBAC belüli konkrét szerepköröket. További információ ezekről az Office 365-szolgáltatásokról: [office 365 rendszergazdai szerepkörök](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> A SharePoint-rendszergazdai szerepkörhöz, az eszköz rendszergazdai szerepköréhez és a Microsoft biztonsági és megfelelőségi központhoz hozzáférő szerepkörökhöz tartozó jogosult felhasználók a szerepkör aktiválása után akár néhány órával is előfordulhatnak késésben. Ezekkel a csapatokkal dolgozunk a problémák megoldásában.

## <a name="next-steps"></a>További lépések

- [Azure AD-szerepkörök kiosztása Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Azure-beli erőforrás-szerepkörök kiosztása Privileged Identity Management](pim-resource-roles-assign-roles.md)

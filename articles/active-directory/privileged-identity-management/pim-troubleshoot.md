---
title: Privileged Identity Management-Azure Active Directory problémáinak elhárítása | Microsoft Docs
description: Ismerje meg, hogyan lehet elhárítani a rendszerhibákat Azure AD Privileged Identity Management (PIM) szerepköreivel.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/18/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d2ba5806e23aa87e5d7787be300512444f95b25
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84742147"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>A Privileged Identity Management problémáinak elhárítása

Problémát tapasztal a Privileged Identity Management (PIM) Azure Active Directory (Azure AD) szolgáltatásban? A következő információk segíthetnek a dolgok újbóli működésében.

## <a name="access-to-azure-resources-denied"></a>Hozzáférés a megtagadott Azure-erőforrásokhoz

### <a name="problem"></a>Probléma

Az Azure-erőforrásokhoz tartozó aktív tulajdonosi vagy felhasználói hozzáférési rendszergazdaként megtekintheti az erőforrást Privileged Identity Managementon belül, de nem végezhet olyan műveleteket, mint például a jogosult hozzárendelés vagy a szerepkör-hozzárendelések listájának megtekintése az erőforrás-Áttekintés lapon. A műveletek bármelyike hitelesítési hibát eredményez.

### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha a PIM-szolgáltatásnév felhasználói hozzáférésének rendszergazdai szerepköre véletlenül el lett távolítva az előfizetésből. Ahhoz, hogy a Privileged Identity Management szolgáltatás hozzáférhessen az Azure-erőforrásokhoz, az MS-PIM egyszerű szolgáltatásnak mindig hozzá kell rendelnie a [felhasználói hozzáférés rendszergazdai szerepkörét](../../role-based-access-control/built-in-roles.md#user-access-administrator) az Azure-előfizetéshez.

### <a name="resolution"></a>Megoldás:

Rendelje hozzá a felhasználói hozzáférés rendszergazdai szerepkört az előfizetési szinten található Privileged Identity Management egyszerű névhez (MS – PIM). Ez a hozzárendelés lehetővé teszi, hogy a Privileged Identity Management szolgáltatás hozzáférjen az Azure-erőforrásokhoz. A szerepkör a követelményektől függően a felügyeleti csoport szintjén vagy az előfizetés szintjén is hozzárendelhető. További információ az egyszerű szolgáltatásokról: [alkalmazás társítása szerepkörhöz](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application).

## <a name="next-steps"></a>További lépések

- [A Privileged Identity Management használatára vonatkozó licencfeltételek](subscription-requirements.md)
- [Emelt szintű hozzáférés biztosítása Azure AD hibrid- és felhőkörnyezetekhez](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [A Privileged Identity Management üzembe helyezése](pim-deployment-plan.md)

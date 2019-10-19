---
title: Privileged Identity Management-Azure Active Directory problémáinak elhárítása | Microsoft Docs
description: Ismerje meg, hogyan lehet elhárítani a rendszerhibákat Azure AD Privileged Identity Management (PIM) szerepköreivel.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b7fc3508f7f672e277577f92218ff1860b676cb
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72559475"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>A Privileged Identity Management problémáinak elhárítása

Problémát tapasztal a Privileged Identity Management (PIM) Azure Active Directory (Azure AD) szolgáltatásban? A következő információk segíthetnek a dolgok újbóli működésében.

## <a name="access-to-azure-resources-denied"></a>Hozzáférés a megtagadott Azure-erőforrásokhoz

### <a name="problem"></a>Probléma

Ha az Azure AD-rendszergazdai szerepkörhöz szeretne hozzáférni, és nem fér hozzá az Azure-erőforrásokhoz Privileged Identity Management alatt, akkor hibaüzenet jelenik meg. Nem fér hozzá az Azure-erőforrásokhoz a Privileged Identity Management alatt, még akkor is, ha Ön globális rendszergazda, és az előfizetés tulajdonosa.

### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha a PIM-szolgáltatásnév felhasználói hozzáférésének rendszergazdai szerepköre véletlenül el lett távolítva az előfizetésből. Ahhoz, hogy a Privileged Identity Management szolgáltatás hozzáférhessen az Azure-erőforrásokhoz, az MS-PIM egyszerű szolgáltatásnak mindig hozzá kell rendelnie a [felhasználói hozzáférés rendszergazdai szerepkörét](../../role-based-access-control/built-in-roles.md#user-access-administrator) az Azure-előfizetéshez.

### <a name="resolution"></a>Felbontás

Rendelje hozzá a felhasználói hozzáférés rendszergazdai szerepkört az előfizetési szinten található Privileged Identity Management egyszerű névhez (MS – PIM). Ez a hozzárendelés lehetővé teszi, hogy a Privileged Identity Management szolgáltatás hozzáférjen az Azure-erőforrásokhoz. A szerepkör a követelményektől függően a felügyeleti csoport szintjén vagy az előfizetés szintjén is hozzárendelhető. További információ az egyszerű szolgáltatásokról: [alkalmazás társítása szerepkörhöz](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role).

## <a name="next-steps"></a>Következő lépések

- [A Privileged Identity Management használatára vonatkozó licencfeltételek](subscription-requirements.md)
- [Emelt szintű hozzáférés biztosítása Azure AD hibrid- és felhőkörnyezetekhez](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Privileged Identity Management üzembe helyezése](pim-deployment-plan.md)

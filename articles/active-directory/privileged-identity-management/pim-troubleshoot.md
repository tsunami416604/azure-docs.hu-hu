---
title: Kiemelt identitáskezelés – Azure Active Directory – probléma elhárítása | Microsoft dokumentumok
description: Ismerje meg, hogyan háríthatja el a szerepkörökkel kapcsolatos rendszerhibákat az Azure AD emelt szintű identitáskezelés (PIM) szolgáltatásában.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/18/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 474f2634e6f7ddc1840548c39ae86cb54c3bf08e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299669"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>A Kiemelt identitáskezelés szolgáltatással kapcsolatos probléma elhárítása

Problémája van a kiemelt identitáskezelés (PIM) az Azure Active Directoryban (Azure AD)? Az alábbi információk segíthetnek abban, hogy a dolgok újra működjenek.

## <a name="access-to-azure-resources-denied"></a>Hozzáférés megtagadva az Azure-erőforrásokhoz

### <a name="problem"></a>Probléma

Egy Azure-erőforrás aktív tulajdonosaként vagy felhasználói hozzáférés-rendszergazdájaként láthatja az erőforrást a Kiemelt identitáskezelés ben, de nem hajthat végre olyan műveleteket, mint például egy jogosult hozzárendelés végrehajtása vagy az erőforrásszerepkör-hozzárendelések listájának megtekintése áttekintő oldalon. Ezen műveletek bármelyike engedélyezési hibát eredményez.

### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha a PIM egyszerű szolgáltatáscsoport felhasználói hozzáférés-rendszergazdai szerepkörét véletlenül eltávolították az előfizetésből. Ahhoz, hogy a kiemelt identitáskezelési szolgáltatás képes legyen az Azure-erőforrások eléréséhez, az MS-PIM egyszerű szolgáltatásnak mindig hozzá kell rendelnie a [felhasználói hozzáférés-rendszergazdai szerepkört](../../role-based-access-control/built-in-roles.md#user-access-administrator) az Azure-előfizetéshez.

### <a name="resolution"></a>Megoldás:

Rendelje hozzá a Felhasználói hozzáférés-rendszergazda szerepkört a kiemelt identitáskezelés szolgáltatás egyszerű nevéhez (MS–PIM) az előfizetés szintjén. Ez a hozzárendelés lehetővé kell tennie a kiemelt identitáskezelési szolgáltatás az Azure-erőforrások eléréséhez. A szerepkör a követelményektől függően felügyeleti csoport vagy előfizetési szinten is hozzárendelhető. További információ a szolgáltatásegyszerű, [lásd: Alkalmazás hozzárendelése egy szerepkörhöz](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application).

## <a name="next-steps"></a>További lépések

- [A kiemelt identitáskezelés használatára vonatkozó licenckövetelmények](subscription-requirements.md)
- [Emelt szintű hozzáférés biztosítása Azure AD hibrid- és felhőkörnyezetekhez](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [A Privileged Identity Management üzembe helyezése](pim-deployment-plan.md)

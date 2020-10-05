---
title: Kiemelt Azure AD-csoportok kezelése a Privileged Identity Managementban (PIM) | Microsoft Docs
description: Kiemelt jogosultságú hozzáférési csoportok tagjainak és tulajdonosainak kezelése Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b6709d7589ebf4c82ec25c6b7a79fa31a305839
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "87506945"
---
# <a name="management-capabilities-for-privileged-access-azure-ad-groups-preview"></a>A Privileged Access Azure AD-csoportok felügyeleti képességei (előzetes verzió)

Privileged Identity Management (PIM) mostantól jogosultságot rendelhet az emelt szintű hozzáférési csoportok tagságához vagy tulajdonosához. Ettől az előzetes verziótól kezdődően Azure Active Directory (Azure AD) beépített szerepköröket rendelhet a felhőalapú csoportokhoz, és a PIM használatával kezelheti a csoporttagokat és a tulajdonosi jogosultságokat és az aktiválást. További információ a szerepkör-hozzárendelésre használható csoportokról Az Azure AD-ben: [felhőalapú csoportok használata a szerepkör-hozzárendelések kezeléséhez Azure Active Directory (előzetes verzió)](../users-groups-roles/roles-groups-concept.md).

## <a name="require-different-policies-for-each-role-assignable-group"></a>Különböző szabályzatok megkövetelése az egyes szerepkörökhöz hozzárendelhető csoportok számára

Néhány szervezet olyan eszközöket használ, mint például az Azure AD Business-to-Business (B2B) együttműködés, hogy meghívja a partnereit az Azure AD-szervezetnek vendégként. A Kiemelt szerepkörhöz tartozó összes hozzárendelésre vonatkozó egyszeri, igény szerinti házirend helyett létrehozhat két különböző privilegizált hozzáférési csoportot a saját házirendjével. A megbízható alkalmazottakra kevésbé szigorú követelményeket és szigorúbb követelményeket is kikényszerítheti, például a partnerek jóváhagyási munkafolyamatát, amikor aktiválást kérnek a hozzárendelt csoportba.

## <a name="activate-multiple-role-assignments-in-a-single-request"></a>Több szerepkör-hozzárendelés aktiválása egyetlen kérelemben

A Privileged Access groups előzetes verziójával a munkaterhelés-specifikus rendszergazdák gyors hozzáférést biztosíthatnak több szerepkörhöz egyetlen igény szerinti kérelem esetén. Előfordulhat például, hogy a 3. rétegbeli Office-rendszergazdáknak szükségük van az Exchange-rendszergazda, az Office-alkalmazások rendszergazdája, a csapatok rendszergazdája és a keresési rendszergazdai szerepkörök elérésére, hogy naponta alaposan megvizsgálják az incidenseket. A mai naptól négy egymást követő kérelmet kellene megkövetelni, amelyek egy kis időt vesznek igénybe. Ehelyett létrehozhat egy "3. rétegbeli Office-rendszergazdák" nevű szerepkör-hozzárendelési csoportot, hozzárendelheti a korábban említett négy szerepkörhöz (vagy bármely Azure AD beépített szerepkörhöz), és engedélyezheti a jogosultsági szintű hozzáféréshez a csoport tevékenység szakaszában. Ha engedélyezve van az emelt szintű hozzáférés, beállíthatja a csoport tagjainak igény szerinti beállításait, és jogosultként rendelheti hozzá a rendszergazdákat és a tulajdonosokat. Ha a rendszergazdák a csoportba emelnek, akkor mind a négy Azure AD-szerepkör tagjai lesznek.

## <a name="extend-and-renew-group-assignments"></a>Csoportos hozzárendelések kiterjesztése és megújítása

Az időhöz kötött tulajdonosi vagy tagsági hozzárendelések beállítása után az első kérdés, hogy mi történik, ha egy hozzárendelés lejár? Ebben az új verzióban két lehetőséget biztosítunk ehhez a forgatókönyvhöz:

- Kiterjesztés – ha egy szerepkör-hozzárendelés közel van a lejárathoz, a felhasználó a Privileged Identity Management használatával kérheti a szerepkör-hozzárendelés kiterjesztését
- Megújítás – ha egy szerepkör-hozzárendelés már lejárt, a felhasználó a Privileged Identity Management használatával kérheti a szerepkör-hozzárendelés megújítását

A felhasználó által kezdeményezett műveletekhez a globális rendszergazda vagy a Kiemelt szerepkörű rendszergazda jóváhagyása szükséges. A rendszergazdáknak többé nem kell megadniuk a lejáratok kezelésének üzleti tevékenységét. Csak megvárhatják a kiterjesztési vagy megújítási kérelmeket, és jóváhagyják őket, ha a kérelem érvényes.

## <a name="next-steps"></a>További lépések

- [Emelt szintű hozzáférési csoport tulajdonosának vagy tagjának kiosztása](groups-assign-member-owner.md)
- [Jogosultsági szintű hozzáférési csoport tagjainak és tulajdonosainak szóló aktiválási kérelmek jóváhagyása vagy elutasítása](groups-approval-workflow.md)

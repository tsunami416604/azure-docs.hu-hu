---
title: Mi az Privileged Identity Management? – Az azure Active Directory |} A Microsoft Docs
description: Áttekintést nyújt Azure AD Privileged Identity Management (PIM) szolgáltatásról.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5009310dd4ea4e99e1da2871085ee2d2d99a6eab
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804506"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Mi az az Azure AD Privileged Identity Management?

A Azure Active Directory (Azure AD) Privileged Identity Management (PIM) szolgáltatás lehetővé teszi a szervezet fontos erőforrásaihoz való hozzáférés kezelését, vezérlését és figyelését. Ebbe beletartozik az Azure AD, az Azure-erőforrások és más online Microsoft-szolgáltatások, például az Office 365 vagy a Microsoft Intune erőforrásaihoz való hozzáférés.

## <a name="why-should-i-use-pim"></a>Miért érdemes a PIM-t használni?

A szervezetek szeretnék csökkenteni azon személyek számát, akiknek hozzáférésük van az információkhoz vagy az erőforrásokhoz, mivel ez csökkenti annak az esélyét, hogy egy rosszindulatú szereplő hozzáfér az adott hozzáféréshez, vagy ha egy jogosult felhasználó véletlenül kihathat egy bizalmas erőforrásra. Azonban a felhasználóknak ettől függetlenül el kell végezniük bizonyos emelt szintű műveleteket az Azure AD-, Azure-, Office 365- vagy SaaS-alkalmazásokban. A szervezetek az Azure-erőforrásokhoz és az Azure AD-hez igénybe vehetik az igény szerinti (JIT) jogosultságú hozzáférést. Szükség van arra, hogy a felhasználók hogyan használják a rendszergazdai jogosultságokat. A PIM segít csökkenteni a túlzott, szükségtelen vagy nem használt hozzáférési jogosultságok kockázatát.

## <a name="what-can-i-do-with-pim"></a>Mire használhatom a PIM-t?

A PIM lényegében segítséget nyújt a WHO, mi, hol, hol és miért az Ön számára fontos erőforrások kezelésében. Íme a PIM legfontosabb funkciói:

- Az Azure AD-hez és **Az Azure-** erőforrásokhoz való, igény szerinti, Kiemelt jogosultságokkal rendelkező hozzáférés biztosítása
- Az erőforrásokhoz való **időhöz kötött** hozzáférés kiosztása a kezdő és a záró dátumok használatával
- **Jóváhagyás** megkövetelése a Kiemelt szerepkörök aktiválásához
- A **többtényezős hitelesítés** betartatása bármely szerepkör aktiválásához
- **Indoklás** használata a felhasználók aktiválásának megértéséhez
- **Értesítések** beszerzése a Kiemelt szerepkörök aktiválása esetén
- **Hozzáférési felülvizsgálatok** végrehajtása annak biztosításához, hogy a felhasználóknak továbbra is szükségük van
- **Naplózási előzmények** letöltése belső vagy külső naplózáshoz

## <a name="terminology"></a>Terminológia

A PIM és a hozzá tartozó dokumentáció jobb megismeréséhez tekintse át az alábbi feltételeket.

| Kifejezés vagy fogalom | Szerepkör-hozzárendelési kategória | Leírás |
| --- | --- | --- |
| támogatható | Type | Szerepkör-hozzárendelés, amely megköveteli, hogy a felhasználó egy vagy több műveletet hajtson végre a szerepkör használatához. Ha egy felhasználó jogosult a szerepkörre, az azt jelenti, hogy aktiválhatja a szerepkört, amikor kiemelt feladatokat kell elvégeznie. A hozzáférés nem különbözik a jogosult szerepkör-hozzárendeléssel rendelkező valakinek. Az egyetlen különbség, hogy néhány embernek nincs szüksége arra, hogy minden alkalommal hozzáférjen. |
| aktív | Type | Szerepkör-hozzárendelés, amely nem követeli meg, hogy a felhasználó bármilyen műveletet hajtson végre a szerepkör használatához. Az aktívként hozzárendelt felhasználók rendelkeznek a szerepkörhöz hozzárendelt jogosultságokkal. |
| aktiválás |  | Egy vagy több művelet végrehajtásának folyamata olyan szerepkör használatára, amelyhez a felhasználó jogosult. A műveletek tartalmazhatják a többtényezős hitelesítés (MFA) ellenőrzését, üzleti indoklást biztosítanak, vagy a kijelölt jóváhagyók jóváhagyását kérik. |
| rendelt | State | Aktív szerepkör-hozzárendeléssel rendelkező felhasználó. |
| aktivált | State | A jogosult szerepkör-hozzárendeléssel rendelkező felhasználók elvégezték a szerepkör aktiválásához szükséges műveleteket, és most aktívak.  Az aktiválás után a felhasználó használhatja a szerepkört egy előre konfigurált időszakra, mielőtt újra aktiválni kellene. |
| állandó jogosultság | Duration | Szerepkör-hozzárendelés, amelyben a felhasználó mindig jogosult a szerepkör aktiválásához. |
| állandó aktív | Duration | Szerepkör-hozzárendelés, amelyben a felhasználók minden művelet végrehajtása nélkül bármikor használhatják a szerepkört. |
| jogosultság lejárata | Duration | Szerepkör-hozzárendelés, amelyben a felhasználó jogosult a szerepkör aktiválására a megadott kezdési és befejezési dátumon belül. |
| aktív lejárat | Duration | Szerepkör-hozzárendelés, amelyben a felhasználó a megadott kezdési és befejezési dátumon belüli műveletek végrehajtása nélkül használhatja a szerepkört. |
| igény szerinti (JIT) hozzáférés |  | Olyan modell, amelyben a felhasználók ideiglenes engedélyeket kapnak a Kiemelt feladatok végrehajtásához, ami megakadályozza, hogy a rosszindulatú vagy jogosulatlan felhasználók hozzáférjenek az engedélyek lejárta után. A hozzáférés csak akkor adható meg, ha a felhasználóknak szüksége van rá. |
| a minimális jogosultsági szintű hozzáférés elve |  | Ajánlott biztonsági eljárás, amelyben minden felhasználó csak a végrehajtásra jogosult feladatok végrehajtásához szükséges minimális jogosultságokkal rendelkezik. Ez a gyakorlat lekicsinyíti a globális rendszergazdák számát, és bizonyos helyzetekben adott rendszergazdai szerepköröket használ. |

## <a name="what-does-pim-look-like"></a>Mire hasonlít a PIM?

A PIM beállítása után a bal oldali navigációs menüben megjelennek a **feladatok**, a **kezelés**és a **tevékenységek** beállításai. Rendszergazdaként választhatja az **Azure ad-szerepkörök** és az Azure- **erőforrások** szerepköreinek kezelését. Ha kiválasztja a felügyelni kívánt szerepkörök típusát, az adott szerepkör típusához hasonló beállítások jelennek meg.

![Képernyőfelvétel a PIM-ról a Azure Portal](./media/pim-configure/pim-overview.png)

## <a name="who-can-do-what-in-pim"></a>Ki teheti a PIM-t?

Ha Ön az első, aki a PIM-t használja, automatikusan hozzárendeli a [biztonsági rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) és a [Kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkört a címtárhoz.

Az Azure AD-szerepkörök esetében csak a Kiemelt szerepkörű rendszergazda szerepkörrel rendelkező felhasználók kezelhetik a PIM más rendszergazdáinak hozzárendeléseit. [Hozzáférést biztosíthat más rendszergazdáknak a PIM kezeléséhez](pim-how-to-give-access-to-pim.md). A globális rendszergazdák, a biztonsági rendszergazdák és a biztonsági olvasók megtekinthetik a PIM Azure AD szerepköreinek hozzárendeléseit.

Az Azure erőforrás-szerepkörök esetében csak az előfizetés rendszergazdája, az erőforrás tulajdonosa vagy az erőforrás felhasználói hozzáférés-kezelő rendszergazdája kezelheti a PIM más rendszergazdáinak hozzárendeléseit. Azok a felhasználók, akik Kiemelt szerepkörű rendszergazdák, biztonsági rendszergazdák vagy biztonsági olvasók, alapértelmezés szerint nem férnek hozzá a PIM-beli Azure-erőforrás-szerepkörök hozzárendeléseinek megtekintéséhez.

## <a name="scenarios"></a>Forgatókönyvek

A PIM a következő forgatókönyveket támogatja:

**Kiemelt szerepkörű rendszergazdaként a következőkre jogosult:**

- Jóváhagyás engedélyezése speciális szerepkörökhöz
- Kérelmek jóváhagyására jogosult felhasználók és/vagy csoportok meghatározása
- Speciális szerepkörökhöz tartozó kérelmek és jóváhagyások előzményeinek megtekintése

**Jóváhagyóként a következőket teheti:**

- Függőben lévő jóváhagyások (kérelmek) megtekintése
- Szerepkör-kiterjesztésre irányuló kérelmek jóváhagyása vagy visszautasítása (egyszerre egy és/vagy több)
- Indoklás fűzése a jóváhagyáshoz/elutasításhoz 

**Jogosult szerepkör-felhasználóként a következőket teheti:**

- Jóváhagyást igénylő szerepkör aktiválásának kérelme
- Az aktiválási kérelem állapotának megtekintése
- Az aktiválás jóváhagyása után feladatok végrehajtása az Azure AD-ben

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

További információ a felhasználói licencekről: a [PIM használatára vonatkozó licencfeltételek](subscription-requirements.md).

## <a name="next-steps"></a>További lépések

- [A PIM használatára vonatkozó licencfeltételek](subscription-requirements.md)
- [Emelt szintű hozzáférés biztosítása Azure AD hibrid- és felhőkörnyezetekhez](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [PIM üzembe helyezése](pim-deployment-plan.md)

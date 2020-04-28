---
title: Mi a kiemelt identitáskezelés? - Azure AD | Microsoft dokumentumok
description: Áttekintést nyújt az Azure AD kiemelt identitáskezelés (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 04/21/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfb0cc61b61328df86c27498a1007f2372fb9548
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867450"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Mi az az Azure AD Privileged Identity Management?

Az Azure Active Directory (Azure AD) privilegizált identitáskezelése (PIM) egy olyan szolgáltatás, amely lehetővé teszi a szervezet fontos erőforrásaihoz való hozzáférés kezelését, vezérlését és figyelését. Ezek az erőforrások az Azure AD, az Azure és más Microsoft Online Services, például az Office 365 vagy a Microsoft Intune erőforrásait tartalmazzák.

## <a name="reasons-to-use"></a>A használat okai

A szervezetek minimálisra szeretnék csökkenteni a biztonságos adatokhoz vagy erőforrásokhoz hozzáférő személyek számát, mivel ez csökkenti annak az esélyét, hogy egy rosszindulatú szereplő hozzáférjen a hozzáféréshez, vagy egy jogosult felhasználó véletlenül hatással van egy bizalmas erőforrásra. Azonban a felhasználóknak ettől függetlenül el kell végezniük bizonyos emelt szintű műveleteket az Azure AD-, Azure-, Office 365- vagy SaaS-alkalmazásokban. A vállalatoknak lehetőségük van arra, hogy igény szerinti (JIT) emelt szintű hozzáférést adjanak a felhasználóknak olyan Azure-erőforrásokhoz, mint az Azure AD. Szükség van a felügyelet, amit ezek a felhasználók csinálnak a rendszergazdai jogosultságokat.

## <a name="what-does-it-do"></a>Mire jó ez?

A Kiemelt identitáskezelés időalapú és jóváhagyási szerepkör-aktiválást biztosít a túlzott, szükségtelen vagy a megfelelő hozzáféréssel rendelkező erőforrásokhoz való hozzáférési engedélyek kockázatának csökkentése érdekében. Íme néhány a kiemelt identitáskezelés legfontosabb jellemzői:

- **Just-in-time** kiemelt hozzáférést biztosít az Azure AD és az Azure-erőforrások
- **Időhöz kötött** hozzáférés hozzárendelése erőforrásokhoz kezdő és záró dátummal
- **Jóváhagyás** szükséges a kiemelt szerepkörök aktiválásához
- **Többtényezős hitelesítés kényszerítése** bármely szerepkör aktiválásához
- Indoklás **justification** használata annak megértéséhez, hogy a felhasználók miért aktiválják
- **Értesítések betöltése** a kiemelt szerepkörök aktiválásáról
- **Hozzáférés-felülvizsgálatok lefolytatása** annak érdekében, hogy a felhasználóknak továbbra is szükségük legyen szerepkörökre
- Naplózási **előzmények** letöltése belső vagy külső naplózáshoz

## <a name="what-can-i-do-with-it"></a>Mit csináljak vele?

A Kiemelt identitáskezelés beállítása után a bal oldali navigációs menüben a **Feladatok,** **a Kezelés**és a **Tevékenység** beállítások láthatók. Rendszergazdaként az **Azure AD-szerepkörök** és az **Azure-erőforrás-szerepkörök** kezelése között választhat. Ha kiválasztja a kezelni kívánt szerepkörök típusát, az adott szerepkörtípushoz hasonló beállítások jelennek meg.

![Képernyőkép a kiemelt identitáskezelésről az Azure Portalon](./media/pim-configure/pim-quickstart.png)

## <a name="who-can-do-what"></a>Ki mire képes?

Az Azure AD-szerepkörök privilegizált identitáskezelés, csak a kiemelt szerepkör-rendszergazdai vagy globális rendszergazdai szerepkörben lévő felhasználó kezelheti a hozzárendelések más rendszergazdák számára. A [kiemelt identitáskezelés kezeléséhez más rendszergazdák](pim-how-to-give-access-to-pim.md)is hozzáférhetnek. A globális rendszergazdák, a biztonsági rendszergazdák, a globális olvasók és a biztonsági olvasók is megtekinthetik az Azure AD-szerepkörök hozzárendeléseit a kiemelt identitáskezelésben.

A kiemelt identitáskezelés ben az Azure-erőforrás-szerepkörök esetében csak egy előfizetés-rendszergazda, egy erőforrás-tulajdonos vagy egy erőforrás-felhasználói hozzáférés-rendszergazda kezelheti a más rendszergazdák hozzárendeléseit. A kiemelt szerepkör-rendszergazdák, biztonsági rendszergazdák vagy biztonsági olvasók alapértelmezés szerint nem rendelkeznek hozzáféréssel az Azure-erőforrás-szerepkörök hozzárendeléseinek megtekintéséhez a kiemelt identitáskezelésben.

## <a name="scenarios"></a>Forgatókönyvek

A Privileged Identity Management a következő forgatókönyveket támogatja:

### <a name="privileged-role-administrator-permissions"></a>Kiemelt szerepkör-rendszergazdai engedélyek

- Jóváhagyás engedélyezése speciális szerepkörökhöz
- Jóváhagyó felhasználók vagy csoportok megadása a kérelmek jóváhagyásához
- Speciális szerepkörökhöz tartozó kérelmek és jóváhagyások előzményeinek megtekintése

### <a name="approver-permissions"></a>Jóváhagyó engedélyei

- Függőben lévő jóváhagyások (kérelmek) megtekintése
- Szerepkör-emelési kérelmek jóváhagyása vagy elutasítása (egyszeri és tömeges)
- Jóváhagyásom vagy elutasításom indoklása

### <a name="eligible-role-user-permissions"></a>Jogosult szerepkör felhasználói engedélyei

- Jóváhagyást igénylő szerepkör aktiválásának kérelme
- Az aktiválási kérelem állapotának megtekintése
- Az aktiválás jóváhagyása után feladatok végrehajtása az Azure AD-ben

## <a name="terminology"></a>Terminológia

A kiemelt identitáskezelés és a dokumentáció jobb megértéséhez tekintse át az alábbi feltételeket.

| Kifejezés vagy fogalom | Szerepkör-hozzárendelési kategória | Leírás |
| --- | --- | --- |
| Támogatható | Típus | Olyan szerepkör-hozzárendelés, amelya szerepkör használatához egy vagy több műveletet kell végrehajtania. Ha egy felhasználó jogosulttá vált egy szerepkörre, ez azt jelenti, hogy aktiválhatja a szerepkört, amikor kiemelt feladatokat kell végrehajtania. Nincs különbség a hozzáférés adott valaki egy állandó versus jogosult szerepkör-hozzárendelés. Az egyetlen különbség az, hogy néhány ember nem kell, hogy a hozzáférés minden alkalommal. |
| Aktív | Típus | Olyan szerepkör-hozzárendelés, amely nem igényel felhasználótól semmilyen műveletet a szerepkör használatához. Az aktívként hozzárendelt felhasználók rendelkeznek a szerepkörhöz rendelt jogosultságokkal. |
| aktiválás |  | Egy vagy több művelet végrehajtása egy olyan szerepkör használatára, amelyre a felhasználó jogosult. A műveletek közé tartozhat a többtényezős hitelesítés (MFA) ellenőrzése, az üzleti indoklás megadása vagy a kijelölt jóváhagyók jóváhagyásának kérése. |
| Hozzárendelt | Állapot | Aktív szerepkör-hozzárendeléssel rendelkező felhasználó. |
| Aktivált | Állapot | Olyan felhasználó, aki jogosult szerepkör-hozzárendeléssel rendelkezik, végrehajtotta a szerepkör aktiválásához szükséges műveleteket, és most aktív.  Aktiválásután a felhasználó használhatja a szerepkört egy előre konfigurált időszak-of-time, mielőtt újra aktiválnia kell. |
| állandó támogatható | Időtartam | Szerepkör-hozzárendelés, ahol a felhasználó mindig jogosult a szerepkör aktiválására. |
| állandó aktív | Időtartam | Szerepkör-hozzárendelés, ahol a felhasználó mindig használhatja a szerepkört anélkül, hogy bármilyen műveletet. |
| lejár jogosult | Időtartam | Olyan szerepkör-hozzárendelés, amelyben a felhasználó jogosult a szerepkör aktiválására egy megadott kezdési és befejezési dátumon belül. |
| lejárat aktív | Időtartam | Olyan szerepkör-hozzárendelés, amelyben a felhasználó anélkül használhatja a szerepkört, hogy bármilyen műveletet végrehajtana egy megadott kezdési és befejezési dátumon belül. |
| just-in-time (JIT) hozzáférés |  | Olyan modell, amelyben a felhasználók ideiglenes engedélyeket kapnak a kiemelt feladatok végrehajtására, ami megakadályozza, hogy rosszindulatú vagy jogosulatlan felhasználók hozzáférjenek az engedélyek lejárta után. A hozzáférés csak akkor érhető el, ha a felhasználóknak szükségük van rá. |
| a legkisebb jogosultsághoz való hozzáférés elve |  | Ajánlott biztonsági gyakorlat, amelyben minden felhasználó csak a feladatok elvégzéséhez szükséges minimális jogosultságokkal rendelkezik. Ez a gyakorlat minimálisra csökkenti a globális rendszergazdák számát, és ehelyett bizonyos esetekben adott rendszergazdai szerepköröket használ. |

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

A felhasználók licenceiről a [Kiemelt identitáskezelés licenckövetelményei](subscription-requirements.md)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

- [A kiemelt identitáskezelés használatára vonatkozó licenckövetelmények](subscription-requirements.md)
- [Emelt szintű hozzáférés biztosítása Azure AD hibrid- és felhőkörnyezetekhez](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [A Privileged Identity Management üzembe helyezése](pim-deployment-plan.md)

---
title: E-mail értesítések a PIM szolgáltatásban – Azure Active Directory | Microsoft dokumentumok
description: Az Azure AD kiemelt identitáskezelés (PIM) e-mail-értesítéseinek ismertetése.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/21/2020
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 201abd24bc4056337f1ffecd2dabd002ae352c74
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866419"
---
# <a name="email-notifications-in-pim"></a>E-mail értesítések a PIM-ben

A kiemelt identitáskezelés (PIM) lehetővé teszi, hogy az Azure Active Directory (Azure AD) szervezetében, például amikor egy szerepkör van hozzárendelve vagy aktiválva, tudni adhatja, ha fontos események történnek az Azure Active Directory (Azure AD) szervezetében. A Kiemelt identitáskezelés e-mailes értesítések küldésével tájékoztatja Önt és a többi résztvevőt. Ezek az e-mailek tartalmazhatnak a releváns feladatokra mutató hivatkozásokat is, például egy szerepkör aktiválását vagy megújítását. Ez a cikk leírja, hogy néznek ki ezek az e-mailek, mikor küldik el őket, és ki kapja meg őket.

## <a name="sender-email-address-and-subject-line"></a>Feladó e-mail címe és tárgya

A Kiemelt identitáskezelésből az Azure AD-hez és az Azure-erőforrás-szerepkörökhöz küldött e-mailek a következő feladói e-mail címmel rendelkeznek:

- E-mail cím: **azure-noreply\@microsoft.com**
- Megjelenítendő név: Microsoft Azure

Ezek az e-mailek tartalmaznak egy **PIM** előtagot a tárgysorban. Például:

- PIM: Alain Charon véglegesen a Biztonságimentés-olvasó szerepkört kapta

## <a name="notifications-for-azure-ad-roles"></a>Értesítések az Azure AD-szerepkörökhöz

A kiemelt identitáskezelés e-maileket küld, ha az Azure AD-szerepkörökhöz a következő események fordulnak elő:

- Ha egy kiemelt szerepkör aktiválása jóváhagyásra vár
- Ha egy kiemelt szerepkör-aktiválási kérelem befejeződött
- Ha az Azure AD kiemelt identitáskezelés engedélyezve van

Az, hogy ki kapja ezeket az e-maileket az Azure AD-szerepkörökhöz, a szerepkörtől, az eseménytől és az értesítések beállításátótól függ:

| Felhasználó | A szerepkör aktiválása jóváhagyásra vár | A szerepkör aktiválási kérelme befejeződött | A PIM engedélyezve van |
| --- | --- | --- | --- |
| Kiemelt szerepkör-rendszergazda</br>(Aktiválva/Jogosult) | Igen</br>(csak akkor, ha nincs megadva kifejezett jóváhagyó) | Igen* | Igen |
| Biztonsági rendszergazda</br>(Aktiválva/Jogosult) | Nem | Igen* | Igen |
| Globális rendszergazda</br>(Aktiválva/Jogosult) | Nem | Igen* | Igen |

\*Ha az [ **Értesítések** beállítás](pim-how-to-change-default-settings.md#notifications) **engedélyezése**beállításra van állítva.

Az alábbi képen egy példa e-mailt, amely akkor kerül elküldésre, amikor egy felhasználó aktiválja az Azure AD-szerepkört a kitalált Contoso szervezet.

![Új kiemelt identitáskezelési e-mail az Azure AD-szerepkörökhöz](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Heti kiemelt identitáskezelés kivonata az Azure AD-szerepkörökben

Az Azure AD-szerepkörök heti kiemelt identitáskezelés összefoglaló e-mail-címét a rendszer elküldi a kiemelt szerepkör-rendszergazdáknak, a biztonsági rendszergazdáknak és a globális rendszergazdáknak, akik engedélyezték a kiemelt identitáskezelést. Ez a heti e-mail pillanatképet biztosít a kiemelt identitáskezelési tevékenységekről a hétre, valamint a kiemelt szerepkör-hozzárendelésekről. Csak a nyilvános felhőben lévő bérlők számára érhető el. Íme egy példa e-mail:

![Heti kiemelt identitáskezelés kivonata az Azure AD-szerepkörökben](./media/pim-email-notifications/email-directory-weekly.png)

Az e-mail négy csempét tartalmaz:

| Csempe | Leírás |
| --- | --- |
| **Aktivált felhasználók** | Azon alkalmak száma, amikor a felhasználók aktiválták a jogosult szerepkört a bérlőn belül. |
| **A felhasználók állandóvá tették** | Azon alkalmak száma, amikor a jogosult hozzárendeléssel rendelkező felhasználók állandóvá válik. |
| **Szerepkör-hozzárendelések a kiemelt identitáskezelésben** | Azon alkalmak száma, amikor a felhasználók jogosultsággal rendelkező identitáskezelésen belül kapnak jogosult szerepkört. |
| **Pim-en kívüli szerepkör-hozzárendelések** | Azon alkalmak száma, amikor a felhasználók állandó szerepkört kapnak a kiemelt identitáskezelésen kívül (az Azure AD-n belül). |

A **felső szerepkörök áttekintése** szakasz felsorolja az első öt szerepkör a bérlőben az egyes szerepkörök állandó és jogosult rendszergazdák teljes száma alapján. A **Művelet eleévével** hivatkozás megnyitja a [PIM varázslót,](pim-security-wizard.md) ahol az állandó rendszergazdákat kötegekben jogosult rendszergazdákká alakíthatja.

## <a name="email-timing-for-activation-approvals"></a>Az aktiválás jóváhagyásának e-mailes időzítése

Amikor a felhasználók aktiválják a szerepkörüket, és a szerepkör-beállítás jóváhagyást igényel, a jóváhagyók minden jóváhagyáshoz három e-mailt kapnak:

- A felhasználó aktiválási kérelmének jóváhagyására vagy elutasítására irányuló kérelem (amelyet a kérelem-jóváhagyási motor küldött)
- A felhasználó kérelmét jóváhagyták (a kérelem-jóváhagyási motor küldte)
- A felhasználó szerepköre aktiválva van (a kiemelt identitáskezelés küldi)

A kérelem-jóváhagyási motor által küldött első két e-mail késhet. Jelenleg az e-mailek 90% -a 3-10 percet vesz igénybe, de az 1% -os ügyfelek számára sokkal hosszabb, legfeljebb tizenöt perc lehet.

Ha egy jóváhagyási kérelmet az első e-mail elküldése előtt jóváhagynak az Azure Portalon, az első e-mail már nem aktiválódik, és más jóváhagyók nem kap értesítést e-mailben a jóváhagyási kérelemről. Úgy tűnhet, mintha nem kapnának e-mailt, de ez a várt viselkedés.

## <a name="pim-emails-for-azure-resource-roles"></a>PIM-e-mailek az Azure-erőforrás-szerepkörökhöz

A kiemelt identitáskezelés e-maileket küld a tulajdonosoknak és a felhasználói hozzáférés rendszergazdáinak, ha az Azure-erőforrás-szerepkörökhöz a következő események fordulnak elő:

- Ha egy szerepkör-hozzárendelés jóváhagyásra vár
- Szerepkör hozzárendelésekénte
- Ha egy szerepkör hamarosan lejár
- Ha egy szerepkör meghosszabbítható
- Amikor egy szerepkört egy végfelhasználó újít meg
- Szerepkör-aktiválási kérelem befejezésekor

A kiemelt identitáskezelés e-maileket küld a végfelhasználóknak, ha az Azure-erőforrás-szerepkörökhöz a következő események fordulnak elő:

- Ha egy szerepkör hozzá van rendelve a felhasználóhoz
- Ha egy felhasználó szerepköre lejárt
- Ha egy felhasználó szerepköre ki van terjesztve
- Amikor egy felhasználó szerepkör-aktiválási kérelme befejeződött

Az alábbi képen egy példa e-mailt, amely akkor kerül elküldésre, amikor egy felhasználó hoz egy Azure-erőforrás szerepkört a kitalált Contoso szervezet.

![Új kiemelt identitáskezelési e-mail az Azure-erőforrás-szerepkörökhöz](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>További lépések

- [Az Azure AD szerepkör-beállításainak konfigurálása a kiemelt identitáskezelésben](pim-how-to-change-default-settings.md)
- [Azure AD-szerepkörök kéréseinek jóváhagyása vagy megtagadása a kiemelt identitáskezelésben](azure-ad-pim-approval-workflow.md)

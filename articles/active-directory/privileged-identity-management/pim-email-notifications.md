---
title: E-mail-értesítések a PIM-Azure Active Directoryban | Microsoft Docs
description: A Azure AD Privileged Identity Management (PIM) e-mail értesítéseinek ismertetése.
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
ms.date: 01/05/2019
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0db4828ef7a6d3392ce53b2f2f95b0f7f014d65
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804544"
---
# <a name="email-notifications-in-pim"></a>E-mail-értesítések a PIM-ben

A Azure Active Directory (Azure AD) Privileged Identity Management (PIM) segítségével megtudhatja, hogy mikor fordulnak elő fontos események, például ha egy szerepkör hozzárendelése vagy aktiválása történik. A PIM értesíti Önt és a többi résztvevő e-mailes értesítéseit. Ezek az e-mailek a kapcsolódó feladatokra mutató hivatkozásokat is tartalmazhatnak, például aktiválják vagy megújítják a szerepkört. Ez a cikk leírja, hogy ezek az e-mailek hogyan jelennek meg, amikor elküldik őket, és kik kapják meg őket.

## <a name="sender-email-address-and-subject-line"></a>Feladó e-mail-címe és tárgya

A PIM által küldött e-mailek az Azure AD-hez és az Azure-erőforrás szerepköreihez a következő feladói e-mail-cím tartozik:

- E-mail cím: **Azure – nem\@válaszoló Microsoft.com**
- Megjelenítendő név: Microsoft Azure

Ezek az e-mailek tartalmazzák a **PIM** -előtagot a Tárgy sorban. Például:

- PIM: Alain Charon véglegesen hozzárendelte a biztonsági mentési olvasó szerepkört

## <a name="pim-emails-for-azure-ad-roles"></a>PIM-e-mailek az Azure AD-szerepkörökhöz

A PIM e-maileket küld, ha az alábbi események történnek az Azure AD-szerepköröknél:

- Ha a Kiemelt szerepkör aktiválása jóváhagyásra vár
- Rendszerjogosultságú szerepkör-aktiválási kérelem befejezésekor
- Ha az Azure AD PIM engedélyezve van

Kik kapják meg ezeket az e-maileket az Azure AD-szerepkörökhöz a szerepkörtől, az eseménytől és az értesítések beállításától függően:

| Felhasználó | A szerepkör aktiválása jóváhagyásra vár | Szerepkör-aktiválási kérelem befejeződött | A PIM engedélyezve van |
| --- | --- | --- | --- |
| Kiemelt szerepkörű rendszergazda</br>(Aktivált/jogosult) | Igen</br>(csak akkor, ha nincs megadva explicit jóváhagyó) | Igen* | Igen |
| Biztonsági rendszergazda</br>(Aktivált/jogosult) | Nem | Igen* | Igen |
| Globális rendszergazda</br>(Aktivált/jogosult) | Nem | Igen* | Igen |

\*Ha az [ **értesítések** beállítás](pim-how-to-change-default-settings.md#notifications) **engedélyezve**értékre van állítva.

Az alábbi példa egy olyan e-mailt mutat be, amelyet a rendszer akkor küld, amikor egy felhasználó aktivál egy Azure AD-szerepkört a kitalált contoso-szervezet számára.

![Új PIM e-mail az Azure AD-szerepkörökhöz](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-pim-digest-email-for-azure-ad-roles"></a>Heti PIM kivonatoló e-mail az Azure AD-szerepkörökhöz

Az Azure AD szerepköreinek heti PIM összefoglaló e-mail-címe a Kiemelt szerepkörű rendszergazdák, a biztonsági rendszergazdák és a PIM-t engedélyező globális rendszergazdák számára lesz elküldve. Ez a heti e-mail a PIM-tevékenységek pillanatképét tartalmazza a héten, valamint a Kiemelt szerepkör-hozzárendeléseket. Csak a nyilvános felhőben lévő bérlők számára érhető el. Íme egy példa e-mailben:

![Heti PIM kivonatoló e-mail az Azure AD-szerepkörökhöz](./media/pim-email-notifications/email-directory-weekly.png)

Az e-mail négy csempét tartalmaz:

| Csempe | Leírás |
| --- | --- |
| **Aktivált felhasználók** | Az a szám, ahányszor a felhasználók aktiválták a jogosult szerepkört a bérlőn belül. |
| **Felhasználó által állandó** | A jogosult hozzárendeléssel rendelkező felhasználók száma állandó. |
| **Szerepkör-hozzárendelések a PIM-ben** | Azon alkalmak száma, amikor a felhasználók egy jogosult szerepkörhöz vannak rendelve a PIM-on belül. |
| **A PIM-n kívüli szerepkör-hozzárendelések** | Azon alkalmak száma, amikor a felhasználók állandó szerepkört kapnak a PIM-n kívül (az Azure AD-n belül). |

A **legfelső szintű szerepkörök áttekintése** szakasz a bérlő első öt szerepkörét listázza az egyes szerepkörökhöz tartozó állandó és jogosult rendszergazdák teljes száma alapján. A **művelet elvégzése** hivatkozás megnyitja a [PIM varázslót](pim-security-wizard.md) , ahol az állandó rendszergazdák a batchs szolgáltatásban jogosult rendszergazdákra konvertálhatók.

## <a name="pim-emails-for-azure-resource-roles"></a>PIM-e-mailek az Azure-erőforrás szerepköreihez

A PIM e-maileket küld a tulajdonosoknak és a felhasználói hozzáférés rendszergazdáinak, ha az alábbi események történnek az Azure-erőforrás szerepköreiben:

- Függőben lévő szerepkör-hozzárendelés jóváhagyása
- Szerepkör hozzárendelésekor
- Ha egy szerepkör hamarosan lejár
- Ha egy szerepkör kiterjeszthető
- Amikor egy végfelhasználó megújította a szerepkört
- Szerepkör-aktiválási kérelem befejezésekor

A PIM e-maileket küld a végfelhasználóknak, ha az alábbi események történnek az Azure-erőforrás szerepköreiben:

- Szerepkör hozzárendelése a felhasználóhoz
- Ha a felhasználó szerepköre lejárt
- Ha a felhasználó szerepköre ki van bővítve
- A felhasználó szerepkör-aktiválási kérelmének befejezésekor

Az alábbi példa egy olyan e-mailt mutat be, amelyet a rendszer akkor küld, amikor egy felhasználó hozzárendel egy Azure-erőforrás-szerepkört a kitalált contoso-szervezethez.

![Új PIM e-mail az Azure-erőforrás szerepköreihez](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>További lépések

- [Az Azure AD szerepkör beállításainak konfigurálása a PIM-ben](pim-how-to-change-default-settings.md)
- [Azure AD-szerepkörökre vonatkozó kérelmek jóváhagyása vagy elutasítása a PIM-ben](azure-ad-pim-approval-workflow.md)

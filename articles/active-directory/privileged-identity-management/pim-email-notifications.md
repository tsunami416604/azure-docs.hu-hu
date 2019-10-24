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
ms.openlocfilehash: ee5f2edbae28276f8485ae774a5b1c52e1af2fd1
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756387"
---
# <a name="email-notifications-in-pim"></a>E-mail-értesítések a PIM-ben

Privileged Identity Management (PIM) segítségével megtudhatja, hogy a Azure Active Directory (Azure AD) szervezeten belül milyen fontos események történnek, például ha egy szerepkör hozzárendelése vagy aktiválása történik. A Privileged Identity Management értesíti Önt és a többi résztvevő e-mailes értesítéseit. Ezek az e-mailek a kapcsolódó feladatokra mutató hivatkozásokat is tartalmazhatnak, például aktiválják vagy megújítják a szerepkört. Ez a cikk leírja, hogy ezek az e-mailek hogyan jelennek meg, amikor elküldik őket, és kik kapják meg őket.

## <a name="sender-email-address-and-subject-line"></a>Feladó e-mail-címe és tárgya

Az Azure AD és az Azure Resource szerepköreinek Privileged Identity Management küldött e-mailek a következő feladói e-mail-címmel rendelkeznek:

- E-mail cím: **Azure – nem válaszoló \@microsoft. com**
- Megjelenítendő név: Microsoft Azure

Ezek az e-mailek tartalmazzák a **PIM** -előtagot a Tárgy sorban. Például:

- PIM: Alain Charon véglegesen hozzárendelte a biztonsági mentési olvasó szerepkört

## <a name="notifications-for-azure-ad-roles"></a>Azure AD-szerepkörökkel kapcsolatos értesítések

Privileged Identity Management e-maileket küld, ha az alábbi események történnek az Azure AD-szerepköröknél:

- Ha a Kiemelt szerepkör aktiválása jóváhagyásra vár
- Rendszerjogosultságú szerepkör-aktiválási kérelem befejezésekor
- Ha Azure AD Privileged Identity Management engedélyezve van

Kik kapják meg ezeket az e-maileket az Azure AD-szerepkörökhöz a szerepkörtől, az eseménytől és az értesítések beállításától függően:

| Felhasználó | A szerepkör aktiválása jóváhagyásra vár | Szerepkör-aktiválási kérelem befejeződött | A PIM engedélyezve van |
| --- | --- | --- | --- |
| Kiemelt szerepkörű rendszergazda</br>(Aktivált/jogosult) | Igen</br>(csak akkor, ha nincs megadva explicit jóváhagyó) | Igen* | Igen |
| Biztonsági rendszergazda</br>(Aktivált/jogosult) | Nem | Igen* | Igen |
| Globális rendszergazda</br>(Aktivált/jogosult) | Nem | Igen* | Igen |

\*, ha az [ **értesítések** beállítás](pim-how-to-change-default-settings.md#notifications) **engedélyezve**értékre van állítva.

Az alábbi példa egy olyan e-mailt mutat be, amelyet a rendszer akkor küld, amikor egy felhasználó aktivál egy Azure AD-szerepkört a kitalált contoso-szervezet számára.

![Új Privileged Identity Management e-mail az Azure AD-szerepkörökhöz](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Heti Privileged Identity Management kivonatoló e-mail az Azure AD-szerepkörökhöz

Az Azure AD-szerepkörökhöz hetente Privileged Identity Management összefoglaló e-mailt küldünk a Kiemelt szerepkörű rendszergazdák, a biztonsági rendszergazdák és a Privileged Identity Management engedélyező globális rendszergazdák számára. Ez a heti e-mail egy pillanatképet biztosít a héten Privileged Identity Management tevékenységekről és a Kiemelt szerepkör-hozzárendelésekről. Csak a nyilvános felhőben lévő bérlők számára érhető el. Íme egy példa e-mailben:

![Heti Privileged Identity Management kivonatoló e-mail az Azure AD-szerepkörökhöz](./media/pim-email-notifications/email-directory-weekly.png)

Az e-mail négy csempét tartalmaz:

| Csempe | Leírás |
| --- | --- |
| **Aktivált felhasználók** | Az a szám, ahányszor a felhasználók aktiválták a jogosult szerepkört a bérlőn belül. |
| **Felhasználó által állandó** | A jogosult hozzárendeléssel rendelkező felhasználók száma állandó. |
| **Szerepkör-hozzárendelések Privileged Identity Management** | Azon alkalmak száma, amikor a felhasználók jogosult szerepkört kapnak Privileged Identity Managementon belül. |
| **A PIM-n kívüli szerepkör-hozzárendelések** | Azon alkalmak száma, amikor a felhasználók a Privileged Identity Managementon kívül (az Azure AD-n belül) állandó szerepkört kapnak. |

A **legfelső szintű szerepkörök áttekintése** szakasz a bérlő első öt szerepkörét listázza az egyes szerepkörökhöz tartozó állandó és jogosult rendszergazdák teljes száma alapján. A **művelet elvégzése** hivatkozás megnyitja a [PIM varázslót](pim-security-wizard.md) , ahol az állandó rendszergazdák a batchs szolgáltatásban jogosult rendszergazdákra konvertálhatók.

## <a name="pim-emails-for-azure-resource-roles"></a>PIM-e-mailek az Azure-erőforrás szerepköreihez

Privileged Identity Management e-maileket küld a tulajdonosoknak és a felhasználói hozzáférés rendszergazdáinak, ha az alábbi események történnek az Azure-beli erőforrás-szerepköröknél:

- Függőben lévő szerepkör-hozzárendelés jóváhagyása
- Szerepkör hozzárendelésekor
- Ha egy szerepkör hamarosan lejár
- Ha egy szerepkör kiterjeszthető
- Amikor egy végfelhasználó megújította a szerepkört
- Szerepkör-aktiválási kérelem befejezésekor

Privileged Identity Management e-maileket küld a végfelhasználóknak, ha az alábbi események történnek az Azure-erőforrás szerepköreiben:

- Szerepkör hozzárendelése a felhasználóhoz
- Ha a felhasználó szerepköre lejárt
- Ha a felhasználó szerepköre ki van bővítve
- A felhasználó szerepkör-aktiválási kérelmének befejezésekor

Az alábbi példa egy olyan e-mailt mutat be, amelyet a rendszer akkor küld, amikor egy felhasználó hozzárendel egy Azure-erőforrás-szerepkört a kitalált contoso-szervezethez.

![Új Privileged Identity Management e-mail az Azure-erőforrás szerepköreihez](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Következő lépések

- [Azure AD-szerepkör beállításainak konfigurálása Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Azure AD-szerepkörökre vonatkozó kérelmek jóváhagyása vagy elutasítása Privileged Identity Management](azure-ad-pim-approval-workflow.md)

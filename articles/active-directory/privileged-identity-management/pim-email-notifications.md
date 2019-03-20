---
title: E-mail-értesítések a PIM – Azure |} A Microsoft Docs
description: Az Azure AD Privileged Identity Management (PIM) e-mail-értesítések ismerteti.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/05/2019
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f2e5674413cb4057187b7573fde0bcd8899caab
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57905171"
---
# <a name="email-notifications-in-pim"></a>A PIM e-mail-értesítések

Az Azure AD Privileged Identity Management (PIM) lehetővé teszi, hogy amikor fontos események történnek, például amikor egy szerepkör hozzárendelése vagy aktiválása. A PIM követi Önt folyamatosan értesíteni, és egyéb résztvevői e-mail-értesítések küldésével. E-mailek tartalmazhat kapcsolódó feladatok, az ilyen aktiválása vagy szerepkör megújítása mutató hivatkozásokat is. Ez a cikk bemutatja, hogyan meg e-mailek, mikor kapják, és ki kapja.

## <a name="sender-email-address-and-subject-line"></a>Feladó e-mail címét és a tulajdonos sor

Mind az Azure ad PIM által küldött e-mailek és az Azure erőforrás-szerepkörökkel rendelkezik a következő feladó e-mail címe:

- E-mail-cím: **azure-noreply\@microsoft.com**
- Megjelenített név: Microsoft Azure

E-mailek tartalmaznak egy **PIM** a tárgysorban előtag. Például:

- PIM: Alain Charon tartósan hozzá volt rendelve a biztonsági olvasói szerepköre

## <a name="pim-emails-for-azure-ad-roles"></a>A PIM e-mailek, az Azure AD-szerepkörök

A PIM e-maileket küld, ha az alábbi események történnek az Azure AD-szerepkörök:

- Ha egy rendszerjogosultságú szerepkör aktiválása a jóváhagyásra van
- Ha egy rendszerjogosultságú szerepkör-aktiválási kérelem befejeződött
- Ha engedélyezve van-e az Azure AD PIM-ben

Ki kapja, e-mailek, az Azure AD-szerepkörökhöz tartozó függ a szerepkört, az esemény és az értesítések beállítása:

| Felhasználó | Szerepkör-aktiválás jóváhagyása függőben van | Szerepkör-aktiválási kérelem befejeződött | A PIM engedélyezve van |
| --- | --- | --- | --- |
| Kiemelt szerepkörű rendszergazda</br>(Activated/Eligible) | Igen</br>(csak akkor, ha nincs explicit jóváhagyók vannak megadva) | Igen* | Igen |
| Biztonsági rendszergazda</br>(Activated/Eligible) | Nem | Igen* | Igen |
| Globális rendszergazda</br>(Activated/Eligible) | Nem | Igen* | Igen |

\* Ha a [ **értesítések** beállítás](pim-how-to-change-default-settings.md#notifications) értékre van állítva **engedélyezése**.

Az alábbiakban látható egy például szolgáló e-mail érkezik, amikor egy felhasználó aktiválja a fiktív Contoso szervezet Azure AD-szerepkörhöz.

![A PIM-új e-mail cím az Azure AD-szerepkörök](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-pim-digest-email-for-azure-ad-roles"></a>Az Azure AD-szerepkörök heti PIM összefoglaló e-mail

Az Azure AD-szerepkörökhöz tartozó heti PIM összefoglaló e-mail küldése a kiemelt szerepkörű rendszergazdák, biztonsági rendszergazdák és a globális rendszergazdák, amelyeken engedélyezve a PIM. A heti e-mailt biztosít a PIM-tevékenységet pillanatképét a hét, valamint a kiemelt szerepkörök hozzárendeléseit. A nyilvános felhő bérlők csak érhető el. Íme egy példa e-mailt:

![Az Azure AD-szerepkörök heti PIM összefoglaló e-mail](./media/pim-email-notifications/email-directory-weekly.png)

Az e-mail tartalmazza a négy csempére:

| Csempe | Leírás |
| --- | --- |
| **Aktív felhasználók** | Ennyi alkalommal felhasználók aktiválása a jogosult szerepkör a bérlőn belül. |
| **Véglegessé felhasználók** | Jogosult hozzárendelés rendelkező felhasználók véglegessé hányszor. |
| **A PIM szerepkör-hozzárendelések** | Felhasználók vannak hozzárendelve a PIM belül egy jogosult szerepkör hányszor. |
| **PIM-en kívül a szerepkör-hozzárendelések** | Felhasználók vannak hozzárendelve egy állandó szerepkör (Azure AD) a belül PIM-en kívül hányszor. |

A **a felső szerepkörök áttekintése** szakasz sorolja fel az első öt szerepkörök a bérlő minden egyes szerepkörhöz tartozó állandó és jogosult rendszergazdák teljes száma alapján. A **semmit** hivatkozás megnyílik az [PIM varázsló](pim-security-wizard.md) ahol átválthat kötegekben jogosult rendszergazdák állandó rendszergazdák.

## <a name="pim-emails-for-azure-resource-roles"></a>A PIM e-mailek, az Azure-erőforrásszerepkörök

A PIM küld e-mailek tulajdonosai és rendszergazdái a felhasználói hozzáférést, ha az alábbi események történnek az Azure-erőforrások szerepköreihez tartozó:

- Jóváhagyásra váró szerepkör-hozzárendelés esetén
- Ha a szerepkör hozzá van rendelve
- Ha a szerepkör az hamarosan lejár
- Ha a szerepkör jogosult bővítése az
- Ha egy szerepkör megújítása a felhasználó által
- Ha egy szerepkör-aktiválási kérelem befejeződött

A PIM végfelhasználók számára küldhet e-maileket, amikor az alábbi események történnek az Azure-erőforrások szerepköreihez tartozó:

- Ha egy szerepkört a felhasználóhoz hozzárendelt
- Amikor egy felhasználó szerepkörét lejárt
- Amikor egy felhasználó szerepkörét ki van bővítve
- Amikor a felhasználó szerepkör-aktiválási kérelem befejeződött

Az alábbiakban látható egy például szolgáló e-mail érkezik, amikor egy felhasználó hozzá van rendelve egy Azure-erőforrás szerepkör a fiktív Contoso szervezet számára.

![A PIM-új e-mail cím az Azure-erőforrásszerepkörök](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>További lépések

- [A PIM az Azure AD directory szerepkör-beállítások konfigurálása](pim-how-to-change-default-settings.md)
- [Hagyja jóvá vagy utasítsa a PIM az Azure AD-címtárbeli szerepkörökhöz tartozó kérelmek](azure-ad-pim-approval-workflow.md)

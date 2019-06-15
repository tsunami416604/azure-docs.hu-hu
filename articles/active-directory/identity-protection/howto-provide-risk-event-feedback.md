---
title: Visszajelzés küldése az Azure AD Identity Protection – Azure Active Directory kockázati események
description: Hogyan és miért érdemes, visszajelzést az Identity Protection kockázati események.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66d53590e89afb1a903b22ff60e32871a1502ada
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827905"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>kézikönyv: Az Azure AD Identity Protection kockázati visszajelzés

Az Azure AD Identity Protection lehetővé teszi a visszajelzés küldése a saját kockázatértékelésén alapul. A következő dokumentum azokat a forgatókönyveket, hol szeretné visszajelzés küldése az Azure AD Identity Protection kockázatértékelés és hogyan tudjuk tartalmazzák.

## <a name="what-is-a-detection"></a>Mi az, egy észlelési?

Az Identity Protection észlelés a azt jelzi, hogy gyanús tevékenységek identitás kockázati szempontból. Ezek a gyanús tevékenységek kockázati események nevezzük. Az azonosító-alapú észlelések heurisztika alapulhat, gépi tanulás, vagy partner termékek származhatnak. Ezek az észlelések segítségével meghatározhatja a bejelentkezési kockázat és felhasználói kockázat,

* Felhasználói kockázat jelöli annak a valószínűsége, az identitás biztonsága sérült.
* Bejelentkezési kockázati annak a valószínűsége, egy bejelentkezési biztonsága sérül jelöli (például a bejelentkezés nem engedélyezett az identitás tulajdonosa).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Miért kell adni az Azure AD kockázatfelmérések kockázati visszajelzés? 

Miért az Azure AD kockázati visszajelzés adjon több oka is van:

1. **Az Azure AD-felhasználó vagy a bejelentkezési kockázatértékelés helytelen található**. Például a "Kockázatos bejelentkezések" jelentésben szereplő bejelentkezési volt jóindulatú, és az, hogy jelentkezzen be minden észlelés téves volt.
1. **Azt ellenőrzi, hogy az Azure AD-felhasználó, vagy jelentkezzen be a hitelkockázat értékelése megfelelő**. Például a "Kockázatos bejelentkezések" jelentésben szereplő bejelentkezés volt valóban rosszindulatú, és szeretné tudni, hogy az adott bejelentkezési minden észlelés valódi pozitívok volt-e az Azure AD.
1. **Akkor javítja a kockázat, hogy a felhasználó Azure AD Identity Protection kívül a** és azt szeretné, hogy a felhasználó kockázati szint frissíteni kell.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Milyen Azure AD a kockázati visszajelzés használni?

Az Azure AD visszajelzést használ az alapul szolgáló felhasználói és/vagy bejelentkezési kockázatát frissítéséhez. A visszajelzés biztonságossá tehető a végfelhasználó számára. Például Miután meggyőződött a bejelentkezési biztonsága sérül, közvetlenül az Azure AD növeli a felhasználói kockázat és bejelentkezési a összesített kockázati (nem a valós idejű kockázati) a magas. Ez a felhasználó szerepel a felhasználói kockázati házirend kényszerítése a magas kockázatú felhasználók biztonságosan új jelszót kérjenek, ha a felhasználó automatikusan kijavítja magát a következő alkalommal be.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>Hogyan kell adnia kockázati visszajelzést, és mi történik a motorháztető alatt?

Az alábbiakban a forgatókönyveket és a kockázati visszajelzés küldése az Azure AD-mechanizmus.

| Forgatókönyv | Hogyan visszajelzés? | Mi történik, a motorháztető alatt? | Megjegyzések |
| --- | --- | --- | --- |
| **Jelentkezzen be nem sérült a biztonsága (hamis pozitív)** <br> "Kockázatos bejelentkezések" a jelentés jeleníti meg a kockázatos besorolású bejelentkezési [állapot kockázati veszélyben =] azonban, hogy a bejelentkezés nem feltörték. | Válassza ki a bejelentkezéshez, és kattintson a "Megerősítése be biztonságos". | Azure ad-ben a bejelentkezési összesített kockázati áthelyezi a nincs [kockázati állapot = biztonságos; megerősítve Kockázati szint (összesítés) =-] és a hatása visszafordítja a felhasználói kockázat. | A "Megerősítése be biztonságos" lehetőség jelenleg csak elérhető "Kockázatos bejelentkezések" jelentésben. |
| **Jelentkezzen be sérült a biztonsága (valódi pozitív)** <br> "Kockázatos bejelentkezések" a jelentés jeleníti meg a kockázatos besorolású bejelentkezési [állapot kockázati veszélyben =] kevés kockázattal rendelkező [kockázati szint (összesítés) = alacsony] és a bejelentkezési valóban feltörték. | Válassza ki a bejelentkezéshez, és kattintson a "Megerősítése bejelentkezési megsérül". | Az Azure AD helyezi át a bejelentkezési összesített kockázati és a felhasználói kockázat nagy [kockázati állapot = biztonsága sérült; megerősítve Kockázati szint = magas]. | A "megerősítése bejelentkezési megsérül" lehetőség jelenleg csak elérhető "Kockázatos bejelentkezések" jelentésben. |
| **Feltört felhasználói (valódi pozitív)** <br> "Kockázatos felhasználók" a jelentés jeleníti meg a kockázatos besorolású felhasználóként [állapot kockázati veszélyben =] kevés kockázattal rendelkező [kockázati szint = alacsony] és, hogy a felhasználó valóban feltörték. | Válassza ki a felhasználót, majd kattintson a "Feltört megerősítése felhasználó". | Az Azure AD át a felhasználói kockázat nagy [állapot kockázati = biztonsága sérült; megerősítve Kockázati szint = magas] és hozzáad egy új észlelése "Megerősített feltört felhasználói rendszergazda". | A "Feltört megerősítése felhasználó" lehetőség jelenleg "Kockázatos felhasználók" jelentésben csak érhető el. <br> Az észlelés a "Nincs felhasználóhoz kapcsolva, a bejelentkezési kockázati események" lapon a "Kockázatos felhasználók" jelentés "Megerősített feltört felhasználói rendszergazda" látható. |
| **Az Azure AD Identity Protection (valódi pozitív + Remediated) kívül szervizelt felhasználói** <br> "Kockázatos felhasználók" a jelentés jeleníti meg a kockázatos besorolású felhasználóként, és ezt követően I rendelkezik javítja a felhasználó Azure AD Identity Protection kívül. | 1. Válassza ki a felhasználót, és kattintson a "Feltört megerősítése felhasználó". (Ez a folyamat megerősíti, hogy az Azure ad-hez, hogy a felhasználó valóban feltörték.) <br> 2. Várjon, amíg a felhasználó "kockázati szintje: magas ugorhat. (Ez idő lehetőséget biztosít az Azure AD szükséges idő a fenti visszajelzés figyelembe a kockázat motoron.) <br> 3. Válassza ki a felhasználót, és kattintson a "Felhasználói kockázat elvetése" gombra. (Ez a folyamat megerősíti, hogy az Azure ad-hez, hogy a felhasználó már nem sérül.) |  Az Azure AD a felhasználói kockázat áthelyezi a nincs [állapot kockázati Dismissed; = Kockázati szint =-], és bezárja az összes meglévő bejelentkezés aktív kockázati kellene a kockázatokat. | "Elvetés felhasználói kockázat" gombra kattintva bezáródik a minden kockázat a felhasználóra és múltbeli bejelentkezések. Ez a művelet nem vonható vissza. |
| **Felhasználó nem sérült a biztonsága (hamis pozitív)** <br> "Kockázatos felhasználók" a jelentés jeleníti meg a kockázatos besorolású felhasználó, de a felhasználó nem sérül. | Válassza ki a felhasználót, és kattintson a "Felhasználói kockázat elvetése" gombra. (Ez a folyamat megerősíti, hogy az Azure ad-hez, hogy a felhasználó nem sérül.) | Az Azure AD a felhasználói kockázat áthelyezi a nincs [állapot kockázati Dismissed; = Kockázati szint =-]. | "Elvetés felhasználói kockázat" gombra kattintva bezáródik a minden kockázat a felhasználóra és múltbeli bejelentkezések. Ez a művelet nem vonható vissza. |
| Zárja be a felhasználói kockázat szeretnék, de nem biztos, hogy a felhasználó feltört / biztonságos-e. | Válassza ki a felhasználót, és kattintson a "Felhasználói kockázat elvetése" gombra. (Ez a folyamat megerősíti, hogy az Azure ad-hez, hogy a felhasználó már nem sérül.) | Az Azure AD a felhasználói kockázat áthelyezi a nincs [állapot kockázati Dismissed; = Kockázati szint =-]. | "Elvetés felhasználói kockázat" gombra kattintva bezáródik a minden kockázat a felhasználóra és múltbeli bejelentkezések. Ez a művelet nem vonható vissza. Javasoljuk, hogy javítsa a felhasználó a "Jelszó alaphelyzetbe állítása" gombra kattintva, vagy a felhasználó biztonságosan alaphelyzetbe állítása és módosítása a hitelesítő adatok igényléséhez. |

Felhasználói kockázati események az Identity Protection a visszajelzés feldolgozása kapcsolat nélküli módban, és frissítése hosszabb időt is igénybe vehet. A kockázat állam oszlop feldolgozása biztosít a visszajelzés feldolgozása aktuális állapotát.

![Kockázati kockázatos felhasználói jelentés feldolgozási állapota](./media/howto-provide-risk-event-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>További lépések

[Az Azure Active Directory Identity Protection kockázati események leírása](risk-events-reference.md)
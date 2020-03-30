---
title: Kockázati visszajelzés küldése az Azure Active Directory identitásvédelemben
description: Hogyan és miért kell visszajelzést adni az identitásvédelem kockázatészlelése.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4be8290f12d64f0c556100c63ec159bd414c6fcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382091"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>Útmutató: Kockázati visszajelzés küldése az Azure AD Identity Protection szolgáltatásban

Az Azure AD Identity Protection lehetővé teszi, hogy visszajelzést adjon a kockázatértékelés. A következő dokumentum felsorolja azokat a forgatókönyveket, ahol visszajelzést szeretne adni az Azure AD Identity Protection kockázatértékelésével, és hogyan építjük be.

## <a name="what-is-a-detection"></a>Mi az észlelés?

Az identitásvédelem észlelése a gyanús tevékenységek identitáskockázati szempontból mutatója. Ezeket a gyanús tevékenységeket kockázatészlelésnek nevezzük. Ezek az identitásalapú észlelések alapulhatnak heurisztikán, gépi tanuláson, vagy származhatnak partnertermékekből. Ezek az észlelések a bejelentkezési kockázat és a felhasználói kockázat

* A felhasználói kockázat azt a valószínűséget jelenti, hogy egy identitás feltört.
* A bejelentkezési kockázat azt a valószínűséget jelenti, hogy a bejelentkezés biztonsága sérül (például a bejelentkezést nem engedélyezi az identitás tulajdonosa).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Miért adjak kockázati visszajelzést az Azure AD kockázatértékeléséhez? 

Számos oka van annak, hogy miért kell az Azure AD kockázati visszajelzést adni:

- **Helytelennek találta az Azure AD felhasználóját vagy bejelentkezési kockázatértékelését.** Például egy bejelentkezési jelenik meg a "Kockázatos bejelentkezések" jelentés jóindulatú volt, és az összes észlelések, hogy a bejelentkezés hamis pozitív volt.
- **Igazolta, hogy az Azure AD felhasználói vagy bejelentkezési kockázatértékelés helyes volt.** Például egy bejelentkezési jelenik meg a "Kockázatos bejelentkezések" jelentés valóban rosszindulatú, és azt szeretné, hogy az Azure AD tudni arról, hogy az észlelések, hogy a bejelentkezés valódi pozitív.
- **Az Azure AD Identity Protection-en kívüli kockázatot orvosolta,** és szeretné, hogy a felhasználó kockázati szintje frissüljön.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Hogyan használja fel az Azure AD a kockázati visszajelzésemet?

Az Azure AD a visszajelzés segítségével frissíti az alapul szolgáló felhasználó és/vagy bejelentkezés kockázatát és az események pontosságát. Ez a visszajelzés segít a végfelhasználó védelmében. Ha például meggyőződött arról, hogy egy bejelentkezés biztonsága sérül, az Azure AD azonnal növeli a felhasználó kockázatát, és a bejelentkezés összesített kockázatát (nem valós idejű kockázat) magasra növeli. Ha ez a felhasználó szerepel a felhasználói kockázati szabályzatban, hogy kényszerítse a magas kockázatú felhasználókat a jelszavuk biztonságos alaphelyzetbe állítására, a felhasználó automatikusan orvosolja magát a következő bejelentkezéskor.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>Hogyan adjak kockázati visszajelzést, és mi történik a motorháztető alatt?

Íme a forgatókönyvek és mechanizmusok, hogy kockázati visszajelzést az Azure AD.

| Forgatókönyv | Hogyan adjunk visszajelzést? | Mi történik a motorháztető alatt? | Megjegyzések |
| --- | --- | --- | --- |
| **A bejelentkezés nem sérült (hamis pozitív)** <br> A "Kockázatos bejelentkezések" jelentés egy kockázatmentes bejelentkezést mutat [Kockázati állapot = Kockázatos], de a bejelentkezés nem sérült. | Válassza ki a bejelentkezést, és kattintson a "Bejelentkezési biztonság megerősítése" gombra. | Az Azure AD áthelyezi a bejelentkezés összesített kockázatát egyikre sem [Kockázati állapot = Megerősített biztonságos; Kockázati szint (összesített) = -] és megfordítja a felhasználói kockázatra gyakorolt hatását. | Jelenleg a "Bejelentkezési biztonságos megerősítése" beállítás csak a "Kockázatos bejelentkezések" jelentésben érhető el. |
| **Bejelentkezés sérült (Igaz pozitív)** <br> A "Kockázatos bejelentkezések" jelentés egy alacsony kockázatú [Kockázati állapot = Kockázatnak kitett] kockázatnak kitett bejelentkezést mutat, amelynek kockázata alacsony [kockázati szint (összesített) = alacsony], és hogy a bejelentkezés valóban veszélybe került. | Válassza ki a bejelentkezést, és kattintson a "Bejelentkezés megerősítése sérült" gombra. | Az Azure AD áthelyezi a bejelentkezés összesített kockázatát és a felhasználói kockázatot magas [Kockázati állapot = Megerősített feltört; Kockázati szint = Magas]. | Jelenleg a "Bejelentkezés megerősítése feltört" beállítás csak a "Kockázatos bejelentkezések" jelentésben érhető el. |
| **A felhasználó feltört (Igaz pozitív)** <br> A "Kockázatos felhasználók" jelentés egy veszélyeztetett felhasználót mutat [Kockázati állapot = Kockázatnak kitéve] alacsony kockázatú [Kockázati szint = Alacsony] kockázattal, és ez a felhasználó valóban veszélybe került. | Válassza ki a felhasználót, és kattintson a "Felhasználó biztonságának ellenőrzése" gombra. | Az Azure AD a felhasználói kockázatot magasra mozgatja [Kockázati állapot = Megerősített feltört; Kockázati szint = Magas], és hozzáad egy új észlelési "Admin megerősítette felhasználó feltört". | Jelenleg a "Felhasználó feltörésének megerősítése" lehetőség jelenleg csak a "Kockázatos felhasználók" jelentésben érhető el. <br> A "Rendszergazda megerősített felhasználó által feltört" észlelés a "Kockázatos felhasználók" jelentés "A bejelentkezéshez nem kapcsolódó kockázatészlelések" lapon jelenik meg. |
| **Az Azure AD Identity Protection szolgáltatáson kívül remediált felhasználó (Igaz pozitív + Kiigazított)** <br> A "Kockázatos felhasználók" jelentés egy veszélyeztetett felhasználót jelenít meg, és ezt követően az Azure AD Identity Protection-en kívül kiigazítottam a felhasználót. | 1. Válassza ki a felhasználót, és kattintson a "Felhasználó biztonságának megerősítése" gombra. (Ez a folyamat megerősíti az Azure AD számára, hogy a felhasználó valóban veszélybe került.) <br> 2. Várja meg, amíg a felhasználó "kockázati szintje" a Magas szintre kerül. (Ez az idő biztosítja az Azure AD-nek a szükséges időt, hogy a fenti visszajelzést a kockázati motor.) <br> 3. Válassza ki a felhasználót, és kattintson a "Felhasználói kockázat elvetése" gombra. (Ez a folyamat megerősíti az Azure AD számára, hogy a felhasználó már nem sérült.) |  Az Azure AD a felhasználói kockázatot egyikre sem helyezi át [Kockázati állapot = Elutasítva; Kockázati szint = -] és lezárja a kockázatot az összes aktív kockázattal rendelkező meglévő bejelentkezéseknél. | A "Felhasználói kockázat elvetése" gombra kattintva minden kockázatot megszüntet a felhasználóra és a korábbi bejelentkezésekre. Ez a művelet nem állítható vissza. |
| **A felhasználó nem sérült (hamis pozitív)** <br> A "Kockázatos felhasználók" jelentés a veszélyeztetett felhasználónál jelenik meg, de a felhasználó nem sérült. | Válassza ki a felhasználót, és kattintson a "Felhasználói kockázat elvetése" gombra. (Ez a folyamat megerősíti az Azure AD számára, hogy a felhasználó nem sérült.) | Az Azure AD a felhasználói kockázatot egyikre sem helyezi át [Kockázati állapot = Elutasítva; Kockázati szint = -]. | A "Felhasználói kockázat elvetése" gombra kattintva minden kockázatot megszüntet a felhasználóra és a korábbi bejelentkezésekre. Ez a művelet nem állítható vissza. |
| Azt akarom, hogy lezárja a felhasználói kockázatot, de nem vagyok biztos benne, hogy a felhasználó veszélybe / biztonságos. | Válassza ki a felhasználót, és kattintson a "Felhasználói kockázat elvetése" gombra. (Ez a folyamat megerősíti az Azure AD számára, hogy a felhasználó már nem sérült.) | Az Azure AD a felhasználói kockázatot egyikre sem helyezi át [Kockázati állapot = Elutasítva; Kockázati szint = -]. | A "Felhasználói kockázat elvetése" gombra kattintva minden kockázatot megszüntet a felhasználóra és a korábbi bejelentkezésekre. Ez a művelet nem állítható vissza. Javasoljuk, hogy a "Jelszó visszaállítása" gombra kattintva orvosolja a felhasználót, vagy kérje meg a felhasználót, hogy biztonságosan állítsa vissza/módosítsa a hitelesítő adatait. |

Az Identitásvédelem ben a felhasználói kockázatészlelésekre vonatkozó visszajelzések offline feldolgozása folyamatban van, és a frissítés némi időt vehet igénybe. A kockázatfeldolgozási állapot oszlop biztosítja a visszajelzés feldolgozásának aktuális állapotát.

![Kockázatfeldolgozási állapot kockázatos felhasználói jelentésesetén](./media/howto-identity-protection-risk-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>További lépések

- [Az Azure Active Directory-identitásvédelem kockázatészlelési hivatkozása](risk-events-reference.md)

---
title: Az Azure Active Directory identitásvédelem biztonságának áttekintése
description: Ismerje meg, hogy a Biztonság áttekintése hogyan nyújt betekintést a szervezet biztonsági helyzetébe.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9550684ad154f28a02ee347fd0a79c1ec286beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382184"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Azure Active Directory identitásvédelem – biztonság – áttekintés

Az Azure Portal [biztonsági áttekintése](https://aka.ms/IdentityProtectionRefresh) betekintést nyújt a szervezet biztonsági helyzetébe. Segít azonosítani a lehetséges támadásokat, és megérteni a szabályzatok hatékonyságát.

A "Biztonsági áttekintés" nagyjából két részből áll:

- A bal oldali trendek a szervezeten belüli kockázatok idővonalát biztosítják.
- A jobb oldalon található csempék kiemelik a szervezetben folyamatban lévő legfontosabb problémákat, és javaslatot tesznek a gyors cselekvésre.

![Biztonsági áttekintés](./media/concept-identity-protection-security-overview/01.png)
  
## <a name="trends"></a>Trendek

### <a name="new-risky-users-detected"></a>Új kockázatos felhasználók észlelve

Ez a diagram a kiválasztott időszakban észlelt új kockázatos felhasználók számát mutatja. A diagram nézetét felhasználói kockázati szint (alacsony, közepes, magas) szerint szűrheti. Vigye az UTC-dátumlépések fölé az adott napon észlelt kockázatos felhasználók számának megtekintéséhez. A diagramra kattintva megjelenik a "Kockázatos felhasználók" jelentés. A veszélyeztetett felhasználók kiiktatása érdekében fontolja meg a jelszavuk módosítását.

### <a name="new-risky-sign-ins-detected"></a>Új kockázatos bejelentkezések észlelve

Ez a diagram a kiválasztott időszakban észlelt kockázatos bejelentkezések számát mutatja. A diagram nézetét a bejelentkezési kockázat típusa (valós idejű vagy összesítés) és a bejelentkezési kockázati szint (alacsony, közepes, magas) szerint szűrheti. A nem védett bejelentkezések olyan sikeres valós idejű kockázati bejelentkezések, amelyek nem voltak megkérdőjelezve az MFA-val szemben. (Megjegyzés: Az offline észlelések miatt kockázatos bejelentkezések nem védhetők valós időben a bejelentkezési kockázati szabályzatokkal). Vigye az UTC-dátumlépések fölé az adott napon észlelt bejelentkezések számának megtekintéséhez. A diagramra kattintva megjelenik a "Kockázatos bejelentkezések" jelentés.

## <a name="tiles"></a>Csempék
 
### <a name="high-risk-users"></a>Magas kockázatú felhasználók

A "Magas kockázatú felhasználók" csempe mutatja a felhasználók legfrissebb száma nagy valószínűséggel identitás-kompromisszum. Ezeknek kiemelt prioritást kell élvezniük a nyomozás során. A "Magas kockázatú felhasználók" csempére való kattintás átirányítja a "Kockázatos felhasználók" jelentés szűrt nézetére, amely csak a magas kockázati szintű felhasználókat jeleníti meg. Ezzel a jelentéssel további információval és jelszó-visszaállítással orvosolhatja ezeket a felhasználókat.

![Biztonsági áttekintés](./media/concept-identity-protection-security-overview/02.png)

### <a name="medium-risk-users"></a>Közepes kockázatú felhasználók
A "Közepes kockázatú felhasználók" csempe az identitásbiztonság közepes valószínűségű felhasználók legfrissebb számát jeleníti meg. A "Közepes kockázatú felhasználók" csempére való kattintás átirányítja a "Kockázatos felhasználók" jelentés szűrt nézetére, amely csak a közepes kockázati szintű felhasználókat jeleníti meg. A jelentés használatával tovább vizsgálhatja és kiigazíthatja ezeket a felhasználókat.

### <a name="unprotected-risky-sign-ins"></a>Védelem nélküli kockázatos bejelentkezések

A "Nem védett kockázatos bejelentkezések" csempe mutatja a múlt heti száma a sikeres, valós idejű kockázatos bejelentkezések, amelyek nem blokkolt, vagy MFA megtámadta a feltételes hozzáférési szabályzat, identitásvédelem kockázati házirend, vagy felhasználónkénti MFA. Ezek potenciálisan kompromittált bejelentkezések, amelyek sikeresek voltak, és nem MFA kétségbe. Az ilyen bejelentkezések védelme érdekében a jövőben alkalmazza a bejelentkezési kockázati szabályzatot. A "Nem védett kockázatos bejelentkezések" csempére kattintva átirányítja a bejelentkezési kockázati házirend konfigurációs panelre, ahol konfigurálhatja a bejelentkezési kockázati szabályzatot, hogy a megadott kockázati szintű bejelentkezési modulon követelje meg az MFA-t.

### <a name="legacy-authentication"></a>Örökölt hitelesítés

Az "Örökölt hitelesítés" csempe a szervezeten belüli örökölt hitelesítések múlt heti számát jeleníti meg. Az örökölt hitelesítési protokollok nem támogatják a modern biztonsági módszereket, például az MFA-t. Az örökölt hitelesítés megakadályozása érdekében feltételes hozzáférési szabályzatot alkalmazhat. Az "Örökölt hitelesítés" csempére kattintva átirányítja önt az "Identitás biztonságos pontszámára".

### <a name="identity-secure-score"></a>Identitás biztonságos pontszáma

Az identitásbiztonságos pontszám méri és összehasonlítja a biztonsági állapotot az iparági mintákkal. Ha az "Identitás biztonságos pontszáma (előnézet)" csempére kattint, az átirányítja az "Identitás biztonságos pontszáma" panelre, ahol többet megtudhat a biztonsági testhelyzet javításáról.

## <a name="next-steps"></a>További lépések

- [Mi a kockázat?](concept-identity-protection-risks.md)

- [A kockázatok csökkentésére rendelkezésre álló politikák](concept-identity-protection-policies.md)

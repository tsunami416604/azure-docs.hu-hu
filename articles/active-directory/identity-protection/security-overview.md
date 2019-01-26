---
title: Az Azure Active Directory (Azure AD) Identity Protection biztonsági áttekintése |} A Microsoft Docs
description: Ismerje meg, hogyan biztonsági áttekintése biztosít abba, a szervezet biztonsági állapotáról.
services: active-directory
keywords: az Azure active directory identity protection a következőket cloud app discovery szolgáltatást, alkalmazások, biztonság, kockázati, kockázati szint, biztonsági rést, biztonsági házirend kezelése
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: a16c1cbc4549568cfdeb2312511fa4cb3564dceb
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913904"
---
# <a name="azure-active-directory-identity-protection-security-overview"></a>Az Azure Active Directory Identity Protection biztonsági áttekintése

Biztonsági áttekintés abba a szervezet biztonsági állapotáról, biztosít. Segít azonosítani a potenciális támadások, és megismerheti a szabályzatok hatékonyságát.

Biztonsági áttekintés nagyjából két szakaszra oszlik:

- Trendeket, a bal oldalon adjon meg egy idővonalon a szervezet kockázatok kiszámításában.

- Csempék, a jobb oldalon jelölje ki a fő kapcsolatban felmerülő problémákról a szervezetben, és gyorsan reagálhat hogyan javasolnak.


![Biztonsági áttekintés](./media/security-overview/01.png)
  
## <a name="trends"></a>Trendek

### <a name="new-risky-users-detected"></a>A rendszer új kockázatos felhasználókat észlelt

Ez a diagram a választott időszakban észlelt új kockázatos felhasználók számát jeleníti meg. A nézet a diagram felhasználói kockázati szint (alacsony, közepes, nagy) szerint szűrheti. A kurzort az UTC dátum növekszik, az adott napra észlelt kockázatos felhasználók száma. Elemre kattint, ez a diagram irányítja a "Kockázatos felhasználók" jelentés. Veszélyeztetett felhasználók szervizelése, fontolja meg a jelszó módosítása.

### <a name="new-risky-sign-ins-detected"></a>A rendszer új kockázatos bejelentkezéseket észlelt

Ezen a diagramon tekintheti meg a kockázatos bejelentkezések észlelt a választott időszakban. Szűrheti a bejelentkezési kockázat típusa (valós idejű vagy összesítő) és a bejelentkezési kockázati szint (alacsony, közepes, nagy) a diagram nézete. Nem védett bejelentkezések valós idejű kockázati sikeres bejelentkezések, amelyek nem merül fel az MFA. (Megjegyzés: Sign-ins, amelyek a kockázatos miatt offline észlelések nem látható el védelemmel, a valós idejű az bejelentkezési kockázati házirend). A kurzort az UTC dátum lépésekben való bejelentkezéseket észlelt veszélyben az adott napra számának megtekintéséhez. Elemre kattint, ez a diagram irányítja a "Kockázatos bejelentkezések" jelentés.

## <a name="tiles"></a>Mozaik elrendezés
 
### <a name="high-risk-users"></a>Nagy kockázatú felhasználók

A "magas kockázatú felhasználók" csempe a felhasználók legújabb száma az identitás veszélyeztetése, nagy valószínűséggel jeleníti meg. Ezek a vizsgálat a legnagyobb prioritással kell lennie. Elemre kattint, a "magas kockázatú felhasználók" csempe lesz irányítsa át a megjelenítő csak a felhasználó magas kockázati szintű "Kockázatos felhasználók" jelentés szűrt nézetét. Ez a jelentés használata esetén további, és ezek a jelszó alaphelyzetbe állítása a felhasználók szervizelése.

![Biztonsági áttekintés](./media/security-overview/02.png)


### <a name="medium-risk-users"></a>Közepes kockázatú felhasználók
A "közepes kockázatú felhasználók" csempe a felhasználók legújabb száma az identitás veszélyeztetése közepes valószínűségét jeleníti meg. A "közepes kockázatú felhasználók" csempén kattintson a megjelenítő csak a közepes kockázati szintű felhasználók "Kockázatos felhasználók" jelentés szűrt nézetét, átirányítja. Ez a jelentés használata esetén is további vizsgálata, és ezek a felhasználók szervizelése.

### <a name="unprotected-risky-sign-ins"></a>Nem védett kockázatos bejelentkezések

A "Nem védett kockázatos bejelentkezések" csempe sikeres, valós idejű kockázatos bejelentkezések nem futtatható, sem az MFA próbálnak futtatni rajta egy feltételes hozzáférési szabályzat, az Identity Protection kockázati házirend vagy a felhasználónkénti MFA Hitelesítést az elmúlt hét számát mutatja. Ezek a potenciálisan veszélyeztetett sikeres bejelentkezéseket, és nem MFA merül fel. Ilyen bejelentkezések a jövőbeli védelme érdekében alkalmazzon bejelentkezési kockázati házirendet. A "Nem védett kockázatos bejelentkezések" csempén kattintson a bejelentkezési kockázati házirend konfigurációs paneljére, amellyel konfigurálhatja a bejelentkezési kockázati házirend többtényezős hitelesítés megkövetelése a egy jelentkezzen be egy adott kockázati szint átirányítja.


### <a name="legacy-authentication"></a>Örökölt hitelesítés

Az "Örökölt hitelesítés" csempe örökölt hitelesítések száma az elmúlt egy hétben a szervezet jeleníti meg. Az örökölt hitelesítési protokollok nem támogatja a modern biztonsági módszerek, például egy MFA. Az örökölt hitelesítési megelőzése érdekében a feltételes hozzáférési szabályzat is alkalmazhat. Az "Örökölt hitelesítés" csempére kattintás átirányítja Önt a "Identity biztonságos pontszámot".


### <a name="identity-secure-score"></a>Biztonságos identitás-pontszám

Az identitás biztonságos pontszám méri, és összehasonlítja az iparági minták meg biztonsági állapotát. Ha az "Identity biztonságos pontszám (előzetes verzió)" csempére kattint, az "Identity biztonságos pontszám (előzetes verzió)" panel, ahol további biztonsági helyzetét növelésével kapcsolatos átirányítja.


## <a name="next-steps"></a>További lépések

- [Channel 9-on: Az Azure AD és az identitás megjelenítése: Identity Protection előzetes verziója](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

- [Az Azure Active Directory Identity Protection engedélyezése](enable.md)


---
title: A Azure Active Directory (Azure AD) Identity Protection biztonsági áttekintése | Microsoft Docs
description: Ismerje meg, hogyan tekintheti át a szervezet biztonsági állapotát a "biztonsági áttekintés" című témakörben.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 02/14/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: b894f7020083dd6ca46c394ec2930a3da36bc76d
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335202"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Azure Active Directory Identity Protection – biztonsági áttekintés

A [Biztonság áttekintése](https://aka.ms/IdentityProtectionRefresh) betekintést nyújt a szervezet biztonsági állapotára. Segít azonosítani a lehetséges támadásokat és értelmezni a szabályzatok hatékonyságát.

A "biztonsági áttekintés" széles körben két szakaszra oszlik:

- A bal oldali trendek a szervezeten belüli kockázati idővonalat biztosítanak.
- A csempék a jobb oldalon kiemelik a szervezeten belüli aktuális problémákat, és arra utalnak, hogyan lehet gyorsan végrehajtani a műveleteket.

![Biztonsági áttekintés](./media/security-overview/01.png)
  
## <a name="trends"></a>Trendek

### <a name="new-risky-users-detected"></a>A rendszer új kockázatos felhasználókat észlelt

Ez a diagram a kiválasztott időszakban észlelt új kockázatos felhasználók számát jeleníti meg. A diagram nézetét felhasználói kockázati szint alapján szűrheti (alacsony, közepes, magas). Vigye az egérmutatót az UTC dátum fölé, hogy megtekintse az adott napon észlelt kockázatos felhasználók számát. A diagramra kattintva megjelenik a "kockázatos felhasználók" jelentés. A veszélyeztetett felhasználók szervizeléséhez érdemes lehet módosítani a jelszavát.

### <a name="new-risky-sign-ins-detected"></a>A rendszer új kockázatos bejelentkezéseket észlelt

Ez a diagram a kiválasztott időszakban észlelt kockázatos bejelentkezések számát jeleníti meg. A diagram nézetét szűrheti a bejelentkezési kockázat típusa (valós idejű vagy Összesítés) és a bejelentkezési kockázati szint (alacsony, közepes, magas) alapján. A nem védett bejelentkezések olyan valós idejű kockázatos bejelentkezések, amelyek nem lettek kitéve az MFA-nak. (Megjegyzés: Az offline észlelések miatt kockázatos bejelentkezések nem védhetők valós időben a bejelentkezési kockázati szabályzatok alapján. Vigye az egérmutatót az UTC dátum fölé, hogy megtekintse az adott nap során a kockázattal észlelt bejelentkezések számát. A diagramra kattintva a "kockázatos bejelentkezések" jelentésbe kerül.

## <a name="tiles"></a>Mozaik elrendezés
 
### <a name="high-risk-users"></a>Nagy kockázatú felhasználók

A "magas kockázatú felhasználók" csempe azon felhasználók legújabb számát jeleníti meg, akik nagy valószínűséggel veszélyeztetik az identitást. Ennek a vizsgálat legfontosabb prioritásának kell lennie. A "magas kockázatú felhasználók" csempére kattintva a rendszer átirányítja a "kockázatos felhasználók" jelentés szűrt nézetére, amely csak a kockázati szinttel rendelkező felhasználókat jeleníti meg. A jelentés használatával további információkat tudhat meg, és elháríthatja ezeket a felhasználókat jelszó-visszaállítással.

![Biztonsági áttekintés](./media/security-overview/02.png)

### <a name="medium-risk-users"></a>Közepes kockázatú felhasználók
A "közepes kockázatú felhasználók" csempe azon felhasználók legújabb számát jeleníti meg, akik közepes valószínűséggel veszélyeztetik az identitást. A "közepes kockázatú felhasználók" csempére kattintva a rendszer átirányítja a "kockázatos felhasználók" jelentés szűrt nézetére, amely csak a közepes kockázati szintű felhasználókat jeleníti meg. A jelentés használatával tovább vizsgálhatja és javíthatja ezeket a felhasználókat.

### <a name="unprotected-risky-sign-ins"></a>Nem védett kockázatos bejelentkezések

A "nem védett kockázatos bejelentkezések" csempe a legutóbbi hét azon sikeres és valós idejű, kockázatos bejelentkezések számát jeleníti meg, amelyeket a feltételes hozzáférési szabályzat, az Identity Protection kockázati házirendje vagy a felhasználónkénti MFA nem adott meg. Ezek olyan feltört bejelentkezések, amelyek sikeresek voltak, és az MFA-t nem vitatták meg. Az ilyen bejelentkezések jövőbeli biztosításához alkalmazza a bejelentkezési kockázati házirendet. Ha a "nem védett kockázatos bejelentkezések" csempére kattint, a rendszer átirányítja a bejelentkezési kockázati házirend konfigurálási paneljére, ahol konfigurálhatja a bejelentkezési kockázati házirendet, hogy az MFA-t egy megadott kockázati szinttel rendelkező bejelentkezéshez is megkövetelje.

### <a name="legacy-authentication"></a>Örökölt hitelesítés

Az "örökölt hitelesítés" csempe a szervezet korábbi hitelesítésének múlt heti számát mutatja. Az örökölt hitelesítési protokollok nem támogatják a modern biztonsági módszereket, például az MFA-t. A régi hitelesítés megakadályozása érdekében alkalmazhat feltételes hozzáférési szabályzatot is. Az "örökölt hitelesítés" csempére kattintva átirányítjuk a "személyazonosság biztonságos pontszáma" értékre.

### <a name="identity-secure-score"></a>Identitásbiztonsági pontszám

Az identitások biztonságos pontszáma méri és összehasonlítja a biztonsági helyzeteket az iparági mintákkal. Ha a "személyazonosság biztonságos pontszám (előzetes verzió)" csempére kattint, a rendszer átirányítja a "személyazonosság biztonságos pontszáma (előzetes verzió)" panelre, ahol további információt talál a biztonsági helyzet javításával kapcsolatban.

## <a name="next-steps"></a>További lépések

- [Channel 9: Azure AD-és identitás-megjelenítés: Identity Protection – előzetes verzió](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
- [Azure Active Directory Identity Protection engedélyezése](enable.md)

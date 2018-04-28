---
title: 'Az Azure AD Connect: Áteresztő hitelesítés |} Microsoft Docs'
description: Ez a cikk ismerteti az Azure Active Directory (Azure AD) áteresztő hitelesítés és hogyan lehetővé teszi az Azure AD bejelentkezések érvényesítésével azonosítsa a felhasználói jelszavakat, a helyszíni Active Directoryban.
services: active-directory
keywords: Mi az Azure AD Connect áteresztő hitelesítés, az Active Directory, az Azure AD, SSO, szükséges összetevők telepítése egyszeri bejelentkezést.
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: billmath
ms.openlocfilehash: 377a8255961a4c7fa55f8bbc5cb98ae4deff3205
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Felhasználó bejelentkezhet az Azure Active Directory áteresztő hitelesítés

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Mi az Azure Active Directory átmenő hitelesítést?

Az Azure Active Directory (Azure AD) áteresztő hitelesítés lehetővé teszi, hogy a felhasználók számára a helyszíni és a felhőalapú alkalmazások azonos jelszóval bejelentkezni. Ez a szolgáltatás biztosít a felhasználók hatékonyabb működését - ne feledje, hogy egy kisebb jelszó és csökkenti az informatikai segélyszolgálat költségeket, mivel a felhasználók bejelentkezés elfelejti kevésbé valószínű. Amikor a felhasználók bejelentkeznek az Azure AD, ez a szolgáltatás ellenőrzi a felhasználói jelszavak közvetlenül a helyszíni Active Directory általi.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Ez a szolgáltatás nem helyett [Azure AD Jelszókivonat-szinkronizálást](active-directory-aadconnectsync-implement-password-hash-synchronization.md), amely biztosítja, hogy az azonos előnye, hogy a szervezetek számára a felhőalapú hitelesítés. Azonban egyes szervezetek biztonsági és megfelelőségi házirendek nem lehetővé teszik a szervezetek számára küldése a felhasználói jelszavakat, még akkor is, kivonatolt formában, a belső határain kívül. Áteresztő hitelesítés az olyan szervezetek ideális megoldás.

![Az Azure AD-áteresztő hitelesítés](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

Áteresztő hitelesítés kombinálhatja a [zökkenőmentes egyszeri bejelentkezést](active-directory-aadconnect-sso.md) szolgáltatás. Így amikor a felhasználók érnek el alkalmazásokat a vállalati számítógépeken a vállalati hálózaton belüli, nem kell beírnia a jelszavukat a bejelentkezéshez.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Azure AD átmenő hitelesítést használó legfontosabb előnyei

- *Kiváló felhasználói élmény*
  - Felhasználó használja ugyanazt a jelszót, mind a helyszíni és felhőalapú alkalmazások bejelentkezni.
  - Felhasználók kevesebb időt segítségével az informatikai segélyszolgálat feloldó jelszó kapcsolatos problémákat.
  - Felhasználók fejezheti be [önkiszolgáló jelszókezelés](../authentication/active-directory-passwords-overview.md) feladatok a felhőben.
- *Könnyen üzembe helyezése és felügyelete*
  - Nincs szükség bonyolult helyszíni telepítésekkel vagy a hálózati konfigurációt.
  - Csak egy egyszerűsített helyszíni telepített ügynök kell.
  - Egyetlen kezelési terhelés mellett. Az ügynök automatikusan megkapja a fejlesztései és hibajavításokat tartalmaz.
- *Biztonságos*
  - A helyszíni jelszavak soha nem a felhő semmilyen formában vannak tárolva.
  - Az ügynök csak lehetővé teszi a kimenő kapcsolatok a hálózaton belül. Ezért nincs szükség a szegélyhálózaton, más néven DMZ az ügynök telepítéséhez.
  - A felhasználói fiókok védi használata zökkenőmentesen [Azure AD feltételes hozzáférési házirendek](../active-directory-conditional-access-azure-portal.md), beleértve a multi-factor Authentication (MFA) és az [találgatásos jelszó támadások kiszűrése](active-directory-aadconnect-pass-through-authentication-smart-lockout.md).
- *Magas rendelkezésre állású*
  - További ügynökök bejelentkezési kérelmek magas rendelkezésre állás biztosításához több helyszíni kiszolgálókon telepíthető.

## <a name="feature-highlights"></a>A szolgáltatás kiemelések

- Felhasználói bejelentkezés támogatja minden böngésző alapú webalkalmazás és a Microsoft Office ügyfél használó alkalmazások [modern hitelesítést](https://aka.ms/modernauthga).
- Bejelentkezési felhasználónevek vagy a helyszíni alapértelmezett felhasználónév lehet (`userPrincipalName`) vagy az Azure AD Connect konfigurált egy másik attribútum (úgynevezett `Alternate ID`).
- A funkció zökkenőmentesen együttműködik a [feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md) funkciók, például a multi-factor Authentication (MFA) biztonságossá tétele a felhasználók számára.
- Integrálva a felhő alapú [önkiszolgáló jelszókezelés](../authentication/active-directory-passwords-overview.md), beleértve a jelszavak visszaírását a helyszíni Active Directory és a jelszavas védelem tiltó által leggyakrabban használt jelszavakat.
- Többerdős környezetben támogatottak, ha a AD erdők között erdőszintű megbízhatóság, és ha névutótag megfelelően van beállítva.
- Egy szabad szolgáltatást, és nem kell használni az Azure AD bármely fizetős verziója.
- Keresztül engedélyezhető [az Azure AD Connect](active-directory-aadconnect.md).
- Egy egyszerűsített helyszíni ügynök figyeli, és a jelszó érvényesítése kérelmére reagálás használ.
- Több ügynökök telepítése bejelentkezési kérelmek magas rendelkezésre állású biztosít.
- Az [védi](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) a helyszíni fiókok elleni találgatásos kényszerítése jelszó támadások a felhőben.

## <a name="next-steps"></a>További lépések

- [**Gyors üzembe helyezési** ](active-directory-aadconnect-pass-through-authentication-quick-start.md) - létrehozásához, és fut az Azure AD áteresztő hitelesítés.
- [**Intelligens zárolás** ](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) -intelligens zárolás konfigurálása képességet a bérlő a felhasználói fiókok védelme.
- [**Aktuális korlátozások** ](active-directory-aadconnect-pass-through-authentication-current-limitations.md) – ismerje meg, melyik forgatókönyvek is támogatottak, és melyek nem.
- [**Műszaki mélyreható** ](active-directory-aadconnect-pass-through-authentication-how-it-works.md) – Ez a funkció működésének megismerése.
- [**Gyakran ismételt kérdések** ](active-directory-aadconnect-pass-through-authentication-faq.md) -gyakran feltett kérdésekre adott válaszokat.
- [**Hibaelhárítás** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) -Útmutató: a szolgáltatással kapcsolatos gyakori problémák megoldása.
- [**Biztonsági mélyreható** ](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) -részletes műszaki további információ a szolgáltatásról.
- [**Az Azure AD zökkenőmentes SSO** ](active-directory-aadconnect-sso.md) -további információ a kiegészítő funkció.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új funkciókérések tárolásához.

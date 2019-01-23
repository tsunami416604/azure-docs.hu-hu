---
title: 'Azure AD Connect: Az átmenő hitelesítés |} A Microsoft Docs'
description: Ez a cikk ismerteti az Azure Active Directory (Azure AD) átmenő hitelesítés és az hogyan teszi az Azure AD bejelentkezési érvényesítésével azonosítsa a helyi Active Directorybeli felhasználói jelszavakat.
services: active-directory
keywords: Mi az Azure AD Connect az átmenő hitelesítés, Active Directory, Azure AD egyszeri bejelentkezés, a szükséges összetevők telepítése egyszeri bejelentkezés
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 4705690c2cf0c6e2ba3867d74d40e0678784d967
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54478042"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Felhasználó jelentkezzen be az Azure Active Directory átmenő hitelesítése

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Mi az Azure Active Directory átmenő hitelesítés?

Az Azure Active Directory (Azure AD) átmenő hitelesítés lehetővé teszi, hogy a felhasználókat, hogy jelentkezzen be a helyszíni és felhőalapú alkalmazások használatával ugyanazt a jelszót is. Ezzel a szolgáltatással javítható a felhasználók felhasználói élménye, mivel eggyel kevesebb jelszót kell megjegyezni, továbbá csökkenthetők az informatikai ügyfélszolgálat költségei, mivel a felhasználók kisebb valószínűséggel felejtik el a bejelentkezési adataikat. Amikor a felhasználók bejelentkeznek az Azure AD-vel, ez a szolgáltatás érvényesíti a közvetlenül a helyi Active Directorybeli felhasználói jelszavakat.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Ez a funkció csak az alternatív [Azure AD a Jelszókivonat-szinkronizálás](how-to-connect-password-hash-synchronization.md), amely biztosítja, hogy az azonos előnye, hogy a szervezetek számára a felhőalapú hitelesítés. Azonban egyes szervezetek számára, aki a saját helyszíni Active Directory biztonsági és jelszóházirendek, dönthet úgy, hogy átmenő hitelesítést használjon. Felülvizsgálat [Ez az útmutató](https://docs.microsoft.com/azure/security/azure-ad-choose-authn) összehasonlítása az Azure AD bejelentkezési módszerek és a szervezet bejelentkezési megfelelő módszer kiválasztásában.

![Az Azure AD átmenő hitelesítés](./media/how-to-connect-pta/pta1.png)

Az átmenő hitelesítés kombinálhatja a [zökkenőmentes egyszeri bejelentkezést](how-to-connect-sso.md) funkció. Így amikor a felhasználók érnek el alkalmazásokat a vállalati gépekre a vállalati hálózaton belüli, nem kell beírnia az jelszavait, hogy jelentkezzen be.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Az Azure AD átmenő hitelesítés legfontosabb előnyei

- *Nagyszerű felhasználói élmény*
  - Felhasználó használja ugyanazt a jelszót, jelentkezzen be a helyszíni és felhőbeli alkalmazásokat is.
  - Felhasználók kevesebb időt az informatikai segélyszolgálat feloldó jelszó kapcsolatos problémák folytatott kommunikációra.
  - Felhasználók befejezheti [önkiszolgáló jelszókezelés](../authentication/active-directory-passwords-overview.md) feladatokat a felhőben.
- *Egyszerű üzembe helyezése és felügyelete*
  - Nincs szükség összetett helyszíni üzemelő vagy a hálózati konfigurációt.
  - Csak egy egyszerűsített helyszíni telepített ügynök szükséges.
  - Nincs munkaterhelést. Az ügynök automatikusan kap fejlesztéseket és hibajavítások.
- *Biztonságos*
  - A helyszíni jelszavak soha nem a felhőben, bármilyen formában vannak tárolva.
  - A felhasználói fiókokhoz védi zökkenőmentesen dolgozik [Azure AD feltételes hozzáférési szabályzatok](../active-directory-conditional-access-azure-portal.md), többek között a multi-factor Authentication (MFA), [örökölt hitelesítés](../conditional-access/conditions.md) , illetve [ kiszűri a találgatásos jelszó támadások](../authentication/howto-password-smart-lockout.md).
  - Az ügynök csak lehetővé teszi a kimenő kapcsolatokat a hálózaton belül. Ezért esetében nem követelmény az ügynök telepítéséhez a szegélyhálózaton, más néven DMZ-t.
  - Egy ügynök és az Azure AD közötti kommunikáció Tanúsítványalapú hitelesítés használatával lett biztonságossá téve. Ezek a tanúsítványok automatikusan történik meg az Azure AD néhány havonta.
- *Magas rendelkezésre állású*
  - További ügynökök bejelentkezési kérelmek magas rendelkezésre állást biztosít több helyszíni kiszolgálókon telepíthető.

## <a name="feature-highlights"></a>A szolgáltatás emeli ki.

- Felhasználói bejelentkezés támogatja minden böngésző alapú webalkalmazás és a Microsoft Office-ügyfélalkalmazások, amelyek [modern hitelesítést](https://aka.ms/modernauthga).
- Bejelentkezési felhasználónév vagy a helyszíni alapértelmezett felhasználónév lehet (`userPrincipalName`) vagy egy másik attribútum konfigurálva az Azure AD Connectben (más néven `Alternate ID`).
- A szolgáltatás problémamentesen működik együtt [feltételes hozzáférési](../active-directory-conditional-access-azure-portal.md) szolgáltatások például a multi-factor Authentication (MFA) való biztonságossá tétele a felhasználók számára.
- Integrált felhő alapú [önkiszolgáló jelszókezelés](../authentication/active-directory-passwords-overview.md), beleértve a jelszavak visszaírását a helyszíni Active Directory és a jelszavas védelem eltiltanak bizonyos gyakran használt jelszavakat.
- Többerdős környezetben támogatottak, ha az AD-erdők között erdőszintű megbízhatóság, és ha névutótag megfelelően van konfigurálva.
- Egy ingyenes szolgáltatás, és nem kell minden fizetős kiadásban az Azure AD használatát.
- Azt is engedélyezhetők [az Azure AD Connect](whatis-hybrid-identity.md).
- A parancs egy egyszerűsített helyszíni ügynök, amely figyeli és a jelszó érvényesítése kérésekre válaszol.
- A bejelentkezési kérelmek magas rendelkezésre állást több ügynökök telepítése biztosít.
- Ez [védi](../authentication/howto-password-smart-lockout.md) a helyszíni fiókok elleni találgatásos jelszó támadások, a felhőben kényszerítése.

## <a name="next-steps"></a>További lépések

- [Gyors üzembe helyezési](how-to-connect-pta-quick-start.md) – és az Azure AD átmenő hitelesítés futtató.
- [Az AD FS át az átmenő hitelesítés](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) – egy részletes útmutató, amellyel áttelepíteni az átmenő hitelesítés az Active Directory összevonási szolgáltatások (vagy más összevonási technológiákkal).
- [Az intelligens zárolási](../authentication/howto-password-smart-lockout.md) -konfigurálása az intelligens zárolás funkciót a bérlő felhasználói fiókok védelmét.
- [Aktuális korlátozások](how-to-connect-pta-current-limitations.md) – ismerje meg, melyik forgatókönyvek is támogatottak, és melyek nem.
- [Részletes technikai](how-to-connect-pta-how-it-works.md) – Ez a funkció működésének megismerése.
- [Gyakran ismételt kérdések](how-to-connect-pta-faq.md) – a gyakran feltett kérdésekre adott válaszok.
- [Hibaelhárítás](tshoot-connect-pass-through-authentication.md) – ismerje meg, a szolgáltatással kapcsolatos gyakori problémák megoldása.
- [A biztonság részletes bemutatása](how-to-connect-pta-security-deep-dive.md) – további technikai információ a szolgáltatásról.
- [Az Azure AD közvetlen egyszeri bejelentkezés](how-to-connect-sso.md) -további információ a kiegészítő funkció.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új funkcióra vonatkozó javaslata tárolásához.

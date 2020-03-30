---
title: 'Azure AD Connect: Átmenő hitelesítés | Microsoft dokumentumok'
description: Ez a cikk ismerteti az Azure Active Directory (Azure AD) átmenő hitelesítés, és hogyan teszi lehetővé az Azure AD-bejelentkezések a felhasználók jelszavainak ellenőrzése a helyszíni Active Directory.
services: active-directory
keywords: mi az Azure AD Connect áthaladási hitelesítése, az Active Directory telepítése, az Azure AD, egyszeri bejelentkezés, egyszeri bejelentkezés szükséges összetevői
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4b52a3025bfb15e2679709353cebf28254a75c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185504"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Felhasználói bejelentkezés az Azure Active Directory átmenő hitelesítésével

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Mi az Azure Active Directory átmenő hitelesítése?

Az Azure Active Directory (Azure AD) átadó hitelesítéslehetővé teszi a felhasználók számára, hogy jelentkezzen be mind a helyszíni és a felhőalapú alkalmazások ugyanazt a jelszavakat. Ezzel a szolgáltatással javítható a felhasználók felhasználói élménye, mivel eggyel kevesebb jelszót kell megjegyezni, továbbá csökkenthetők az informatikai ügyfélszolgálat költségei, mivel a felhasználók kisebb valószínűséggel felejtik el a bejelentkezési adataikat. Amikor a felhasználók az Azure AD használatával jelentkeznek be, ez a funkció közvetlenül a helyszíni Active Directoryval szemben érvényesíti a felhasználók jelszavait.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Ez a funkció az [Azure AD jelszókivonat-szinkronizálás](how-to-connect-password-hash-synchronization.md)alternatívája, amely a felhőalapú hitelesítés előnyeit nyújtja a szervezetek számára. Egyes szervezetek azonban, amelyek ki akarják kényszeríteni a helyszíni Active Directory biztonsági és jelszóházirendjeiket, dönthetnek úgy, hogy átmenő hitelesítést használnak. Tekintse át ezt az [útmutatót](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) a különböző Azure AD bejelentkezési módszerek összehasonlításához, és hogyan válassza ki a megfelelő bejelentkezési módszert a szervezet számára.

![Azure AD áthaladási hitelesítése](./media/how-to-connect-pta/pta1.png)

Az átmenő hitelesítést kombinálhatja a [zökkenőmentes egyszeri bejelentkezés](how-to-connect-sso.md) funkcióval. Így amikor a felhasználók a vállalati hálózaton belüli vállalati gépeiken férnek hozzá az alkalmazásokhoz, nem kell beírniuk a jelszavukat a bejelentkezéshez.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Az Azure AD áthaladási hitelesítésének legfontosabb előnyei

- *Nagyszerű felhasználói élmény*
  - A felhasználók ugyanazokat a jelszavakat használják a helyszíni és a felhőalapú alkalmazásokba való bejelentkezéshez.
  - A felhasználók kevesebb időt töltenek az informatikai ügyfélszolgálattal a jelszóval kapcsolatos problémák megoldásában.
  - A felhasználók [önkiszolgáló jelszókezelési](../authentication/active-directory-passwords-overview.md) feladatokat végezhetnek el a felhőben.
- *Könnyen telepíthető & felügyelet*
  - Nincs szükség összetett helyszíni telepítésekre vagy hálózati konfigurációra.
  - Szüksége van csak egy könnyű ügynök kell telepíteni a helyszínen.
  - Nincs kezelési többletterhelés. Az ügynök automatikusan megkapja a fejlesztéseket és a hibajavításokat.
- *Biztonságos*
  - A helyszíni jelszavakat soha nem tárolja a felhőben semmilyen formában.
  - Védi a felhasználói fiókokat az [Azure AD feltételes hozzáférésházirendjeivel](../active-directory-conditional-access-azure-portal.md)való zökkenőmentes együttműködéssel, beleértve a többtényezős hitelesítést (MFA), [az örökölt hitelesítés blokkolását](../conditional-access/concept-conditional-access-conditions.md) és a [találgatásos jelszótámadások kiszűrésével.](../authentication/howto-password-smart-lockout.md)
  - Az ügynök csak a hálózaton belülről létesít kimenő kapcsolatokat. Ezért nincs követelmény az ügynök telepítése a peremhálózaton, más néven a DMZ.
  - Az ügynök és az Azure AD közötti kommunikáció tanúsítványalapú hitelesítéssel védett. Ezeket a tanúsítványokat az Azure AD néhány havonta automatikusan megújítja.
- *Magas rendelkezésre állású*
  - További ügynökök több helyszíni kiszolgálókon is telepíthetők a bejelentkezési kérelmek magas rendelkezésre állásának biztosítása érdekében.

## <a name="feature-highlights"></a>Kiemelt funkciók

- Támogatja a felhasználó bejelentkezését az összes webböngésző-alapú alkalmazásba és a [Modern hitelesítést](https://aka.ms/modernauthga)használó Microsoft Office ügyfélalkalmazásokba.
- A bejelentkezési felhasználónevek lehetnek a helyszíni alapértelmezett`userPrincipalName`felhasználónév ( ) vagy az Azure AD Connectben (más néven) `Alternate ID`konfigurált másik attribútum.
- A szolgáltatás zökkenőmentesen működik a [feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md) funkcióival, például a többtényezős hitelesítéssel (MFA) a felhasználók védelme érdekében.
- Integrálva a felhőalapú [önkiszolgáló jelszókezeléssel,](../authentication/active-directory-passwords-overview.md)beleértve a jelszó-visszaírást a helyszíni Active Directoryba, valamint a jelszóvédelmet a gyakran használt jelszavak betiltásával.
- A többerdős környezetek akkor támogatottak, ha az AD-erdők között erdőszintű bizalmi kapcsolatok vannak, és ha a névutótag-útválasztás megfelelően van konfigurálva.
- Ez egy ingyenes funkció, és nem kell semmilyen fizetett kiadása az Azure AD-hez, hogy használja.
- Az [Azure AD Connect](whatis-hybrid-identity.md)en keresztül engedélyezhető.
- Egy könnyű helyszíni ügynököt használ, amely figyeli és válaszol a jelszó-érvényesítési kérelmekre.
- Több ügynök telepítése biztosítja a bejelentkezési kérelmek magas rendelkezésre állását.
- [Megvédi](../authentication/howto-password-smart-lockout.md) a helyszíni fiókokat a felhőben elkövetett találgatásos jelszótámadásoktól.

## <a name="next-steps"></a>További lépések

- [Rövid útmutató](how-to-connect-pta-quick-start.md) – Az Azure AD áthaladási hitelesítésének üzembe használata.
- [Az AD FS-ről átmenő hitelesítésre való áttelepítés](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) részletes útmutató az AD FS-ről (vagy más összevonási technológiákról) az átmenő hitelesítésre való áttelepítéshez.
- [Smart Lockout](../authentication/howto-password-smart-lockout.md) – Konfigurálja az intelligens zárolási képességet a bérlőn a felhasználói fiókok védelme érdekében.
- [Jelenlegi korlátozások](how-to-connect-pta-current-limitations.md) – Ismerje meg, hogy mely forgatókönyvek támogatottak, és melyek nem.
- [Műszaki Deep Dive](how-to-connect-pta-how-it-works.md) - Ismerje meg, hogyan működik ez a funkció.
- [Gyakori kérdések](how-to-connect-pta-faq.md) – Válaszok a gyakran ismételt kérdésekre.
- [Hibaelhárítás](tshoot-connect-pass-through-authentication.md) – Ismerje meg, hogyan oldhatja meg a szolgáltatással kapcsolatos gyakori problémákat.
- [Security Deep Dive](how-to-connect-pta-security-deep-dive.md) - További mély technikai információkat a funkció.
- [Azure AD seamless sso](how-to-connect-sso.md) – További információ erről a kiegészítő funkcióról.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - A bejelentés új funkció kérelmeket.

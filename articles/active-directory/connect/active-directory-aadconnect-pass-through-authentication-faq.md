---
title: "Az Azure AD Connect: Áteresztő hitelesítés – gyakori kérdések |} Microsoft Docs"
description: "Azure Active Directory áteresztő hitelesítés gyakran feltett kérdésekre adott válaszok."
services: active-directory
keywords: "Az Azure AD Connect áteresztő hitelesítés, telepítés Active Directory szükséges összetevőket az Azure AD, SSO, egyszeri bejelentkezést."
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: 821d70ec7236e165d4f60ed6217c49d10de1cfc3
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2017
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Az Azure Active Directory áteresztő hitelesítés: Gyakran ismételt kérdések

Ebben a cikkben oldjuk Azure Active Directory (Azure AD) áteresztő hitelesítés kapcsolatos gyakori kérdésekre. Vissza az új tartalom ellenőrizni.

## <a name="which-of-the-azure-ad-sign-in-methods---pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs---should-i-choose"></a>-Áteresztő hitelesítés, Jelszókivonat-szinkronizálást és az Active Directory összevonási szolgáltatások (AD FS) – az Azure AD bejelentkezési módszerek közül melyik válasszam?

Ez a helyszíni környezetben és a szervezeti követelményektől függ. Tekintse át a jelen cikk egy [a különböző Azure AD bejelentkezési módszerek összehasonlítása](active-directory-aadconnect-user-signin.md).

## <a name="is-pass-through-authentication-a-free-feature"></a>Az áteresztő hitelesítés szabad szolgáltatás?

Áteresztő hitelesítés szabad funkció, nem kell használni az Azure AD bármely fizetős verziója.

## <a name="is-pass-through-authentication-available-in-microsoft-cloud-germanyhttpwwwmicrosoftdecloud-deutschland-and-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>A érhető áteresztő hitelesítés [Microsoft Cloud Németország](http://www.microsoft.de/cloud-deutschland) és [Microsoft Azure Government felhő](https://azure.microsoft.com/features/gov/)?

Nem, áteresztő hitelesítés csak akkor az Azure AD világszerte példányát.

## <a name="does-conditional-accessactive-directory-conditional-access-azure-portalmd-work-with-pass-through-authentication"></a>Does [feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md) munkahelyi átmenő hitelesítést?

Igen, az összes feltételes hozzáférés lehetőségekről, beleértve a Azure multi-factor Authentication áteresztő hitelesítés működik.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Támogatja áteresztő hitelesítés "A másik azonosító", a felhasználónév helyett a "userPrincipalName"?

Igen. Áteresztő hitelesítés támogat `Alternate ID` konfigurálásakor az Azure AD Connectben látható felhasználónévként [Itt](active-directory-aadconnect-get-started-custom.md). Nem minden Office 365-alkalmazások támogatják a `Alternate ID`. Tekintse meg a támogatási nyilatkozattal az adott alkalmazás dokumentációját.

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>Jelszókivonat-szinkronizálást áteresztő hitelesítés fallback összekötőként?

Nem, áteresztő hitelesítés _nem_ automatikusan feladatátvétel Jelszókivonat-szinkronizálást. Csak a tartalékként működik [szolgáltatásokat, amelyek jelenleg nem támogatja az áteresztő hitelesítés](active-directory-aadconnect-pass-through-authentication-current-limitations.md#unsupported-scenarios). Felhasználói bejelentkezési hibák elkerülése érdekében, konfigurálnia kell az áteresztő hitelesítés [magas rendelkezésre állású](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="can-i-install-an-azure-ad-application-proxyactive-directory-application-proxy-get-startedmd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Telepíthető egy [az Azure AD-alkalmazásproxy](../active-directory-application-proxy-get-started.md) összekötő áteresztő hitelesítés megbízottként ugyanazon a kiszolgálón?

Igen, ez a konfiguráció az áteresztő hitelesítés ügynök rebranded verzióiban támogatott (1.5.193.0 verzió vagy újabb).

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Az Azure AD Connect és az áteresztő hitelesítés ügynök mely verzióit van szüksége?

1.1.486.0 verziójára van szükség, vagy később az Azure AD Connect és 1.5.58.0 vagy később a áteresztő hitelesítés ügynököt, hogy ez a szolgáltatás működéséhez. Minden szoftver a Windows Server 2012 R2 vagy újabb kiszolgálókon kell telepíteni.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-using-pass-through-authentication"></a>Mi történik, ha a felhasználó jelszava lejárt, és próbáljon meg bejelentkezni a áteresztő hitelesítés használatával?

Ha már konfigurált [jelszóvisszaírás](../active-directory-passwords-update-your-own-password.md) egy adott felhasználó, és ha a felhasználó bejelentkezésekor átmenő hitelesítést használ, módosítsa vagy visszaállíthassák a jelszavukat. A jelszavak vissza a helyszíni Active Directory megfelelően kerülnek.

Azonban ha egy adott felhasználó nincs konfigurálva a jelszóvisszaírás, vagy ha a felhasználó nem rendelkezik egy érvényes Azure AD-licenccel, a felhasználó nem tudja frissíteni a jelszavát, a felhőben. Azok a jelszavát nem frissíthető, még akkor is, ha a jelszó lejárt. A felhasználó ehelyett ezt az üzenetet látja: "a szervezet nem engedélyezi, hogy frissítse a jelszavát, ezen a helyen. Adja a szervezete által ajánlott módszer a frissítést, vagy kérje meg a rendszergazdát, ha segítségre van szüksége." A felhasználó vagy a rendszergazda rendelkezik jelszavuk a helyszíni Active Directoryban.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Hogyan áteresztő hitelesítés elleni védelemre találgatásos jelszó támadások ellen?

Olvasási [Ez a cikk](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) további információt.

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Mi tegye áteresztő hitelesítés ügynökök kommunikálni 80-as és 443-as porton keresztül?

- A hitelesítési ügynökök ellenőrizze a HTTPS-kéréseket a szolgáltatás minden műveletet a 443-as porton keresztül.
- A hitelesítési ügynökök ellenőrizze a HTTP-kérelmek SSL-tanúsítvány visszavonási listák letöltése 80-as porton keresztül.

     >[!NOTE]
     >A legújabb frissítéseket azt a szolgáltatás szükséges portok száma csökken. Ha az Azure AD Connect vagy a hitelesítési ügynök régebbi verzióját, megnyitva, ezeket a portokat is: 5671, 8080-as, 9090, 9091, 9350, 9352 és 10100-10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Az áteresztő hitelesítés ügynökök kommunikálhatnak egy kimenő proxy webkiszolgálón keresztül?

Igen. Ha WPAD (Web Proxy Auto-Discovery) engedélyezve van a helyszíni környezetben, a hitelesítési ügynökök automatikusan megpróbálja keresse meg és webes proxykiszolgáló használata a hálózaton.

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Két vagy több áteresztő hitelesítés ügynök telepíthető ugyanarra a kiszolgálóra?

Nem, csak telepíthető egy csatlakoztatott hitelesítési ügynök egyetlen kiszolgálóra. Ha azt szeretné, áteresztő hitelesítés konfigurálása a magas rendelkezésre állású, kövesse az utasításokat, ez a [cikk](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability) helyette.

## <a name="i-already-use-active-directory-federation-services-ad-fs-for-azure-ad-sign-in-how-do-i-switch-it-to-pass-through-authentication"></a>Már az Active Directory összevonási szolgáltatások (AD FS) az Azure AD-bejelentkezés. Hogyan váltani az áteresztő hitelesítés?

Ha konfigurálta az AD FS legyen a bejelentkezési módszer, az Azure AD Connect varázsló segítségével, módosítsa a felhasználói bejelentkezési módszer áteresztő hitelesítés. Ez a módosítás lehetővé teszi, hogy átmenő hitelesítést a tenant, és konvertálja _összes_ a felügyelt tartományok külső tartományait. Az összes további bejelentkezési kérelmet az Ön bérelt szolgáltatásának áteresztő hitelesítés kezeli. Jelenleg nem támogatott az Azure AD Connect belül használatának módja az AD FS és az áteresztő hitelesítés különböző tartományokban.

Ha az AD FS bejelentkezési módszer konfigurálása _kívül_ az Azure AD Connect varázsló módosítsa a felhasználói bejelentkezési módszer áteresztő hitelesítés (a "Nem konfigurálása" lehetőséget). Ez a módosítás lehetővé teszi, hogy átmenő hitelesítést a tenant. Azonban a külső tartományok továbbra is az AD FS a bejelentkezéshez. PowerShell használatával manuálisan vagy egésze a külső tartományok átalakítása felügyelt tartományok. Ezt követően minden bejelentkezési kérelmek felügyelt tartományok (_csak_) áteresztő hitelesítés kezeli.

>[!IMPORTANT]
>Áteresztő hitelesítés nem kezeli az bejelentkezés csak felhőalapú Azure Active Directory-felhasználók.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-ad-environment"></a>Használható átmenő hitelesítés AD Többerdős környezetben?

Igen. Többerdős környezetben támogatottak, ha a AD erdők között erdőszintű megbízhatóság, és ha névutótag megfelelően van beállítva.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Hány áteresztő hitelesítés ügynök telepítéséhez van?

Több áteresztő hitelesítés ügynökök telepítése biztosítja [magas rendelkezésre állású](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability). Azonban nem biztosítják a hitelesítési ügynökök között determinisztikus terheléselosztást.

Fontolja meg a maximális és átlagos betöltési bejelentkezési kérelmek várható tekintse meg a bérlő. Egy alapként hitelesítési ügynököt 300,000 való 400000 hitelesítések másodpercenként egy standard 4 magos processzor, 16 GB RAM-kiszolgáló képes kezelni. A legtöbb felhasználó esetén két vagy három hitelesítési ügynökök összesen elegendőek a magas rendelkezésre álláshoz és kapacitásának.

Azt javasoljuk, hogy telepíti a hitelesítési ügynököt megközelíti a bejelentkezési késés javítása érdekében a tartományvezérlőket.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Az első áteresztő hitelesítés ügynök telepíthető más Azure AD Connect futtató kiszolgálón?

Nem, ez a forgatókönyv van _nem_ támogatott.

## <a name="how-many-pass-through-authentication-agents-should-i-install"></a>Hány áteresztő hitelesítés ügynököt kell telepíteni?

Azt javasoljuk, hogy:

- Két vagy három hitelesítési ügynökök teljes telepítése. Ez a konfiguráció is elegendő a legtöbb felhasználó.
- A tartományvezérlőkre telepíthető hitelesítési ügynökök (vagy megközelíti a lehető azok regisztrációja, mivel) bejelentkezési késés javítása érdekében.
- Biztosíthatja, hogy a kiszolgáló (amelyen hitelesítési ügynökök telepítve vannak) hozzáadva a felhasználókat, amelyeknek a jelszavánál kell érvényesíteni eltérő AD-erdőben.

>[!NOTE]
>A rendszer korlátainak 12 hitelesítési ügynökök bérlőnként van.

## <a name="how-can-i-disable-pass-through-authentication"></a>Hogyan letilthatja az áteresztő hitelesítés?

Az Azure AD Connect varázsló ismételt futtatása, és módosítsa a felhasználói bejelentkezési módszer az áteresztő hitelesítés valamilyen más módszerre. Ez letiltja az átmenő hitelesítést a tenant és a hitelesítési ügynök eltávolítása a kiszolgálóról. Akkor manuálisan kell eltávolítania a hitelesítési ügynökök más kiszolgálókról.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Mi történik, ha távolítható el egy átmenő hitelesítés ügynök?

Egy csatlakoztatott hitelesítési ügynök eltávolítása a kiszolgálóról hatására úgy, hogy a bejelentkezési kérelmek fogadását. Győződjön meg arról, hogy egy másik hitelesítési ügynök fut, ez a művelet végrehajtása előtt a bérlő a felhasználói bejelentkezés megtörje elkerülése érdekében.

## <a name="next-steps"></a>Következő lépések
- [**Aktuális korlátozások** ](active-directory-aadconnect-pass-through-authentication-current-limitations.md) – ismerje meg, melyik forgatókönyvek is támogatottak, és melyek nem.
- [**Gyors üzembe helyezési** ](active-directory-aadconnect-pass-through-authentication-quick-start.md) - létrehozásához, és fut az Azure AD áteresztő hitelesítés.
- [**Intelligens zárolás** ](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) -intelligens zárolás konfigurálása képességet a bérlő a felhasználói fiókok védelme.
- [**Műszaki mélyreható** ](active-directory-aadconnect-pass-through-authentication-how-it-works.md) – Ez a funkció működésének megismerése.
- [**Hibaelhárítás** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) -Útmutató: a szolgáltatással kapcsolatos gyakori problémák megoldása.
- [**Biztonsági mélyreható** ](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) -részletes műszaki további információ a szolgáltatásról.
- [**Az Azure AD zökkenőmentes SSO** ](active-directory-aadconnect-sso.md) -további információ a kiegészítő funkció.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új funkciókérések tárolásához.

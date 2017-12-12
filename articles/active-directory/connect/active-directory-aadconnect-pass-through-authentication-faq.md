---
title: "Az Azure AD Connect: Áteresztő hitelesítés – gyakori kérdések |} Microsoft Docs"
description: "Azure Active Directory áteresztő hitelesítés gyakran feltett kérdésekre adott válaszok"
services: active-directory
keywords: "Az Azure AD Connect áteresztő hitelesítés, telepítés Active Directory szükséges összetevőket az Azure AD, SSO, egyszeri bejelentkezést."
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: billmath
ms.openlocfilehash: 12ebfdfaaf9325ba57fe3972ee073fa5181cdbff
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Az Azure Active Directory áteresztő hitelesítés: Gyakran ismételt kérdések

Ez a cikk foglalkozik az Azure Active Directory (Azure AD) áteresztő hitelesítés kapcsolatos gyakori kérdésekre. Vissza a frissített tartalom ellenőrizni.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Amely bejelentkezni az Azure AD, áteresztő hitelesítési módszerek jelszó kivonat-szinkronizálás és az Active Directory összevonási szolgáltatások (AD FS), célszerű választani?

Ez a helyszíni környezetben és a szervezeti követelményektől függ. Tekintse át a [felhasználói bejelentkezés lehetőségei az Azure AD Connect](active-directory-aadconnect-user-signin.md) cikk összehasonlítása a különböző módszerek az Azure AD-bejelentkezés.

## <a name="is-pass-through-authentication-a-free-feature"></a>Az áteresztő hitelesítés szabad szolgáltatás?

Áteresztő hitelesítés egy ingyenes szolgáltatás. A használatára az Azure AD bármely fizetős verziója nem szükséges.

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Áteresztő hitelesítés is elérhető a [Microsoft Azure Németország felhő](http://www.microsoft.de/cloud-deutschland) és a [a Microsoft Azure Government felhő](https://azure.microsoft.com/features/gov/)?

Nem. Áteresztő hitelesítés csak érhető el a világméretű az Azure AD-példányban.

## <a name="does-conditional-accessactive-directory-conditional-access-azure-portalmd-work-with-pass-through-authentication"></a>Does [feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md) munkahelyi átmenő hitelesítést?

Igen. Minden feltételes hozzáférés lehetőségekről, beleértve a Azure multi-factor Authentication áteresztő hitelesítés használata.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Támogatja áteresztő hitelesítés "A másik azonosító", a felhasználónév helyett a "userPrincipalName"?

Igen. Áteresztő hitelesítés támogat `Alternate ID` az Azure AD Connectben konfigurálásakor felhasználóneveként. További információkért lásd: [az Azure AD Connect egyéni telepítési](active-directory-aadconnect-get-started-custom.md). Nem minden Office 365-alkalmazások támogatják a `Alternate ID`. Tekintse meg a kérdéses alkalmazás dokumentáció támogatási nyilatkozattal.

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>Jelszókivonat-szinkronizálást áteresztő hitelesítés fallback összekötőként?

Nem. Áteresztő hitelesítés _nem_ automatikusan feladatátvétel Jelszókivonat-szinkronizálást. Csak a tartalékként működik [szolgáltatásokat, amelyek jelenleg nem támogatja az áteresztő hitelesítés](active-directory-aadconnect-pass-through-authentication-current-limitations.md#unsupported-scenarios). Felhasználói bejelentkezési hibák elkerülése érdekében, konfigurálnia kell az áteresztő hitelesítés [magas rendelkezésre állású](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="can-i-install-an-azure-ad-application-proxyactive-directory-application-proxy-get-startedmd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Telepíthető egy [az Azure AD-alkalmazásproxy](../active-directory-application-proxy-get-started.md) összekötő áteresztő hitelesítés megbízottként ugyanazon a kiszolgálón?

Igen. A csatlakoztatott hitelesítési ügynök verziója 1.5.193.0 rebranded verziói vagy újabb rendszert támogatja ezt a konfigurációt.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Az Azure AD Connect és az áteresztő hitelesítés ügynök mely verzióit van szüksége?

Ez a funkció használatához, 1.1.486.0 verziójára van szükség, vagy később az Azure AD Connect és 1.5.58.0, vagy később az áteresztő hitelesítés ügynök számára. A szoftver telepítése a kiszolgálókon Windows Server 2012 R2 vagy újabb verziója.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>Mi történik, ha a felhasználó jelszava lejárt, és próbáljon meg bejelentkezni áteresztő hitelesítés használatával?

Ha már konfigurált [jelszóvisszaírás](../active-directory-passwords-update-your-own-password.md) egy adott felhasználó, valamint, ha a felhasználó áteresztő hitelesítés használatával, módosítása és visszaállíthassák a jelszavukat. A jelszavak vissza a helyszíni Active Directory megfelelően kerülnek.

Ha nem állított be egy adott felhasználó jelszóvisszaírás, vagy ha a felhasználó nem rendelkezik egy érvényes Azure AD-licenccel, a felhasználó nem tudja frissíteni a jelszavát, a felhőben. A jelszavát, azokat nem lehet frissíteni, akkor is, ha a jelszó lejárt. A felhasználó ehelyett ezt az üzenetet látja: "a szervezet nem engedélyezi, hogy frissítse a jelszavát, ezen a helyen. A szervezete által ajánlott módszer a frissítést, vagy kérje meg a rendszergazdát, ha segítségre van szüksége." A felhasználó vagy a rendszergazda-kód alaphelyzetbe állítását a jelszavát a helyszíni Active Directoryban.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Hogyan áteresztő hitelesítés elleni védelemre jelszó találgatásos támadások?

Olvasási [Azure Active Directory áteresztő hitelesítés: az intelligens zárolás](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) további információt.

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Mi tegye áteresztő hitelesítés ügynökök kommunikálni 80-as és 443-as porton keresztül?

- A hitelesítési ügynökök ellenőrizze a HTTPS-kéréseket a szolgáltatás minden műveletet a 443-as porton keresztül.
- A hitelesítési ügynökök HTTP-kérelmek ellenőrizze az SSL visszavont tanúsítványok listáját (CRL) letöltése a 80-as porton keresztül.

     >[!NOTE]
     >A legújabb frissítéseket kevesebb a szolgáltatás által igényelt portokat. Ha az Azure AD Connect vagy a hitelesítési ügynök régebbi verzióját, a portok nyitva hagyja, valamint: 5671, 8080-as, 9090, 9091, 9350, 9352 és 10100-10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Az áteresztő hitelesítés ügynökök kommunikálhatnak egy kimenő proxy webkiszolgálón keresztül?

Igen. Webes Proxy automatikus felderítését a lekérdezés (WPA) engedélyezve van a helyszíni környezetben, ha a hitelesítési ügynökök automatikusan megpróbálja megkeresni és webes proxykiszolgáló használata a hálózaton.

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Két vagy több áteresztő hitelesítés ügynök telepíthető ugyanarra a kiszolgálóra?

Nem, csak telepíthető egy csatlakoztatott hitelesítési ügynök egyetlen kiszolgálóra. Ha szeretne áteresztő hitelesítés konfigurálása a magas rendelkezésre állású, kövesse az utasításokat a [Azure Active Directory áteresztő hitelesítés: gyors üzembe helyezési](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Már alkalmazni az AD FS az Azure AD bejelentkezés. Hogyan váltani az áteresztő hitelesítés?

Ha konfigurálta az AD FS az Azure AD Connect varázsló lépéseinek bejelentkezni az módszerként, módosítsa a a módszerrel, amely a felhasználó bejelentkezni az áteresztő hitelesítés. Ez a módosítás lehetővé teszi, hogy átmenő hitelesítést a tenant, és konvertálja _összes_ az összevont tartományt a felügyelt tartományok. Áteresztő hitelesítés bejelentkezni a bérlő az összes további kérelmet kezeli. Jelenleg nem támogatott az Azure AD Connect belül használatának módja az AD FS és az áteresztő hitelesítés különböző tartományokban.

Ha az AD FS konfigurálása a bejelentkezéshez módszerként _kívül_ az Azure AD Connect varázsló, a módszer váltása a felhasználói bejelentkezés áteresztő hitelesítés. A módosítást a a **ne konfiguráljon** lehetőséget. Ez a módosítás lehetővé teszi, hogy a áteresztő hitelesítés a tenant, de továbbra is az összevont tartományt használja az AD FS bejelentkezési. PowerShell használatával manuálisan vagy azok egy részét a összevont tartományt átalakítása felügyelt tartományok. Miután a módosítás, *csak* áteresztő hitelesítés kezeli az összes kérelmet, jelentkezzen be a felügyelt tartományok.

>[!IMPORTANT]
>Áteresztő hitelesítés nem kezeli az bejelentkezés csak felhőalapú Azure Active Directory-felhasználók.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Használható átmenő hitelesítés Active Directory Többerdős környezetben?

Igen. Többerdős környezetben támogatottak, ha az Active Directory-erdők között erdőszintű megbízható kapcsolatokon, és ha névutótag megfelelően van beállítva.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Hány áteresztő hitelesítés ügynök telepítéséhez van?

Több áteresztő hitelesítés ügynökök telepítése biztosítja [magas rendelkezésre állású](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability). Azonban nem biztosítják a hitelesítési ügynökök között determinisztikus terheléselosztást.

Fontolja meg a maximális és átlagos betöltési bejelentkezési kérelmek várható tekintse meg a bérlő. Egy alapként hitelesítési ügynököt képes kezelni másodpercenként egy standard 4 magos processzor, 16 GB RAM server 300-400 hitelesítések.

Hálózati forgalom becslése, használja a következő olvasható méretezési Útmutató:
- Minden egyes kérelem rendelkezik a tartalom méretét (0,5 KB + 1 K * num_of_agents) bájt; például adatokat az Azure AD a hitelesítési ügynök. "Num_of_agents" jelzi a bérlő regisztrált hitelesítési ügynökök.
- Minden válasz korlátja hasznos 1 KB; például adatait a hitelesítési ügynök az Azure ad Szolgáltatásba.

A legtöbb felhasználó esetén két vagy három hitelesítési ügynökök összesen elegendőek a magas rendelkezésre álláshoz és kapacitásának. Hitelesítési ügynökök telepítse megközelíti a bejelentkezési késés javítása érdekében a tartományvezérlőket.

>[!NOTE]
>A rendszer korlátainak 12 hitelesítési ügynökök bérlőnként van.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Az első áteresztő hitelesítés ügynök telepíthető más Azure AD Connect futtató kiszolgálón?

Nem, ez a forgatókönyv van _nem_ támogatott.

## <a name="how-can-i-disable-pass-through-authentication"></a>Hogyan letilthatja az áteresztő hitelesítés?

Az Azure AD Connect varázsló ismételt futtatása, és módosítsa a felhasználói bejelentkezési módszer az áteresztő hitelesítés valamilyen más módszerre. Ez letiltja az átmenő hitelesítést a tenant és a hitelesítési ügynök eltávolítása a kiszolgálóról. A hitelesítési ügynököt manuálisan kell eltávolítania, a kiszolgálókról.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Mi történik, ha távolítható el egy átmenő hitelesítés ügynök?

Ha egy csatlakoztatott hitelesítési ügynök eltávolítása a kiszolgálóról, azt eredményezi, a kiszolgáló bejelentkezési kérelmek fogadását. Ha el szeretné kerülni a felhasználói bejelentkezési képesség a tenant, ellenőrizze, hogy egy másik hitelesítési ügynök fut egy csatlakoztatott hitelesítési ügynök eltávolítása előtt.

## <a name="next-steps"></a>Következő lépések
- [Aktuális korlátozások](active-directory-aadconnect-pass-through-authentication-current-limitations.md): megtudhatja, mely forgatókönyvek is támogatottak, és melyek nem.
- [Gyors üzembe helyezési](active-directory-aadconnect-pass-through-authentication-quick-start.md):, amelyekből megismerheti az Azure AD áteresztő hitelesítés.
- [Intelligens zárolás](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): megtudhatja, hogyan konfigurálja az intelligens zárolás funkció a bérlő felhasználói fiókok védelme.
- [Műszaki mélyreható](active-directory-aadconnect-pass-through-authentication-how-it-works.md): a csatlakoztatott hitelesítési szolgáltatás működésének megismerése.
- [Hibaelhárítás](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Útmutató: az áteresztő hitelesítés szolgáltatás kapcsolatos gyakori problémák megoldásához.
- [Biztonsági mélyreható](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): részletes műszaki információért az áteresztő hitelesítés szolgáltatást.
- [Az Azure AD zökkenőmentes SSO](active-directory-aadconnect-sso.md): további információk a kiegészítő funkció.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): az Azure Active Directory fórumán használja a következő fájl új frissítéseiről.


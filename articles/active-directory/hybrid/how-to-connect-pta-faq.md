---
title: 'Azure AD Connect: Átmenő hitelesítés – gyakori kérdések | Microsoft dokumentumok'
description: Válaszok az Azure Active Directory áterdes hitelesítésével kapcsolatos gyakori kérdésekre
services: active-directory
keywords: Azure AD Connect áthaladási hitelesítés, active directory telepítése, szükséges összetevők az Azure AD, Egyszeri bejelentkezés, Egyszeri bejelentkezés hez
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59bf7ae5914f5cc886d95f25b36abccfdf09c4a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331289"
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Azure Active Directory átadó hitelesítés: gyakori kérdések

Ez a cikk az Azure Active Directory (Azure AD) átadó hitelesítésével kapcsolatos gyakori kérdéseket ismerteti. Nézz vissza a frissített tartalomért.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Az Azure AD-be, az átadó hitelesítésbe, a jelszókivonat-szinkronizálásba és az Active Directory összevonási szolgáltatásokba (AD FS) való bejelentkezés melyik módszert válasszam?

Tekintse át ezt az [útmutatót](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) a különböző Azure AD bejelentkezési módszerek összehasonlításához, és hogyan válassza ki a megfelelő bejelentkezési módszert a szervezet számára.

## <a name="is-pass-through-authentication-a-free-feature"></a>Az áthaladási hitelesítés ingyenes funkció?

Az átmenő hitelesítés ingyenes funkció. Az Azure AD fizetős kiadásaihasználatára nincs szükség.

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloud-and-the-microsoft-azure-government-cloud"></a>Elérhető az átmenő hitelesítés a [Microsoft Azure Germany felhőben](https://www.microsoft.de/cloud-deutschland) és a [Microsoft Azure Government felhőben?](https://azure.microsoft.com/features/gov/)

Nem. Az átmenő hitelesítés csak az Azure AD világméretű példányában érhető el.

## <a name="does-conditional-access-work-with-pass-through-authentication"></a>Működik [a feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md) az átmenő hitelesítéssel?

Igen. Minden feltételes hozzáférési képesség, beleértve az Azure többtényezős hitelesítést is, az átadó hitelesítéssel működik.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Az átmenő hitelesítés támogatja az "Alternatív azonosító" nevet a "userPrincipalName" helyett?
A bejelentkezés nem egyszerű felhasználóhálózati értékkel, például egy másodlagos e-maillel történik, jelenleg privát előzetes verzióban tesztelik mind az átadó hitelesítést (PTA), mind a jelszókivonat-szinkronizálást (PHS).

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>A jelszókivonat-szinkronizálás tartalékként működik az átmenő hitelesítéshez?

Nem. Az átadó hitelesítés _nem adja_ át automatikusan a jelszókivonat-szinkronizálást. A felhasználói bejelentkezési hibák elkerülése érdekében konfigurálnia kell az átmenő hitelesítést [a magas rendelkezésre álláshoz.](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="what-happens-when-i-switch-from-password-hash-synchronization-to-pass-through-authentication"></a>Mi történik, ha a jelszókivonat-szinkronizálásról átmenő hitelesítésre váltok?

Amikor az Azure AD Connect használatával vált át a bejelentkezési módszert a jelszókivonat-szinkronizálásról átmenő hitelesítésre, az átmenő hitelesítés lesz a felügyelt tartományok felhasználóinak elsődleges bejelentkezési módszere. Kérjük, vegye figyelembe, hogy a jelszókivonatokkal korábban szinkronizált összes felhasználó jelszókivonata az Azure AD-n marad.

## <a name="can-i-install-an-azure-ad-application-proxy-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Telepíthetek egy [Azure AD alkalmazásproxy-összekötőt](../manage-apps/application-proxy.md) ugyanarra a kiszolgálóra, mint egy átmenő hitelesítési ügynök?

Igen. Az átmenő hitelesítési ügynök átnevezett verziói, az 1.5.193.0-s vagy újabb verziók támogatják ezt a konfigurációt.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Az Azure AD Connect és az átmenő hitelesítési ügynök mely verzióira van szüksége?

Ahhoz, hogy ez a funkció működjön, az Azure AD Connect hez és az 1.5.193.0-s vagy újabb verzióhoz az Átmenő hitelesítési ügynökhöz az 1.1.750.0-s vagy újabb verzióra van szükség. Telepítse az összes szoftvert a Windows Server 2012 R2 vagy újabb rendszert tartalmazó kiszolgálókra.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>Mi történik, ha a felhasználóm jelszava lejárt, és áteres hitelesítéssel próbálnak bejelentkezni?

Ha egy adott felhasználóhoz beállította a [jelszó-visszaírást,](../authentication/concept-sspr-writeback.md) és a felhasználó átmenő hitelesítéssel jelentkezik be, módosíthatja vagy alaphelyzetbe állíthatja a jelszavait. A jelszavak a várt módon kerülnek vissza a helyszíni Active Directoryba.

Ha nem konfigurált a jelszó-visszaírás egy adott felhasználó, vagy ha a felhasználó nem rendelkezik érvényes Azure AD-licenc hozzárendeléssel, a felhasználó nem tudja frissíteni a jelszót a felhőben. Nem tudják frissíteni a jelszavukat, még akkor sem, ha a jelszavuk lejárt. A felhasználó ehelyett ezt az üzenetet látja: "A szervezet nem engedélyezi a jelszó frissítését ezen a webhelyen. Frissítse a szervezet által ajánlott módszer szerint, vagy kérdezze meg a rendszergazdát, ha segítségre van szüksége." A felhasználónak vagy a rendszergazdának alaphelyzetbe kell állítania a jelszavát a helyszíni Active Directoryban.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Hogyan védi az átmenő hitelesítés a találgatásos jelszótámadásokat?

[Olvassa el a Smart Lockout](../authentication/howto-password-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Mit kommunikálnak az átmenő hitelesítési ügynökök a 80-as és a 443-as porton keresztül?

- A hitelesítési ügynökök https-kérelmeket a 443-as porton keresztül az összes szolgáltatásművelethez.
- A hitelesítési ügynökök http-kérelmeket küldanek a 80-as porton keresztül a TLS/SSL tanúsítvány-visszavonási listák (CRLs) letöltéséhez.

     >[!NOTE]
     >A legutóbbi frissítések csökkentették a szolgáltatás által igényelt portok számát. Ha az Azure AD Connect vagy a hitelesítési ügynök régebbi verzióival rendelkezik, tartsa nyitva ezeket a portokat is: 5671, 8080, 9090, 9091, 9350, 9352 és 10100-10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Az átmenő hitelesítési ügynökök kommunikálhatnak egy kimenő webproxy-kiszolgálón keresztül?

Igen. Ha a webproxy automatikus felderítése (WPAD) engedélyezve van a helyszíni környezetben, a hitelesítési ügynökök automatikusan megkísérlik megkeresni és használni a hálózaton lévő webproxy-kiszolgálót.

Ha nincs WPAD a környezetben, hozzáadhat proxyadatokat (az alábbiak szerint), hogy egy átadó hitelesítési ügynök kommunikáljon az Azure AD-vel:
- Mielőtt telepíti az átmenő hitelesítési ügynököt a kiszolgálóra, konfigurálja a proxyadatokat az Internet Explorer programban. Ez lehetővé teszi a hitelesítési ügynök telepítésének befejezését, de továbbra **is inaktívként** jelenik meg a felügyeleti portálon.
- A kiszolgálón keresse meg a "C:\Program Files\Microsoft Azure AD Connect authentication agent" mappát.
- Szerkessze az "AzureADConnectAuthenticationAgentService" konfigurációs fájlt,\:és adja hozzá a következő sorokat (cserélje le a "http //contosoproxy.com:8080" szót a tényleges proxycímre):

```
   <system.net>
      <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy
            usesystemdefault="true"
            proxyaddress="http://contosoproxy.com:8080"
            bypassonlocal="true"
         />
     </defaultProxy>
   </system.net>
```

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Telepíthetek két vagy több átmenő hitelesítési ügynököt ugyanarra a kiszolgálóra?

Nem, csak egy átmenő hitelesítési ügynököt telepíthet egyetlen kiszolgálóra. Ha az átmenő hitelesítést magas rendelkezésre állásra szeretné konfigurálni, [kövesse az itt található utasításokat.](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="do-i-have-to-manually-renew-certificates-used-by-pass-through-authentication-agents"></a>Manuálisan kell megújítanom az átmenő hitelesítési ügynökök által használt tanúsítványokat?

Az egyes átmenő hitelesítési ügynök és az Azure AD közötti kommunikáció tanúsítványalapú hitelesítéssel védett. Ezeket [a tanúsítványokat az Azure AD néhány havonta automatikusan megújítja.](how-to-connect-pta-security-deep-dive.md#operational-security-of-the-authentication-agents) Nincs szükség a tanúsítványok manuális megújítására. Szükség szerint megtisztíthatja a régebbi lejárt tanúsítványokat.

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Hogyan távolíthatok el átmenő hitelesítési ügynököt?

Mindaddig, amíg egy átadó hitelesítési ügynök fut, aktív marad, és folyamatosan kezeli a felhasználói bejelentkezési kérelmeket. Ha el szeretne távolítani egy hitelesítési ügynököt, nyissa meg a **Vezérlőpult > programok -> programok és szolgáltatások lapot,** és távolítsa el a **Microsoft Azure AD Connect hitelesítési ügynököt** és a **Microsoft Azure AD Connect Agent Updater** programokat.

Ha az előző lépés végrehajtása után ellenőrzi az átadó hitelesítés panelt az [Azure Active Directory felügyeleti központban,](https://aad.portal.azure.com) megjelenik a hitelesítési ügynök **inaktívként.** Ez _várható ._ A hitelesítési ügynök néhány nap múlva automatikusan lekerül a listáról.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Már az AD FS-t használom az Azure AD-be való bejelentkezéshez. Hogyan válthatok átmenő hitelesítésre?

Ha az AD FS-ről (vagy más összevonási technológiákról) átmenő hitelesítésre vándorol, javasoljuk, hogy kövesse [az itt](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true)közzétett részletes telepítési útmutatónkat.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Használhatom az átadó hitelesítést többerdős Active Directory környezetben?

Igen. A többerdős környezetek akkor támogatottak, ha az Active Directory-erdők között erdőszintű bizalmi kapcsolatok vannak, és ha a névutótag-útválasztás megfelelően van konfigurálva.

## <a name="does-pass-through-authentication-provide-load-balancing-across-multiple-authentication-agents"></a>Átmenő hitelesítés biztosít terheléselosztás több hitelesítési ügynökök között?

Nem, több átmenő hitelesítési ügynök telepítése csak [magas rendelkezésre állást](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)biztosít. Nem biztosít determinisztikus terheléselosztást a hitelesítési ügynökök között. Bármely hitelesítési ügynök (véletlenszerűen) feldolgozhat egy adott felhasználói bejelentkezési kérelmet.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Hány átmenő hitelesítési ügynököt kell telepítenem?

Több átmenő hitelesítési ügynök telepítése biztosítja a [magas rendelkezésre állást.](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) De nem biztosít determinisztikus terheléselosztás a hitelesítési ügynökök között.

Vegye figyelembe a bejelentkezési kérelmek csúcs- és átlagos terhelését, amelyek várhatóan a bérlőn jelennek meg. Viszonyítási alapként egyetlen hitelesítési ügynök képes kezelni 300–400 hitelesítésmásodpercenként egy szabványos 4-magos CPU, 16 GB RAM-kiszolgáló.

A hálózati forgalom becsléséhez használja a következő méretezési útmutatót:
- Minden kérelem hasznos mérete (0,5 K + 1K * num_of_agents) bájt; azaz az Azure AD-ből a hitelesítési ügynökre vonatkozó adatok. Itt a "num_of_agents" a bérlőn regisztrált hitelesítési ügynökök számát jelzi.
- Minden válasz hasznos mérete 1K bájt; azaz a hitelesítési ügynök től az Azure AD-ig.

A legtöbb ügyfél számára összesen két vagy három hitelesítési ügynök elegendő a magas rendelkezésre álláshoz és kapacitáshoz. A bejelentkezési késés javítása érdekében telepítse a tartományvezérlők közelében lévő hitelesítési ügynököket.

>[!NOTE]
>Bérlőnként 40 hitelesítési ügynök rendszerkorlát van.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Telepíthetem az első átmenő hitelesítési ügynököt az Azure AD Connectet futtató kiszolgálótól eltérő kiszolgálóra?

Nem, ez a forgatókönyv _nem_ támogatott.

## <a name="why-do-i-need-a-cloud-only-global-administrator-account-to-enable-pass-through-authentication"></a>Miért van szükségem csak felhőalapú globális rendszergazdai fiókra az átmenő hitelesítés engedélyezéséhez?

Javasoljuk, hogy engedélyezze vagy tiltsa le az átmenő hitelesítést egy csak felhőalapú globális rendszergazdai fiókhasználatával. További információ [a csak felhőalapú globális rendszergazdai fiók hozzáadásáról.](../active-directory-users-create-azure-portal.md) Ha így csinálja, akkor nem zárják ki a bérlőből.

## <a name="how-can-i-disable-pass-through-authentication"></a>Hogyan tudom letiltani az átmenő hitelesítést?

Futtassa újra az Azure AD Connect varázslót, és módosítsa a felhasználói bejelentkezési módszert áthaladási hitelesítésről egy másik metódusra. Ez a módosítás letiltja az átmenő hitelesítést a bérlőn, és eltávolítja a hitelesítési ügynököt a kiszolgálóról. A hitelesítési ügynököket manuálisan kell eltávolítania a többi kiszolgálóról.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Mi történik, ha eltávolítok egy átmenő hitelesítési ügynököt?

Ha egy átmenő hitelesítési ügynököt távolít el egy kiszolgálóról, a kiszolgáló leállítja a bejelentkezési kérelmek fogadását. A bérlő bejelentkezési képességének megtörésének elkerülése érdekében győződjön meg arról, hogy egy másik hitelesítési ügynök fut, mielőtt eltávolítana egy átmenő hitelesítési ügynököt.

## <a name="i-have-an-older-tenant-that-was-originally-setup-using-ad-fs--we-recently-migrated-to-pta-but-now-are-not-seeing-our-upn-changes-synchronizing-to-azure-ad--why-are-our-upn-changes-not-being-synchronized"></a>Van egy régebbi bérlő, amely eredetileg beállító segítségével AD FS.  Nemrég áttelepítettük a PTA-ba, de most nem látjuk az UpN-módosításokszinkronizálását az Azure AD-vel.  Miért nincsszinkronizálva az upn-módosítások szinkronizálása?

A: A következő körülmények között előfordulhat, hogy a helyszíni upn-módosítások nem szinkronizálódnak, ha:

- Az Azure AD-bérlő 2015.
- Kezdetben az Azure AD-bérlővel lett összeegyítve az AD FS használatával a hitelesítéshez
- Váltott, hogy a felügyelt felhasználók pta hitelesítésként használják

Ennek az az oka, hogy a 2015 június 15-e előtt létrehozott bérlők alapértelmezett viselkedése az UPN-módosítások blokkolása volt.  Ha le kell tiltania az UPN-módosításokat, a következő PowerShell-parancslasztit kell futtatnia:  

`Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $True`

2015. június 15-e után létrehozott bérlők alapértelmezett viselkedése az upn-módosítások szinkronizálása.   



## <a name="next-steps"></a>További lépések
- [Jelenlegi korlátozások](how-to-connect-pta-current-limitations.md): Ismerje meg, hogy mely forgatókönyvek támogatottak, és melyek nem.
- [Gyors indítás:](how-to-connect-pta-quick-start.md)Az Azure AD áthaladási hitelesítése.
- [Az AD FS-ről átmenő hitelesítésre való áttelepítés](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) részletes útmutató az AD FS-ről (vagy más összevonási technológiákról) az átmenő hitelesítésre való áttelepítéshez.
- [Intelligens zárolás:](../authentication/howto-password-smart-lockout.md)Ismerje meg, hogyan konfigurálhatja a bérlő intelligens zárolási képességét a felhasználói fiókok védelme érdekében.
- [Technikai mélymerülés:](how-to-connect-pta-how-it-works.md)Ismerje meg, hogyan működik az átmenő hitelesítés funkció.
- [Hibaelhárítás:](tshoot-connect-pass-through-authentication.md)Ismerje meg, hogyan oldhatja meg az átmenő hitelesítés szolgáltatással kapcsolatos gyakori problémákat.
- [Biztonsági mélymerülés:](how-to-connect-pta-security-deep-dive.md)Részletes technikai információk beszerezhető az átmenő hitelesítés funkcióról.
- [Azure AD seamless egyszeri bejelentkezés:](how-to-connect-sso.md)További információ erről a kiegészítő funkcióról.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Az Azure Active Directory fórum használatával új szolgáltatáskérelmek et nyújthat be.


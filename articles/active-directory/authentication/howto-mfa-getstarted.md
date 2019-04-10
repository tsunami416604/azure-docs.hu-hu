---
title: Hajtsa végre az Azure multi-factor Authentication szolgáltatás üzembe helyezése – Azure Active Directory és megtervezése
description: A Microsoft Azure multi-factor Authentication üzembe helyezésének tervezése
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c2c5006eb050b70b783ab8199724e0e98766381
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358107"
---
# <a name="planning-a-cloud-based-azure-multi-factor-authentication"></a>A felhőalapú Azure multi-factor Authentication tervezése

Emberek egyre összetettebb forgatókönyveket a szervezeti erőforrásokhoz csatlakozik. A vállalat által birtokolt személyes és nyilvános eszközökről, kapcsolja ki a vállalati hálózat okostelefonon, táblagépen, számítógépek és laptopok, több platformon gyakran használatával kapcsolódnak a felhasználók. Ez mindig csatlakoztatva, több eszközre és többplatformos világában a felhasználói fiókok biztonságát fontos több, mint valaha. A jelszavak, függetlenül attól, hogy a használt eszközök, hálózat és a platformok összetettségük már nem elegendőek a felhasználói fiók védelme érdekében, különösen akkor, ha a felhasználók általában a jelszavak újbóli fiókok között. Kifinomult adathalászati és más pszichológiai manipuláció támadások felhasználónevek és jelszavak közzétett és a sötét webes értékesített eredményezhet.

[Az Azure multi-factor Authentication (MFA)](concept-mfa-howitworks.md) megkönnyíti adatokhoz és alkalmazásokhoz való biztonságos hozzáférést. Egy további hitelesítési második űrlap segítségével biztonsági réteget biztosít. Szervezetek használhatják a [feltételes hozzáférési](../conditional-access/overview.md) , hogy a megoldás az egyedi igényeknek.

## <a name="prerequisites"></a>Előfeltételek

Az Azure multi-factor Authentication szolgáltatás a telepítés megkezdése előtt nincsenek előfeltételként szükséges elemeknél tapasztalt kell figyelembe venni.

| Forgatókönyv | Előfeltétel |
| --- | --- |
| **Kizárólag felhőalapú** identitás környezetben a modern hitelesítéssel | **Nincsenek további előkészületi teendők elvégzése** |
| **Hibrid** identitás forgatókönyvek | [Az Azure AD Connect](../hybrid/whatis-hybrid-identity.md) üzemel, és a felhasználói identitások vannak szinkronizálva, vagy összevonva a helyszíni Active Directory Domain Services az Azure Active Directoryval. |
| A helyi cloud-access-hez közzétett örökölt alkalmazások | Az Azure AD [alkalmazásproxy](../manage-apps/application-proxy.md) üzemel. |
| Az Azure MFA RADIUS-hitelesítéssel | A [hálózati házirend-kiszolgáló (NPS)](howto-mfa-nps-extension.md) üzemel. |
| Felhasználók rendelkeznek a Microsoft Office 2010 vagy korábbi, vagy az Apple Mail az iOS 11 vagy korábbi | Frissítés a [a Microsoft Office 2013 vagy újabb](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) és az Apple mail 12. vagy újabb iOS-hez. Feltételes hozzáférés nem támogatja az örökölt hitelesítési protokollok. |

## <a name="plan-user-rollout"></a>Felhasználói bevezetésének megtervezése

Az MFA-bevezetési terv telepítési regisztrálást, a támogatási kapacitás belüli követ próbatelepítések tartalmaznia kell. A bevezetés során először a feltételes hozzáférési szabályzatok alkalmazása a próbafelhasználók kis csoportja számára. Milyen hatása a próbaüzem felhasználóinak, a folyamat használt, valamint a regisztrációs viselkedés kiértékelését, miután további csoportok felvétele a szabályzatba, vagy további felhasználókat vehet fel a meglévő csoportokhoz.

### <a name="user-communications"></a>Felhasználói kommunikáció során

Fontos, annak ellenére tájékoztassa a felhasználókat, a tervezett kommunikáció jövőbeni változtatásokról, az Azure MFA regisztrációs követelményeket és a szükséges felhasználói műveletek. Azt javasoljuk, hogy a szervezetben, mint például a kommunikációs, a változáskezelés vagy emberi erőforrások részleg képviselői-sel fejlesztett kommunikáció.

A Microsoft biztosít [kommunikációs sablonok](https://aka.ms/mfatemplates) és [végfelhasználói dokumentációt](../user-help/security-info-setup-signin.md) vázlatszintű a kommunikáció érdekében. Elküldheti a felhasználók számára [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com) kiválasztásával közvetlenül regisztrálni a **biztonsági adatok** erre az oldalra mutató hivatkozások.

## <a name="deployment-considerations"></a>Telepítési szempontok

Az Azure multi-factor Authentication van üzembe helyezve, a feltételes hozzáférési házirendeket tartat be. A [feltételes hozzáférési szabályzat](../conditional-access/overview.md) megkövetelhetik a felhasználóktól többtényezős hitelesítés végrehajtására, például bizonyos feltételek teljesülése esetén:

* Minden felhasználó, egy adott felhasználó, egy csoporthoz, vagy a hozzárendelt szerepkör tagja
* Egyes adott felhőalapú alkalmazás hozzáférésének
* Eszközplatform
* Eszköz állapota
* Hálózati hely vagy a geo-található IP-cím
* Ügyfélalkalmazások
* Bejelentkezési kockázati (Identity Protection igényel)
* Megfelelő eszköz
* Hibrid Azure AD-csatlakoztatott eszköz
* Jóváhagyott ügyfélalkalmazás
 

Használatával a testre szabható poszterek és e-mail sablonok [a multi-factor authentication szolgáltatás bevezetés anyagok] a többtényezős hitelesítés bevezetése a szervezet számára. (https://www.microsoft.com/en-us/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all)

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>A feltételes hozzáférés többtényezős hitelesítés engedélyezése

Feltételes hozzáférési szabályzatok kényszeríti a regisztrációt, a nem regisztrált pédául első bejelentkezés, egy fontos biztonsági teendő a regisztráció befejezéséhez.


[Az Azure AD Identity Protection](../identity-protection/howto-configure-risk-policies.md) járul hozzá a regisztrációs házirendet és a kockázati észlelése és eltávolítása szabályzatokat az Azure multi-factor Authentication történetet. Szabályzatok kényszerítése a jelszó módosítására, feltört identitás fenyegetés esetén hozhatók létre, és többtényezős hitelesítés kérése, amikor egy bejelentkezési sikertelennek kockázatos az alábbiak szerint [események](../reports-monitoring/concept-risk-events.md):

* Kiszivárgott hitelesítő adatok
* Bejelentkezések névtelen IP-címről
* Bejelentkezés szokatlan helyekről
* Bejelentkezések ismeretlen helyekről
* Bejelentkezések fertőzött eszközökről
* Gyanús tevékenységek IP-címekről indított bejelentkezések

Az Azure Active Directory Identity Protection által észlelt kockázati események némelyike valós időben történik, és a egy offline feldolgozást igényel. A rendszergazdák kiválaszthatják meggátolja a felhasználókat, akik mutatnak a kockázatos viselkedés, és manuálisan javítása, jelszómódosítás megkövetelése vagy a többtényezős hitelesítés megkövetelése a feltételes hozzáférési szabályzatok részeként.

## <a name="define-network-locations"></a>Hálózati helyek definiálása

Azt javasoljuk, hogy a szervezetek feltételes hozzáférés használata meghatározásához, hogy a hálózat használatával [nevesített helyek](../conditional-access/location-condition.md#named-locations). Ha a szervezet az Identity Protection, fontolja meg kockázatalapú szabályzatok nevesített helyek helyett.

### <a name="configuring-a-named-location"></a>Egy elnevezett hely konfigurálása

1. Nyissa meg **Azure Active Directory** az Azure Portalon
2. Kattintson a **feltételes hozzáférés**
3. Kattintson a **nevesített helyek**
4. Kattintson a **új helye**
5. Az a **neve** mezőbe, adjon meg egy kifejező nevet
6. Válassza ki, hogy a hely, IP-címtartományok vagy országok/régiók definiálása
   1. IP-címtartományok használatakor
      1. Döntse el, jelölje meg megbízhatóként a hely-e. A megbízható helyről történő bejelentkezésnél kisebb a felhasználó bejelentkezési kockázata. Csak akkor jelölje meg megbízhatóként ezen a helyen, ha tudja, hogy a megadott IP-címtartományok használata megalapozott és hihető a szervezetben.
      2. Adja meg az IP-címtartományok
   2. Ha használja az országok/régiók
      1. Bontsa ki a legördülő menüből, és válassza ki a országok vagy régiók ezen a helyen elnevezett meghatározni kívánt.
      2. Ismeretlen területek belefoglalása döntsön. Az ismeretlen területek olyan IP-címek, melyeket nem lehet országhoz/régióhoz hozzárendelni.
7. Kattintson a **Create** (Létrehozás) gombra

## <a name="plan-authentication-methods"></a>Hitelesítési módszerek tervezése

A rendszergazdák választhatják a [hitelesítési módszerek](../authentication/concept-authentication-methods.md) , hogy szeretné-e a felhasználók számára elérhetővé tenni. Fontos, hogy több mint egy egyetlen hitelesítési módszer, hogy a felhasználók egy biztonsági mentési módszer érhető el abban az esetben, ha az elsődleges módszer nem érhető el. Ahhoz, hogy a rendszergazdák az alábbi módszerek érhetők el:

### <a name="notification-through-mobile-app"></a>Értesítés mobilalkalmazáson keresztül

Leküldéses értesítés érkezik a Microsoft Authenticator alkalmazást a mobileszközén. A felhasználó megtekinti az értesítést, és kiválasztja **jóváhagyás** ellenőrzés befejezéséhez. Leküldéses értesítések, a mobilalkalmazáson keresztül a felhasználók számára zavaró legalább lehetőséget nyújt. Szerepelnek a legtöbb megbízható és biztonságos beállítás mivel telefonos helyett egy adatkapcsolatot használ.

> [!NOTE]
> Ha a szervezete személyzet dolgozik vagy utazás, Kína, a **értesítés mobilalkalmazáson keresztül** metódust **Android-eszközök** nem működik az adott országban. Alternatív módszerek ezen felhasználók számára elérhetővé kell tenni.

### <a name="verification-code-from-mobile-app"></a>Mobilalkalmazás ellenőrzőkódja

Egy olyan mobilalkalmazás, például a Microsoft Authenticator alkalmazást hoz létre egy új OATH-Ellenőrzőkód 30 másodpercenként. A felhasználó a bejelentkezési felületen kerül, az ellenőrző kódot. A mobilalkalmazás beállítás is használható, a telefonos adatok vagy mobilhálózati jel rendelkezik-e.

### <a name="call-to-phone"></a>Megadott telefonszám hívása

Automatikus hanghívást indít a felhasználó helyezkedik el. A felhasználó felveszi a hívás és identitásváltása **#** hitelesítésüket jóváhagyása lenyomja a telefon billentyűzetén meg. Telefonszámot a nagy biztonsági mentési módszer a mobilalkalmazásokból értesítési vagy ellenőrző kódot.

### <a name="text-message-to-phone"></a>SMS küldése megadott telefonszámra

Egy ellenőrző kódot tartalmazó szöveges üzenetet küld a felhasználónak, kéri a felhasználót, hogy a bejelentkezési felületen meg kell adnia az ellenőrzőkódot.

### <a name="choose-verification-options"></a>Ellenőrzési beállítások kiválasztása

1. Keresse meg a **az Azure Active Directory**, **felhasználók**, **multi-factor Authentication**.

   ![A multi-factor Authentication-portál elérése az Azure AD-felhasználók panel az Azure Portalon](media/howto-mfa-getstarted/users-mfa.png)

1. Az új lapon megnyíló keresse meg a **Szolgáltatásbeállítások**.
1. A **ellenőrzési lehetőségek**, jelölje be a felhasználók által választható módszerek mindegyike.

   ![Ellenőrzési módszerek konfigurálása a multi-factor Authentication szolgáltatás beállítások lap](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Kattintson a **Mentés** gombra.
1. Zárja be a **Szolgáltatásbeállítások** fülre.

## <a name="plan-registration-policy"></a>Regisztrációs házirend megtervezése

A rendszergazdák meg kell határoznia, hogyan felhasználók regisztrálni fogja a módszereket. Szervezetek kell [engedélyezze az új kombinált regisztrációs felület](howto-registration-mfa-sspr-combined.md) az Azure MFA és az önkiszolgáló jelszó-visszaállítást (SSPR). SSPR lehetővé teszi a felhasználóknak jelszavuk ugyanazokat a módszereket használ a multi-factor authentication szolgáltatás használatával biztonságos módon. Javasoljuk, hogy ez a kombinált regisztrációs jelenleg nyilvános előzetes verzióban elérhető a felhasználók számára, és lehetővé teszi mindkét szolgáltatás esetében egyszer regisztrálni nagyszerű felhasználói élményt, mert. Ugyanazokat a módszereket az SSPR és az Azure MFA engedélyezése lehetővé teszi a felhasználók mindkét funkció használatához regisztrálni kell.

### <a name="registration-with-identity-protection"></a>Regisztráció az Identity Protection

Ha a szervezet az Azure Active Directory Identity Protection, [az MFA regisztrációs szabályzatának konfigurálása](../identity-protection/howto-mfa-policy.md) regisztrálni, amikor legközelebb bejelentkeznek az interaktív módon a felhasználóknak.

### <a name="registration-without-identity-protection"></a>Regisztráció nélkül identity Protection

A szervezet nem rendelkezik licenccel, amely az Identity Protection engedélyezése, ha a MFA szükség, jelentkezzen be a következő futtatáskor regisztrálja a rendszer kéri a felhasználókat. Felhasználók nem regisztrálható a multi-factor Authentication, ha az MFA által védett alkalmazások nem használnak. Fontos hatékonyan végrehajtható a fiók összes felhasználók regisztrálni, hogy kártékony elemek nem lehet kitalálni a felhasználó jelszavát, és regisztrálja a multi-factor Authentication a felhasználók nevében.

#### <a name="enforcing-registration"></a>Regisztrációs kényszerítése

Az alábbi lépéseket követve feltételes hozzáférési szabályzattal kényszerítheti a felhasználók regisztráljanak a multi-factor Authentication

1. Hozzon létre egy csoportot, adja hozzá az összes felhasználó jelenleg nincs regisztrálva.
2. A feltételes hozzáférés használatának kényszerítése a multi-factor authentication szolgáltatást a csoport az erőforrásokhoz való teljes hozzáférés.
3. Rendszeres időközönként kiértékeli a csoport tagságát, és a regisztrált felhasználók eltávolítása a csoportból.

PowerShell-parancsokkal által használt, előfordulhat, hogy azonosította a regisztrált és nem regisztrált az Azure MFA-felhasználók a [MSOnline PowerShell modul](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).

#### <a name="identify-registered-users"></a>A regisztrált felhasználók azonosítása

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Nem regisztrált felhasználók azonosítása

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

## <a name="plan-conditional-access-policies"></a>Tervezi feltételes hozzáférési szabályzatok

Tervezze meg a feltételes hozzáférési szabályzat stratégiáját, ez határozza meg, amikor az MFA- és más vezérlőket szükségesek, tekintse meg [Mi az az Azure Active Directory feltételes hozzáférés?](../conditional-access/overview.md).

Fontos kívül az Azure AD-bérlő véletlenül zárolva megakadályozza. Csökkentheti a rendszergazdai hozzáférést a nem szándékos hiánya hatását [két vagy több vészelérési fiókok létrehozása a bérlőben](../users-groups-roles/directory-emergency-access.md) és kizárva őket a feltételes hozzáférési szabályzatot.

### <a name="create-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy globális rendszergazdai fiókkal.
1. Keresse meg a **az Azure Active Directory**, **feltételes hozzáférési**.
1. Válassza ki **új szabályzat**.
1. Adjon meg egy kifejező nevet a szabályzathoz.
1. A **felhasználók és csoportok**:
   * Az a **Belefoglalás** lapon jelölje be a **minden felhasználó** választógomb
   * Az a **kizárása** lapra, jelölje be a **felhasználók és csoportok** , és válassza a vészelérési fiókok.
   * Kattintson a **Done** (Kész) gombra.
1. A **Felhőalkalmazások**, jelölje be a **az összes felhőalapú alkalmazások** választógombot.
   * IGÉNY SZERINT: Az a **kizárása** adja meg, hogy a szervezetnek nincs szüksége MFA felhőalapú alkalmazásokat.
   * Kattintson a **Done** (Kész) gombra.
1. A **feltételek** szakaszban:
   * IGÉNY SZERINT: Ha engedélyezte az Azure Identity Protection, válassza ki a szabályzat részeként értékeli a bejelentkezési kockázat.
   * IGÉNY SZERINT: Ha már konfigurálta a megbízható helyek vagy nevesített helyek, megadhat vagy kizárja a ezeken a helyeken a szabályzat alól.
1. A **Grant**, győződjön meg arról, a **hozzáférést** választógomb van kiválasztva.
    * Jelölje be a **többtényezős hitelesítés megkövetelése**.
    * Kattintson a **Kiválasztás** gombra.
1. Ugrás a **munkamenet** szakaszban.
1. Állítsa be a **házirend engedélyezése** kapcsolót **a**.
1. Kattintson a **Create** (Létrehozás) gombra.

![Az MFA engedélyezése az Azure portálon a felhasználóknak a próbaüzemi csoport feltételes hozzáférési szabályzat létrehozása](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)

## <a name="plan-integration-with-on-premises-systems"></a>A helyszíni rendszerekkel való integráció megtervezése

Egyes örökölt és a helyszíni alkalmazások Azure AD-val közvetlenül nem hitelesítenek használata, beleértve a többtényezős hitelesítés további lépések szükségesek:

* Örökölt helyszíni alkalmazások pedig, amelyek kell Application proxy használatára.
* A helyszíni RADIUS-alkalmazások, amelyek kell használnia az MFA-adapterrel az NPS-kiszolgálóval.
* A helyszíni AD FS-alkalmazások, amelyek az AD FS 2016 MFA-adapterrel használniuk kell.

Az alkalmazásokat, amelyek közvetlenül az Azure AD-hitelesítés és a modern hitelesítés nélküli (WS-Fed, SAML, OAuth, OpenID Connect) Győződjön meg arról is, a feltételes hozzáférési szabályzatot közvetlenül használni.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Az Azure MFA használata az Azure AD-alkalmazásproxyval

Helyszíni adataihoz teheti közzé az Azure AD-alkalmazások bérlői keresztül [Azure AD-alkalmazásproxy](../manage-apps/application-proxy.md) , és kihasználhatja az Azure multi-factor Authentication, ha az Azure AD használatára vannak konfigurálva előhitelesítés során.

Ezeket az alkalmazásokat, amelyeket az Azure multi-factor Authentication, csakúgy, mint bármely más Azure AD-val integrált alkalmazás feltételes hozzáférési szabályzatok vonatkoznak.

Hasonlóképpen ha az összes felhasználói bejelentkezéseket az Azure multi-factor Authentication érvénybe léptetése a helyszíni fogja védeni az Azure AD-alkalmazásproxyval közzétett alkalmazások.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>Azure multi-factor Authentication szolgáltatás integrálása a hálózati házirend-kiszolgáló

A hálózati házirend-kiszolgáló (NPS) kiterjesztése az Azure MFA felhőalapú MFA képességek hozzáadása a meglévő kiszolgálók használatával hitelesítési infrastruktúráját. Az NPS-bővítményt a telefonhívás, szöveges üzenet vagy telefonos alkalmazás ellenőrzés adhat a meglévő hitelesítési folyamatot. Ez az integráció a következő korlátozások vonatkoznak:

* A CHAPv2 protokoll csak az authenticator alkalmazás leküldéses értesítéseket és hanghívások támogatottak.
* Nem lehet alkalmazni a feltételes hozzáférési szabályzatokat.

Az NPS-bővítményének funkcionál, RADIUS és Azure MFA felhőalapú és a hitelesítés második tényezőjét, adja meg a védelme közötti csatoló [VPN](howto-mfa-nps-extension-vpn.md), [távoli asztali átjáró kapcsolatok](howto-mfa-nps-extension-rdg.md), vagy más RADIUS-kompatibilis az alkalmazások. A felhasználóknak, hogy ebben a környezetben az Azure MFA-kiszolgáló regisztrálása a minden hitelesítési kísérlet merül fel, a feltételes hozzáférési szabályzatok középérték MFA hiánya mindig szükség.

#### <a name="implementing-your-nps-server"></a>A hálózati házirend-kiszolgáló megvalósítása

Ha egy hálózati házirend-kiszolgáló példány telepítve van, vagy használja már, referencia [a meglévő hálózati házirend-kiszolgáló infrastruktúra integrálása az Azure multi-factor Authentication](howto-mfa-nps-extension.md). Ha első alkalommal állítja be a hálózati házirend-kiszolgáló, [hálózati házirend-kiszolgáló (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) útmutatást. A cikkben található hibaelhárítási útmutatót [oldja meg a hibaüzeneteket az NPS-bővítményt az Azure multi-factor Authentication](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>A felhasználók számára, amely nincs regisztrálva a multi-factor Authentication NPS előkészítése

Válassza ki, mi történik, ha a felhasználók, amely nincs regisztrálva a többtényezős hitelesítéssel próbál hitelesítést. A beállításjegyzék-beállítással `REQUIRE_USER_MATCH` a beállításjegyzékbeli elérési út `HKLM\Software\Microsoft\AzureMFA` funkció viselkedését vezérlő. Ezzel a beállítással rendelkezik olyan egyetlen konfigurációs beállítással.

| Kulcs | Érték | Alapértelmezett |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | TRUE / FALSE (HAMIS) | Nincs beállítva (egyenértékű, True) |

Ez a beállítás célja határozza meg, mi a teendő, ha a felhasználó nincs regisztrálva az MFA-hoz. Ez a beállítás hatását az alábbi táblázatban láthatók.

| Beállítások | Felhasználói MFA-állapota | Hatás |
| --- | --- | --- |
| Kulcs nem létezik. | Nincs regisztrálva | MFA-hitelesítést nem sikerül |
| Érték igaz értékre van állítva / nincs beállítva | Nincs regisztrálva | MFA-hitelesítést nem sikerül |
| Kulcs "false" értékűre. | Nincs regisztrálva | Többtényezős hitelesítés nélküli hitelesítés |
| Kulcs beállítva vagy True a False (hamis) | Regisztrált | Hitelesítést kell végeznie az MFA |

### <a name="integrate-with-active-directory-federation-services"></a>Az Active Directory összevonási szolgáltatások integrálása

Ha a szervezet az Azure AD-összevonást használ, akkor használhatja [az AD FS-erőforrások védelme az Azure multi-factor Authentication](multi-factor-authentication-get-started-adfs.md), mind a helyszíni és a felhőben. Az Azure MFA segítségével is csökkenthető a jelszavak és biztonságosabb mód a hitelesítésre. Windows Server 2016-kezdődően konfigurálhat az Azure MFA az elsődleges hitelesítéshez.

Ellentétben az AD FS a Windows Server 2012 R2, az AD FS 2016 az Azure MFA-adapterrel közvetlenül integrálódik az Azure ad-ben, és nem szükséges egy helyszíni Azure MFA-kiszolgálón. Az Azure MFA-adapterrel Windows Server 2016 be van építve, és nem kell további telepítéséhez.

Ha az Azure MFA és az AD FS 2016 és a célalkalmazás feltételes hozzáférési szabályzat hatálya alá tartozik, nincsenek további szempontok:

* Ha az alkalmazás egy függő entitás az AD FS 2016 összevonva az Azure AD feltételes hozzáférési érhető el.
* Ha az alkalmazás egy függő entitás az AD FS 2016 és a felügyelt vagy az AD FS 2016 összevont, a feltételes hozzáférés nem érhető el.
* Feltételes hozzáférés esetén még nem érhető el az AD FS 2016 az Azure MFA használatát az elsődleges hitelesítési módszerként van konfigurálva.

#### <a name="ad-fs-logging"></a>Az AD FS-naplózás

Információ a hitelesítési kéréseket, és azok sikerességét vagy sikertelenségét standard AD FS 2016 bejelentkezés a Windows biztonsági napló és a az AD FS felügyeleti napló tartalmazza. Belül ezeket az eseményeket az Eseménynapló-adatokat lett-e használni az Azure MFA jelzi. Ha például egy AD FS naplózási eseményazonosító 1200-as tartalmazza:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Újítsa meg és tanúsítványok kezelése

Az AD FS-kiszolgálókon, a helyi számítógép My Store, lesz egy önaláírt Azure MFA-tanúsítvány című OU = Microsoft AD FS az Azure MFA, amely tartalmazza a tanúsítvány lejárati dátuma. Az AD FS-kiszolgálókon a lejárati dátum meghatározásához a tanúsítvány érvényességi idejének ellenőrzéséhez.

Ha a tanúsítvány érvényességi ideje hamarosan megszűnik, lejárati [hozza létre és ellenőrzi az AD FS-kiszolgálókon található új MFA tanúsítvány](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

Az alábbi útmutatás részletesen kezelése az Azure MFA-tanúsítványokat az AD FS-kiszolgálókra. Az AD FS az Azure MFA konfigurálásakor a tanúsítványok használatával jön létre a `New-AdfsAzureMfaTenantCertificate` PowerShell-parancsmag 2 évig érvényesek. Újítsa meg, és a megújított tanúsítványokat a lejárat előtt telepítse az MFA szolgáltatás tojás alakúak zavarokat.

## <a name="implement-your-plan"></a>A terv megvalósítása

Most, hogy a megoldás megtervezése, valósíthat meg az alábbi lépéseket követve:

1. Megfelel a szükséges előfeltételeket
   1. Üzembe helyezése [az Azure AD Connect](../hybrid/whatis-hybrid-identity.md) bármely hibrid forgatókönyvek esetén
   1. Üzembe helyezése [Azure AD-alkalmazásproxy](../manage-apps/application-proxy.md) a helyszíni alkalmazások a cloud-access-hez közzétett
   1. Üzembe helyezése [NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) bármely RADIUS-hitelesítés
   1. Győződjön meg, hogy a felhasználó frissítette a támogatott verziók a Microsoft Office modern hitelesítés engedélyezve
1. Konfigurálja a kiválasztott [hitelesítési módszerek](#choose-verification-options)
1. Adja meg a [nevű hálózati helyek](../conditional-access/location-condition.md#named-locations)
1. Válassza ki a csoportokat a bevezetésüket MFA.
1. Konfigurálja a [feltételes hozzáférési szabályzatok](#create-conditional-access-policy)
1. Az MFA regisztrációs szabályzatának konfigurálása
   1. [Kombinált MFA és az SSPR](howto-registration-mfa-sspr-combined.md)
   1. A [Identity Protection](../identity-protection/howto-mfa-policy.md)
1. Felhasználói küldhessen és kattintva regisztrálhatnak a felhasználók beolvasása [https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Nyomon követheti, aki regisztrálva van](#identify-non-registered-users)

## <a name="manage-your-solution"></a>A megoldás kezelése

Jelentések az Azure MFA-hoz

Az Azure multi-factor Authentication az Azure Portalon keresztül jelentéseket biztosítja:

| Jelentés | Hely | Leírás |
| --- | --- | --- |
| Használat és a csalási riasztás | Az Azure AD > bejelentkezések | Információt nyújt az általános használat – felhasználói összefoglalás és felhasználói adatait; csakúgy, mint a megadott dátumtartományban küldött visszaélési riasztások előzményeit. |

## <a name="troubleshoot-mfa-issues"></a>MFA-problémák hibaelhárítása

Megoldások keresése a gyakori problémák az Azure MFA, az [Azure multi-factor Authentication hibaelhárítási cikk](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) a Microsoft Support Center.

## <a name="next-steps"></a>További lépések

* [Mik a hitelesítési módszerek?](concept-authentication-methods.md)
* [A hiperkonvergens regisztráció az Azure multi-factor Authentication és az Azure AD önkiszolgáló jelszó-visszaállítás engedélyezése](concept-registration-mfa-sspr-converged.md)
* Miért lett egy felhasználó kéri vagy a rendszer nem kéri hajthatok végre MFA? Című témakör [az Azure AD bejelentkezési jelentések a jelentések az Azure multi-factor Authentication dokumentum](howto-mfa-reporting.md#azure-ad-sign-ins-report).

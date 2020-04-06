---
title: Az Azure többtényezős hitelesítésének üzembe helyezési szempontjai
description: Ismerje meg a telepítési szempontokat és az Azure többtényezős hitelesítés sikeres megvalósításának stratégiáját
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: a70c6ae3ebc7f5b39550508594bd4d4907e68a67
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667343"
---
# <a name="plan-an-azure-multi-factor-authentication-deployment"></a>Azure többtényezős hitelesítési telepítésének megtervezése

Az emberek egyre bonyolultabb esetekben csatlakoznak a szervezeti erőforrásokhoz. Az emberek a vállalati hálózaton és azon kívül is a szervezet tulajdonában lévő, személyes és nyilvános eszközökről csatlakoznak okostelefonokkal, táblagépekkel, számítógépekkel és laptopokkal, gyakran több platformon. Ebben a mindig összekapcsolt, többeszközös és többplatformos világban a felhasználói fiókok biztonsága minden eddiginél fontosabb. Az eszközök, hálózatok és platformok között használt jelszavak , függetlenül azok összetettségétől, már nem elegendőek a felhasználói fiók biztonságának biztosításához, különösen akkor, ha a felhasználók hajlamosak a jelszavak at használni a fiókok között. A kifinomult adathalászat és más közösségi tervezési támadások a felhasználónevek és jelszavak közzétételét és értékesítését eredményezhetik a sötét weben.

[Az Azure többtényezős hitelesítés (MFA)](concept-mfa-howitworks.md) segít az adatokhoz és alkalmazásokhoz való hozzáférés biztosításában. Ez egy további biztonsági réteget biztosít a hitelesítés második formájával. A szervezetek [feltételes hozzáféréssel](../conditional-access/overview.md) biztosíthatják, hogy a megoldás megfeleljen az egyedi igényeiknek.

Ez a telepítési útmutató bemutatja, hogyan tervezheti meg, majd tesztelheti az Azure többtényezős hitelesítés bevezetését.

Az Azure többtényezős hitelesítésének gyors megtekintéséhez működés közben, majd a további üzembe helyezési szempontok megismeréséhez:

> [!div class="nextstepaction"]
> [Az Azure Multi-Factor Authentication engedélyezése](tutorial-enable-azure-mfa.md)

## <a name="prerequisites"></a>Előfeltételek

Az Azure többtényezős hitelesítés üzembe helyezésének megkezdése előtt vannak olyan előfeltételű elemek, amelyeket figyelembe kell venni.

| Forgatókönyv | Előfeltétel |
| --- | --- |
| **Csak felhőalapú** identitáskörnyezet modern hitelesítéssel | **Nincsenek további előfeltétel-feladatok** |
| **Hibrid** identitásforgatókönyvek | [Az Azure AD Connect](../hybrid/whatis-hybrid-identity.md) telepítve van, és a felhasználói identitások szinkronizálva vannak, vagy összevannak kapcsolva a helyszíni Active Directory tartományi szolgáltatásokkal az Azure Active Directoryval. |
| Helyszíni örökölt alkalmazások közzétéve a felhőalapú hozzáférést | Az Azure AD [alkalmazásproxy](../manage-apps/application-proxy.md) telepítve van. |
| Az Azure MFA használata RADIUS-hitelesítéssel | A [hálózati házirend-kiszolgáló (NPS)](howto-mfa-nps-extension.md) telepítve van. |
| A felhasználók microsoft Office 2010-es vagy korábbi verziói, illetve iOS 11-es vagy korábbi Apple Mail-verzióval rendelkeznek | Frissítsen a [Microsoft Office 2013-as vagy újabb verzióra,](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) valamint az iOS 12-es vagy újabb verziós Apple mailre. A feltételes hozzáférést az örökölt hitelesítési protokollok nem támogatják. |

## <a name="plan-user-rollout"></a>A felhasználó bevezetésének megtervezése

Az MFA bevezetési tervének tartalmaznia kell egy kísérleti üzembe helyezést, amelyet a támogatási kapacitáson belüli üzembe helyezési hullámok követnek. Kezdje el a bevezetést a feltételes hozzáférési szabályzatok alkalmazásával a kísérleti felhasználók egy kis csoportjára. Miután kiértékelte a kísérleti felhasználókra, a használt folyamatokra és a regisztrációs viselkedésre gyakorolt hatást, hozzáadhat további csoportokat a házirendhez, vagy további felhasználókat adhat hozzá a meglévő csoportokhoz.

### <a name="user-communications"></a>Felhasználói kommunikáció

Fontos, hogy tájékoztassa a felhasználókat a tervezett kommunikáció, a közelgő változások, az Azure MFA regisztrációs követelmények és a szükséges felhasználói műveletek. Javasoljuk, hogy a kommunikációt a szervezeten belüli képviselőkkel, például a kommunikációval, a változáskezeléssel vagy az emberi erőforrások részlegekkel összehangoltan fejlesztsük.

A Microsoft kommunikációs sablonokat és [végfelhasználói dokumentációt](https://aka.ms/mfatemplates) biztosít a kommunikáció elkészítéséhez. [end-user documentation](../user-help/security-info-setup-signin.md) A felhasználókat [https://myprofile.microsoft.com](https://myprofile.microsoft.com) közvetlenül is elküldheti a regisztrációhoz, ha kiválasztja az adott oldalon található **Biztonsági adatok** hivatkozásokat.

## <a name="deployment-considerations"></a>Telepítési szempontok

Az Azure többtényezős hitelesítésfeltételes hozzáféréssel való kényszerítésével van telepítve. A [feltételes hozzáférési házirendek](../conditional-access/overview.md) megkövetelhetik a felhasználóktól, hogy többtényezős hitelesítést hajtsanak végre, ha bizonyos feltételek teljesülnek, például:

* Minden felhasználó, egy adott felhasználó, egy csoport tagja vagy hozzárendelt szerepkör
* Elérett felhőalapú alkalmazás
* Eszközplatform
* Az eszköz állapota
* Hálózati hely vagy földrajzi IP-cím
* Ügyfélalkalmazások
* Bejelentkezési kockázat (identitásvédelemszükséges)
* Megfelelő eszköz
* Hibrid Azure AD-csatlakozású eszköz
* Jóváhagyott ügyfélalkalmazás

A [többtényezős hitelesítési bevezetési anyagok](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) testreszabható posztereit és e-mail sablonjait használva többtényezős hitelesítést is betölthet a szervezetbe.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Többtényezős hitelesítés engedélyezése feltételes hozzáféréssel

A feltételes hozzáférési házirendek kényszerítik a regisztrációt, és a nem regisztrált felhasználóknak az első bejelentkezéskor kell befejezniük a regisztrációt, ami fontos biztonsági szempont.

[Az Azure AD Identity Protection](../identity-protection/howto-configure-risk-policies.md) egyaránt hozzájárul a regisztrációs szabályzat és az automatikus kockázatészlelési és -szervizelési szabályzatok az Azure többtényezős hitelesítési történet. Szabályzatok hozhatók létre a jelszómódosítások kényszerítésére, ha a rendszer feltört identitással fenyeget, vagy többszintű hitelesítést igényel, ha a bejelentkezést a következő [események](../reports-monitoring/concept-risk-events.md)kockázatosnak ítélik:

* Kiszivárgott hitelesítő adatok
* Bejelentkezések névtelen IP-címről
* Bejelentkezés szokatlan helyekről
* Bejelentkezések ismeretlen helyekről
* Bejelentkezések fertőzött eszközökről
* Bejelentkezés gyanús tevékenységekkel rendelkező IP-címekről

Az Azure Active Directory identity protection által észlelt kockázatészlelések egy része valós időben történik, és néhány offline feldolgozást igényel. A rendszergazdák letilthatják a kockázatos viselkedést mutató felhasználókat, és manuálisan kiigazítják, jelszómódosítást igényelnek, vagy feltételes hozzáférési házirendjeik részeként többtényezős hitelesítést igényelnek.

## <a name="define-network-locations"></a>Hálózati helyek meghatározása

Azt javasoljuk, hogy a szervezetek feltételes hozzáférést használjanak a hálózatuk [névvel ellátott helyek](../conditional-access/location-condition.md#named-locations)használatával történő meghatározásához. Ha a szervezet az Identitásvédelem használata, fontolja meg a kockázatalapú szabályzatok helyett elnevezett helyeken.

### <a name="configuring-a-named-location"></a>Elnevezett hely konfigurálása

1. **Az Azure Active Directory megnyitása** az Azure Portalon
2. **Biztonsági** kijelölés
3. A **Kezelés csoportban**válassza **a Named Locations (Elnevezett helyek) lehetőséget.**
4. **Új hely** kiválasztása
5. A **Név** mezőben adjon meg értelmes nevet
6. Válassza ki, hogy a helyet *IP-tartományok* vagy *országok/régiók* használatával határozza-e meg.
   1. *IP-tartományok* használata esetén
      1. Döntse el, hogy *megbízható helyként jelöli-e meg*a jelet. A megbízható helyről történő bejelentkezésnél kisebb a felhasználó bejelentkezési kockázata. Csak akkor jelölje meg ezt a helyet megbízhatóként, ha tudja, hogy a megadott IP-tartományok a szervezetben létrejöttek és hitelesek.
      2. Az IP-tartományok megadása
   2. Ha *országokat/régiókat* használ
      1. Bontsa ki a legördülő menüt, és válassza ki azokat az országokat vagy régiókat, amelyeket ehhez az elnevezett helyhez szeretne definiálni.
      2. Döntse el, hogy *az ismeretlen területeket is*tartalmazza-e. Az ismeretlen területek olyan IP-címek, amelyek nem képezhetők le egy országra/régióra.
7. Válassza a **Létrehozás lehetőséget**

## <a name="plan-authentication-methods"></a>Hitelesítési módszerek tervezése

A rendszergazdák kiválaszthatják azokat a [hitelesítési módszereket,](../authentication/concept-authentication-methods.md) amelyeket elérhetővé kívánnak tenni a felhasználók számára. Fontos, hogy egyetlen hitelesítési módszernél több hitelesítési módszert engedélyezze, hogy a felhasználók rendelkezhessenek egy biztonsági mentési módszerrel arra az esetre, ha elsődleges módszerük nem lenne elérhető. A rendszergazdák a következő módszereket engedélyezhetik:

### <a name="notification-through-mobile-app"></a>Értesítés mobilalkalmazáson keresztül

A rendszer leküldéses értesítést küld a Microsoft Authenticator alkalmazásnak a mobileszközén. A felhasználó megtekinti az értesítést, és az ellenőrzés befejezéséhez a Jóváhagyás lehetőséget **választja.** A mobilalkalmazáson keresztüli leküldéses értesítések biztosítják a legkevésbé tolakodó lehetőséget a felhasználók számára. Ők is a legmegbízhatóbb és biztonságos lehetőség, mert az általuk használt adatkapcsolat helyett telefonálás.

> [!NOTE]
> Ha a szervezet munkatársai Kínában dolgoznak vagy Kínába utaznak, az **Értesítés mobilalkalmazáson keresztül** módszer **Android-eszközökön** nem működik az adott országban. Alternatív módszereket kell elérhetővé tenni a felhasználók számára.

### <a name="verification-code-from-mobile-app"></a>Ellenőrző kód a mobilalkalmazásból

Egy mobilalkalmazás, például a Microsoft Authenticator alkalmazás 30 másodpercenként új OATH-ellenőrző kódot hoz létre. A felhasználó beírja az ellenőrző kódot a bejelentkezési felületre. A mobilalkalmazás-beállítás használható, függetlenül attól, hogy a telefon rendelkezik-e adat- vagy mobiljellel.

### <a name="call-to-phone"></a>Telefonhívás

A felhasználó automatikus hanghívást kezdeményez. A felhasználó válaszol a **#** hívásra, és lenyomja a telefon billentyűzetén, hogy jóváhagyja a hitelesítést. Hívás a telefon egy nagyszerű biztonsági mentési módszer értesítési vagy ellenőrző kódot egy mobil app.

### <a name="text-message-to-phone"></a>Sms-ben telefonon

A rendszer egy ellenőrző kódot tartalmazó szöveges üzenetet küld a felhasználónak, és a rendszer kéri, hogy adja meg az ellenőrző kódot a bejelentkezési felületen.

### <a name="choose-verification-options"></a>Ellenőrzési beállítások megadása

1. Tallózással keresse meg az **Azure Active Directory**, **felhasználók**, **többtényezős hitelesítés**.

   ![A többtényezős hitelesítési portál elérése az Azure AD-felhasználók panelről az Azure Portalon](media/howto-mfa-getstarted/users-mfa.png)

1. A megnyíló új lapon keresse meg a **szolgáltatás beállításait.**
1. Az **Ellenőrzési beállítások csoportban**jelölje be az összes jelölőnégyzetet, hogy vannak-e a felhasználók számára elérhető módszerek.

   ![Ellenőrzési módszerek konfigurálása a Többtényezős hitelesítés szolgáltatás beállításai lapon](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Kattintson a **Mentés gombra.**
1. Zárja be a **szolgáltatásbeállítások** lapot.

## <a name="plan-registration-policy"></a>A tervezés regisztrációs irányelvei

A rendszergazdáknak meg kell határozniuk, hogy a felhasználók hogyan regisztrálják módszereiket. A szervezeteknek engedélyezniük kell [az azure-beli mfa](howto-registration-mfa-sspr-combined.md) és az önkiszolgáló jelszó-visszaállítás (SSPR) új kombinált regisztrációs élményét. Az SSPR lehetővé teszi a felhasználók számára, hogy biztonságos módon alaphelyzetbe állítsák jelszavukat a többtényezős hitelesítéshez használt módszerekkel. Javasoljuk, hogy ez a kombinált regisztráció, jelenleg nyilvános előzetes verzióban, mert ez egy nagyszerű élmény a felhasználók számára, azzal a képességgel, hogy regisztráljon egyszer mindkét szolgáltatáshoz. Az SSPR és az Azure MFA ugyanazokat a módszereket engedélyezve lehetővé teszi a felhasználók számára, hogy mindkét funkció használatára regisztrálva legyenek.

### <a name="registration-with-identity-protection"></a>Regisztráció a személyazonosság-védelemmel

Ha a szervezet az Azure Active Directory-identitásvédelem, [konfigurálja az MFA regisztrációs szabályzatot,](../identity-protection/howto-mfa-policy.md) hogy kérje a felhasználókat, hogy regisztráljon a következő bejelentkezéskor interaktív.

### <a name="registration-without-identity-protection"></a>Regisztráció személyazonosság-védelem nélkül

Ha a szervezet nem rendelkezik olyan licencekkel, amelyek lehetővé teszik az Identitásvédelem, a felhasználók nak regisztrálniuk kell a következő alkalommal, amikor többéves korra van szükség a bejelentkezéskor. Előfordulhat, hogy a felhasználók nincsenek regisztrálva az MFA-hoz, ha nem használnak többfa-védelemmel védett alkalmazásokat. Fontos, hogy minden felhasználó regisztrált, hogy a rossz szereplők nem tudja kitalálni a jelszót a felhasználó, és regisztráljon az MFA a nevükben, hatékonyan átveszi az irányítást a számla.

#### <a name="enforcing-registration"></a>A regisztráció érvényesítése

A következő lépések használatával a feltételes hozzáférési házirend kényszerítheti a felhasználókat a többtényezős hitelesítésre való regisztrációra.

1. Hozzon létre egy csoportot, és adja hozzá az összes jelenleg nem regisztrált felhasználót.
2. Feltételes hozzáférés használatával kényszerítse ki a csoport többtényezős hitelesítését az összes erőforráshoz való hozzáféréshez.
3. Rendszeresen értékelje újra a csoporttagságot, és távolítsa el a csoportból regisztrált felhasználókat.

Az [MSOnline PowerShell-modult](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0)használó PowerShell-parancsokkal regisztrált és nem regisztrált Azure MFA-felhasználókat azonosíthat.

#### <a name="identify-registered-users"></a>Regisztrált felhasználók azonosítása

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Nem regisztrált felhasználók azonosítása

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Felhasználók átalakítása felhasználónkénti többszintű többszintű támogatásból feltételes hozzáférésalapú többszintű hozzáférésű többszintű hozzáférési programra

Ha a felhasználók engedélyezve voltak a felhasználónkénti engedélyezett és kényszerített Azure többtényezős hitelesítés használatával, a következő PowerShell segíthet a feltételes hozzáférésalapú Azure többtényezős hitelesítésre való átalakításban.

Futtassa ezt a PowerShellt egy `.PS1` ISE-ablakban, vagy mentse fájlként a helyi futtatáshoz.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Nemrég módosítottuk a viselkedést és a PowerShell-parancsfájl t ennek megfelelően. Korábban a parancsfájl mentette ki az MFA-metódusok, letiltotta az MFA, és visszaállította a módszereket. Erre már nincs szükség, most, hogy a letiltás alapértelmezett viselkedése nem törli a metódusok at.

## <a name="plan-conditional-access-policies"></a>Feltételes hozzáférési házirendek tervezése

A feltételes hozzáférési szabályzat tervének megtervezéséhez, amely meghatározza, hogy mikor szükséges az MFA és más vezérlők, olvassa el [a Mi a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

Fontos, hogy megakadályozza, hogy véletlenül kizárva az Azure AD-bérlő. Csökkentheti a felügyeleti hozzáférés véletlen hiányának hatását, ha [két vagy több vészelérési fiókot hoz létre a bérlőben,](../users-groups-roles/directory-emergency-access.md) és kizárja őket a feltételes hozzáférési szabályzatból.

### <a name="create-conditional-access-policy"></a>Feltételes hozzáférési házirend létrehozása

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) egy globális rendszergazdai fiók használatával.
1. Tallózással keresse meg az **Azure Active Directory** > **biztonsági** > **feltételes hozzáférését.**
1. Válassza az **Új házirend lehetőséget.**
   ![Feltételes hozzáférési szabályzat létrehozása az Azure Portal felhasználói számára az MFA engedélyezéséhez a próbacsoportban](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. Adjon meg értelmes nevet a házirendnek.
1. Felhasználók **és csoportok alatt:**
   * A **Belefoglalás** lapon válassza a **Minden felhasználó** választógombját.
   * A **Kizárás** lapon jelölje be a **Felhasználók és csoportok** jelölőnégyzetet, és válassza ki a segélyhívó fiókokat.
   * Kattintson a **Done** (Kész) gombra.
1. A **Felhőalkalmazások csoportban**válassza a **Minden felhőalapú alkalmazás** választógombot.
   * OPCIONÁLISAN: A **Kizárás** lapon válassza ki azokat a felhőalkalmazásokat, amelyekhez a szervezet nem igényel többéves kort.
   * Kattintson a **Done** (Kész) gombra.
1. A **Feltételek** szakaszban:
   * VÁLASZTHATÓ: Ha engedélyezte az Azure Identity Protection, választhatja ki, hogy értékelje a bejelentkezési kockázatot a szabályzat részeként.
   * VÁLASZTHATÓ: Ha megbízható vagy elnevezett helyeket konfigurált, megadhatja, hogy ezeket a helyeket felkell vonnia vagy ki kell zárnia a házirendből.
1. A **Grant**csoportban győződjön meg arról, hogy a **Grant access** választógomb ki van jelölve.
    * Jelölje be a **Többtényezős hitelesítés megkövetelése**jelölőnégyzetet.
    * Kattintson a **Kiválasztás** gombra.
1. Hagyja ki a **Munkamenet** szakaszt.
1. Állítsa a **Házirend engedélyezése** kapcsolót **Be**beállításra.
1. Kattintson **a Létrehozás gombra.**

## <a name="plan-integration-with-on-premises-systems"></a>Integráció megtervezése a helyszíni rendszerekkel

Egyes örökölt és helyszíni alkalmazások, amelyek nem hitelesítik közvetlenül az Azure AD további lépéseket igényel a többórás hitelesítés használatához, beleértve:

* Örökölt helyszíni alkalmazások, amelyek alkalmazásproxyt kell használniuk.
* Helyszíni RADIUS-alkalmazások, amelyeknek MFA-adaptert kell használniuk a hálózati kiszolgálóval.
* Helyszíni AD FS-alkalmazások, amelyeknek Az AD FS 2016-os vagy újabb MFA-adaptert kell használniuk.

Az Azure AD-vel közvetlenül hitelesítendő és modern hitelesítéssel (WS-Fed, SAML, OAuth, OpenID Connect) rendelkező alkalmazások közvetlenül használhatják a feltételes hozzáférési szabályzatokat.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Azure MFA használata az Azure AD alkalmazásproxyval

A helyszíni használatra szolgáló alkalmazások közzétehetők az Azure AD-bérlőn keresztül [az Azure AD-alkalmazásproxy,](../manage-apps/application-proxy.md) és kihasználhatják az Azure többtényezős hitelesítés, ha konfigurálva vannak az Azure AD előhitelesítés.

Ezek az alkalmazások feltételes hozzáférési szabályzatok, amelyek kényszerítik az Azure többtényezős hitelesítés, csakúgy, mint bármely más Azure AD-integrált alkalmazás.

Hasonlóképpen, ha az Azure többtényezős hitelesítés minden felhasználói bejelentkezésesetén érvénybe lép, az Azure AD alkalmazásproxyval közzétett helyszíni alkalmazások védettek lesznek.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>Az Azure többtényezős hitelesítésintegrálása a hálózati házirend-kiszolgálóval

Az Azure MFA hálózati házirend-kiszolgáló (NPS) bővítménye felhőalapú MFA-képességeket ad a hitelesítési infrastruktúrához a meglévő kiszolgálók használatával. A nps kiterjesztéssel telefonhívást, sms-t vagy telefonos alkalmazás-ellenőrzést adhat hozzá a meglévő hitelesítési folyamathoz. Ennek az integrációnak a következő korlátai vannak:

* A CHAPv2 protokollcsak hitelesítő alkalmazásleküldéses értesítések és hanghívás támogatott.
* Feltételes hozzáférési házirendek nem alkalmazhatók.

A hálózati kiszolgáló bővítmény a RADIUS és a felhőalapú Azure MFA közötti adapterként működik, hogy egy második hitelesítési tényezőt biztosítson a [VPN,](howto-mfa-nps-extension-vpn.md) [a Távoli asztali átjáró kapcsolatok](howto-mfa-nps-extension-rdg.md)vagy más RADIUS-kompatibilis alkalmazások védelme érdekében. Az Azure MFA-ra ebben a környezetben regisztráló felhasználók minden hitelesítési kísérlet hez kihívást jelentenek, a feltételes hozzáférési szabályzatok hiánya azt jelenti, hogy mindig szükség van az MFA-ra.

#### <a name="implementing-your-nps-server"></a>A nps-kiszolgáló megvalósítása

Ha már telepítve van egy nps-példány, és már használatban van, hivatkozzon [a meglévő nps-infrastruktúra integrálása az Azure többtényezős hitelesítéssel](howto-mfa-nps-extension.md)című dokumentumra. Ha első alkalommal állítja be a hálózati házirend-kiszolgálót, olvassa el a [Hálózati házirend-kiszolgáló (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) című útmutatót az utasításokért. A hibaelhárítási útmutató a [Hibaüzenetek feloldása az Azure többtényezős hitelesítéshez a hálózati kiszolgáló bővítményéből című](howto-mfa-nps-extension-errors.md)témakörben található.

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>A nps előkészítése azon felhasználók számára, akik nincsenek beiratkozva az MFA-hoz

Válassza ki, mi történik, ha az MFA-val nem regisztrált felhasználók megpróbálnak hitelesíteni. A szolgáltatás `REQUIRE_USER_MATCH` viselkedésének szabályozásához használja a beállításjegyzék elérési útjának `HKLM\Software\Microsoft\AzureMFA` beállítását. Ez a beállítás egyetlen konfigurációs beállítással rendelkezik.

| Kulcs | Érték | Alapértelmezett |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | IGAZ / HAMIS | Nincs beállítva (egyenértékű az IGAZ értékével) |

Ennek a beállításnak az a célja, hogy meghatározza, mi a teendő, ha egy felhasználó nincs bejelentve az MFA-hoz. A beállítás módosításának hatásai az alábbi táblázatban találhatók.

| Beállítások | Felhasználói MFA-állapot | Hatások |
| --- | --- | --- |
| A kulcs nem létezik | Nincs beiratkozva | MFA kihívás sikertelen |
| Érték értéke Igaz / nincs beállítva | Nincs beiratkozva | MFA kihívás sikertelen |
| A kulcs értéke Hamis | Nincs beiratkozva | Hitelesítés többéves hitelesítés nélkül |
| A kulcs értéke Hamis vagy Igaz | Regisztrálva | Hitelesíteni kell az MFA-val |

### <a name="integrate-with-active-directory-federation-services"></a>Integráció az Active Directory összevonási szolgáltatásokkal

Ha a szervezet össze van kapcsolva az Azure AD-vel, az [Azure többtényezős hitelesítésével biztosíthatja az AD FS-erőforrásokat,](multi-factor-authentication-get-started-adfs.md)mind a helyszínen, mind a felhőben. Az Azure MFA lehetővé teszi a jelszavak csökkentését és a hitelesítés biztonságosabb módját. A Windows Server 2016-tól kezdve most már konfigurálhatja az Azure MFA-t az elsődleges hitelesítéshez.

A Windows Server 2012 R2 AD FS szolgáltatással ellentétben az AD FS 2016 Azure MFA-adapter közvetlenül integrálható az Azure AD-vel, és nem igényel helyszíni Azure MFA-kiszolgálót. Az Azure MFA-adapter be van építve a Windows Server 2016-ba, és nincs szükség további telepítésre.

Ha az Azure MFA-t az AD FS 2016-tal használja, és a célalkalmazás feltételes hozzáférési szabályzat hatálya alá tartozik, további szempontok merülnek fel:

* Feltételes hozzáférés érhető el, ha az alkalmazás egy függő entitás az Azure AD, összevont AD FS 2016 vagy újabb.
* A feltételes hozzáférés nem érhető el, ha az alkalmazás az AD FS 2016 vagy az AD FS 2019 függő entitása, és az AD FS 2016 vagy az AD FS 2019 kezelése vagy összevonta.
* Feltételes hozzáférés is nem érhető el, ha az AD FS 2016 vagy AD FS 2019 van konfigurálva, hogy az Azure MFA elsődleges hitelesítési módszerként.

#### <a name="ad-fs-logging"></a>AD FS naplózása

A Windows biztonsági naplóban és az AD FS felügyeleti naplóban az AD FS 2016-os és 2019-es szabványos naplózása a hitelesítési kérelmekkel és azok sikerével vagy sikertelenségével kapcsolatos információkat tartalmaz. Eseménynapló-adatok ezekben az eseményekben jelzi, hogy az Azure MFA használták-e. Az AD FS 1200-as azonosítójú naplózási eseménypéldául a következőket tartalmazhatja:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Tanúsítványok megújítása és kezelése

Minden AD FS-kiszolgálón, a helyi számítógép My Store, lesz egy önaláírt Azure MFA tanúsítvány nevű OU=Microsoft AD FS Azure MFA, amely tartalmazza a tanúsítvány lejárati dátumát. A lejárati dátum meghatározásához ellenőrizze a tanúsítvány érvényességi idejét minden AD FS-kiszolgálón.

Ha a tanúsítványok érvényességi ideje a lejáratfelé közeledik, [hozzon létre és ellenőrizzen egy új MFA-tanúsítványt minden Egyes AD FS-kiszolgálón.](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers)

Az alábbi útmutató ismerteti, hogyan kezelheti az Azure MFA-tanúsítványokat az AD FS-kiszolgálókon. Amikor konfigurálja az AD FS-t az Azure `New-AdfsAzureMfaTenantCertificate` MFA-val, a PowerShell-parancsmagon keresztül létrehozott tanúsítványok két évig érvényesek. A megújult tanúsítványok megújítása és telepítése a lejárat előtt az MFA-szolgáltatás ban bekövetkező üres fennakadások miatt.

## <a name="implement-your-plan"></a>A terv megvalósítása

Most, hogy megtervezte a megoldást, az alábbi lépéseket követve valósíthatja meg:

1. Teljesítsminden szükséges előfeltételt
   1. Az [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) üzembe helyezése bármilyen hibrid forgatókönyvhöz
   1. [Az Azure AD-alkalmazásproxy](../manage-apps/application-proxy.md) üzembe helyezése a felhőalapú hozzáféréshez közzétett bármely helyszíni alkalmazáshoz
   1. A [nps](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) telepítése bármely RADIUS-hitelesítéshez
   1. Annak ellenőrzése, hogy a felhasználók modern hitelesítéssel frissítettek-e a Microsoft Office támogatott verzióira
1. Választott [hitelesítési módszerek konfigurálása](#choose-verification-options)
1. Az [elnevezett hálózati helyek](../conditional-access/location-condition.md#named-locations) meghatározása
1. Válassza ki azokat a csoportokat, amelyeket meg szeretne kezdeni az MFA bevezetése érdekében.
1. A [feltételes hozzáférési házirendek konfigurálása](#create-conditional-access-policy)
1. Az MFA regisztrációs házirendjének konfigurálása
   1. [Kombinált MFA és SSPR](howto-registration-mfa-sspr-combined.md)
   1. [Identitásvédelemmel](../identity-protection/howto-mfa-policy.md)
1. Felhasználói kommunikáció küldése és a felhasználók igénylése a[https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Nyomon követheti, hogy ki regisztrált](#identify-non-registered-users)

> [!TIP]
> A kormányzati felhőfelhasználók regisztrálhatnak[https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>A megoldás kezelése

Jelentések az Azure MFA-hoz

Az Azure többtényezős hitelesítése jelentéseket biztosít az Azure Portalon keresztül:

| Jelentés | Hely | Leírás |
| --- | --- | --- |
| Használati és csalási riasztások | Az Azure AD > bejelentkezések | Tájékoztatást nyújt az általános használatról, a felhasználói összegzésről és a felhasználó adatairól; valamint a megadott dátumtartományban benyújtott csalási riasztások előzményeit. |

## <a name="troubleshoot-mfa-issues"></a>MFA-problémák elhárítása

Megoldásokat találhat az Azure MFA-val kapcsolatos gyakori problémákra a Microsoft támogatási központjában található [Azure többtényezős hitelesítéselhárítási cikkében.](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues)

## <a name="next-steps"></a>További lépések

Az Azure többtényezős hitelesítésének működés közbeni megtekintéséhez végezze el az alábbi oktatóanyagot:

> [!div class="nextstepaction"]
> [Az Azure Multi-Factor Authentication engedélyezése](tutorial-enable-azure-mfa.md)

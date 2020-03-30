---
title: Egyszeri bejelentkezés az alkalmazásokba – Azure Active Directory | Microsoft dokumentumok
description: Megtudhatja, hogyan választhat egyetlen bejelentkezési módszert az Azure Active Directoryban (Azure AD) az alkalmazások konfigurálásakor. Használja az egyszeri bejelentkezést, így a felhasználóknak nem kell minden alkalmazáshoz megjegyzik a jelszavakat, és egyszerűsíteniük kell a fiókkezelés felügyeletét.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: mimart
ms.reviewer: arvindh, japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: f46bcf412403d8f911e484e12a9d1f421b1666f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366070"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>Egyszeri bejelentkezés az alkalmazásokba az Azure Active Directoryban

Egyszeri bejelentkezés (SSO) növeli a biztonságot és a kényelmet, amikor a felhasználók bejelentkeznek az alkalmazások az Azure Active Directoryban (Azure AD). Ez a cikk ismerteti az egyszeri bejelentkezési módszereket, és segít kiválasztani a legmegfelelőbb egyszeri bejelentkezési módszert az alkalmazások konfigurálásakor.

- **Egyszeri bejelentkezésesetén a**felhasználók egyszer jelentkeznek be egy fiókkal a tartományhoz csatlakozó eszközök, a vállalati erőforrások, a szoftver szolgáltatásként (SaaS) alkalmazások és a webalkalmazások eléréséhez. A bejelentkezés után a felhasználó alkalmazásokat indíthat az Office 365 portálról vagy az Azure AD MyApps hozzáférési panelről. A rendszergazdák központosíthatják a felhasználói fiókok kezelését, és a csoporttagság alapján automatikusan hozzáadhatják vagy eltávolíthatják az alkalmazásokhoz való felhasználói hozzáférést.

- **Egyszeri bejelentkezés nélkül a**felhasználóknak emlékezniük kell az alkalmazásspecifikus jelszavakra, és be kell jelentkezniük az egyes alkalmazásokba. Az informatikai személyzetnek felhasználói fiókokat kell létrehoznia és frissítenie minden alkalmazáshoz, például az Office 365-höz, a Boxhoz és a Salesforce-hoz. A felhasználóknak meg kell emlékezniük a jelszavukra, és időt kell fordítaniuk arra, hogy bejelentkezhessenek az egyes alkalmazásokba.

## <a name="choosing-a-single-sign-on-method"></a>Egyszeri bejelentkezési módszer kiválasztása

Az alkalmazások egyszeri bejelentkezésre konfigurálásának számos módja van. Az egyszeri bejelentkezési módszer kiválasztása attól függ, hogy az alkalmazás hogyan van konfigurálva a hitelesítéshez.

- A felhőalapú alkalmazások az OpenID Connect, az OAuth, az SAML, a jelszóalapú, a csatolt vagy a letiltott módszereket használhatják egyszeri bejelentkezéshez. 
- A helyszíni alkalmazások jelszóalapú, integrált Windows-hitelesítést, fejlécalapú, csatolt vagy letiltott metódusokat használhatnak egyszeri bejelentkezéshez. A helyszíni lehetőségek akkor működnek, ha az alkalmazások alkalmazásproxyhoz vannak konfigurálva.

Ez a folyamatábra segít eldönteni, hogy melyik egyszeri bejelentkezési módszer a legmegfelelőbb az Ön helyzetéhez.

![Döntési folyamatábra az egyszeri bejelentkezési módszerhez](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

Az alábbi táblázat összefoglalja az egyszeri bejelentkezési módszereket, és további részletekre mutató hivatkozásokat tartalmaz.

| Egyszeri bejelentkezési módszer | Alkalmazástípusok | A következő esetekben használja |
| :------ | :------- | :----- |
| [OpenID Connect és OAuth](#openid-connect-and-oauth) | csak felhőben | Új alkalmazás kialakításakor használja az OpenID Connect és az OAuth alkalmazást. Ez a protokoll leegyszerűsíti az alkalmazások konfigurálását, könnyen használható SDK-kat használ, és lehetővé teszi az alkalmazás számára az MS Graph használatát.
| [SAML](#saml-sso) | felhő- és helyszíni | Amikor csak lehetséges, válassza az SAML lehetőséget az OpenID Connect vagy OAuth nem használó meglévő alkalmazásokhoz. Az SAML olyan alkalmazásoknál működik, amelyek az EGYIK SAML protokoll használatával hitelesítik magukat.|
| [Jelszóalapú](#password-based-sso) | felhő- és helyszíni | Akkor válassza a jelszóalapú lehetőséget, amikor az alkalmazás felhasználónévvel és jelszóval hitelesíti magát. A jelszóalapú egyszeri bejelentkezés lehetővé teszi az alkalmazásjelszó biztonságos tárolását és visszajátszását egy webböngésző-bővítmény vagy mobilalkalmazás használatával. Ez a módszer az alkalmazás által biztosított meglévő bejelentkezési folyamatot használja, de lehetővé teszi a rendszergazda számára a jelszavak kezelését. |
| [Kapcsolódó](#linked-sign-on) | felhő- és helyszíni | Válassza a csatolt bejelentkezést, ha az alkalmazás egy másik identitásszolgáltató szolgáltatásában egyszeri bejelentkezésre van konfigurálva. Ez a beállítás nem ad hozzá egyszeri bejelentkezést az alkalmazáshoz. Előfordulhat azonban azonban, hogy az alkalmazás már rendelkezik egy bejelentkezési megvalósítású egy másik szolgáltatás, például az Active Directory összevonási szolgáltatások használatával.|
| [Letiltva](#disabled-sso) | felhő- és helyszíni | Válassza a letiltott egyszeri bejelentkezést, ha az alkalmazás nem áll készen az egyszeri bejelentkezésre való konfigurálásra. Ez a mód az alapértelmezett az alkalmazás létrehozásakor.|
| [Integrált Windows-hitelesítés (IWA)](#integrated-windows-authentication-iwa-sso) | csak a helyszínen | Válassza az IWA egyszeri bejelentkezést [az integrált Windows-hitelesítést (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)vagy jogcímbarát alkalmazásokat használó alkalmazásokhoz. Az IWA esetében az alkalmazásproxy-összekötők Kerberos korlátozott delegálást (KCD) használnak az alkalmazás felhasználóinak hitelesítéséhez. |
| [Fejlécalapú](#header-based-sso) | csak a helyszínen | Akkor használjon fejlécalapú egyszeri bejelentkezést, ha az alkalmazás fejléceket használ a hitelesítéshez. A fejlécalapú egyszeri bejelentkezéshez PingAccess szükséges az Azure AD-hez. Az alkalmazásproxy az Azure AD segítségével hitelesíti a felhasználót, majd továbbítja a forgalmat az összekötő szolgáltatáson keresztül.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect és OAuth

Új alkalmazások fejlesztésekor használjon olyan modern protokollokat, mint az OpenID Connect és az OAuth, hogy a legjobb egyszeri bejelentkezési élményt nyújtsa az alkalmazáshoz több eszközön. Az OAuth lehetővé teszi a felhasználók vagy rendszergazdák számára, hogy [beleegyezést adjanak](configure-user-consent.md) az olyan védett erőforrásokhoz, mint a [Microsoft Graph](/graph/overview). Könnyen alkalmazható [SDK-kat](../develop/reference-v2-libraries.md) biztosítunk az alkalmazáshoz, és az alkalmazás készen áll a [Microsoft Graph](/graph/overview)használatára is.

További információkért lásd:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [A Microsoft identity platform fejlesztői útmutatója](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

## <a name="saml-sso"></a>SAML SSO

Az **SAML egyszeri bejelentkezéssel az**Azure AD hitelesíti magát az alkalmazás a felhasználó Azure AD-fiókjával. Az Azure AD egy kapcsolatprotokollon keresztül közli a bejelentkezési adatokat az alkalmazással. Az SAML-alapú egyszeri bejelentkezéssel a felhasználókat az SAML-jogcímekben megadott szabályok alapján adott alkalmazásszerepkörökhöz képezheti.

Válassza az SAML-alapú egyszeri bejelentkezést, ha az alkalmazás támogatja azt.

Az SAML-alapú egyszeri bejelentkezés az alábbi protokollok bármelyikét használó alkalmazások esetében támogatott:

- SAML 2.0
- WS-Federation

Saml-alapú egyszeri bejelentkezéss saas-alkalmazás konfigurálásához olvassa [el az SAML-alapú egyszeri bejelentkezés konfigurálása című témakört.](configure-single-sign-on-non-gallery-applications.md) Emellett számos szoftver szolgáltatásként (SaaS) alkalmazás rendelkezik egy [alkalmazásspecifikus oktatóanyaggal,](../saas-apps/tutorial-list.md) amely végiglépti az SAML-alapú egyszeri bejelentkezés konfigurációját.

A WS-Federation alkalmazáskonfigurálásához kövesse ugyanezt az útmutatást az SAML-alapú egyszeri bejelentkezés alkalmazásának konfigurálásához, [lásd: SAML-alapú egyszeri bejelentkezés konfigurálása.](configure-single-sign-on-non-gallery-applications.md) Az alkalmazás Azure AD-használatára való konfigurálásának lépésében le kell cserélnie az Azure AD bejelentkezési URL-címét a WS-Federation végponthoz. `https://login.microsoftonline.com/<tenant-ID>/wsfed`

Az SAML-alapú egyszeri bejelentkezéshez helyszíni alkalmazás konfigurálásához olvassa el [az EGY-HELYSZÍNI ALKALMAZÁSOK SAML egyszeri bejelentkezéscímű témakörét.](application-proxy-configure-single-sign-on-on-premises-apps.md)

Az SAML protokollról további információt az [Egyszeri bejelentkezéssaml protokoll című témakörben talál.](../develop/single-sign-on-saml-protocol.md)

## <a name="password-based-sso"></a>Jelszóalapú egyszeri bejelentkezés

A jelszóalapú bejelentkezéssel a felhasználók felhasználónévvel és jelszóval jelentkeznek be az alkalmazásba, amikor először férnek hozzá. Az első bejelentkezés után az Azure AD biztosítja a felhasználónevet és a jelszót az alkalmazáshoz.

A jelszóalapú egyszeri bejelentkezés az alkalmazás által biztosított meglévő hitelesítési folyamatot használja. Ha engedélyezi a jelszó egyszeri bejelentkezésegy alkalmazás, az Azure AD összegyűjti és biztonságosan tárolja a felhasználóneveket és jelszavakat az alkalmazáshoz. A felhasználói hitelesítő adatok titkosított állapotban tárolódnak a könyvtárban.

Válassza ki a jelszóalapú egyszeri bejelentkezést, ha:

- Egy alkalmazás nem támogatja az SAML egyszeri bejelentkezési protokollt.
- Az alkalmazások hozzáférési jogkivonatok és fejlécek helyett felhasználónévvel és jelszóval hitelesítik magukat.

A jelszóalapú egyszeri bejelentkezés minden olyan felhőalapú alkalmazás esetében támogatott, amely HTML-alapú bejelentkezési lappal rendelkezik. A felhasználó az alábbi böngészők bármelyikét használhatja:

- Internet Explorer 11 Windows 7-es vagy újabb verzióról
   > [!NOTE]
   > Az Internet Explorer korlátozott támogatással rendelkezik, és már nem kap új szoftverfrissítéseket. A Microsoft Edge az ajánlott böngésző.

- Microsoft Edge windows 10-es évfordulós kiadásban vagy újabb verzióban
- Microsoft Edge iOS és Android rendszeren
- Intune Managed Browser
- Chrome Windows 7 vagy újabb, valamint MacOS X vagy újabb rendszeren
- Firefox 26.0 vagy újabb verzió Windows XP SP2 vagy újabb, valamint Mac OS X 10.6 vagy újabb rendszeren

Ha egy felhőalapú egyszeri bejelentkezéshez szeretne beállítani egy felhőalapú alkalmazást, olvassa [el a Jelszó egyszeri bejelentkezés konfigurálása című témakört.](configure-password-single-sign-on-non-gallery-applications.md)

Ha egy helyszíni alkalmazást szeretne konfigurálni az alkalmazásproxyn keresztüli egyszeri bejelentkezéshez, olvassa el [a Jelszó-tárolók az alkalmazásproxyval történő egyszeri bejelentkezéshez című témakört.](application-proxy-configure-single-sign-on-password-vaulting.md)

### <a name="how-authentication-works-for-password-based-sso"></a>A jelszóalapú egyszeri bejelentkezés hitelesítésének működése

Egy felhasználó hitelesítéséhez egy alkalmazás, az Azure AD lekéri a felhasználó hitelesítő adatait a címtárból, és beírja azokat az alkalmazás bejelentkezési lapjára.  Az Azure AD biztonságosan továbbítja a felhasználói hitelesítő adatokat egy webböngésző-bővítményen vagy mobilalkalmazáson keresztül. Ez a folyamat lehetővé teszi a rendszergazda számára a felhasználói hitelesítő adatok kezelését, és nem követeli meg a felhasználóktól, hogy megjegyezzék a jelszavukat.

> [!IMPORTANT]
> A hitelesítő adatok a felhasználó tól az automatikus bejelentkezési folyamat során vannak eltimedve. A hitelesítő adatok azonban felderíthetők a webhiba-kereső eszközök használatával. A felhasználóknak és a rendszergazdáknak ugyanazokat a biztonsági házirendeket kell követniük, mintha a hitelesítő adatokat közvetlenül a felhasználó adta volna meg.

### <a name="managing-credentials-for-password-based-sso"></a>A jelszóalapú egyszeri bejelentkezés hitelesítő adatainak kezelése

Az egyes alkalmazások jelszavait az Azure AD-rendszergazda vagy a felhasználók is feltudják oldani.

Amikor az Azure AD-rendszergazda kezeli a hitelesítő adatokat:  

- A felhasználónak nem kell alaphelyzetbe állítania vagy megjegyeznie a felhasználónevet és a jelszót. A felhasználó elérheti az alkalmazást, ha rákattint a hozzáférési panelen vagy egy megadott linken keresztül.
- A rendszergazda felügyeleti feladatokat is eltud végezni a hitelesítő adatokon. A rendszergazda például frissítheti az alkalmazáshozzáférést a felhasználói csoporttagságok és az alkalmazottak állapota szerint.
- A rendszergazda rendszergazdai hitelesítő adatokkal biztosíthat hozzáférést a sok felhasználó között megosztott alkalmazásokhoz. A rendszergazda például engedélyezheti, hogy mindenki, aki hozzáfér egy alkalmazáshoz, hozzáférhessen egy közösségi médiához vagy dokumentummegosztó alkalmazáshoz.

Amikor a végfelhasználó kezeli a hitelesítő adatokat:

- A felhasználók szükség szerint frissíthetik vagy frissíthetik jelszavukat.
- A rendszergazdák továbbra is új hitelesítő adatokat állíthatnak be az alkalmazáshoz.

## <a name="linked-sign-on"></a>Csatolt bejelentkezés
A csatolt bejelentkezés lehetővé teszi, hogy az Azure AD egyszeri bejelentkezést biztosítson egy olyan alkalmazásnak, amely már konfigurálva van egy másik szolgáltatás egyszeri bejelentkezéséhez. A csatolt alkalmazás megjelenhet a végfelhasználók számára az Office 365 portálon vagy az Azure AD MyApps portálon. A felhasználó például elindíthat egy olyan alkalmazást, amely az Office 365-portálról egyszeri bejelentkezésre van konfigurálva az Active Directory összevonási szolgáltatások 2.0-s (AD FS) szolgáltatásban. További jelentéskészítési is elérhető az Office 365 portálról vagy az Azure AD MyApps portálról indított csatolt alkalmazásokhoz. A csatolt bejelentkezési alkalmazás konfigurálásához olvassa el [a Csatolt bejelentkezés konfigurálása című témakört.](configure-linked-sign-on.md)

### <a name="linked-sign-on-for-application-migration"></a>Csatolt bejelentkezés az alkalmazások áttelepítéséhez

A csatolt bejelentkezés egységes felhasználói élményt biztosíthat az alkalmazások egy adott időszakon keresztüli áttelepítése során. Ha alkalmazásokat telepít át az Azure Active Directoryba, a csatolt bejelentkezés sel gyorsan közzéteheti az összes áttelepíteni kívánt alkalmazásra mutató hivatkozásokat.  A felhasználók megtalálhatják az összes hivatkozást a [MyApps portálon](../user-help/active-directory-saas-access-panel-introduction.md) vagy az [Office 365 alkalmazásindítójában.](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) A felhasználók nem fogják tudni, hogy egy csatolt vagy egy áttelepített alkalmazáshoz férnek hozzá.  

Miután a felhasználó hitelesítette magát egy csatolt alkalmazással, létre kell hozni egy fiókrekordot, mielőtt a végfelhasználó egyszeri bejelentkezési hozzáférést kapna. A fiókbejegyzés kiépítése automatikusan, vagy manuálisan is előfordulhat a rendszergazda által.

## <a name="disabled-sso"></a>Letiltott sso

Letiltott mód azt jelenti, egyszeri bejelentkezés nem használható az alkalmazáshoz. Ha az egyszeri bejelentkezés le van tiltva, előfordulhat, hogy a felhasználóknak kétszer kell hitelesíteniük magukat. Először a felhasználók hitelesítik magukat az Azure AD-ben, majd bejelentkeznek az alkalmazásba.

Letiltott egyszeri bejelentkezési mód használata:

- Ha nem áll készen arra, hogy integrálja ezt az alkalmazást az Azure AD egyszeri bejelentkezéssel, vagy
- Ha az alkalmazás más aspektusait teszteli, vagy
- A helyszíni alkalmazások biztonsági rétegeként, amely nem igényli a felhasználók hitelesítését. Letiltás után a felhasználónak hitelesítenie kell magát.

Vegye figyelembe, hogy ha konfigurálta az alkalmazást sp-kezdeményezett SAML alapú egyszeri bejelentkezés, és módosítja az Egyszeri bejelentkezés mód letiltása, ez nem akadályozza meg a felhasználókat abban, hogy az alkalmazásba a MyApps portálon kívül. Ennek eléréséhez le kell [tiltania a felhasználók bejelentkezési képességét](disable-user-sign-in-portal.md)

## <a name="integrated-windows-authentication-iwa-sso"></a>Integrált Windows-hitelesítési (IWA) sso

[Az alkalmazásproxy](application-proxy.md) egyszeri bejelentkezést (SSO) biztosít [az integrált Windows-hitelesítést (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)vagy jogcímbarát alkalmazásokat használó alkalmazások számára. Ha az alkalmazás IWA-t használ, az alkalmazásproxy a Kerberos korlátozott delegálás (KCD) használatával hitelesíti magát az alkalmazás ban. Egy jogcímbarát alkalmazás, amely megbízik az Azure Active Directoryban, egyszeri bejelentkezés működik, mert a felhasználó már hitelesített az Azure AD használatával.

Válassza az integrált Windows-hitelesítés egyszeri bejelentkezési módot, ha egyszeri bejelentkezést szeretne biztosítani egy olyan helyszíni alkalmazásba, amely az IWA-val hitelesíti magát.

Az IWA helyszíni alkalmazásának konfigurálásához olvassa el a [Kerberos korlátozott delegálása az alkalmazásokba az alkalmazásproxyval történő egyszeri bejelentkezésről című témakört.](application-proxy-configure-single-sign-on-with-kcd.md)

### <a name="how-single-sign-on-with-kcd-works"></a>Hogyan működik az egyszeri bejelentkezés a KCD-vel?
Ez az ábra ismerteti a folyamatot, amikor egy felhasználó hozzáfér egy iWA-t használó helyszíni alkalmazáshoz.

![Microsoft Azure AD hitelesítési folyamatábrája](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. A felhasználó adja meg az URL-címet a helyszíni alkalmazás eléréséhez alkalmazásproxyn keresztül.
1. Az alkalmazásproxy átirányítja a kérelmet az Azure AD hitelesítési szolgáltatásaihoz az előzetes hitelesítéshez. Ezen a ponton az Azure AD alkalmazza a vonatkozó hitelesítési és engedélyezési szabályzatok, például a többtényezős hitelesítés. Ha a felhasználó érvényesítve van, az Azure AD létrehoz egy jogkivonatot, és elküldi azt a felhasználónak.
1. A felhasználó átadja a jogkivonatot az alkalmazásproxynak.
1. Az alkalmazásproxy érvényesíti a jogkivonatot, és lekéri a felhasználó egyszerű nevét (UPN) a jogkivonatból. Ezután elküldi a kérelmet, az egyszerű felhasználónév és a szolgáltatás egyszerű neve (SPN) az összekötő egy kettős hitelesítésű biztonságos csatornán keresztül.
1. Az összekötő kerberos korlátozott delegálás (KCD) egyeztetést használ a helyszíni AD-vel, és megszemélyesíti a felhasználót, hogy Kerberos-jogkivonatot kapjon az alkalmazáshoz.
1. Az Active Directory elküldi az alkalmazás Kerberos-jogkivonatát az összekötőnek.
1. Az összekötő elküldi az eredeti kérelmet az alkalmazáskiszolgálónak az AD-től kapott Kerberos-jogkivonat használatával.
1. Az alkalmazás elküldi a választ az összekötőnek, amelyezután visszakerül az alkalmazásproxy szolgáltatásnak, és végül a felhasználónak.

## <a name="header-based-sso"></a>Fejlécalapú egyszeri felsorak

A fejlécalapú egyszeri bejelentkezés http-fejléceket használó alkalmazásokesetében működik a hitelesítéshez. Ez a bejelentkezési módszer egy pingaccess nevű külső hitelesítési szolgáltatást használ. A felhasználónak csak az Azure AD-nek kell hitelesítenie magát.

Válassza a fejlécalapú egyszeri bejelentkezést, ha az alkalmazásproxy és a PingAccess konfigurálva van az alkalmazáshoz.

A fejlécalapú hitelesítés konfigurálásához olvassa el [a Fejlécalapú hitelesítés az alkalmazásproxyval történő egyszeri bejelentkezéshez című témakört.](application-proxy-configure-single-sign-on-with-ping-access.md)

### <a name="what-is-pingaccess-for-azure-ad"></a>Mi az Azure AD PingAccess?

Az Azure AD-hez készült PingAccess használatával a felhasználók hozzáférhetnek a hitelesítéshez fejléceket használó alkalmazásokhoz, és egyszeri bejelentkezéssel. Az alkalmazásproxy ezeket az alkalmazásokat ugyanúgy kezeli, mint bármely más alkalmazást, az Azure AD használatával hitelesíti a hozzáférést, majd továbbítja a forgalmat az összekötő szolgáltatáson keresztül. Hitelesítés után történik, a PingAccess szolgáltatás lefordítja az Azure AD hozzáférési jogkivonatot egy fejléc formátumba, amely az alkalmazásnak küldött.

A felhasználók nem vesznek észre semmi mást, amikor bejelentkeznek a vállalati alkalmazások használatához. Továbbra is működhetnek bárhonnan bármilyen eszközön. Az alkalmazásproxy-összekötők a távoli forgalmat irányítják az összes alkalmazáshoz, és továbbra is automatikusan betöltik az egyensúlyt.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Hogyan kaphatok licencet a PingAccess-hez?

Mivel ez a forgatókönyv az Azure AD és a PingAccess közötti partnerségen keresztül érhető el, mindkét szolgáltatáshoz licencre van szüksége. Az Azure AD Premium-előfizetések azonban tartalmaznak egy alapvető PingAccess-licencet, amely legfeljebb 20 alkalmazást fed le. Ha 20-nál több fejlécalapú alkalmazást kell közzétennie, további licencet szerezhet be a PingAccess programtól.

További információ: [Azure Active Directory-kiadások.](../fundamentals/active-directory-whatis.md)

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](../saas-apps/tutorial-list.md)
* [SAML-alapú egyszeri bejelentkezés konfigurálása](configure-single-sign-on-non-gallery-applications.md)
* [Jelszóalapú egyszeri bejelentkezés konfigurálása](configure-password-single-sign-on-non-gallery-applications.md)
* [Csatolt bejelentkezés konfigurálása](configure-linked-sign-on.md)
* [Bevezetés az alkalmazásokhoz való hozzáférés kezelésébe](what-is-access-management.md)
* Letöltési hivatkozás: [Egyszeri bejelentkezési telepítési terv](https://aka.ms/SSODeploymentPlan).

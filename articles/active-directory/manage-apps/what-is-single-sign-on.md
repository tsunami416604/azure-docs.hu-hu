---
title: Egyszeri bejelentkezés az Azure Active Directory-alkalmazást – |} A Microsoft Docs
description: Ismerje meg, hogyan válassza ki egy egyszeri bejelentkezési módszer, ha alkalmazások konfigurálása az Azure Active Directoryban (Azure AD). Egyszeri bejelentkezés használatára, a felhasználóknak nem kell minden alkalmazáshoz tartozó jelszavakat megjegyezni és fiókkezelési felügyeletének egyszerűsítése érdekében.
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
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79366070"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>Egyszeri bejelentkezés az Azure Active Directory-alkalmazások

Az egyszeri bejelentkezés (SSO) biztonsági és kényelmi funkciókat biztosít, amikor a felhasználók bejelentkeznek a Azure Active Directory (Azure AD) alkalmazásaiba. Ez a cikk az egyszeri bejelentkezés módszereit ismerteti, és segít kiválasztani a legmegfelelőbb SSO-módszert az alkalmazások konfigurálásakor.

- **Egyszeri bejelentkezés**esetén a felhasználók egyszer jelentkeznek be egyetlen fiókkal a tartományhoz csatlakoztatott eszközök, a vállalati erőforrások, a szolgáltatott szoftver (SaaS) alkalmazások és a webalkalmazások eléréséhez. Bejelentkezés után a felhasználó el is indíthatja az alkalmazások az Office 365 portál és az Azure AD MyApps hozzáférési panelen. A rendszergazdák központosítása felhasználóifiók-kezelés, és automatikusan hozzáadhat vagy eltávolíthat felhasználók csoport tagsága alapján alkalmazásokhoz való hozzáférése.

- **Egyszeri bejelentkezés nélkül a**felhasználóknak emlékezniük kell az alkalmazásfüggő jelszavakra, és be kell jelentkezniük minden alkalmazásba. Informatikai munkatársak kell létrehozni és frissíteni a felhasználói fiókok az egyes alkalmazások, például az Office 365-höz, a Box vagy a Salesforce. Felhasználók ne felejtse el a jelszavukat, valamint időt kelljen szánnia a jelentkezzen be a minden alkalmazás kell.

## <a name="choosing-a-single-sign-on-method"></a>Egyszeri bejelentkezés módszer kiválasztása

Többféleképpen is lehet az alkalmazás egyszeri bejelentkezés konfigurálása. Az egyszeri bejelentkezés módszerének kiválasztása attól függ, hogy az alkalmazás hogyan van konfigurálva a hitelesítéshez.

- A felhőalapú alkalmazások OpenID Connect-, OAuth-, SAML-, jelszó-alapú, csatolt vagy letiltott metódusokat használhatnak az egyszeri bejelentkezéshez. 
- A helyszíni alkalmazások használhatják a jelszóalapú, integrált Windows hitelesítést, az egyszeri bejelentkezés fejlécalapú, összekapcsolt vagy letiltott metódust. A helyszíni lehetőségek működnek, ha a alkalmazások Application Proxy van konfigurálva.

Ez a folyamatábra segít eldönteni, ajánlott az adott helyzetben melyik egyszeri bejelentkezésének módját.

![Az egyszeri bejelentkezési módszer döntési folyamatábrája](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

Az alábbi táblázat foglalja össze az egyszeri bejelentkezési módszereket, és hivatkozásokat tartalmaz további részleteket.

| Egyszeri bejelentkezési módszer | Alkalmazástípusok | A következő esetekben használja |
| :------ | :------- | :----- |
| [OpenID Connect és OAuth](#openid-connect-and-oauth) | csak felhő | Az OpenID Connect és a OAuth használata új alkalmazás fejlesztésekor. Ez a protokoll leegyszerűsíti az alkalmazások konfigurációját, és könnyen használható SDK-kat használ, és lehetővé teszi az alkalmazás számára az MS Graph használatát.
| [SAML](#saml-sso) | felhőbeli és helyszíni | Válassza ki az SAML-t, amikor lehetséges olyan meglévő alkalmazások esetében, amelyek nem használnak OpenID connectet vagy OAuth. Az SAML olyan alkalmazások esetében működik, amelyek az egyik SAML protokoll használatával végzik a hitelesítést.|
| [Jelszó-alapú](#password-based-sso) | felhőbeli és helyszíni | Válassza a jelszó-alapú lehetőséget, ha az alkalmazás hitelesíti magát a felhasználónévvel és a jelszóval. Jelszavas egyszeri bejelentkezés lehetővé teszi a biztonságos tárolását és ismétlését egy webböngésző-bővítmény vagy mobilalkalmazás segítségével. Ezt a módszert használja a meglévő bejelentkezési folyamat az alkalmazás által biztosított, de lehetővé teszi, hogy egy rendszergazda kezelje a jelszavakat. |
| [Csatolt](#linked-sign-on) | felhőbeli és helyszíni | Válassza a csatolt bejelentkezés lehetőséget, ha az alkalmazás az egyszeri bejelentkezéshez van konfigurálva egy másik identitás-szolgáltatói szolgáltatásban. Ez a beállítás nem adja hozzá egyszeri bejelentkezés az alkalmazáshoz. Az alkalmazás azonban már előfordulhat, hogy rendelkezik az egyszeri bejelentkezés egy másik szolgáltatás, például az Active Directory összevonási szolgáltatások használatával implementált.|
| [Letiltva](#disabled-sso) | felhőbeli és helyszíni | Válassza a letiltott egyszeri bejelentkezés lehetőséget, ha az alkalmazás nem áll készen az egyszeri bejelentkezésre való konfigurálásra. Ez az alapértelmezett mód az alkalmazás létrehozásakor.|
| [Integrált Windows-hitelesítés (IWA)](#integrated-windows-authentication-iwa-sso) | csak helyszíni | Az [integrált Windows-hitelesítést (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)vagy jogcímbarát alkalmazásokat használó alkalmazások esetében válassza az IWA egyszeri bejelentkezés lehetőséget. A IWA esetében az alkalmazásproxy-összekötők a Kerberos által korlátozott delegálás (KCD) használatával hitelesítik a felhasználókat az alkalmazásban. |
| [Fejléc-alapú](#header-based-sso) | csak helyszíni | Használja fejléc-alapú egyszeri bejelentkezés, ha az alkalmazás fejléceket használja a hitelesítéshez. A fejléc-alapú egyszeri bejelentkezéshez az Azure AD PingAccess van szükség. Az alkalmazásproxy az Azure AD segítségével hitelesíti a felhasználót, és ezután továbbítja a forgalmat az összekötő szolgáltatást.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect és OAuth

Új alkalmazások fejlesztésekor a modern protokollok, például az OpenID Connect és a OAuth segítségével érheti el a legjobb egyszeri bejelentkezési élményt az alkalmazáshoz több eszköz platformján. A OAuth lehetővé teszi [, hogy a](configure-user-consent.md) felhasználók vagy a rendszergazdák beleegyeznek a védett erőforrások, például a [Microsoft Graph](/graph/overview)számára. Egyszerűen bevezetheti az [SDK](../develop/reference-v2-libraries.md) -kat az alkalmazásához, és emellett az alkalmazás készen áll a [Microsoft Graph](/graph/overview)használatára.

További információkért lásd:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [A Microsoft Identity platform fejlesztői útmutatója](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

## <a name="saml-sso"></a>SAML SSO

Az **SAML egyszeri bejelentkezéssel**az Azure ad hitelesíti az alkalmazást a felhasználó Azure ad-fiókjának használatával. Azure ad-ben a bejelentkezési adatait az alkalmazás egy kapcsolat protokollon keresztül kommunikál. Az SAML-alapú egyszeri bejelentkezéssel az SAML-jogcímek által meghatározott szabályok alapján leképezheti a felhasználókat bizonyos alkalmazási szerepkörökre.

Ha az alkalmazás támogatja, válassza az SAML-alapú egyszeri bejelentkezés lehetőséget.

SAML-alapú egyszeri bejelentkezés bármely ezeket a protokollokat használó alkalmazások esetében támogatott:

- SAML 2.0
- WS-Federation

Az SAML-alapú egyszeri bejelentkezéshez használható SaaS-alkalmazás konfigurálásával kapcsolatban lásd: [SAML-alapú egyszeri bejelentkezés konfigurálása](configure-single-sign-on-non-gallery-applications.md). Emellett számos szoftveres (SaaS) alkalmazás rendelkezik egy [alkalmazásspecifikus oktatóanyaggal](../saas-apps/tutorial-list.md) , amely VÉGIGVEZETI az SAML-alapú egyszeri bejelentkezés konfigurációjának lépésein.

A WS-Federation alkalmazás konfigurálásához kövesse ugyanezt az útmutatást az alkalmazás SAML-alapú egyszeri bejelentkezéshez való konfigurálásához: [SAML-alapú egyszeri bejelentkezés konfigurálása](configure-single-sign-on-non-gallery-applications.md). Az alkalmazás Azure AD használatára való konfigurálásának lépéséhez le kell cserélnie a WS-Federation végpont `https://login.microsoftonline.com/<tenant-ID>/wsfed`Azure AD bejelentkezési URL-címét.

A helyszíni alkalmazások SAML-alapú egyszeri bejelentkezéshez való konfigurálásáról lásd: [SAML egyszeri bejelentkezés a helyszíni alkalmazásokhoz alkalmazásproxy](application-proxy-configure-single-sign-on-on-premises-apps.md)használatával.

További információ az SAML protokollról: [egyszeri bejelentkezéses SAML protokoll](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>Jelszóalapú egyszeri bejelentkezés

A jelszó-alapú bejelentkezéssel a felhasználók első alkalommal jelentkeznek be az alkalmazásba egy felhasználónévvel és jelszóval. Az első bejelentkezés után az Azure AD megadja a felhasználónevet és a jelszót az alkalmazáshoz.

Jelszavas egyszeri bejelentkezést használ a meglévő hitelesítési folyamatot, az alkalmazás által biztosított. Ha engedélyezi a jelszó egyszeri bejelentkezést az alkalmazáshoz, az Azure ad-ben gyűjt, és biztonságosan tárolja a felhasználói neveket és jelszavakat az alkalmazás. Felhasználói hitelesítő adatok titkosítására a könyvtárban vannak tárolva.

Válassza a jelszó alapú egyszeri bejelentkezés lehetőséget, ha:

- Egy alkalmazás nem támogatja az SAML egyszeri bejelentkezési protokollt.
- Felhasználónév és jelszó helyett hozzáférési jogkivonatok és a fejlécek az alkalmazás végzi a hitelesítést.

Jelszavas egyszeri bejelentkezés bármely felhőalapú alkalmazás, amely rendelkezik egy HTML-alapú bejelentkezési oldal támogatott. A felhasználó az alábbi böngészők bármelyike használható:

- Az Internet Explorer 11 Windows 7-es vagy újabb
   > [!NOTE]
   > Az Internet Explorer korlátozott támogatást nyújt, és többé nem kap új szoftverfrissítéseket. A Microsoft Edge az ajánlott böngésző.

- Microsoft Edge a Windows 10 évfordulós kiadásán vagy később
- Microsoft Edge iOS és Android rendszerhez
- Intune Managed Browser
- Chrome, a Windows 7 vagy újabb, és a MacOS X rendszeren vagy újabb
- Firefox 26.0 vagy újabb, Windows XP SP2 vagy újabb, és a Mac OS X 10.6 vagy újabb

A jelszó-alapú egyszeri bejelentkezéshez használt felhőalapú alkalmazás konfigurálásával kapcsolatban lásd: [jelszó egyszeri bejelentkezésének konfigurálása](configure-password-single-sign-on-non-gallery-applications.md).

Ha helyszíni alkalmazást szeretne konfigurálni az Application proxyn keresztüli egyszeri bejelentkezéshez, tekintse meg a [jelszó-tároló az egyszeri bejelentkezéshez az alkalmazásproxy](application-proxy-configure-single-sign-on-password-vaulting.md) használatával című témakört.

### <a name="how-authentication-works-for-password-based-sso"></a>Hogyan működik a hitelesítés a jelszó-alapú egyszeri bejelentkezéshez

Ha egy felhasználót szeretne hitelesíteni egy alkalmazásban, az Azure AD lekéri a felhasználó hitelesítő adatait a címtárból, és beírja azokat az alkalmazás bejelentkezési oldalára.  Az Azure AD biztonságosan adja át a felhasználói hitelesítő adatok egy webböngésző-bővítmény vagy mobilalkalmazás segítségével. Ez a folyamat lehetővé teszi, hogy egy rendszergazda kezelje a felhasználói hitelesítő adatokat, és nincs szükség a felhasználók számára ne felejtse el a jelszavát.

> [!IMPORTANT]
> A hitelesítő adatokat a rendszer az automatikus bejelentkezési folyamat során eltorzítja a felhasználótól. Azonban a hitelesítő adatokat is felderíthető webhelyen-hibakereső eszközeivel. A felhasználók és rendszergazdák kell követniük ugyanazon biztonsági szabályzatok, mintha közvetlenül a felhasználó által megadott hitelesítő adatok.

### <a name="managing-credentials-for-password-based-sso"></a>Hitelesítő adatok jelszóalapú egyszeri bejelentkezés kezelése

Egyes alkalmazásokhoz tartozó jelszavakat vagy az Azure AD-rendszergazda vagy a felhasználók kezelhető.

Ha az Azure AD-rendszergazda kezeli a hitelesítő adatokat:  

- A felhasználónak nem kell alaphelyzetbe állítása, vagy jegyezze meg a felhasználónevet és jelszót. A felhasználók elérhetik az alkalmazást a hozzáférési paneljükön, illetve keresztül megadott hivatkozás megnyitásához.
- A rendszergazda megteheti a hitelesítő adatokat a felügyeleti feladatokat. A rendszergazda például frissítheti az alkalmazás-hozzáférés vendégfelhasználói csoporttagságok és az alkalmazott állapota alapján.
- A rendszergazda a rendszergazdai hitelesítő adatok segítségével hozzáférést biztosíthat az alkalmazásokhoz számos felhasználó használ. Ha például a rendszergazda engedélyezheti mindenki számára, ki férhet hozzá az alkalmazás rendelkezik hozzáféréssel a közösségi médiában vagy a dokumentumot megosztó alkalmazás.

Amikor a felhasználó kezeli a hitelesítő adatokat:

- Felhasználók kezelhetik a jelszavukat a frissítése vagy törlése őket igény szerint.
- A rendszergazdák továbbra is képesek az alkalmazás új hitelesítő adatok beállítása.

## <a name="linked-sign-on"></a>Csatolt bejelentkezés
Csatolt bejelentkezés lehetővé teszi az Azure AD egyszeri bejelentkezés egy alkalmazás, amely már konfigurálva van az egyszeri bejelentkezés egy másik szolgáltatás biztosít. A társított alkalmazást a végfelhasználók számára az Office 365 portálon vagy az Azure AD MyApps portálról is megjelenhetnek. Például egy felhasználó indíthatja el olyan alkalmazás, amely konfigurálva van egyszeri bejelentkezés az Active Directory összevonási szolgáltatások 2.0 (AD FS) az Office 365-portálról. További jelentési érhető el az Office 365 portálon vagy az Azure AD MyApps portálról indítja társított alkalmazások esetében. Ha egy alkalmazást a csatolt bejelentkezéshez szeretne konfigurálni, tekintse meg a [csatolt bejelentkezés konfigurálása](configure-linked-sign-on.md)című témakört.

### <a name="linked-sign-on-for-application-migration"></a>Csatolt bejelentkezés az alkalmazás áttelepítéséhez

A csatolt bejelentkezés egységes felhasználói élményt biztosíthat az alkalmazások egy adott időszakban történő áttelepítésekor. Ha Azure Active Directoryre telepíti át az alkalmazásokat, a csatolt bejelentkezés használatával gyorsan közzéteheti az összes áttelepíteni kívánt alkalmazás hivatkozásait.  A felhasználók a [MyApps-portálon](../user-help/active-directory-saas-access-panel-introduction.md) vagy az [Office 365 Application launcherben](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a)is megtalálhatják a hivatkozásokat. Felhasználók nem fognak ismerni a társított alkalmazások vagy az áttelepített alkalmazásokat fér hozzá.  

Miután egy felhasználó hitelesítette az társított alkalmazást, egy ügyfélrekordot kell létrehozni, mielőtt a felhasználó egyszeri bejelentkezéses hozzáférést van megadva. Ez a fiók rekord kiépítés vagy automatikusan történik, vagy azt egy rendszergazda manuálisan fordulhat.

## <a name="disabled-sso"></a>Letiltott egyszeri bejelentkezés

Letiltott mód azt jelenti, hogy egyszeri bejelentkezés az alkalmazás nem használ. Ha az egyszeri bejelentkezés le van tiltva, a felhasználóknak kell előfordulhat, hogy kétszer hitelesíteni. Először a felhasználók hitelesítéséhez az Azure AD, és majd bejelentkeznek az alkalmazásba.

Használata le van tiltva az egyszeri bejelentkezési mód:

- Ha még nem készen áll az alkalmazás integrálása az Azure AD egyszeri bejelentkezés, vagy
- Ha a tesztelést az alkalmazás más aspektusait, vagy
- Egy helyszíni alkalmazásba biztonsági réteget, a felhasználóknak a hitelesítést nem igénylő. Le van tiltva a felhasználónak hitelesítenie kell.

Vegye figyelembe, hogy ha konfigurálta az alkalmazást a (z) SP-ben kezdeményezett SAML-alapú egyszeri bejelentkezéshez, és az egyszeri bejelentkezés üzemmódját a Letiltás értékre módosítja, akkor nem állítja le a felhasználókat a MyApps-portálon kívüli alkalmazásba való bejelentkezésre. Ennek eléréséhez [le kell tiltania a felhasználók bejelentkezési képességét](disable-user-sign-in-portal.md)

## <a name="integrated-windows-authentication-iwa-sso"></a>Integrált Windows-hitelesítés (IWA) egyszeri bejelentkezés

Az [alkalmazásproxy](application-proxy.md) lehetővé teszi az egyszeri bejelentkezést (SSO) olyan alkalmazások számára, amelyek [integrált Windows-hitelesítést (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)vagy jogcímbarát alkalmazásokat használnak. Ha az alkalmazás IWA, alkalmazásproxy hitelesíti magát az alkalmazás a Kerberos által korlátozott delegálás (KCD) használatával. Jogcímbarát alkalmazáshoz, amely megbízik az Azure Active Directory egyszeri bejelentkezés működik, mert az Azure AD-vel már hitelesíti a felhasználót.

Az integrált Windows-hitelesítés egyszeri bejelentkezési módjának kiválasztásával egyszeri bejelentkezést biztosíthat egy helyszíni alkalmazás számára, amely a IWA-vel végzi a hitelesítést.

A helyszíni alkalmazások IWA való konfigurálásával kapcsolatban lásd: [Kerberos által korlátozott delegálás az alkalmazásokba való egyszeri bejelentkezéshez az alkalmazásproxy használatával](application-proxy-configure-single-sign-on-with-kcd.md).

### <a name="how-single-sign-on-with-kcd-works"></a>Az egyszeri bejelentkezés KCD Works
Ez az ábra ismerteti a folyamatot, amikor egy felhasználó hozzáfér IWA használó helyszíni alkalmazások.

![Microsoft Azure AD hitelesítési folyamat diagramja](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. A felhasználó beírja az URL-címet a helyszíni alkalmazás alkalmazás-proxyn keresztüli eléréséhez.
1. Az alkalmazásproxy átirányítja a kérést az Azure AD authentication szolgáltatások tudnak preauthenticate. Ezen a ponton az Azure AD bármilyen megfelelő hitelesítési és engedélyezési házirendeket, mint a többtényezős hitelesítés vonatkozik. Ha a felhasználó érvényesítését, Azure ad-ben létrehoz egy jogkivonatot, és elküldi azokat a felhasználó.
1. A felhasználó az alkalmazásproxy továbbítja a jogkivonatot.
1. Az alkalmazásproxy érvényesíti a jogkivonatot, és beolvassa az egyszerű felhasználónév (UPN) a jogkivonatot. Ez ezután elküldi a kérést, az egyszerű Felhasználónevet és az egyszerű szolgáltatásnév (SPN) az összekötő dually hitelesített biztonságos csatornán keresztül történik.
1. Az összekötő Kerberos által korlátozott delegálás (KCD) egyeztetést használ a helyszíni AD-vel, megszemélyesítve a felhasználót, hogy Kerberos-tokent kapjon az alkalmazáshoz.
1. Az Active Directory elküldi az alkalmazás az összekötő Kerberos-jogkivonat.
1. Az összekötő az eredeti kérést küld a kiszolgáló, az AD-ből kapott Kerberos-jogkivonat használatával.
1. Az alkalmazás visszaküldi a választ, az összekötőre, amelyet ezután visszaküld az alkalmazásproxy-szolgáltatás, és végül a felhasználó számára.

## <a name="header-based-sso"></a>Fejléc-alapú egyszeri bejelentkezés

Fejléc-alapú egyszeri bejelentkezés a hitelesítéshez a HTTP-fejlécek használó alkalmazások esetében működik. A bejelentkezési módszer PingAccess nevű harmadik fél hitelesítési szolgáltatást használ. A felhasználó csak az Azure AD hitelesítenie kell.

Válassza a fejléc-alapú egyszeri bejelentkezés lehetőséget, ha az alkalmazásproxy és a PingAccess konfigurálva van az alkalmazáshoz.

A fejléc-alapú hitelesítés konfigurálásához tekintse [meg az Application proxyval történő egyszeri bejelentkezéshez használt fejléc-alapú hitelesítést](application-proxy-configure-single-sign-on-with-ping-access.md).

### <a name="what-is-pingaccess-for-azure-ad"></a>Mi az az Azure AD-alapú PingAccess?

PingAccess Azure AD-felhasználók számára is hozzáférést és egyszeri bejelentkezés használatával olyan alkalmazások, amelyek a fejlécek használata a hitelesítéshez. Application Proxy ezeket az alkalmazásokat, mint bármely más, az Azure AD segítségével hitelesíti a hozzáférést, majd az összekötő szolgáltatás forgalmát kezeli. Miután történik hitelesítés, a PingAccess szolgáltatás fordítja le az Azure AD hozzáférési jogkivonatot, amelyet az alkalmazás elküld egy fejléc formátuma.

A felhasználók nem figyelje meg, hogy bármi más, amikor bejelentkeznek a vállalati alkalmazások használatát. Továbbra is dolgozhatnak bárhol, bármilyen eszközön. Az alkalmazásproxy-összekötők közvetlen alkalmazások távoli forgalmat, és azok továbbra is automatikusan terheléselosztásához.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Hogyan szerezhetek licencet a PingAccess?

Mivel ez a forgatókönyv az Azure AD és a PingAccess közötti partneri kapcsolaton keresztül érhető el, mindkét szolgáltatáshoz licenc szükséges. Prémium szintű Azure AD előfizetések azonban egy alapszintű PingAccess-licencet tartalmaznak, amely akár 20 alkalmazást is magában foglal. Ha több mint 20 fejléc-alapú alkalmazások közzétételét van szüksége, további licencre a PingAccess szerezheti be.

További információk: [Azure Active Directory editions](../fundamentals/active-directory-whatis.md) (Azure Active Directory-kiadások).

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](../saas-apps/tutorial-list.md)
* [SAML-alapú egyszeri bejelentkezés konfigurálása](configure-single-sign-on-non-gallery-applications.md)
* [Jelszó alapú egyszeri bejelentkezés konfigurálása](configure-password-single-sign-on-non-gallery-applications.md)
* [Csatolt bejelentkezés konfigurálása](configure-linked-sign-on.md)
* [Az alkalmazásokhoz való hozzáférés kezelésének bemutatása](what-is-access-management.md)
* Letöltési hivatkozás: [egyszeri bejelentkezési telepítési terv](https://aka.ms/SSODeploymentPlan).

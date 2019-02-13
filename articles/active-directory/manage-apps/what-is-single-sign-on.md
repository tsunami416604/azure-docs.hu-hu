---
title: Egyszeri bejelentkezés az Azure Active Directory-alkalmazást – |} A Microsoft Docs
description: Ismerje meg, hogyan válassza ki egy egyszeri bejelentkezési módszer, ha alkalmazások konfigurálása az Azure Active Directoryban (Azure AD). Egyszeri bejelentkezés használatára, a felhasználóknak nem kell minden alkalmazáshoz tartozó jelszavakat megjegyezni és fiókkezelési felügyeletének egyszerűsítése érdekében.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: celested
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6984307dda58aeba840f2b6d08e84fb4f60cacc8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163070"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>Egyszeri bejelentkezés az Azure Active Directory-alkalmazások
Egyszeri bejelentkezés (SSO) ad hozzá biztonsági és kényelmet, amikor a felhasználók bejelentkezés az Azure Active Directoryban (Azure AD-) alkalmazások. Ez a cikk az egyszeri bejelentkezési módszereket írja le, és segítséget nyújt az alkalmazások konfigurálásakor, válassza ki a legmegfelelőbb egyszeri bejelentkezési módszert.

- **Az egyszeri bejelentkezés**, egy fiókhoz való hozzáférés a tartományhoz csatlakoztatott eszközök bejelentkezéssel felhasználók vállalati erőforrásokhoz, mint a szoftverszolgáltatások (SaaS) alkalmazások, szoftverek és webalkalmazásokat. Bejelentkezés után a felhasználó el is indíthatja az alkalmazások az Office 365 portál és az Azure AD MyApps hozzáférési panelen. A rendszergazdák központosítása felhasználóifiók-kezelés, és automatikusan hozzáadhat vagy eltávolíthat felhasználók csoport tagsága alapján alkalmazásokhoz való hozzáférése. 

- **Egyszeri bejelentkezés nélkül**, felhasználók kell alkalmazásspecifikus jelszó megjegyzése, és jelentkezzen be minden alkalmazáshoz. Informatikai munkatársak kell létrehozni és frissíteni a felhasználói fiókok az egyes alkalmazások, például az Office 365-höz, a Box vagy a Salesforce. Felhasználók ne felejtse el a jelszavukat, valamint időt kelljen szánnia a jelentkezzen be a minden alkalmazás kell.

## <a name="choosing-a-single-sign-on-method"></a>Egyszeri bejelentkezés módszer kiválasztása

Többféleképpen is lehet az alkalmazás egyszeri bejelentkezés konfigurálása. Egyszeri bejelentkezés módszer kiválasztása attól függ, hogy az alkalmazás hogyan van konfigurálva a hitelesítéshez. 

- A felhőalapú alkalmazások OpenID Connect, OAuth, SAML, jelszóalapú, a társított, vagy letiltott módszereket használhatja az egyszeri bejelentkezés. 
- A helyszíni alkalmazások használhatják a jelszóalapú, integrált Windows hitelesítést, az egyszeri bejelentkezés fejlécalapú, összekapcsolt vagy letiltott metódust. A helyszíni lehetőségek működnek, ha a alkalmazások Application Proxy van konfigurálva.

Ez a folyamatábra segít eldönteni, ajánlott az adott helyzetben melyik egyszeri bejelentkezésének módját. 

![Egyszeri bejelentkezés módszer kiválasztása](./media/what-is-single-sign-on/choose-single-sign-on-method.png)

Az alábbi táblázat foglalja össze az egyszeri bejelentkezési módszereket, és hivatkozásokat tartalmaz további részleteket. 

| Egyszeri bejelentkezési módszer | Alkalmazástípusok | A következő esetekben használja |
| :------ | :------- | :----- |
| [OpenID Connect és az OAuth révén](#openid-connect-and-oauth) | Csak a felhőben | OpenID Connect és az OAuth használata egy új alkalmazások fejlesztése során. Ez a protokoll egyszerűbbé teszi az alkalmazás konfigurációja, egyszerűen használható SDK-kkal rendelkezik, és lehetővé teszi, hogy az alkalmazás használhatja az MS Graph.
| [SAML](#saml-sso) | Csak a felhőben | Válassza ki a SAML, amikor csak lehetséges, a meglévő alkalmazásokat, amelyek nem használnak az OpenID Connect vagy az OAuth szolgáltatástól. Az SAML az alkalmazásokat, amelyek hitelesítést végezni a SAML-protokoll esetében működik.|
| [Jelszóalapú](#password-based-sso) | felhőbeli és helyszíni | Válassza a jelszóalapú, amikor az alkalmazás hitelesíti a felhasználónevet és jelszót. Jelszavas egyszeri bejelentkezés lehetővé teszi a biztonságos tárolását és ismétlését egy webböngésző-bővítmény vagy mobilalkalmazás segítségével. Ezt a módszert használja a meglévő bejelentkezési folyamat az alkalmazás által biztosított, de lehetővé teszi, hogy egy rendszergazda kezelje a jelszavakat. |
| [A csatolt](#linked-sso) | felhőbeli és helyszíni | Válassza ki társított egyszeri bejelentkezést, amikor az alkalmazás úgy van konfigurálva, az egyszeri bejelentkezés egy másik identitás szolgáltató szolgáltatásban. Ez a beállítás nem adja hozzá egyszeri bejelentkezés az alkalmazáshoz. Az alkalmazás azonban már előfordulhat, hogy rendelkezik az egyszeri bejelentkezés egy másik szolgáltatás, például az Active Directory összevonási szolgáltatások használatával implementált.|
| [Letiltva](#disabled-sso) | felhőbeli és helyszíni | Válassza a letiltott egyszeri bejelentkezés, ha az alkalmazás nem áll készen az egyszeri bejelentkezéshez konfigurálandó. Adja meg a felhasználónevüket és jelszavukat minden alkalommal, amikor az alkalmazás indításakor kell felhasználók.|
| [Integrált Windows-hitelesítés (IWA)](#integrated-windows-authentication-iwa-sso) | csak helyszíni | IWA egyszeri bejelentkezést használó alkalmazások esetében válassza [integrált Windows-hitelesítés (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication), vagy jogcímeket használó alkalmazásokat. IWA az alkalmazásproxy-összekötők használatával a Kerberos által korlátozott delegálás (KCD) hitelesítheti a felhasználókat az alkalmazáshoz. | 
| [Fejlécalapú](#header-based-sso) | csak helyszíni | Használja fejléc-alapú egyszeri bejelentkezés, ha az alkalmazás fejléceket használja a hitelesítéshez. Fejléc-alapú egyszeri bejelentkezés szükséges a PingAccess Azure AD-hez. Az alkalmazásproxy az Azure AD segítségével hitelesíti a felhasználót, és ezután továbbítja a forgalmat az összekötő szolgáltatást.  | 

## <a name="openid-connect-and-oauth"></a>OpenID Connect és az OAuth révén
Új alkalmazások fejlesztésekor a modern protokollok, mint például az OpenID Connect és az OAuth használatával érhet el a legjobb egyszeri bejelentkezést az alkalmazáshoz több eszköz platformon. OAuth lehetővé teszi a felhasználók vagy rendszergazdák [hozzájárulását](configure-user-consent.md) a védett erőforrások, például a [az MS Graph](/graph/overview). Kínálunk egyszerű bevezetéséhez [SDK-k](../develop/reference-v2-libraries.md) az alkalmazáshoz, és emellett az alkalmazás használatra kész lesz [az MS Graph](/graph/overview).

További információkért lásd:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Az Azure Active Directory fejlesztői útmutatója](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

## <a name="saml-sso"></a>SAML EGYSZERI BEJELENTKEZÉS
A **SAML egyszeri bejelentkezés**, az Azure AD akkor hitelesíti az alkalmazásnak a felhasználó Azure AD-fiók használatával. Azure ad-ben a bejelentkezési adatait az alkalmazás egy kapcsolat protokollon keresztül kommunikál. SAML-alapú egyszeri bejelentkezés, a felhasználók leképezheti a SAML-jogcímek az meghatározott szabályok alapján adott alkalmazás-szerepkörökhöz.

Válassza az SAML-alapú egyszeri bejelentkezés, ha az alkalmazás ezt támogatja.


SAML-alapú egyszeri bejelentkezés bármely ezeket a protokollokat használó alkalmazások esetében támogatott:

- SAML 2.0
- WS-Federation

Egy alkalmazás az SAML-alapú egyszeri bejelentkezés beállítása: [konfigurálása SAML-alapú egyszeri bejelentkezés](configure-single-sign-on-portal.md). Is, mint a szoftverszolgáltatások (SaaS) alkalmazások számos szoftver rendelkezik egy [alkalmazásspecifikus oktatóanyag](../saas-apps/tutorial-list.md) , amely végigvezeti az SAML-alapú egyszeri bejelentkezés konfigurációja. 

Az SAML-protokoll kapcsolatos további információkért lásd: [egyszeri bejelentkezéses SAML-protokoll](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>Jelszóalapú egyszeri bejelentkezés
Jelszóalapú bejelentkezés, a felhasználók jelentkezzen be az alkalmazást az első használatakor a felhasználónevet és jelszót. Miután az első bejelentkezés Azure ad-ben a felhasználónevet és jelszót, hogy az alkalmazás választékát kínálja. 

Jelszavas egyszeri bejelentkezést használ a meglévő hitelesítési folyamatot, az alkalmazás által biztosított. Ha engedélyezi a jelszó egyszeri bejelentkezést az alkalmazáshoz, az Azure ad-ben gyűjt, és biztonságosan tárolja a felhasználói neveket és jelszavakat az alkalmazás. Felhasználói hitelesítő adatok titkosítására a könyvtárban vannak tárolva. 

Válassza ki a jelszóalapú egyszeri bejelentkezés mikor:

- Egy alkalmazás nem támogatja az SAML egyszeri bejelentkezési protokoll.
- Felhasználónév és jelszó helyett hozzáférési jogkivonatok és a fejlécek az alkalmazás végzi a hitelesítést.

Jelszavas egyszeri bejelentkezés bármely felhőalapú alkalmazás, amely rendelkezik egy HTML-alapú bejelentkezési oldal támogatott. A felhasználó az alábbi böngészők bármelyike használható:

- Az Internet Explorer 11 Windows 7-es vagy újabb
- A Microsoft Edge a Windows 10 Évfordulós kiadása vagy újabb
- Chrome, a Windows 7 vagy újabb, és a MacOS X rendszeren vagy újabb
- Firefox 26.0 vagy újabb, Windows XP SP2 vagy újabb, és a Mac OS X 10.6 vagy újabb

Felhőalapú alkalmazások a jelszóalapú egyszeri bejelentkezés beállítása: [az alkalmazás a jelszavas egyszeri bejelentkezés konfigurálása](application-sign-in-problem-password-sso-gallery.md#configure-the-application-for-password-single-sign-on).

Egyszeri bejelentkezés alkalmazásproxyn keresztül a helyszíni alkalmazás beállítása: [az egyszeri bejelentkezés alkalmazásproxyval való vaulting jelszó](application-proxy-configure-single-sign-on-password-vaulting.md)

### <a name="how-authentication-works-for-password-based-sso"></a>Hitelesítés a jelszóalapú egyszeri bejelentkezés működése

Hitelesíteni a felhasználót, hogy egy alkalmazás, az Azure AD kérdezi le a felhasználó hitelesítő adatait a könyvtárból, és beírja őket az alkalmazás bejelentkezési oldalra.  Az Azure AD biztonságosan adja át a felhasználói hitelesítő adatok egy webböngésző-bővítmény vagy mobilalkalmazás segítségével. Ez a folyamat lehetővé teszi, hogy egy rendszergazda kezelje a felhasználói hitelesítő adatokat, és nincs szükség a felhasználók számára ne felejtse el a jelszavát.

> [!IMPORTANT]
> A hitelesítő adatok vannak rejtjelezett felhasználói az automatikus bejelentkezési folyamat során. Azonban a hitelesítő adatokat is felderíthető webhelyen-hibakereső eszközeivel. A felhasználók és rendszergazdák kell követniük ugyanazon biztonsági szabályzatok, mintha közvetlenül a felhasználó által megadott hitelesítő adatok.

### <a name="managing-credentials-for-password-based-sso"></a>Hitelesítő adatok jelszóalapú egyszeri bejelentkezés kezelése

Egyes alkalmazásokhoz tartozó jelszavakat vagy az Azure AD-rendszergazda vagy a felhasználók kezelhető.

Ha az Azure AD-rendszergazda kezeli a hitelesítő adatokat:  

- A felhasználónak nem kell alaphelyzetbe állítása, vagy jegyezze meg a felhasználónevet és jelszót. A felhasználók elérhetik az alkalmazást a hozzáférési paneljükön, illetve keresztül megadott hivatkozás megnyitásához.
- A rendszergazda megteheti a hitelesítő adatokat a felügyeleti feladatokat. A rendszergazda például frissítheti az alkalmazás-hozzáférés vendégfelhasználói csoporttagságok és az alkalmazott állapota alapján.
- A rendszergazda a rendszergazdai hitelesítő adatok segítségével hozzáférést biztosíthat az alkalmazásokhoz számos felhasználó használ. Ha például a rendszergazda engedélyezheti mindenki számára, ki férhet hozzá az alkalmazás rendelkezik hozzáféréssel a közösségi médiában vagy a dokumentumot megosztó alkalmazás.

Amikor a felhasználó kezeli a hitelesítő adatokat:

- Felhasználók kezelhetik a jelszavukat a frissítése vagy törlése őket igény szerint. 
- A rendszergazdák továbbra is képesek az alkalmazás új hitelesítő adatok beállítása.


## <a name="linked-sso"></a>A csatolt egyszeri bejelentkezés
Csatolt bejelentkezés lehetővé teszi az Azure AD egyszeri bejelentkezés egy alkalmazás, amely már konfigurálva van az egyszeri bejelentkezés egy másik szolgáltatás biztosít. A társított alkalmazást a végfelhasználók számára az Office 365 portálon vagy az Azure AD MyApps portálról is megjelenhetnek. Például egy felhasználó indíthatja el olyan alkalmazás, amely konfigurálva van egyszeri bejelentkezés az Active Directory összevonási szolgáltatások 2.0 (AD FS) az Office 365-portálról. További jelentési érhető el az Office 365 portálon vagy az Azure AD MyApps portálról indítja társított alkalmazások esetében. 

### <a name="linked-sso-for-application-migration"></a>Az alkalmazás áttelepítési társított egyszeri bejelentkezés

Csatolt egyszeri Bejelentkezést is biztosít az egységes felhasználói élmény, míg alkalmazásokat telepíthet át egy időszakon belül. Ha migráláshoz az Azure Active Directory-alkalmazások, használhatja a csatolt egyszeri bejelentkezés hivatkozások gyorsan át szeretne alkalmazásokat közzétenni.  Az összes hivatkozást a felhasználók megtalálják az [MyApps portálról](../user-help/active-directory-saas-access-panel-introduction.md) vagy a [Office 365 alkalmazásindítójában](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Felhasználók nem fognak ismerni a társított alkalmazások vagy az áttelepített alkalmazásokat fér hozzá.  

Miután egy felhasználó hitelesítette az társított alkalmazást, egy ügyfélrekordot kell létrehozni, mielőtt a felhasználó egyszeri bejelentkezéses hozzáférést van megadva. Ez a fiók rekord kiépítés vagy automatikusan történik, vagy azt egy rendszergazda manuálisan fordulhat.

## <a name="disabled-sso"></a>Letiltott egyszeri bejelentkezés

Letiltott mód azt jelenti, hogy egyszeri bejelentkezés az alkalmazás nem használ. Ha az egyszeri bejelentkezés le van tiltva, a felhasználóknak kell előfordulhat, hogy kétszer hitelesíteni. Először a felhasználók hitelesítéséhez az Azure AD, és majd bejelentkeznek az alkalmazásba. 

Használata le van tiltva az egyszeri bejelentkezési mód:

- Ha még nem készen áll az alkalmazás integrálása az Azure AD egyszeri bejelentkezés, vagy
- Ha a tesztelést az alkalmazás más aspektusait, vagy
- Egy helyszíni alkalmazásba biztonsági réteget, a felhasználóknak a hitelesítést nem igénylő. Le van tiltva a felhasználónak hitelesítenie kell. 

## <a name="integrated-windows-authentication-iwa-sso"></a>Integrált Windows-hitelesítés (IWA) egyszeri bejelentkezés

[Az alkalmazásproxy](application-proxy.md) biztosít egyszeri bejelentkezés (SSO) használó alkalmazások [integrált Windows-hitelesítés (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication), vagy jogcímeket használó alkalmazásokat. Ha az alkalmazás IWA, alkalmazásproxy hitelesíti magát az alkalmazás a Kerberos által korlátozott delegálás (KCD) használatával. Jogcímbarát alkalmazáshoz, amely megbízik az Azure Active Directory egyszeri bejelentkezés működik, mert az Azure AD-vel már hitelesíti a felhasználót.

Válassza ki az integrált Windows-hitelesítés egyszeri bejelentkezési mód:

- Egyszeri bejelentkezés egy helyszíni alkalmazásba, amely hitelesíti a biztosít IWA. 

A helyszíni alkalmazás beállítása a IWA: [egyszeri bejelentkezést az alkalmazásproxy használatával az alkalmazások a Kerberos által korlátozott delegálás](application-proxy-configure-single-sign-on-with-kcd.md). 

### <a name="how-single-sign-on-with-kcd-works"></a>Az egyszeri bejelentkezés KCD Works
Ez az ábra ismerteti a folyamatot, amikor egy felhasználó hozzáfér IWA használó helyszíni alkalmazások.

![A Microsoft AAD-hitelesítés folyamatábrája](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. A felhasználó megadja az URL-cím alkalmazásproxyn keresztül a helyszíni alkalmazás eléréséhez.
2. Az alkalmazásproxy átirányítja a kérést az Azure AD authentication szolgáltatások tudnak preauthenticate. Ezen a ponton az Azure AD bármilyen megfelelő hitelesítési és engedélyezési házirendeket, mint a többtényezős hitelesítés vonatkozik. Ha a felhasználó érvényesítését, Azure ad-ben létrehoz egy jogkivonatot, és elküldi azokat a felhasználó.
3. A felhasználó az alkalmazásproxy továbbítja a jogkivonatot.
4. Az alkalmazásproxy érvényesíti a jogkivonatot, és beolvassa az egyszerű felhasználónév (UPN) a jogkivonatot. Ez ezután elküldi a kérést, az egyszerű Felhasználónevet és az egyszerű szolgáltatásnév (SPN) az összekötő dually hitelesített biztonságos csatornán keresztül történik.
5. Az összekötő Kerberos által korlátozott delegálás (KCD) egyeztetési használ a helyszíni Active Directory, az alkalmazásnak a Kerberos jogkivonat beszerzéséhez a felhasználó megszemélyesítésekor.
6. Az Active Directory elküldi az alkalmazás az összekötő Kerberos-jogkivonat.
7. Az összekötő az eredeti kérést küld a kiszolgáló, az AD-ből kapott Kerberos-jogkivonat használatával.
8. Az alkalmazás visszaküldi a választ, az összekötőre, amelyet ezután visszaküld az alkalmazásproxy-szolgáltatás, és végül a felhasználó számára.

## <a name="header-based-sso"></a>Fejléc-alapú egyszeri bejelentkezés

Fejléc-alapú egyszeri bejelentkezés a hitelesítéshez a HTTP-fejlécek használó alkalmazások esetében működik. A bejelentkezési módszer PingAccess nevű harmadik fél hitelesítési szolgáltatást használ. A felhasználó csak az Azure AD hitelesítenie kell. 

Válassza ki a fejléc-alapú egyszeri bejelentkezés mikor:

- Application Proxy és a PingAccess vannak konfigurálva, az alkalmazás

Fejléc-alapú hitelesítés beállítása: [fejlécalapú hitelesítéskor az egyszeri bejelentkezés alkalmazásproxyval való](application-proxy-configure-single-sign-on-with-ping-access.md). 

### <a name="what-is-pingaccess-for-azure-ad"></a>Mi az az Azure AD-alapú PingAccess?

PingAccess Azure AD-felhasználók számára is hozzáférést és egyszeri bejelentkezés használatával olyan alkalmazások, amelyek a fejlécek használata a hitelesítéshez. Application Proxy ezeket az alkalmazásokat, mint bármely más, az Azure AD segítségével hitelesíti a hozzáférést, majd az összekötő szolgáltatás forgalmát kezeli. Miután történik hitelesítés, a PingAccess szolgáltatás fordítja le az Azure AD hozzáférési jogkivonatot, amelyet az alkalmazás elküld egy fejléc formátuma.

A felhasználók nem figyelje meg, hogy bármi más, amikor bejelentkeznek a vállalati alkalmazások használatát. Továbbra is dolgozhatnak bárhol, bármilyen eszközön. Az alkalmazásproxy-összekötők közvetlen alkalmazások távoli forgalmat, és azok továbbra is automatikusan terheléselosztásához.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Hogyan szerezhetek licencet a PingAccess?

Mivel az Azure AD közötti partneri keretében érhető el ebben a forgatókönyvben, és a PingAccess,-licenc szükséges mindkét szolgáltatás esetében. Azonban a prémium szintű Azure AD-előfizetésekben megtalálható egy alapszintű PingAccess licenc, amely lefedi a akár 20 alkalmazáshoz. Ha több mint 20 fejléc-alapú alkalmazások közzétételét van szüksége, további licencre a PingAccess szerezheti be. 

További információk: [Azure Active Directory editions](../fundamentals/active-directory-whatis.md) (Azure Active Directory-kiadások).


## <a name="related-articles"></a>Kapcsolódó cikkek
* [SaaS-alkalmazások integrálása az Azure Active Directory számára oktatóanyagokkal](../saas-apps/tutorial-list.md)
* [Oktatóanyag az egyszeri bejelentkezés konfigurálása](configure-single-sign-on-portal.md)
* [Alkalmazásokhoz való hozzáférés kezelése bemutatása](what-is-access-management.md)
* Letöltési hivatkozás: [Egyszeri bejelentkezés telepítési tervének](https://aka.ms/SSODeploymentPlan).



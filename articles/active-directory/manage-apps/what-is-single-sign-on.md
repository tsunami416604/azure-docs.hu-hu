---
title: Egyszeri bejelentkezés az Azure Active Directory-alkalmazást – |} A Microsoft Docs
description: Ismerje meg, hogyan válassza ki egy egyszeri bejelentkezési módszer, ha alkalmazások konfigurálása az Azure Active Directoryban (Azure AD). Egyszeri bejelentkezés használatára, a felhasználóknak nem kell minden alkalmazáshoz tartozó jelszavakat megjegyezni és fiókkezelési felügyeletének egyszerűsítése érdekében.
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: barbkess
ms.reviewer: arvindh
ms.openlocfilehash: 3012f07d8c56f2581a087bc8e43aa4a089bf9589
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633512"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>Egyszeri bejelentkezés az Azure Active Directory-alkalmazások
Ismerje meg, hogyan válassza ki a legmegfelelőbb egyszeri bejelentkezési módszer, ha alkalmazások konfigurálása az Azure Active Directoryban (Azure AD). 

- **Az egyszeri bejelentkezés**, egy fiókhoz való hozzáférés a tartományhoz csatlakoztatott eszközök bejelentkezéssel felhasználók vállalati erőforrásokhoz, mint a szoftverszolgáltatások (SaaS) alkalmazások, szoftverek és webalkalmazásokat. Bejelentkezés után a felhasználó el is indíthatja az alkalmazások az Office 365 portál és az Azure AD MyApps hozzáférési panelen. A rendszergazdák központosítása felhasználóifiók-kezelés, és automatikusan hozzáadhat vagy eltávolíthat felhasználók csoport tagsága alapján alkalmazásokhoz való hozzáférése. 

- **Egyszeri bejelentkezés nélkül**, felhasználók kell alkalmazásspecifikus jelszó megjegyzése, és jelentkezzen be minden alkalmazáshoz. Informatikai munkatársak kell létrehozni és frissíteni a felhasználói fiókok az egyes alkalmazások, például az Office 365-höz, a Box vagy a Salesforce. Felhasználók ne felejtse el a jelszavukat, valamint időt kelljen szánnia a jelentkezzen be a minden alkalmazás kell.

Ez a cikk az egyszeri bejelentkezési módszereket írja le, és segít a legjobb módszer az alkalmazások számára.

## <a name="choosing-a-single-sign-on-method"></a>Egyszeri bejelentkezés módszer kiválasztása

Többféleképpen is lehet az alkalmazás egyszeri bejelentkezés konfigurálása. Az alkalmazás egyszeri bejelentkezési módszer kiválasztása attól függ, hogy az alkalmazás hogyan van konfigurálva a hitelesítéshez. Minden egyszeri bejelentkezési mód, le van tiltva, kivéve automatikusan bejelentkezhetnek a felhasználók alkalmazások egy második bejelentkezés nélkül.  

- A felhőalapú alkalmazások módszerekkel SAML, jelszóalapú, összekapcsolt vagy letiltva az egyszeri bejelentkezés. SAML a legbiztonságosabb egyszeri bejelentkezési módszer.
- A helyszíni alkalmazások használhatják a jelszóalapú, integrált Windows hitelesítést, az egyszeri bejelentkezés fejlécalapú, összekapcsolt vagy letiltott metódust. A helyszíni lehetőségek működnek, ha a alkalmazások Application Proxy van konfigurálva. 

Ez a folyamatábra segít eldönteni, ajánlott az adott helyzetben melyik egyszeri bejelentkezésének módját. 

![Egyszeri bejelentkezés módszer kiválasztása](./media/what-is-single-sign-on/choose-single-sign-on-method.png)

Az alábbi táblázat foglalja össze az egyszeri bejelentkezési módszereket, és hivatkozásokat tartalmaz további részleteket. 

| Egyszeri bejelentkezési módszer | Alkalmazástípusok | A következő esetekben használja |
| :------ | :------- | :----- |
| [SAML](#saml-sso) | Csak a felhőben | Használja a SAML, amikor csak lehetséges. SAML akkor működik, ha az alkalmazások SAML protokoll használatára van konfigurálva.|
| [Jelszóalapú](#password-based-sso) | felhőbeli és helyszíni | Akkor használja, ha az alkalmazás hitelesíti a felhasználónevet és jelszót. Jelszavas egyszeri bejelentkezés lehetővé teszi a biztonságos tárolását és ismétlését egy webböngésző-bővítmény vagy mobilalkalmazás segítségével. Ezt a módszert használja a meglévő bejelentkezési folyamat az alkalmazás által biztosított, de lehetővé teszi, hogy egy rendszergazda kezelje a jelszavakat. |
| [A csatolt](#linked-sso) | felhőbeli és helyszíni | Használja a csatolt egyszeri bejelentkezés, ha az alkalmazás úgy van konfigurálva, az egyszeri bejelentkezés egy másik identitás szolgáltató szolgáltatásban. Ez a beállítás nem adja hozzá egyszeri bejelentkezés az alkalmazáshoz. Az alkalmazás azonban már előfordulhat, hogy rendelkezik az egyszeri bejelentkezés egy másik szolgáltatás, például az Active Directory összevonási szolgáltatások használatával implementált.|
| [Letiltva](#disabled-sso) | felhőbeli és helyszíni | Használja a letiltott egyszeri bejelentkezés, ha az alkalmazás nem áll készen az egyszeri bejelentkezéshez konfigurálandó. Adja meg a felhasználónevüket és jelszavukat minden alkalommal, amikor az alkalmazás indításakor kell felhasználók.|
| [Integrált Windows-hitelesítés (IWA)](#integrated-windows-authentication-iwa-sso) | csak helyszíni | Ezt a módszert egyszeri bejelentkezést használó alkalmazások esetében [integrált Windows-hitelesítés (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication), vagy jogcímeket használó alkalmazásokat. Az alkalmazásproxy-összekötők a Kerberos által korlátozott delegálás (KCD) segítségével hitelesítheti a felhasználókat az alkalmazáshoz. | 
| [Fejlécalapú](#header-based-sso) | csak helyszíni | Használja fejléc-alapú egyszeri bejelentkezés, ha az alkalmazás fejléceket használja a hitelesítéshez. Fejléc-alapú egyszeri bejelentkezés szükséges PingAccess az Azure Active Directoryhoz. Az alkalmazásproxy az Azure AD segítségével hitelesíti a felhasználót, és ezután továbbítja a forgalmat az összekötő szolgáltatást.  | 

## <a name="saml-sso"></a>SAML EGYSZERI BEJELENTKEZÉS
A **SAML egyszeri bejelentkezés**, az Azure AD akkor hitelesíti az alkalmazásnak a felhasználó Azure AD-fiók használatával. Azure ad-ben a bejelentkezési adatait az alkalmazás egy kapcsolat protokollon keresztül kommunikál. Az SAML-alapú egyszeri bejelentkezés leképezheti felhasználók a SAML-jogcímek az meghatározott szabályok alapján adott alkalmazás-szerepkörök

SAML-alapú egyszeri bejelentkezés a következő:

- Biztonságosabb, mint a jelszóalapú egyszeri bejelentkezés és minden egyéb bejelentkezési módszereket.
- Az ajánlott módszer az egyszeri bejelentkezést.

SAML-alapú egyszeri bejelentkezés bármely ezeket a protokollokat használó alkalmazások esetében támogatott:

- SAML 2.0
- WS-Federation
- OpenID connect

Egy alkalmazás az SAML-alapú egyszeri bejelentkezés beállítása: [konfigurálása SAML-alapú egyszeri bejelentkezés](configure-single-sign-on-portal.md). Sok alkalmazás van arra is, [alkalmazásspecifikus oktatóanyagok](../saas-apps/tutorial-list.md) , amely végigvezeti az SAML-alapú egyszeri bejelentkezés a megfelelő alkalmazások konfigurálása. 

Az SAML-protokoll működésével kapcsolatos további információkért lásd: [egyszeri bejelentkezéses SAML-protokoll](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>Jelszóalapú egyszeri bejelentkezés
Jelszóalapú bejelentkezés az alkalmazás hitelesíti magát az alkalmazást a felhasználónevet és jelszót. Végfelhasználók jelentkezzen be az alkalmazás az első alkalommal. Miután az első bejelentkezés Azure Active Directory felhasználónevet és jelszót az alkalmazáshoz választékát kínálja. 

Jelszavas egyszeri bejelentkezést használ a meglévő hitelesítési folyamatot, az alkalmazás által biztosított. Ha engedélyezi a jelszó egyszeri bejelentkezést az alkalmazáshoz, az Azure ad-ben gyűjt, és biztonságosan tárolja a felhasználói neveket és jelszavakat az alkalmazás. Felhasználói hitelesítő adatok titkosítására a könyvtárban vannak tárolva. 

Használat jelszóalapú egyszeri bejelentkezés mikor:

- Egy alkalmazás nem támogatja az SAML egyszeri bejelentkezési protokoll.
- Felhasználónév és jelszó helyett hozzáférési jogkivonatok és a fejlécek az alkalmazás végzi a hitelesítést.

Jelszavas egyszeri bejelentkezés bármely felhőalapú alkalmazás, amely rendelkezik egy HTML-alapú bejelentkezési oldal támogatott. A felhasználó az alábbi böngészők bármelyike használható:

- Az Internet Explorer 11 Windows 7-es vagy újabb
- Edge a Windows 10 Évfordulós kiadása vagy újabb 
- Chrome, a Windows 7 vagy újabb, és a MacOS X rendszeren vagy újabb
- Firefox 26.0 vagy újabb, Windows XP SP2 vagy újabb, és a Mac OS X 10.6 vagy újabb

Felhőalapú alkalmazások a jelszóalapú egyszeri bejelentkezés beállítása: [az alkalmazás a jelszavas egyszeri bejelentkezés konfigurálása](application-sign-in-problem-password-sso-gallery.md#configure-the-application-for-password-single-sign-on).

Egyszeri bejelentkezés alkalmazásproxyn keresztül a helyszíni alkalmazás beállítása: [az egyszeri bejelentkezés alkalmazásproxyval való vaulting jelszó](application-proxy-configure-single-sign-on-password-vaulting.md)

### <a name="managing-credentials-for-password-based-sso"></a>Hitelesítő adatok jelszóalapú egyszeri bejelentkezés kezelése

Hitelesíteni a felhasználót, hogy egy alkalmazás, az Azure AD kérdezi le a felhasználó hitelesítő adatait a könyvtárból, és beírja őket az alkalmazás bejelentkezési lapján be.  Az Azure AD biztonságosan adja át a felhasználói hitelesítő adatok egy webböngésző-bővítmény vagy mobilalkalmazás segítségével. Ez a folyamat lehetővé teszi, hogy egy rendszergazda kezelje a felhasználói hitelesítő adatokat, és nincs szükség a felhasználók számára ne felejtse el a jelszavát.

> [!IMPORTANT]
> A hitelesítő adatok vannak rejtjelezett a végfelhasználó az automatikus bejelentkezési folyamat során. Azonban a hitelesítő adatokat is felderíthető webhelyen-hibakereső eszközeivel. A felhasználók és rendszergazdák kell követniük ugyanazon biztonsági szabályzatok, mintha közvetlenül a felhasználó által megadott hitelesítő adatok.

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

Csatolt egyszeri bejelentkezés használata:

- Az egységes felhasználói élmény ad meg, alkalmazásokat telepíthet át egy időszakon belül. Ha migráláshoz az Azure Active Directory-alkalmazások, használhatja a csatolt egyszeri bejelentkezés hivatkozások gyorsan át szeretne alkalmazásokat közzétenni.  Az összes hivatkozást a felhasználók megtalálják az [MyApps portálról](../user-help/active-directory-saas-access-panel-introduction.md) vagy a [Office 365 alkalmazásindítójában](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Felhasználók nem fognak ismerni a társított alkalmazások vagy az áttelepített alkalmazásokat fér hozzá.  

Miután egy felhasználó hitelesítette az társított alkalmazást, egy ügyfélrekordot kell létrehozni, mielőtt a felhasználó egyszeri bejelentkezéses hozzáférést van megadva. Ez a fiók rekord kiépítés vagy automatikusan történik, vagy azt egy rendszergazda manuálisan fordulhat.

## <a name="disabled-sso"></a>Letiltott egyszeri bejelentkezés

Letiltott mód azt jelenti, hogy egyszeri bejelentkezés az alkalmazás nem használ. Ha az egyszeri bejelentkezés le van tiltva, a felhasználóknak kell előfordulhat, hogy kétszer hitelesíteni. Először a felhasználók hitelesítéséhez az Azure AD, és majd bejelentkeznek az alkalmazásba. 

Használata le van tiltva az egyszeri bejelentkezési mód:

- Ha még nem készen áll az alkalmazás integrálása az Azure AD egyszeri bejelentkezés, vagy
- Ha a tesztelést az alkalmazás más aspektusait, vagy
- Egy helyszíni alkalmazásba biztonsági réteget, a felhasználóknak a hitelesítést nem igénylő. Le van tiltva a felhasználónak hitelesítenie kell. 

## <a name="integrated-windows-authentication-iwa-sso"></a>Integrált Windows-hitelesítés (IWA) egyszeri bejelentkezés

Az Azure AD-alkalmazásproxy biztosít egyszeri bejelentkezés (SSO) használó alkalmazások [integrált Windows-hitelesítés (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication), vagy jogcímeket használó alkalmazásokat. Ha az alkalmazás IWA, alkalmazásproxy hitelesíti magát az alkalmazás a Kerberos által korlátozott delegálás (KCD) használatával. Jogcímbarát alkalmazáshoz, amely megbízik az Azure Active Directory egyszeri bejelentkezés működik, mert az Azure AD-vel már hitelesíti a felhasználót.

Integrált Windows-hitelesítés egyszeri bejelentkezési mód használata:

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

Használat fejléc-alapú egyszeri bejelentkezés mikor:

- Application Proxy és a PingAccess vannak konfigurálva, az alkalmazás

Fejléc-alapú hitelesítés beállítása: [fejlécalapú hitelesítéskor az egyszeri bejelentkezés alkalmazásproxyval való](application-proxy-configure-single-sign-on-with-ping-access.md). 

### <a name="what-is-pingaccess-for-azure-ad"></a>Mi az az Azure AD-alapú PingAccess?

PingAccess Azure AD-felhasználók számára is hozzáférést és egyszeri bejelentkezés használatával olyan alkalmazások, amelyek a fejlécek használata a hitelesítéshez. Application Proxy ezeket az alkalmazásokat, mint bármely más, az Azure AD segítségével hitelesíti a hozzáférést, majd az összekötő szolgáltatás forgalmát kezeli. Miután történik hitelesítés, a PingAccess szolgáltatás fordítja le az Azure AD hozzáférési jogkivonatot, amelyet az alkalmazás elküld egy fejléc formátuma.

A felhasználók nem figyelje meg, hogy bármi más, amikor bejelentkeznek a vállalati alkalmazások használatát. Továbbra is dolgozhatnak bárhol, bármilyen eszközön. Az alkalmazásproxy-összekötők közvetlen alkalmazások távoli forgalmat, és azok továbbra is automatikusan terheléselosztásához.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Hogyan szerezhetek licencet a PingAccess?

Ebben a forgatókönyvben egy Azure Active Directory és PingAccess közötti partneri keretében érhető el, mivel-licenc szükséges mindkét szolgáltatás esetében. Azonban az Azure Active Directory Premium-előfizetések is tartalmazzák, egy alapszintű PingAccess licenc, amely lefedi a akár 20 alkalmazáshoz. Ha több mint 20 fejléc-alapú alkalmazások közzétételét van szüksége, további licencre a PingAccess szerezheti be. 

További információk: [Azure Active Directory editions](../fundamentals/active-directory-whatis.md) (Azure Active Directory-kiadások).

## <a name="related-articles"></a>Kapcsolódó cikkek
* [SaaS-alkalmazások integrálása az Azure Active Directory számára oktatóanyagokkal](../saas-apps/tutorial-list.md)
* [Oktatóanyag az egyszeri bejelentkezés konfigurálása](configure-single-sign-on-portal.md)
* [Alkalmazásokhoz való hozzáférés kezelése bemutatása](what-is-access-management.md)
* Letöltési hivatkozás: [egyszeri bejelentkezéses telepítési tervének](http://aka.ms/SSODeploymentPlan).



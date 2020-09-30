---
title: Microsoft Enterprise egyszeri bejelentkezéses beépülő modul Apple-eszközökhöz
titleSuffix: Microsoft identity platform | Azure
description: Ismerkedjen meg az iOS-és macOS-eszközökhöz készült Microsoft Azure Active Directory SSO beépülő modullal.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: ec0ab4601e15129ecd8917e0e750a3e1661dc558
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91530697"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Microsoft Enterprise SSO beépülő modul Apple-eszközökhöz (előzetes verzió)

> [!NOTE]
> Ez a funkció nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az *Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modul* egyszeri bejelentkezést (SSO) biztosít a Azure Active Directory (Azure ad) fiókokhoz az Apple [vállalati egyszeri bejelentkezés](https://developer.apple.com/documentation/authenticationservices) funkcióját támogató összes alkalmazásban. A Microsoft az Apple-szel szorosan együttműködve fejleszti ezt a beépülő modult az alkalmazás használhatóságának növeléséhez, miközben az Apple és a Microsoft által biztosított legjobb védelmet nyújtja.

Ebben a nyilvános előzetes kiadásban a vállalati egyszeri bejelentkezési beépülő modul csak iOS-eszközökön érhető el, és bizonyos Microsoft-alkalmazásokban van elterjesztve.

## <a name="features"></a>Funkciók

Az Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modul a következő előnyöket kínálja:

- EGYSZERI bejelentkezést biztosít az Azure AD-fiókokhoz az Apple vállalati egyszeri bejelentkezés funkcióját támogató összes alkalmazásban.
- A rendszer automatikusan leküldi a Microsoft Authenticator, és bármely mobileszköz-kezelési (MDM) megoldással engedélyezhető.

## <a name="requirements"></a>Követelmények

A Microsoft Enterprise SSO beépülő modul használata Apple-eszközökhöz:

- az iOS 13,0-es vagy újabb verziójának telepítve kell lennie az eszközön.
- Az Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modult biztosító Microsoft-alkalmazásnak telepítve kell lennie az eszközön. A nyilvános előzetes verzióban ezek az alkalmazások a [Microsoft Authenticator alkalmazást](../user-help/user-help-auth-app-overview.md)tartalmazzák.
- Az eszköznek MDM kell lennie (például Microsoft Intune).
- A konfigurációt az eszközön kell leküldeni, hogy engedélyezze a Microsoft Enterprise SSO beépülő modult az Apple-eszközökön az eszközön. Ezt a biztonsági korlátozást az Apple igényli.

## <a name="enable-the-sso-plug-in-with-mobile-device-management-mdm"></a>Egyszeri bejelentkezéses beépülő modul engedélyezése a mobileszköz-felügyelettel (MDM)

Az Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modul engedélyezéséhez az eszközöknek egy MDM szolgáltatáson keresztül kell eljuttatnia a jeleket. Mivel a Microsoft a [Microsoft Authenticator alkalmazásban](..//user-help/user-help-auth-app-overview.md)tartalmazza a vállalati egyszeri bejelentkezés beépülő modult, a Mdm használatával konfigurálhatja az alkalmazást, hogy engedélyezze a Microsoft vállalati egyszeri bejelentkezés beépülő modulját.

Az Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modul konfigurálásához használja a következő paramétereket:

- **Típus**: átirányítás
- **BŐVÍTMÉNY azonosítója**: `com.microsoft.azureauthenticator.ssoextension`
- **Csoport azonosítója**: (ez a mező nem szükséges az iOS esetében)
- **URL-címek**:
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="additional-configuration-options"></a>További konfigurációs beállítások
További konfigurációs beállítások adhatók hozzá az SSO-funkciók további alkalmazásokra való kiterjesztéséhez.

#### <a name="enable-sso-for-apps-that-dont-use-msal"></a>Egyszeri bejelentkezés engedélyezése az MSAL-t nem használó alkalmazásokhoz

Az egyszeri bejelentkezéses beépülő modul lehetővé teszi az alkalmazások számára az egyszeri bejelentkezést, még akkor is, ha nem Microsoft SDK-val, például a Microsoft Authentication Library (MSAL) használatával lett kifejlesztve.

Az egyszeri bejelentkezéses beépülő modult automatikusan telepíti a Microsoft Authenticator alkalmazást letöltő és az eszközét a szervezete számára regisztrált eszközök. A szervezet valószínűleg a hitelesítő alkalmazást használja olyan forgatókönyvek esetén, mint például a többtényezős hitelesítés, a jelszó nélküli hitelesítés és a feltételes hozzáférés. Az alkalmazások bármely MDM-szolgáltató használatával bekapcsolhatók, bár a Microsoft az Intune Microsoft Endpoint Managerben való konfigurálását egyszerűen megteheti. Az engedélyezési lista használatával konfigurálhatja ezeket az alkalmazásokat a hitelesítő alkalmazás által telepített SSO beépülő modul használatára.

Csak a natív Apple hálózati technológiákat vagy webnézeteket használó alkalmazások támogatottak. Ha egy alkalmazás a saját hálózati rétegének megvalósítását is elvégezte, a Microsoft Enterprise SSO beépülő modul nem támogatott.  

A következő paraméterekkel konfigurálhatja a Microsoft Enterprise SSO beépülő modult olyan alkalmazásokhoz, amelyek nem használnak MSAL:

- **Kulcs**: `AppAllowList`
- **Írja be a következőt**: `String`
- **Érték**: az alkalmazási köteg azonosítóinak vesszővel tagolt listája azon alkalmazások esetében, amelyek jogosultak az SSO-ban való részvételre
- **Példa**: `com.contoso.workapp, com.contoso.travelapp`

A MDM-rendszergazda által az SSO-ben való részvételre engedélyezett, belefoglalt [alkalmazások](./application-consent-experience.md) a végfelhasználók számára csendes jogkivonatot szerezhetnek. Ezért fontos, hogy csak megbízható alkalmazásokat adjon hozzá az engedélyezési listához. 

Ehhez a listához nem kell hozzáadnia MSAL vagy ASWebAuthenticationSession-t használó alkalmazásokat. Ezek az alkalmazások alapértelmezés szerint engedélyezve vannak. 

#### <a name="allow-creating-sso-session-from-any-application"></a>Egyszeri bejelentkezéses munkamenet létrehozásának engedélyezése bármely alkalmazásból

Alapértelmezés szerint a Microsoft vállalati egyszeri bejelentkezés beépülő modulja csak akkor biztosít egyszeri bejelentkezést az engedélyezve lévő alkalmazásokhoz, ha az egyszeri bejelentkezési beépülő modul már rendelkezik megosztott hitelesítő adatokkal. A Microsoft Enterprise SSO beépülő modulja megosztott hitelesítő adatokat tud beszerezni, ha egy másik ADAL vagy MSAL-alapú alkalmazás hívja meg a token beszerzése során. A legtöbb Microsoft-alkalmazás Microsoft Authenticator vagy SSO beépülő modult használ. Ez azt jelenti, hogy a natív alkalmazások folyamatain kívüli alapértelmezett egyszeri bejelentkezés a legjobb megoldás.  

`browser_sso_interaction_enabled`A Flag engedélyezése lehetővé teszi a nem MSAL alkalmazások és a Safari böngésző számára a kezdeti indítást és a megosztott hitelesítő adatok beszerzését. Ha a Microsoft vállalati egyszeri bejelentkezés beépülő modulja még nem rendelkezik megosztott hitelesítő adatokkal, akkor a rendszer megkísérli a bejelentkezést, amikor egy Azure AD-URL-címről kéri a Safari böngészőben, a ASWebAuthenticationSession-ban, a SafariViewController-ban vagy más engedélyezett natív alkalmazásban.  

- **Kulcs**: `browser_sso_interaction_enabled`
- **Írja be a következőt**: `Integer`
- **Érték**: 1 vagy 0

Javasoljuk, hogy ezt a jelzőt úgy engedélyezze, hogy következetesebb élményt kapjon az összes alkalmazásban. Ez alapértelmezés szerint le van tiltva. 

#### <a name="disable-oauth2-application-prompts"></a>OAuth2-alkalmazási kérések letiltása

A Microsoft Enterprise SSO beépülő modulja egyszeri bejelentkezést biztosít az engedélyezett alkalmazásokból érkező hálózati kérésekhez tartozó megosztott hitelesítő adatok hozzáfűzésével. Előfordulhat, hogy egyes OAuth2-alkalmazások kényszerítik a végfelhasználói kérést a protokoll rétegen. Ezeket az alkalmazásokat a rendszer figyelmen kívül hagyja a megosztott hitelesítő adatokkal.  

A `disable_explicit_app_prompt` jelölő engedélyezése korlátozza a natív és a webalkalmazások képességét, hogy a végfelhasználói kérdést kényszerítse a protokoll rétegén és az egyszeri bejelentkezés mellőzését.

- **Kulcs**: `disable_explicit_app_prompt`
- **Írja be a következőt**: `Integer`
- **Érték**: 1 vagy 0

Javasoljuk, hogy ezt a jelzőt úgy engedélyezze, hogy következetesebb élményt kapjon az összes alkalmazásban. Ez alapértelmezés szerint le van tiltva. 

#### <a name="use-intune-for-simplified-configuration"></a>Az Intune használata egyszerűsített konfigurációhoz

A MDM szolgáltatással Microsoft Intune használhatja a Microsoft vállalati egyszeri bejelentkezés beépülő moduljának konfigurálását. További információt az [Intune konfigurációs dokumentációjában](/intune/configuration/ios-device-features-settings)talál.

## <a name="using-the-sso-plug-in-in-your-application"></a>Az SSO beépülő modul használata az alkalmazásban

Az Apple-eszközök 1.1.0-es és újabb verziójának [Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-objc) verziója támogatja az Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modult.

Ha olyan alkalmazást hoz létre, amely Frontline Worker-forgatókönyvekhez készült, tekintse meg az [iOS-eszközök megosztott eszköz üzemmódját](msal-ios-shared-devices.md) a funkció további beállításához.

## <a name="how-the-sso-plug-in-works"></a>Az SSO beépülő modul működése

A Microsoft Enterprise SSO beépülő modul az [Apple vállalati egyszeri bejelentkezési keretrendszerére](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc)támaszkodik. A keretrendszerbe feltüntető szolgáltatók képesek a tartományok hálózati forgalmának elfogására és a kérelmek kezelésének javítására vagy módosítására. Az SSO beépülő modul például további felhasználói felületet jeleníthet meg a végfelhasználói hitelesítő adatok biztonságos összegyűjtéséhez, a többtényezős hitelesítés megköveteléséhez, vagy a tokenek csendes biztosításához az alkalmazáshoz.

A natív alkalmazások egyéni műveleteket is megvalósítanak, és közvetlenül az egyszeri bejelentkezéses beépülő modullal kommunikálnak.
Az egyszeri bejelentkezési keretrendszerről ebben a [2019 WWDC-videóban olvashat az Apple-től](https://developer.apple.com/videos/play/tech-talks/301/)

### <a name="applications-that-use-msal"></a>MSAL használó alkalmazások

A [Microsoft Authentication Library (MSAL) for Apple Devices](https://github.com/AzureAD/microsoft-authentication-library-for-objc) a 1.1.0 és a magasabb verziója támogatja a Microsoft Enterprise SSO beépülő modult az Apple-eszközökön natív módon munkahelyi és iskolai fiókokhoz. 

Ha követte az [összes javasolt lépést](./quickstart-v2-ios.md) , és az alapértelmezett [átirányítási URI-formátumot](./redirect-uris-ios.md)használta, nincs szükség speciális konfigurációra. Ha egy olyan eszközön fut, amelyen megtalálható az SSO beépülő modul, a MSAL automatikusan meghívja az összes interaktív és csendes jogkivonat-kérelemre, valamint a fiókok enumerálására és a fiókok eltávolítására irányuló műveletekre. Mivel a MSAL olyan natív SSO beépülő modult valósít meg, amely egyéni műveletekre támaszkodik, ez a telepítő a legsimább natív élményt biztosítja a végfelhasználók számára. 

Ha az SSO beépülő modult nem engedélyezi a MDM, de a Microsoft Authenticator alkalmazás megtalálható az eszközön, akkor a MSAL Ehelyett az Microsoft Authenticator alkalmazást használja az interaktív jogkivonat-kérelmekhez. Az egyszeri bejelentkezéses beépülő modul a Microsoft Authenticator alkalmazással is megosztja az egyszeri bejelentkezést.

### <a name="applications-that-dont-use-msal"></a>MSAL nem használó alkalmazások

A MSAL-t nem használó alkalmazások továbbra is kaphatnak SSO-t, ha a rendszergazda explicit módon hozzáadja azokat az engedélyezési listához. 

Ezekben az alkalmazásokban nincs szükség kód módosítására, ha a következő feltételek teljesülnek:

- Az alkalmazás Apple-keretrendszereket használ a hálózati kérelmek végrehajtásához (például [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview), [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession)) 
- Az alkalmazás szabványos protokollokat használ az Azure AD-vel való kommunikációhoz (például OAuth2, SAML, WS-Federation)
- Az alkalmazás nem gyűjti az egyszerű szöveges felhasználónevet és a jelszót a natív felhasználói felületen

Ebben az esetben az SSO akkor van megadva, amikor az alkalmazás létrehoz egy hálózati kérelmet, és megnyit egy webböngészőt a felhasználó aláírásához. Amikor egy felhasználó átirányítja egy Azure AD bejelentkezési URL-címre, az SSO beépülő modul ellenőrzi az URL-címet, és ellenőrzi, hogy van-e elérhető SSO hitelesítő adat az adott URL-címhez. Ha van ilyen, az egyszeri bejelentkezéses beépülő modul átadja az egyszeri bejelentkezéshez használt hitelesítő adatokat az Azure AD-nek, amely engedélyezi az alkalmazás számára a hálózati kérelem elvégzését anélkül, hogy a felhasználónak meg kellene adnia a hitelesítő adatait. Továbbá, ha az eszköz ismert az Azure AD-ben, az egyszeri bejelentkezéses beépülő modul is továbbítja az eszköz tanúsítványát az eszköz alapú feltételes hozzáférés-ellenőrzések teljesítéséhez. 

A nem MSAL alkalmazások egyszeri bejelentkezésének támogatásához az SSO beépülő modul olyan protokollt valósít meg, amely a [Mi az elsődleges frissítési jogkivonat?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt)című témakörben ismertetett Windows-böngésző beépülő modulhoz hasonló. 

A MSAL-alapú alkalmazásokhoz képest az SSO beépülő modul átláthatóbbá teszi a nem MSAL alkalmazások számára az alkalmazások által biztosított meglévő böngészőbeli bejelentkezési élmény integrálásával. A végfelhasználók láthatják a megszokott tapasztalataikat, így nem kell további bejelentkezéseket végezniük az egyes alkalmazásokban. A natív fiók választójának megjelenítése helyett például az SSO beépülő modul SSO-munkameneteket biztosít a webalapú fiók-választói élményhez. 

## <a name="next-steps"></a>További lépések

További információ az iOS-es megosztott eszköz üzemmódról: [megosztott eszköz mód iOS-eszközökhöz](msal-ios-shared-devices.md).

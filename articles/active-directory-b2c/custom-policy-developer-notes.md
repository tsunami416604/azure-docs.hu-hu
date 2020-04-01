---
title: Fejlesztői megjegyzések egyéni házirendekhez
titleSuffix: Azure AD B2C
description: Megjegyzések a fejlesztők számára az Azure AD B2C egyéni szabályzatokkal történő konfigurálása és karbantartása.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 403ca480bcf0743d81e375c122c888db96bbf543
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408718"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Fejlesztői megjegyzések az Azure Active Directory B2C egyéni szabályzataihoz

Az Azure Active Directory B2C egyéni házirend-konfigurációja mostantól általánosan elérhető. Ez a konfigurációs módszer összetett identitásmegoldásokat fejlesztő fejlett identitásfejlesztőket céloz meg. Az egyéni szabályzatok elérhetővé teszik az Identitáskezelési keretrendszer erejét az Azure AD B2C-bérlőkben.
Az egyéni házirendeket használó speciális identitásfejlesztőknek azt kell tervezniük, hogy egy kis időt szánjanak a beolvasási és a referenciadokumentumok olvasására.

Bár a legtöbb elérhető egyéni házirend-beállítás már általánosan elérhető, vannak mögöttes képességek, például a technikai profiltípusok és a tartalomdefiníciós API-k, amelyek a szoftver életciklusának különböző szakaszaiban vannak. Még sokan jönnek. Az alábbi táblázat részletesebb szinten határozza meg a rendelkezésre állás szintjét.

## <a name="features-that-are-generally-available"></a>Általánosan elérhető funkciók

- Egyéni hitelesítési felhasználói utak egyéni házirendek használatával történő szerkesztése és feltöltése.
    - Írja le a felhasználói utakat lépésről lépésre a jogcímszolgáltatók közötti adatcsereként.
    - Feltételes elágazás definiálása a felhasználói utakban.
- Együttműködik a REST API-kompatibilis szolgáltatásokkal az egyéni hitelesítési felhasználói utak során.
- Az OpenIDConnect protokollnak megfelelő identitásszolgáltatókkal.
- Az SAML 2.0 protokollt betartó identitásszolgáltatókkal együttműködve.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Az egyéni szabályzat-szolgáltatás-set fejlesztők felelőssége

A manuális házirend-konfiguráció alacsonyabb szintű hozzáférést biztosít az Azure AD B2C alapul szolgáló platformjához, és egy egyedi, megbízhatósági keretrendszer létrehozását eredményezi. Az egyéni identitásszolgáltatók, a megbízhatósági kapcsolatok, a külső szolgáltatásokkal való integrációk és a részletes munkafolyamatok számos lehetséges permutációja módszertani megközelítést igényel a tervezéshez és a konfigurációhoz.

Az egyéni házirend-szolgáltatáskészletet használó fejlesztőknek be kell tartaniuk az alábbi irányelveket:

- Ismerkedjen meg az egyéni szabályzatok és a kulcs/titkos kulcsok kezelésének konfigurációs nyelvével. További információ: [TrustFrameworkPolicy](trustframeworkpolicy.md).
- A forgatókönyvek és az egyéni integrációk tulajdonjogát. Dokumentálja a munkáját, és tájékoztassa az élő webhely szervezetét.
- Módszeres forgatókönyv-tesztelés végrehajtása.
- Kövesse a szoftverfejlesztést és az előkészítési ajánlott eljárásokat legalább egy fejlesztési és tesztelési környezettel és egy éles környezettel.
- Tájékozódjon az identitásszolgáltatók és a szolgáltatások új fejlesztéseiről. Például nyomon követheti a titkos kulcsok változásait, valamint a szolgáltatás ütemezett és nem ütemezett módosításait.
- Állítsa be az aktív figyelést, és figyelje az éles környezetek válaszképességét. Az Application Insights szolgáltatással való integrációról az [Azure Active Directory B2C: Naplók gyűjtése](analytics-with-application-insights.md)című témakörben talál további információt.
- Tartsa naprakészen a kapcsolattartási e-mail címeket az Azure-előfizetésben, és reagáljon a Microsoft élőwebhely-csoport e-mailjeire.
- A Microsoft élő webhelyre csapatának tanácsára, tegye meg időben az intézkedéseket.

## <a name="terms-for-features-in-public-preview"></a>A nyilvános előzetes verzióban elérhető funkciók feltételei

- Javasoljuk, hogy a nyilvános előzetes verzió funkcióit csak értékelési célokra használja.
- A szolgáltatásiszint-szerződések (SLOS-ok) nem vonatkoznak a nyilvános előzetes verzió funkcióira.
- A nyilvános előzetes funkcióktámogatási kérelmei rendszeres támogatási csatornákon keresztül is benyújthatók.

## <a name="features-by-stage-and-known-issues"></a>Jellemzők szakasz és ismert problémák szerint

Egyéni szabályzat/identitásélmény-keretrendszer képességek állandó és gyors fejlesztés alatt állnak. Az alábbi táblázat a szolgáltatások és az összetevők elérhetőségének indexe.


### <a name="protocols-and-authorization-flows"></a>Protokollok és engedélyezési folyamatok

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OAuth2 engedélyezési kód](authorization-code-flow.md) |  |  | X |  |
| OAuth2 engedélyezési kód a PKCE-vel |  |  | X | Csak mobilalkalmazások  |
| [OAuth2 implicit folyamat](implicit-flow-single-page-application.md) |  |  | X |  |
| [OAuth2 erőforrás-tulajdonos jelszóhitelesítő adatai](ropc-custom.md) |  | X |  |  |
| [OIDC csatlakozás](openid-connect.md) |  |  | X |  |
| [SAML2](connect-with-saml-service-providers.md)  |  |X  |  | POST és átirányítás kötések. |
| OAuth1 |  |  |  | Nem támogatott. |
| WSFED | X |  |  |  |

### <a name="identify-providers-federation"></a>Szolgáltatók összevonásának azonosítása 

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | X | Például a Google+.  |
| [OAuth2](oauth2-technical-profile.md) |  |  | X | Például a Facebook.  |
| [OAuth1](oauth1-technical-profile.md) |  | X |  | Például, Twitter. |
| [SAML2](saml-technical-profile.md) |  |   | X | Például Salesforce, ADFS. |
| WSFED| X |  |  |  |


### <a name="rest-api-integration"></a>REST API-integráció

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
|-------- | :-----------: | :-------: | :--: | ----- |
| [REST API alapszintű hitelesítéssel](secure-rest-api.md#http-basic-authentication) |  |  | X |  |
| [REST API az ügyféltanúsítvány hitelesítésével](secure-rest-api.md#https-client-certificate-authentication) |  |  | X |  |
| [REST API az OAuth2-hordozó hitelesítésével](secure-rest-api.md#oauth2-bearer-authentication) |  | X |  |  |

### <a name="component-support"></a>Összetevő támogatása

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Telefontényező-hitelesítés](phone-factor-technical-profile.md) |  |  | X |  |
| [Azure MFA-hitelesítés](multi-factor-auth-technical-profile.md) |  | X |  |  |
| [Egyszeri jelszó](one-time-password-technical-profile.md) |  | X |  |  |
| [Az Azure Active Directory](active-directory-technical-profile.md) helyi címtárként |  |  | X |  |
| Azure-levelezési alrendszer az e-mailek ellenőrzéséhez |  |  | X |  |
| [Harmadik fél e-mail szolgáltatói](custom-email.md) |  |X  |  |  |
| [Többnyelvű támogatás](localization.md)|  |  | X |  |
| [Predikátum-ellenőrzések](predicates.md) |  |  | X | Például a jelszó összetettsége. |
| [Vezérlők megjelenítése](display-controls.md) |  |X  |  |  |


### <a name="page-layout-versions"></a>Oldalelrendezési verziók

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
| ------- | :-----------: | :-------: | :--: | ----- |
| [2.0.0](page-layout.md#200) |  | X |  |  |
| [1.2.0](page-layout.md#120) |  | X |  |  |
| [1.1.0](page-layout.md#110) |  |  | X |  |
| [1.0.0](page-layout.md#100) |  |  | X |  |
| [JavaScript-támogatás](javascript-samples.md) |  | X |  |  |

### <a name="app-ief-integration"></a>App-IEF integráció

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
| ------- | :-----------: | :-------: | :--: | ----- |
| Lekérdezési karakterlánc paraméter`domain_hint` |  |  | X | Igényként elérhető, átadható az IDP-nek. |
| Lekérdezési karakterlánc paraméter`login_hint` |  |  | X | Igényként elérhető, átadható az IDP-nek. |
| A JSON beszúrása a felhasználói utazásba`client_assertion` | X |  |  | Elavult lesz. |
| JSON beszúrása a felhasználói útra`id_token_hint` |  | X |  | Menj előre, hogy átmenj json-on. |
| [Identitásszolgáltató-jogkivonat átadása az alkalmazásnak](idp-pass-through-custom.md) |  | X |  | Például a Facebooktól az alkalmazásig. |

### <a name="session-management"></a>Munkamenet-kezelés

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Alapértelmezett SSO-munkamenet-szolgáltató](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | X |  |
| [Külső bejelentkezési munkamenet-szolgáltató](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | X |  |
| [SAML SSO-munkamenet-szolgáltató](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | X |  |


### <a name="security"></a>Biztonság

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
|-------- | :-----------: | :-------: | :--: | ----- |
| Házirendkulcsok- generálás, kézi, feltöltés |  |  | X |  |
| Házirendkulcsok- RSA/Tanúsítvány, Titkok |  |  | X |  |


### <a name="developer-interface"></a>Fejlesztői felület

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Portal-IEF felhasználói felület |  |  | X |  |
| Házirend feltöltése |  |  | X |  |
| [Az Application Insights felhasználói útnaplói](troubleshoot-with-application-insights.md) |  | X |  | A fejlesztés során hibaelhárításra szolgál.  |
| [Az Application Insights eseménynaplói](application-insights-technical-profile.md) |  | X |  | A felhasználói folyamatok figyelésére szolgál az éles környezetben. |


## <a name="next-steps"></a>További lépések

- Az [Azure AD B2C-hez elérhető Microsoft Graph-műveletek](microsoft-graph-operations.md) ellenőrzése
- További információ [az egyéni házirendekről és a felhasználói folyamatok közötti különbségekről.](custom-policy-overview.md)

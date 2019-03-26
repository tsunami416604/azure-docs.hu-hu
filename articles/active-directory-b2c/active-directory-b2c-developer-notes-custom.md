---
title: Egyéni szabályzatok – Azure Active Directory B2C megjegyzések fejlesztők számára |} A Microsoft Docs
description: Megjegyzések konfigurálását és karbantartását az Azure AD B2C-vel egyéni szabályzatok a fejlesztők számára készült.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: cf9c6f6a54c38f00e477e2a9d62e72ab5faccdef
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418924"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Az Azure Active Directory B2C-vel egyéni szabályzatok megjegyzések fejlesztők számára

Már általánosan elérhető az Azure Active Directory B2C-vel egyéni házirend-konfigurációt. Ez a módszer, konfiguráció célja a fejlett identitáskezelési a fejlesztők összetett identitás megoldások létrehozásához. Egyéni szabályzatok az Azure AD B2C-bérlők elérhetővé az identitás-kezelőfelületi keretrendszer hatékonyságát. Speciális egyéni szabályzatok segítségével a fejlesztők támogatásán triggerhez kapcsolódó befejezése és segédanyagok olvasási időt kell terveznie identitás.

A legtöbb elérhető egyéni házirend-beállítások mostantól általánosan elérhetők, amíg nincsenek alapul szolgáló képességek, például a technikai profil típusát és tartalomdefiníció API-k szoftveres életciklusainak különböző szakaszaiban. Sok más érkeznek. Az alábbi táblázat a részletesebb szintű rendelkezésre állás szintjét határozza meg.  

## <a name="features-that-are-generally-available"></a>Általánosan elérhető

- Szerző és feltöltése az egyéni hitelesítési felhasználói utak egyéni szabályzatok használatával.  
    - Felhasználói utak cseréje, részletes közötti Jogcímszolgáltatók ismertetik.  
    - Adja meg a felhasználói utak feltételes elágaztatás.  
- Az egyéni hitelesítési felhasználói utak szolgáltatások REST API-támogatású együttműködni.  
- Összevonják identitásszolgáltatókat, amelyek megfelelnek a OpenIDConnect protokoll.  
- Identitásszolgáltatók, amelyek igazodnak a SAML 2.0 protokoll összevonják.   

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Egyéni szabályzat szolgáltatáskészletet fejlesztők feladatai

Manuális szabályzatkonfiguráció alacsonyabb szintű hozzáférést biztosít az Azure AD B2C-vel és a egy egyedi, bizalmi keretrendszer létrehozásának eredményeit az alapul szolgáló platform. Egyéni Identitásszolgáltatók, megbízhatósági kapcsolatokat, számos lehetséges kombinációinak száma az integrációt a külső szolgáltatások és a munkafolyamatok részletes tervezési és konfigurációs módszeres megközelítést igényelnek. 

Egyéni szabályzatkészlet funkciót használó fejlesztők számára meg kell felelnie a következő irányelveket:

- Megismerkedhet az egyéni szabályzatok és a kulcs vagy titkos kódokkal felügyeleti konfiguráció nyelven. További információkért lásd: [TrustFrameworkPolicy](trustframeworkpolicy.md). 
- Saját tulajdonba vétel forgatókönyveket és egyéni Integrációk. Dokumentálja a munkáját, és tájékoztatja a szervezete élő helyen.  
- A forgatókönyv módszeres teszt végrehajtása. 
- Hajtsa végre a szoftverfejlesztést és átmeneti legalább egy fejlesztési és tesztelési környezetet az ajánlott eljárásokról és a egy éles környezetben. 
- Azonnali tájékozódás az új fejlesztések az identitás-szolgáltatóktól és szolgáltatásokat integrálja. Ha például nyomon követheti, változások a titkos kódok és az ütemezett és nem tervezett módosításokat a szolgáltatásban. 
- Aktív figyelés beállítása, és figyelheti az alkalmazások válaszkészségét éles környezetben. Az Application insights szolgáltatással való integrációjáról további információkért lásd: [Azure Active Directory B2C: Naplók gyűjtése](active-directory-b2c-custom-guide-eventlogger-appins.md). 
- Az Azure-előfizetésében naprakészen kapcsolattartási e-mail-címeket, és továbbra is a Microsoft-webhelyek működés közbeni csapatával e-mailekre válaszkész maradhat. 
- Amikor javasoljuk, hogy a webhelyek működés közbeni csoportja. Ehhez kellő időben művelet végrehajtása.

## <a name="terms-for-features-in-public-preview"></a>Nyilvános előzetes verzióban elérhető funkciók feltételek

- Javasoljuk, hogy a nyilvános előzetes verziójú funkciók használatához csak tesztelési célokra. 
- Szolgáltatásiszint-szerződések (SLA) nem vonatkoznak a nyilvános előzetes verziójú funkciókat.
- Nyilvános előzetes verziójú funkciók kapcsolatos támogatási kérelmeket rendszeres támogatási csatornákon keresztül lehet benyújtani. 

## <a name="features-by-stage-and-known-issues"></a>Funkciók szakasz és ismert problémák

Egyéni szabályzat/identitás-kezelőfelületi keretrendszer képességek folyamatos és gyors fejlesztés alatt áll. Az alábbi táblázat az a funkciók és -összetevő rendelkezésre állása index.

### <a name="identity-providers-tokens-protocols"></a>Identitás-szolgáltatók jogkivonatok, protokollok

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
|-------- | ----------- | ------- | -- | ----- |
| IDP-OpenIDConnect |  |  | X | Ha például a Google +.  |
| IDP-OAUTH2 |  |  | X | Ha például a Facebook.  |
| IDP-OAUTH1 (twitter) |  | X |  | Ha például a Twitteren. |
| IDP-OAUTH1 (ex-twitter) |  |  |  | Nem támogatott |
| IDP-SAML |  |   | X | Ha például Salesforce-ban, ADFS. |
| IDP-WSFED | X |  |  |  |
| Függő entitás OAUTH1 |  |  |  | Nem támogatott. |
| Függő entitás OAUTH2 |  |  | X |  |
| Függő entitás OIDC |  |  | X |  |
| Függő entitás SAML | X |  |  |  |
| Függő entitás WSFED | X |  |  |  |
| Az alapszintű REST API-t és az ügyféltanúsítvány-alapú hitelesítés |  |  | X | Ha például az Azure Logic Apps. |

### <a name="component-support"></a>Összetevő-támogatás

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
| ------- | ----------- | ------- | -- | ----- |
| Azure Multi Factor Authentication |  |  | X |  |
| Az Azure Active Directory helyi könyvtárként |  |  | X |  |
| E-mailes ellenőrzés alrendszere Azure e-mailben |  |  | X |  |
| Több nyelv támogatása|  |  | X |  |
| Predikátum ellenőrzések |  |  | X | Ha például a jelszó erősségét. |
| Harmadik féltől származó e-mail-szolgáltatók használata | X |  |  |  |

### <a name="content-definition"></a>Tartalomdefiníció

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
| ------- | ----------- | ------- | -- | ----- |
| Error page, api.error |  |  | X |  |
| Identitásszolgáltató kiválasztása oldal, api.idpselections |  |  | X |  |
| IDP selection for signup, api.idpselections.signup |  |  | X |  |
| Forgot Password, api.localaccountpasswordreset |  |  | X |  |
| Local Account Sign-in, api.localaccountsignin |  |  | X |  |
| Helyi fiók regisztrálási, api.localaccountsignup |  |  | X |  |
| Többtényezős hitelesítés lap, api.phonefactor |  |  | X |  |
| Az önellenőrzött közösségi fiók regisztrációs, api.selfasserted |  |  | X |  |
| Self-asserted profile update, api.selfasserted.profileupdate |  |  | X |  |
| Egyesített regisztrációs vagy bejelentkezési oldal, api.signuporsignin "disableSignup" paraméterrel |  |  | X |  |
| JavaScript / oldalon, szerződéses |  | X |  |  |

### <a name="app-ief-integration"></a>Alkalmazás-IEF integráció

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
| ------- | ----------- | ------- | -- | ----- |
| Lekérdezési karakterlánc paraméter domain_hint |  |  | X | Jogcím, elérhető Identitásszolgáltató adható át. |
| Lekérdezési karakterlánc paraméter login_hint |  |  | X | Jogcím, elérhető Identitásszolgáltató adható át. |
| JSON beillesztése UserJourney client_assertion keresztül | X |  |  | Elavulttá válik. |
| UserJourney id_token_hint, JSON beillesztése |  | X |  | Go-továbbító megközelítés át, JSON. |
| Az alkalmazás Identitásszolgáltató TOKEN átadása |  | X |  | Például a Facebook-alkalmazáshoz. |

### <a name="session-management"></a>Munkamenet-kezelés

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
| ------- | ----------- | ------- | -- | ----- |
| Egyszeri bejelentkezés munkamenet-szolgáltató |  |  | X |  |
| Külső bejelentkezés munkamenet-szolgáltató |  |  | X |  |
| SAML SSO  Session Provider |  |  | X |  |
| Alapértelmezett egyszeri bejelentkezés munkamenet-szolgáltató |  |  | X |  |

### <a name="security"></a>Biztonság

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
|-------- | ----------- | ------- | -- | ----- |
| A házirend kulcsok létrehozása, manuális feltöltése |  |  | X |  |
| Policy Keys- RSA/Cert, Secrets |  |  | X |  |
| Szabályzatfeltöltés |  |  | X |  |

### <a name="developer-interface"></a>Fejlesztői felület

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
| ------- | ----------- | ------- | -- | ----- |
| Azure Portal-IEF UX |  |  | X |  |
| Application Insights UserJourney naplók |  | X |  | Hibaelhárítás a fejlesztés során használt.  |
| Application Insights az eseménynaplókat (keresztül vezénylési lépés) |  | X |  | Éles környezetben a felhasználói folyamatok figyelésére használható. |

## <a name="next-steps"></a>További lépések
[További információ az egyéni szabályzatok és a felhasználói folyamatok kapcsán](active-directory-b2c-overview-custom.md).

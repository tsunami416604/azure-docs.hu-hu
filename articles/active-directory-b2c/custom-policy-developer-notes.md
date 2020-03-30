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
ms.date: 02/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ee3b5bd3278412949074b77f9d1c53d63a467280
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189395"
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

### <a name="identity-providers-tokens-protocols"></a>Identitásszolgáltatók, tokenek, protokollok

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
|-------- | :-----------: | :-------: | :--: | ----- |
| IDP-OpenIDConnect |  |  | X | Például a Google+.  |
| IDP-OAUTH2 |  |  | X | Például a Facebook.  |
| IDP-OAUTH1 (twitter) |  | X |  | Például, Twitter. |
| IDP-OAUTH1 (ex-twitter) |  |  |  | Nem támogatott |
| IDP-SAML |  |   | X | Például Salesforce, ADFS. |
| IDP-WSFED | X |  |  |  |
| Függő fél OAUTH1 |  |  |  | Nem támogatott. |
| Függő fél OAUTH2 |  |  | X |  |
| Függő entitás OIDC |  |  | X |  |
| Függő fél SAML |  |X  |  |  |
| Függő fél WSFED | X |  |  |  |
| REST API alapszintű és tanúsítvány-hitelesítéssel |  |  | X | Például az Azure Logic Apps. |

### <a name="component-support"></a>Összetevő támogatása

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure többtényezős hitelesítés |  |  | X |  |
| Az Azure Active Directory helyi címtárként |  |  | X |  |
| Azure Email alrendszer az e-mailek ellenőrzéséhez |  |  | X |  |
| Többnyelvű támogatás|  |  | X |  |
| Predikátum-érvényesítések |  |  | X | Például a jelszó összetettsége. |
| Harmadik fél e-mail szolgáltatóinak használata |  |X  |  |  |

### <a name="content-definition"></a>Tartalom definíciója

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
| ------- | :-----------: | :-------: | :--: | ----- |
| Hiba oldal, api.error |  |  | X |  |
| IDP kiválasztási oldal, api.idpselections |  |  | X |  |
| IDP-választás a regisztrációhoz, api.idpselections.signup |  |  | X |  |
| Elfelejtett jelszó, api.localaccountpasswordreset |  |  | X |  |
| Helyi fiók bejelentkezése, api.localaccountsignin |  |  | X |  |
| Helyi fiókregisztráció, api.localaccountsignup |  |  | X |  |
| MFA oldal, api.phonefactor |  |  | X |  |
| Önérvényesítő közösségi fiók regisztráció, api.selfasserted |  |  | X |  |
| Önérvényesítő profilfrissítés, api.selfasserted.profileupdate |  |  | X |  |
| Egyesített regisztrációs vagy bejelentkezési oldal, api.signuporsignin, "disableSignup" paraméterrel |  |  | X |  |
| JavaScript / Lap elrendezés |  | X |  |  |

### <a name="app-ief-integration"></a>App-IEF integráció

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
| ------- | :-----------: | :-------: | :--: | ----- |
| Lekérdezési karakterlánc paraméter domain_hint |  |  | X | Igényként elérhető, átadható az IDP-nek. |
| Lekérdezési karakterlánc paraméter login_hint |  |  | X | Igényként elérhető, átadható az IDP-nek. |
| A JSON beszúrása a UserJourney-be client_assertion | X |  |  | Elavult lesz. |
| JSON beszúrása a UserJourney-be id_token_hint |  | X |  | Menj előre, hogy átmenj json-on. |
| IDP TOKEN átad az alkalmazásnak |  | X |  | Például a Facebooktól az alkalmazásig. |

### <a name="session-management"></a>Munkamenet-kezelés

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
| ------- | :-----------: | :-------: | :--: | ----- |
| SSO-munkamenet-szolgáltató |  |  | X |  |
| Külső bejelentkezési munkamenet-szolgáltató |  |  | X |  |
| SAML sso munkamenet-szolgáltató |  |  | X |  |
| Alapértelmezett SSO-munkamenet-szolgáltató |  |  | X |  |

### <a name="security"></a>Biztonság

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
|-------- | :-----------: | :-------: | :--: | ----- |
| Házirendkulcsok- generálás, kézi, feltöltés |  |  | X |  |
| Házirendkulcsok- RSA/Tanúsítvány, Titkok |  |  | X |  |
| Házirend feltöltése |  |  | X |  |

### <a name="developer-interface"></a>Fejlesztői felület

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Portal-IEF felhasználói felület |  |  | X |  |
| Application Insights UserJourney naplók |  | X |  | A fejlesztés során hibaelhárításra szolgál.  |
| Application Insights eseménynaplók (vezénylési lépések) |  | X |  | A felhasználói folyamatok figyelésére szolgál az éles környezetben. |

## <a name="next-steps"></a>További lépések

További információ [az egyéni házirendekről és a felhasználói folyamatok közötti különbségekről.](custom-policy-overview.md)

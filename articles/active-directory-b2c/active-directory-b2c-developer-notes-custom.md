---
title: Egyéni szabályzatok fejlesztői megjegyzései
titleSuffix: Azure AD B2C
description: Megjegyzések a Azure AD B2C egyéni szabályzatokkal történő konfigurálásához és karbantartásához szükséges fejlesztőknek.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: edbd31434715c380badf15118b0779885aed700f
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949755"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Fejlesztői megjegyzések a Azure Active Directory B2C egyéni házirendjeihez

A Azure Active Directory B2C egyéni házirend-konfigurációja már általánosan elérhető. Ez a konfigurációs módszer speciális identitás-fejlesztőknek készült komplex Identity-fejlesztők számára van megcélozva. Az egyéni szabályzatok a Azure AD B2C bérlők számára elérhető Identity Experience keretrendszer hatékonyságát teszik hatékonyabbá.
Az egyéni szabályzatokat használó speciális identitás-fejlesztőknek meg kell tervezniük egy kis idő betöltését és a tájékoztató dokumentumok olvasását.

Habár a rendelkezésre álló egyéni házirend-lehetőségek többsége már általánosan elérhető, olyan mögöttes képességekkel rendelkezik, mint például a technikai profilok típusai és a tartalmi definíciós API-k, amelyek a szoftver életciklusának különböző szakaszaiban találhatók. Számos további újdonság. Az alábbi táblázat a rendelkezésre állás szintjét határozza meg részletesebb szinten.

## <a name="features-that-are-generally-available"></a>Általánosan elérhető funkciók

- Egyéni hitelesítési felhasználói utazások létrehozása és feltöltése egyéni szabályzatok használatával.
    - A felhasználói útvonalak lépésenkénti ismertetése a jogcímek szolgáltatói közötti cseréjeként.
    - Feltételes elágazás definiálása felhasználói útvonalakon.
- A REST API-kompatibilis szolgáltatásokkal való együttműködés az egyéni hitelesítési felhasználói útvonalakon.
- A összevonása az OpenIDConnect protokollnak megfelelő identitás-szolgáltatókkal.
- Összevonása az SAML 2,0 protokollnak megfelelő identitás-szolgáltatókkal.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Az egyéni szabályzat funkcióinak beállítása – fejlesztők

A manuális házirend-konfiguráció alacsonyabb szintű hozzáférést biztosít a Azure AD B2C mögöttes platformhoz, és egy egyedi, megbízhatósági keretrendszer létrehozását eredményezi. Az egyéni identitás-szolgáltatók, a megbízhatósági kapcsolatok, a külső szolgáltatásokkal való integrációk és a lépésenkénti munkafolyamatok számos lehetséges variációja szükséges a tervezéshez és a konfiguráláshoz.

Az egyéni házirend-készletet használó fejlesztőknek be kell tartaniuk a következő irányelveket:

- Ismerkedjen meg az egyéni szabályzatok és a kulcs/titkok kezelésének konfigurációs nyelvével. További információ: [TrustFrameworkPolicy](trustframeworkpolicy.md).
- A forgatókönyvek és az egyéni integrációk saját tulajdonba vételét. Dokumentálja a munkáját, és tájékoztassa az élő hely szervezetét.
- Végezzen tesztelési módszert.
- A szoftverfejlesztés és az előkészítési ajánlott eljárások követése legalább egy fejlesztési és tesztelési környezettel, valamint egy éles környezettel.
- Tájékozódjon arról, hogy az Ön által integrált identitás-szolgáltatók és szolgáltatások milyen új fejleményeket mutatnak be. Például nyomon követheti a titkok és a szolgáltatás ütemezett és nem ütemezett változásainak változásait.
- Állítsa be az aktív figyelést, és figyelje az éles környezetek érzékenységét. További információ a Application Insights integrálásáról [: Azure Active Directory B2C: naplók gyűjtése](active-directory-b2c-custom-guide-eventlogger-appins.md).
- Lépjen kapcsolatba az Azure-előfizetésben aktuálisan használt e-mail címekkel, és válaszoljon a Microsoft Live-site Team-e-mailekre.
- Ha a Microsoft Live-site csapata ezt javasolja, végezze el a szükséges lépéseket.

## <a name="terms-for-features-in-public-preview"></a>A nyilvános előzetes verzióban elérhető funkciók használati feltételei

- Javasoljuk, hogy csak kiértékelési célokra használja a nyilvános előzetes verzió funkcióit.
- A szolgáltatói szerződések (SLA-kat) nem vonatkoznak a nyilvános előzetes verzió szolgáltatásaira.
- A nyilvános előzetes verziójú funkciókra vonatkozó támogatási kérelmeket rendszeres támogatási csatornákon keresztül lehet benyújtani.

## <a name="features-by-stage-and-known-issues"></a>Funkciók szakasz és ismert problémák alapján

Az egyéni házirend-/identitás-keretrendszer képességei állandó és gyors fejlesztés alatt állnak. A következő táblázat a szolgáltatások és az összetevők rendelkezésre állásának indexét tartalmazza.

### <a name="identity-providers-tokens-protocols"></a>Identitás-szolgáltatók, tokenek, protokollok

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
|-------- | :-----------: | :-------: | :--: | ----- |
| IDENTITÁSSZOLGÁLTATÓ – OpenIDConnect |  |  | X | Például: Google +.  |
| IDENTITÁSSZOLGÁLTATÓ – OAUTH2 |  |  | X | Például: Facebook.  |
| IDENTITÁSSZOLGÁLTATÓ-OAUTH1 (Twitter) |  | X |  | Például: Twitter. |
| IDENTITÁSSZOLGÁLTATÓ-OAUTH1 (ex-Twitter) |  |  |  | Nem támogatott |
| IDENTITÁSSZOLGÁLTATÓ – SAML |  |   | X | Például: Salesforce, ADFS. |
| IDENTITÁSSZOLGÁLTATÓ – WSFED | X |  |  |  |
| Függő entitás OAUTH1 |  |  |  | Nem támogatott. |
| Függő entitás OAUTH2 |  |  | X |  |
| Függő entitás OIDC |  |  | X |  |
| Függő entitás SAML-je | X |  |  |  |
| Függő entitás WSFED | X |  |  |  |
| Alapszintű és Tanúsítványos hitelesítés REST API |  |  | X | Például Azure Logic Apps. |

### <a name="component-support"></a>Összetevő-támogatás

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Multi Factor Authentication |  |  | X |  |
| Azure Active Directory helyi könyvtárként |  |  | X |  |
| E-mail-ellenőrzésre szolgáló Azure E-mail alrendszer |  |  | X |  |
| Többnyelvű támogatás|  |  | X |  |
| Predikátumok érvényessége |  |  | X | Például a jelszó bonyolultsága. |
| Harmadik féltől származó e-mail szolgáltatók használata | X |  |  |  |

### <a name="content-definition"></a>Tartalom definíciója

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
| ------- | :-----------: | :-------: | :--: | ----- |
| Hiba lap, API. hiba |  |  | X |  |
| IDENTITÁSSZOLGÁLTATÓ-kiválasztási oldal, API. idpselections |  |  | X |  |
| IDENTITÁSSZOLGÁLTATÓ kiválasztása a regisztrációhoz, API. idpselections. regisztráció |  |  | X |  |
| Elfelejtett jelszó, API. localaccountpasswordreset |  |  | X |  |
| Helyi fiók bejelentkezés, API. localaccountsignin |  |  | X |  |
| Helyi fiók regisztrálása, API. localaccountsignup |  |  | X |  |
| MFA-oldal, API. phonefactor |  |  | X |  |
| Önérvényesített közösségi fiók regisztrálása, API. selfasserted |  |  | X |  |
| Önérvényesített profil frissítése, API. selfasserted. profileUpdate |  |  | X |  |
| Egyesített regisztrációs vagy bejelentkezési oldal, API. signuporsignin, "disableSignup" paraméterrel |  |  | X |  |
| JavaScript/oldal elrendezése |  | X |  |  |

### <a name="app-ief-integration"></a>IEF-integráció

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
| ------- | :-----------: | :-------: | :--: | ----- |
| Lekérdezési karakterlánc paraméterének domain_hint |  |  | X | A jogcímként elérhető lehet a IDENTITÁSSZOLGÁLTATÓ. |
| Lekérdezési karakterlánc paraméterének login_hint |  |  | X | A jogcímként elérhető lehet a IDENTITÁSSZOLGÁLTATÓ. |
| JSON beszúrása a UserJourney-be a client_assertion használatával | X |  |  | Elavulttá válik. |
| JSON beszúrása a UserJourney id_token_hint |  | X |  | Továbbítási megközelítés a JSON továbbítására. |
| IDENTITÁSSZOLGÁLTATÓ-TOKEN továbbítása az alkalmazásnak |  | X |  | Például a Facebookról az alkalmazásra. |

### <a name="session-management"></a>Munkamenet-kezelés

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
| ------- | :-----------: | :-------: | :--: | ----- |
| Egyszeri bejelentkezés munkamenet-szolgáltatója |  |  | X |  |
| Külső bejelentkezési munkamenet-szolgáltató |  |  | X |  |
| SAML SSO-munkamenet-szolgáltató |  |  | X |  |
| Alapértelmezett egyszeri bejelentkezés munkamenet-szolgáltatója |  |  | X |  |

### <a name="security"></a>Biztonság

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
|-------- | :-----------: | :-------: | :--: | ----- |
| Házirend-kulcsok – generál, manuális, feltöltés |  |  | X |  |
| Szabályzat kulcsai – RSA/CERT, Secrets |  |  | X |  |
| Szabályzat feltöltése |  |  | X |  |

### <a name="developer-interface"></a>Fejlesztői felület

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Portal – IEF UX |  |  | X |  |
| Application Insights UserJourney-naplók |  | X |  | A fejlesztés során hibaelhárításra szolgál.  |
| Eseménynaplók Application Insights (az előkészítés lépésein keresztül) |  | X |  | Az élesben használt felhasználói folyamatok figyelésére szolgál. |

## <a name="next-steps"></a>Következő lépések

További információ [az egyéni házirendekről és a felhasználói folyamatokkal kapcsolatos különbségekről](active-directory-b2c-overview-custom.md).

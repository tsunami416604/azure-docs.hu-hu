---
title: Az egyéni szabályzatok technikai profiljainak áttekintése
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan használják a technikai profilokat a Azure Active Directory B2C egyéni házirendjében.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/02/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 69582291ca1da95003e26a6922899defd7d5e477
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982398"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Tudnivalók a Azure Active Directory B2C egyéni szabályzatok technikai profiljairól

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A technikai profil egy beépített mechanizmust biztosít a különböző típusú felekkel való kommunikációhoz Azure Active Directory B2C (Azure AD B2C) egyéni szabályzatának használatával. A technikai profilok a Azure AD B2C Bérlővel való kommunikációhoz, felhasználó létrehozásához vagy felhasználói profil beolvasásához használhatók. A technikai profil önérvényesíthető úgy, hogy lehetővé tegye a felhasználóval való interakciót. Gyűjtsön például a felhasználó hitelesítő adatait a bejelentkezéshez, majd jelenítse meg a regisztrációs lapot vagy a jelszó-visszaállítási lapot.

## <a name="type-of-technical-profiles"></a>Technikai profilok típusa

A technikai profil a következő típusú forgatókönyveket teszi lehetővé:

- [Azure Active Directory](active-directory-technical-profile.md) – támogatást nyújt a Azure Active Directory B2C felhasználói felügyelethez.
- [JWT jogkivonat kiállítója](jwt-issuer-technical-profile.md) – a függő entitás alkalmazásának visszaadott JWT-tokent bocsát ki.
- **Telefonos faktor-szolgáltató** – multi-Factor Authentication.
- [OAuth1](oauth1-technical-profile.md) – összevonás bármely OAuth 1,0 protokoll-identitás-szolgáltatóval.
- [OAuth2](oauth2-technical-profile.md) – összevonás bármely OAuth 2,0 protokoll-identitás-szolgáltatóval.
- [OpenID Connect](openid-connect-technical-profile.md) – összevonás bármely OpenID Connect protokoll-identitás-szolgáltatóval.
- [Jogcím-átalakítás](claims-transformation-technical-profile.md) – hívja meg a kimeneti jogcímek átalakítását a jogcímek értékeinek módosításához, a jogcímek érvényesítéséhez, illetve a kimeneti jogcímek alapértelmezett értékének megadásához.
- [Rest-szolgáltató](restful-technical-profile.md) – olyan REST API szolgáltatásokat hív meg, mint például a felhasználói bevitel ellenőrzése, a felhasználói adatok gazdagítása vagy az üzletági alkalmazásokkal való integráció.
- [Egy saml2](saml-technical-profile.md) – összevonás bármely SAML protokoll identitás-szolgáltatóval.
- [Önérvényesített](self-asserted-technical-profile.md) – interakció a felhasználóval. Gyűjtsön például a felhasználó hitelesítő adatait a bejelentkezéshez, a regisztrációs oldal megjelenítéséhez vagy a jelszó alaphelyzetbe állításához.
- [Munkamenet-kezelés](custom-policy-reference-sso.md) – különböző típusú munkamenetek kezelése.
- **Application bepillantást**
- [Egyszeri jelszó](one-time-password-technical-profile.md) – támogatást nyújt az egyszeri jelszavak létrehozásának és ellenőrzésének kezeléséhez. 

## <a name="technical-profile-flow"></a>Technikai profil folyamata

A műszaki profilok minden típusa ugyanazzal a fogalommal rendelkezik. A bemeneti jogcímeket, a jogcímek átalakítását és a konfigurált féllel folytatott kommunikációt, például egy identitás-szolgáltatót, REST API vagy Azure AD címtárszolgáltatás-szolgáltatást kell küldenie. A folyamat befejeződése után a technikai profil visszaadja a kimeneti jogcímeket, és a kimeneti jogcímek átalakítását is futtathatja. A következő ábra a technikai profilban hivatkozott átalakítások és leképezések feldolgozását mutatja be. Függetlenül attól, hogy a technikai profil milyen fél által kommunikál, a jogcímek átalakításának végrehajtása után a technikai profilból származó kimeneti jogcímeket a rendszer azonnal a jogcím-táskában tárolja.

![A műszaki profil folyamatát ábrázoló diagram](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **InputClaimsTransformation** – a bemeneti [jogcímek átalakításának](claimstransformations.md) bemeneti jogcímeit a rendszer felveszi a jogcím-csomagból, és a végrehajtás után a rendszer visszahelyezi a kimeneti jogcímeket a jogcímek táskába. A bemeneti jogcímek átalakításának kimeneti jogcímei a következő bemeneti jogcímek átalakításának bemeneti jogcímei lehetnek.
2. **Szabályzattípushoz** – a jogcímeket a rendszer a jogcímek táskájában keresi, és a technikai profilhoz használja. Egy [önérvényesített technikai profil](self-asserted-technical-profile.md) például a bemeneti jogcímek használatával előre feltölti a felhasználó által biztosított kimeneti jogcímeket. A REST API műszaki profilok a bemeneti jogcímek használatával küldenek bemeneti paramétereket a REST API végpontnak. A Azure Active Directory a bemeneti jogcímet egyedi azonosítóként használja egy fiók olvasásához, frissítéséhez vagy törléséhez.
3. **Technikai profil végrehajtása** – a technikai profil a jogcímeket a konfigurált fél számára cseréli. Példa:
    - A bejelentkezés befejezéséhez irányítsa át a felhasználót az identitás-szolgáltatóra. A sikeres bejelentkezés után a felhasználó visszatér, és a technikai profil végrehajtása folytatódik.
    - REST API meghívása a paraméterek Szabályzattípushoz való elküldésekor és az információk OutputClaims való visszaszerzése közben.
    - Hozza létre vagy frissítse a felhasználói fiókot.
    - Az MFA szöveges üzenet küldése és ellenőrzése.
4. **ValidationTechnicalProfiles** – egy [önérvényesített technikai profilhoz](self-asserted-technical-profile.md)meghívhat egy bemeneti [ellenőrzési technikai profilt](validation-technical-profile.md). Az érvényesítési technikai profil ellenőrzi a felhasználó által beolvasott adatokat, és egy hibaüzenetet vagy ok értéket ad vissza kimeneti jogcímek vagy azok nélkül. Például mielőtt a Azure AD B2C új fiókot hoz létre, ellenőrzi, hogy a felhasználó már létezik-e a címtárszolgáltatások szolgáltatásban. A saját üzleti logikájának hozzáadásához meghívhat egy REST API technikai profilt is.<p>Az érvényesítési technikai profil kimeneti jogcímeinek hatóköre a technikai profilra korlátozódik, amely meghívja az érvényesítési műszaki profilt és más ellenőrzési technikai profilokat ugyanazon a technikai profilban. Ha a következő előkészítési lépésben a kimeneti jogcímeket szeretné használni, hozzá kell adnia a kimeneti jogcímeket a technikai profilhoz, amely meghívja az érvényesítési technikai profilt.
5. **OutputClaims** – a rendszer visszaküldi a jogcímeket a jogcímek táskába. Ezeket a jogcímeket a következő előkészítési lépésekben vagy a kimeneti jogcímek átalakításában használhatja.
6. **OutputClaimsTransformations** – a kibocsátási [jogcímek átalakításának](claimstransformations.md) bemeneti jogcímeit a rendszer a jogcím-csomagból használja fel. Az előző lépések technikai profiljának kimeneti jogcímei a kimeneti jogcímek átalakításának bemeneti jogcímei. A végrehajtás után a kimeneti jogcímek vissza lesznek helyezve a jogcím-táskába. A kimeneti jogcímek átalakításának kimeneti jogcímei a következő kimeneti jogcímek átalakításának bemeneti jogcímei is lehetnek.
7. Egyszeri **bejelentkezéses (SSO) munkamenet-felügyeleti** - [egyszeri](custom-policy-reference-sso.md) bejelentkezéses munkamenet-kezelési vezérlők a felhasználóval való kommunikációt követően a felhasználó által már hitelesített hitelesítéssel. A rendszergazda például megadhatja, hogy megjelenjenek-e az identitás-szolgáltatók, vagy hogy meg kell-e adni a helyi fiók adatait.

A technikai profil örökölheti egy másik technikai profilt a beállítások módosításához vagy új funkciók hozzáadásához.  A **IncludeTechnicalProfile** elem arra az alapszintű műszaki profilra hivatkozik, amelyből a technikai profil származik.

Például a **HRE-UserReadUsingAlternativeSecurityId-Error** technikai profil tartalmazza a **HRE-UserReadUsingAlternativeSecurityId**. Ez a technikai profil állítja be a **RaiseErrorIfClaimsPrincipalDoesNotExist** metaadat-elemét `true`re, és hibát jelez, ha nem létezik közösségi fiók a címtárban. **HRE-UserReadUsingAlternativeSecurityId – a hiba** felülbírálja ezt a viselkedést, és letiltja a hibaüzenetet, ha a felhasználó nem létezett.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

A **HRE-UserReadUsingAlternativeSecurityId** tartalmazza a `AAD-Common` technikai profilt.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Mind a **HRE-UserReadUsingAlternativeSecurityId** , mind a **HRE-UserReadUsingAlternativeSecurityId** nem adja meg a szükséges **protokoll** elemet, mert az a **HRE-Common** Technical profilban van megadva.

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

A technikai profil tartalmazhat vagy örököl egy másik technikai profilt, amely tartalmazhat egy másikat is. A szintek száma nincs korlátozva. Az üzleti követelményektől függően a felhasználói utazás meghívhatja a **HRE-UserReadUsingAlternativeSecurityId-** t, amely hibát jelez, ha a felhasználó közösségi fiókja nem létezik, vagy **HRE-UserReadUsingAlternativeSecurityId-Error** , amely nem okoz hibát.












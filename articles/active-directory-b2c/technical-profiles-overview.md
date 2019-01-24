---
title: Az Azure Active Directory B2C-vel egyéni szabályzatok műszaki profilokkal kapcsolatos |} A Microsoft Docs
description: Ismerje meg, hogyan műszaki profilokkal kapcsolatos használatban vannak az Azure Active Directory B2C-vel egyéni szabályzatok.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 177e44a53f9976879e15e24313c1d8479cb5a6a7
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54853221"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Az Azure Active Directory B2C-vel egyéni szabályzatok műszaki profilokról

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A technikai profil olyan keretrendszert kínál, az egy beépített mechanizmus a különböző típusú egyéni szabályzat használata az Azure Active Directory (Azure AD) B2C felek kommunikálni. Technikai profilok segítségével kommunikál az Azure AD B2C-bérlőben, hozzon létre egy felhasználót vagy egy felhasználói profil olvasása. Lehet, hogy a technikai profil önellenőrzött felhasználói engedélyezéséhez. Gyűjtsön például a felhasználó hitelesítő adatait, hogy jelentkezzen be, és ezután jelennek meg a regisztrációs oldalon vagy a jelszó alaphelyzetbe állítására szolgáló lap. 

## <a name="type-of-technical-profiles"></a>Technikai profil típusát

A technikai profil lehetővé teszi, hogy az ilyen típusú forgatókönyvek:

- [Az Azure Active Directory](active-directory-technical-profile.md) -támogatást nyújt az Azure Active Directory B2C felhasználói kezelése.
- [JWT jogkivonat kibocsátója](jwt-issuer-technical-profile.md) – a JWT jogkivonat a függő entitás alkalmazásnak visszaadott bocsát ki. 
- **Phone factor szolgáltató** – multi-factor authentication szolgáltatás.
- [OAuth1](oauth1-technical-profile.md) -összevonási bármely OAuth 1.0 protokoll identitásszolgáltatóval.
- [Az OAuth2](oauth2-technical-profile.md) -összevonási minden olyan OAuth 2.0 protokoll identitásszolgáltatóval.
- [OpenIdConnect](openid-connect-technical-profile.md) -összevonási bármely OpenId Connect protokollal identitásszolgáltatóval.
- [Jogcím-átalakítás](claims-transformation-technical-profile.md) – hívás kimeneti jogcímek átalakítása módosítására jogcím-értékek, vagy ellenőrizheti a jogcímeit, kimeneti jogcímek készletének az alapértelmezett értékeket.
- [RESTful szolgáltató](restful-technical-profile.md) -hívás a REST API-szolgáltatások, mint például az adatbevitel érvényesítéséhez felhasználói adatok bővítését, vagy – üzletági alkalmazások integrálhatók.
- [Egy SAML2](saml-technical-profile.md) -összevonási bármely SAML-protokoll identitásszolgáltatóval.
- [Önálló kiszolgáló által megerősített](self-asserted-technical-profile.md) -interakciót folytatni a felhasználóval. Gyűjtsön például a felhasználó hitelesítő adatait, hogy jelentkezzen be, a regisztrációs oldalon jelennek meg, vagy a jelszó-visszaállítási.
- **WsFed** -összevonási bármely WsFed protokoll identitásszolgáltatóval. 
- [Munkamenet-kezelés](active-directory-b2c-reference-sso-custom.md) -munkamenetek különböző típusú kezelni. 
- **Az Application insights**

## <a name="technical-profile-flow"></a>Technikai profil folyamat

Az összes típusú technikai profil ossza meg ugyanazt a fogalmat fedi. A bemeneti jogcímek küldése, jogcímek átalakítását futtatja, és a beállított entitás, például egy identitásszolgáltatóval, REST API-t vagy az Azure AD directory services folytatott kommunikációhoz. A folyamat befejezése után a technikai profil adja vissza a kimeneti jogcímek, és előfordulhat, hogy futtassa a kimeneti jogcímek átalakítását. Az alábbi ábrán látható, az átalakítások és a technikai profilban hivatkozott leképezések feldolgozásának módja. A technikai profil kommunikál, után bármilyen jogcím-átalakítási hajtja végre, a fél függetlenül a kimeneti jogcímek a technikai profil azonnal vannak tárolva a jogcímek tulajdonságcsomag. 

![Technikai profil folyamat](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **InputClaimsTransformation** – adjon meg minden bemeneti jogcímek [jogcím-átalakítás](claimstransformations.md) jogcímek csomagban van, és a kimeneti jogcímek kerüljenek vissza a jogcímek tasakban végrehajtás után mértékének. A kimeneti jogcímek átalakulása a bemeneti jogcímek között, egy későbbi bemeneti jogcímek átalakítását a bemeneti jogcímek között lehet.
2. **InputClaims** - jogcímek, az a jogcím tulajdonságcsomag mértékének és a technikai profil szolgálnak. Ha például egy [önálló kiszolgáló által megerősített technikai profil](self-asserted-technical-profile.md) használja a bemeneti jogcímek, hogy feltöltse a felhasználó megadja a kimeneti jogcímek. A REST API-val technikai profil a bemeneti jogcímek között a REST API-végpont küldendő bemeneti paramétereket használja. Az Azure Active Directory bemeneti jogcímek használja egyedi azonosítóként olvasása, frissítése vagy-fiók törlése.
3. **Technikai profil végrehajtási** – a technikai profilban konfigurált fél a jogcímek adatcseréihez használható. Példa:
    - Átirányítja a felhasználót az identitásszolgáltatóhoz a bejelentkezés befejezéséhez. A sikeres bejelentkezést követően a felhasználó vissza adja vissza, és a technikai profil végrehajtása folytatódik.
    - REST API hívása paraméterek küldjük InputClaims és OutputClaims; az adatok lekérdezése közben.
    - Hozzon létre vagy módosítsa a felhasználói fiókot.
    - Küld, és ellenőrzi az MFA üzenetre.
4. **ValidationTechnicalProfiles** – egy [technikai profil önellenőrzött](self-asserted-technical-profile.md), meghívhatja egy bemeneti [ellenőrzési technikai profil](validation-technical-profile.md). Az ellenőrzési technikai profil ellenőrzi a profilkészítés során létrehozott, a felhasználó által, és a egy hibaüzenet vagy az OK gombra, vagy a kimeneti jogcímek nélkül adja vissza. Például az Azure AD B2C-vel hoz létre egy új fiókot, mielőtt azt ellenőrzi, hogy a felhasználó már szerepel a directory services. Segítségével meghívhatja a REST API-val technikai profil hozzáadása a saját üzleti logikája.<p>A kimeneti jogcímek egy érvényesítési technikai profil hatóköre csak a technikai profil, amely meghívja az érvényesítési technikai profilban és egyéb ellenőrzési technikai profilok azonos technikai profil alatt. Ha azt szeretné, a kimeneti jogcímek használata a következő lépésben vezénylési, hozzá kell a kimeneti jogcímek a műszaki profilba, amely meghívja az érvényesítési technikai profilban.
5. **OutputClaims** -jogcímek visszatér a jogcímek tulajdonságcsomag. Ezeket a következő vezénylések lépéssel, vagy a kimeneti jogcímek átalakítása jogcímeket is használhatja.
6. **OutputClaimsTransformations** – adjon meg minden kimeneti jogcímek [jogcím-átalakítás](claimstransformations.md) származó jogcímek zsák mértékének. A kimeneti jogcímek az előző lépésekben a technikai profil lehet egy kimeneti jogcímek átalakítását a bemeneti jogcímek között. Végrehajtás után a kimeneti jogcímek kerüljenek vissza a jogcímek csomagban. A kimeneti jogcímek egy kimeneti jogcímek átalakítását a bemeneti jogcímek között, egy későbbi kimeneti jogcímek átalakítását is lehet.
7. **Egyszeri bejelentkezés (SSO) munkamenet-kezelés** - [egyszeri bejelentkezés munkamenet-kezelés](active-directory-b2c-reference-sso-custom.md) szabályozza a felhasználó-szal, a felhasználó már rendelkezik hitelesítése után. Ha például a rendszergazda szabályozhatja jelenik-e a választott Identitásszolgáltatók meg, vagy hogy helyi fiók adatainak kell újra megadni.

Egy technikai profil egy másik technikai profil beállításainak módosítása vagy hozzáadása az új funkciókat is örökli.  A **IncludeTechnicalProfile** elem egy hivatkozást az alap technikai profilban, amelyből technikai profil származik.  

Ha például a **AAD-UserReadUsingAlternativeSecurityId-NoError** technikai profil tartalmazza a **AAD-UserReadUsingAlternativeSecurityId**. A technikai profil beállítja a **RaiseErrorIfClaimsPrincipalDoesNotExist** metaadatok elem `true`, és hibát jelez, ha egy közösségi fiók nem létezik a címtárban. **AAD-UserReadUsingAlternativeSecurityId-NoError** felülbírálja ezt a viselkedést, és letiltja a hibaüzenetet, ha a felhasználó még nem létezett.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
``` 

**AAD-UserReadUsingAlternativeSecurityId** magában foglalja a `AAD-Common` technikai profil.

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

Mindkét **AAD-UserReadUsingAlternativeSecurityId-NoError** és **AAD-UserReadUsingAlternativeSecurityId** nem adja meg a szükséges **protokoll** elem mert meg van adva az az **AAD-közös** technikai profil.

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

Technikai profil előfordulhat, hogy tartalmazzák, vagy egy másik technikai profil, amely egy másik öröklik. A szintek száma nincs korlátozva van. Az üzleti követelményeitől függően a felhasználói interakciósorozat meghívhatja **AAD-UserReadUsingAlternativeSecurityId** , amely hibát jelez, ha egy felhasználó közösségi fiók nem létezik, vagy  **AAD-UserReadUsingAlternativeSecurityId-NoError** nem előléptetése, amely hiba.












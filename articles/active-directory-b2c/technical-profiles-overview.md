---
title: Technikai profilok áttekintése egyéni házirendekben
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan használják a technikai profilokat az Azure Active Directory B2C egyéni szabályzatában.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5d89d123fe757b0502f4c6b3a0d33c185b25224b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057317"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Technikai profilok az Azure Active Directory B2C egyéni szabályzataiban

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A technikai profil egy beépített mechanizmussal rendelkező keretrendszert biztosít a különböző típusú felekkel való kommunikációhoz az Azure Active Directory B2C (Azure AD B2C) egyéni szabályzathasználatával. A technikai profilok az Azure AD B2C-bérlővel való kommunikációra, a felhasználó létrehozására vagy egy felhasználói profil olvasására szolgálnak. A technikai profil lehet önálló érvényesítve, hogy a felhasználóval való interakció. Például gyűjtse össze a felhasználó hitelesítő adatait a bejelentkezéshez, majd jelenítse meg a regisztrációs oldalt vagy a jelszó-visszaállítási lapot.

## <a name="type-of-technical-profiles"></a>A műszaki profilok típusa

A technikai profil lehetővé teszi az ilyen típusú forgatókönyvek:

- [Application Insights](application-insights-technical-profile.md) – Eseményadatok küldése az [Application Insights](../azure-monitor/app/app-insights-overview.md)nak.
- [Azure Active Directory](active-directory-technical-profile.md) – Támogatja az Azure Active Directory B2C felhasználói felügyeletét.
- [Azure többtényezős hitelesítés](multi-factor-auth-technical-profile.md) – támogatja a telefonszámok ellenőrzését az Azure többtényezős hitelesítés (MFA) használatával. 
- [Jogcímátalakítás](claims-transformation-technical-profile.md) – A kimeneti jogcímek átalakítása a jogcímértékek kezeléséhez, jogcímek érvényesítéséhez vagy a kimeneti jogcímek készletalapértelmezett értékeinek beállításához.
- [JWT token kibocsátó](jwt-issuer-technical-profile.md) – JWT-jogkivonatot bocsát ki, amely visszakerül a függő entitás alkalmazás.
- [OAuth1](oauth1-technical-profile.md) – Összevonás bármely OAuth 1.0 protokollidentitás-szolgáltatóval.
- [OAuth2](oauth2-technical-profile.md) – Összevonás bármely OAuth 2.0 protokollidentitás-szolgáltatóval.
- [Egyszeri jelszó](one-time-password-technical-profile.md) – Támogatja az egyszeri jelszó létrehozásának és ellenőrzésének kezelését.
- [OpenID Connect](openid-connect-technical-profile.md) – Összevonás bármely OpenID Connect protokollidentitás-szolgáltatóval.
- [Telefontényező](phone-factor-technical-profile.md) – A telefonszámok regisztrálásának és ellenőrzésének támogatása.
- [RESTful szolgáltató](restful-technical-profile.md) – Hívja meg a REST API-szolgáltatásokat, például a felhasználói bevitel érvényesítését, a felhasználói adatok gazdagítását vagy az üzletági alkalmazásokkal való integrációt.
- [SAML2](saml-technical-profile.md) – összevonás bármely SAML protokollidentitás-szolgáltatóval.
- [SAML token kiállító](saml-issuer-technical-profile.md) – SAML-jogkivonatot bocsát ki, amely visszakerül a függő entitás alkalmazás.
- [Önérvényesített](self-asserted-technical-profile.md) - Kölcsönhatásba lépnek a felhasználóval. Például gyűjtse össze a felhasználó hitelesítő adatait a bejelentkezéshez, a regisztrációs oldal megjelenítéséhez vagy a jelszó alaphelyzetbe állításához.
- [Munkamenet-kezelés](custom-policy-reference-sso.md) – különböző típusú munkamenetek kezelése.

## <a name="technical-profile-flow"></a>Műszaki profiláramlás

Minden típusú technikai profilok ugyanazt a koncepciót. Bemeneti jogcímeket küld, jogcímátalakítást futtathat, és kommunikálhat a konfigurált féllel, például egy identitásszolgáltatóval, a REST API-val vagy az Azure AD címtárszolgáltatásokkal. A folyamat befejezése után a technikai profil visszaadja a kimeneti jogcímeket, és futtathatja a kimeneti jogcímek átalakítását. Az alábbi ábra bemutatja, hogyan dolgozza fel a technikai profilban hivatkozott átalakításokat és leképezéseket. Függetlenül attól, hogy a technikai profil a technikai profil ban kommunikál- val, miután bármilyen jogcímátalakítás végrehajtása, a kimeneti igények a technikai profil azonnal tárolja a jogcímcsomag.

![A műszaki profilfolyamatot bemutató diagram](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **Egyszeri bejelentkezés (SSO) munkamenet-kezelés** – visszaállítja a technikai profil munkamenet-állapotát [az Egyszeri bejelentkezés munkamenet-kezelése](custom-policy-reference-sso.md)használatával.
1. **Bemeneti jogcímek átalakítása** – Bemeneti jogcímek minden bemeneti [jogcím átalakítása](claimstransformations.md) a jogcímcsomagból.  A bemeneti jogcímek átalakítása kimeneti jogcímek lehetnek bemeneti jogcímek egy későbbi input jogcímek átalakítása.
1. **Bemeneti igények** – A követelések a jogcímcsomagból származnak, és a technikai profilhoz használatosak. Például egy [önérvényesítő technikai profil](self-asserted-technical-profile.md) a bemeneti jogcímek segítségével előre feltölti a felhasználó által biztosított kimeneti jogcímeket. A REST API technikai profil a bemeneti jogcímek segítségével küld bemeneti paramétereket a REST API-végpontra. Az Azure Active Directory a bemeneti jogcím egyedi azonosítóként használja a fiók olvasásához, frissítéséhez vagy törléséhez.
1. **Technikai profil végrehajtás** – A technikai profil kicseréli a jogcímeket a konfigurált fél. Példa:
    - Irányítsa át a felhasználót az identitásszolgáltatóhoz a bejelentkezés befejezéséhez. Sikeres bejelentkezés után a felhasználó visszatér, és a technikai profil végrehajtása folytatódik.
    - Hívjon meg egy REST API-t, miközben paramétereket küld InputClaims-ként, és információt kap vissza OutputClaims néven.
    - Hozza létre vagy frissítse a felhasználói fiókot.
    - Elküldi és ellenőrzi az MFA szöveges üzenetet.
1. **Validálási technikai profilok** - A [saját érvényesítésű technikai profil](self-asserted-technical-profile.md) [hívhatja érvényesítési technikai profilok](validation-technical-profile.md). Az érvényesítési technikai profil ellenőrzi a felhasználó által profilozott adatokat, és egy hibaüzenetet vagy ok értéket ad vissza kimeneti jogcímekkel vagy anélkül. Például mielőtt az Azure AD B2C új fiókot hoz létre, ellenőrzi, hogy a felhasználó már létezik-e a címtárszolgáltatásokban. A REST API technikai profilját meghívhatja saját üzleti logikájának hozzáadásához.<p>Az érvényesítési technikai profil kimeneti jogcímeinek hatóköre az érvényesítési technikai profilt megmeghívó technikai profilra korlátozódik. és egyéb validálási technikai profilok ugyanazon műszaki profil alapján. Ha a következő vezénylési lépésben szeretné használni a kimeneti jogcímeket, hozzá kell adnia a kimeneti jogcímeket a technikai profilhoz, amely meghívja az érvényesítési technikai profilt.
1. **Kimeneti jogcímek** – A követelések visszakerülnek a jogcímcsomagba. Ezeket a jogcímeket a következő vezénylési lépésben használhatja, vagy kimeneti jogcímátalakításokat.
1. **Kimeneti jogcímek átalakítása –** Input jogcímek minden kimeneti [jogcím átalakítása](claimstransformations.md) a jogcímcsomag. A technikai profil kimeneti jogcímek az előző lépésekből lehetnek bemeneti jogcímek egy kimeneti jogcímek átalakítása. A végrehajtás után a kimeneti jogcímek visszakerülnek a jogcímek táskájába. A kimeneti jogcímek átalakítása kimeneti jogcímek is lehet input jogcímek egy későbbi kimeneti jogcímek átalakítása.
1. **Egyszeri bejelentkezés (SSO) munkamenet-kezelés** – A technikai profil adatainak megőrzése a munkamenetben az [Egyszeri bejelentkezés munkamenet-kezelése](custom-policy-reference-sso.md)használatával.


## <a name="technical-profile-inclusion"></a>Technikai profil felvétele

A technikai profil tartalmazhat egy másik technikai profilt a beállítások módosításához vagy új funkciók hozzáadásához.  Az `IncludeTechnicalProfile` elem arra az alap műszaki profilra való hivatkozás, amelyből a műszaki profil származik. A szintek száma nincs korlátozva.

Például az **AAD-UserReadUsingAlternativeSecurityId-NoError** technikai profil tartalmazza az **AAD-UserReadUsingAlternativeSecurityId.** Ez a technikai `RaiseErrorIfClaimsPrincipalDoesNotExist` profil a `true`metaadat-elemet a beállítására állítja, és hibát jelez, ha nem létezik közösségi fiók a címtárban. **Az AAD-UserReadUsingAlternativeSecurityId-NoError** felülbírálja ezt a viselkedést, és letiltja ezt a hibaüzenetet.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** tartalmazza `AAD-Common` a technikai profilt.

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

Mind **az AAD-UserReadUsingAlternativeSecurityId-NoError** és **az AAD-UserReadUsingAlternativeSecurityId** nem adja meg a szükséges **protokollelemet,** mert az az **AAD-Common** technikai profilban van megadva.

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

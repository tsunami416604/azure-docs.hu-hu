---
title: Egyszeri bejelentkezési munkamenet-kezelés egyéni házirendek használatával
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan kezelheti az SSO-munkameneteket az Azure AD B2C egyéni szabályzatai használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 80cf0d101a29de7fca9d4dd36e188a500d35e290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246031"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Egyszeri bejelentkezési munkamenet ek kezelése az Azure Active Directory B2C-ben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Egyszeri bejelentkezés (SSO) munkamenet-kezelés az Azure Active Directory B2C (Azure AD B2C) lehetővé teszi a rendszergazda számára, hogy ellenőrizzék a felhasználóval való interakciót, miután a felhasználó már hitelesítette. A rendszergazda szabályozhatja például, hogy megjelenjen-e az identitásszolgáltatók kiválasztása, vagy hogy újra meg kell-e adni a helyi fiók adatait. Ez a cikk ismerteti, hogyan konfigurálhatja az SSO-beállítások at Azure AD B2C.

Az SSO munkamenet-kezelés két részből áll. Az első a felhasználó és az Azure AD B2C közvetlen interakcióival, a másik pedig a külső felekkel, például a Facebookkal folytatott interakciókkal foglalkozik. Az Azure AD B2C nem bírálja felül vagy nem kerüli meg a külső felek által megtartott SSO-munkameneteket. Inkább az Azure AD B2C-n keresztül a külső félhez való eljutáshoz vezető útvonal "megemlékezik", így nem kell újra kérni a felhasználót a közösségi vagy vállalati identitásszolgáltató kiválasztására. A végső SSO-döntés a külső félnél marad.

Az SSO-munkamenet-kezelés ugyanazt a szemantikát használja, mint bármely más technikai profil az egyéni házirendekben. Vezénylési lépés végrehajtásakor a lépéshez társított technikai `UseTechnicalProfileForSessionManagement` profil lekérdezése egy hivatkozást. Ha van ilyen, a hivatkozott Egyszeri szolgáltató munkamenet-szolgáltatója ellenőrzi, hogy a felhasználó munkamenet-résztvevő-e. Ha igen, az SSO-munkamenet-szolgáltató a munkamenet újrafeltöltésére szolgál. Hasonlóképpen, ha egy vezénylési lépés végrehajtása befejeződött, a szolgáltató a munkamenetben tárolt információk tárolására szolgál, ha egy SSO-munkamenet-szolgáltató meg van adva.

Az Azure AD B2C számos használható SSO-munkamenet-szolgáltatót határozott meg:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

Az sso felügyeleti osztályok egy `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` technikai profil elemével vannak meghatározva.

## <a name="input-claims"></a>Bemeneti jogcímek

Az `InputClaims` elem üres vagy hiányzik.

## <a name="persisted-claims"></a>Tartós követelések

A munkamenetben tárolni kell a munkamenetben, vagy ki kell egészíteni a felhasználó profiljából a címtárban olvasott jogcímeket, amelyeket vissza kell küldeni az alkalmazásnak, vagy a következő lépések előfeltételei által használva kell tárolni. A megőrzött jogcímek használata biztosítja, hogy a hitelesítési utak nem sikertelen a hiányzó jogcímek. Jogcímek hozzáadásához használja a `<PersistedClaims>` technikai profil elemét. Ha a szolgáltató a munkamenet újrafeltöltésére szolgál, a megőrzött jogcímek hozzáadódnak a jogcímcsomaghoz.

## <a name="output-claims"></a>Kimeneti jogcímek

A `<OutputClaims>` jogcímek munkamenetből történő beolvasására szolgál.

## <a name="session-providers"></a>Munkamenet-szolgáltatók

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Ahogy a neve diktálja, ez a szolgáltató nem csinál semmit. Ez a szolgáltató egy adott technikai profil sso viselkedésének letiltására használható. Az `SM-Noop` [egyéni házirend kezdőcsomagja](custom-policy-get-started.md#custom-policy-starter-pack)a következő technikai profilt tartalmazza.

```XML
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Ez a szolgáltató jogcímek munkamenetben történő tárolására használható. Erre a szolgáltatóra általában egy helyi fiókok kezelésére használt technikai profil hivatkozik. Az `SM-AAD` [egyéni házirend kezdőcsomagja](custom-policy-get-started.md#custom-policy-starter-pack)a következő technikai profilt tartalmazza.

```XML
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="signInName" />
    <PersistedClaim ClaimTypeReferenceId="authenticationSource" />
    <PersistedClaim ClaimTypeReferenceId="identityProvider" />
    <PersistedClaim ClaimTypeReferenceId="newUser" />
    <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

Az `SM-MFA` [egyéni házirend kezdőcsomagja](custom-policy-get-started.md#custom-policy-starter-pack) `SocialAndLocalAccountsWithMfa`a következő technikai profilt tartalmazza. Ez a technikai profil kezeli a többtényezős hitelesítési munkamenetet.

```XML
<TechnicalProfile Id="SM-MFA">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isActiveMFASession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Ez a szolgáltató az "identitásszolgáltató kiválasztása" képernyő letiltására szolgál. Ez általában hivatkozik egy külső identitásszolgáltatóhoz konfigurált technikai profilban, például a Facebookon. Az `SM-SocialLogin` [egyéni házirend kezdőcsomagja](custom-policy-get-started.md#custom-policy-starter-pack)a következő technikai profilt tartalmazza.

```XML
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="AlwaysFetchClaimsFromProvider">true</Item>
  </Metadata>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="AlternativeSecurityId" />
  </PersistedClaims>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás|
| --- | --- | --- |
| AlwaysFetchClaimsFromProvider | Nem | Jelenleg nem használt, figyelmen kívül hagyható. |

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Ez a szolgáltató az Azure AD B2C SAML-munkamenetek kezelésére szolgál egy függő entitás alkalmazás vagy egy összevont SAML identitásszolgáltató között. Ha az SSO-szolgáltatót saml identitásszolgáltató munkamenet tárolására használja, a `RegisterServiceProviders` beállításának a . `false` Az `SM-Saml-idp` [SAML technikai profilja](saml-technical-profile.md)a következő technikai profilt használja.

```XML
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

Ha a szolgáltatót a B2C SAML munkamenet `RegisterServiceProviders` tárolására `true`használja, a kell állítania a . SAML munkamenet kijelentkezés szükséges a `SessionIndex` és `NameID` a teljes.

A `SM-Saml-idp` [saml kibocsátó technikai profilja](saml-issuer-technical-profile.md) a következő technikai profilt használja

```XML
<TechnicalProfile Id="SM-Saml-sp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```
#### <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás|
| --- | --- | --- |
| IncludeSessionIndex | Nem | Jelenleg nem használt, figyelmen kívül hagyható.|
| Szolgáltatói regisztráció | Nem | Azt jelzi, hogy a szolgáltatónak regisztrálnia kell az összes olyan SAML-szolgáltatót, amely nek jogcímeket adott ki. Lehetséges értékek: `true` (alapértelmezett) vagy `false`.|




---
title: Egyszeri bejelentkezés munkamenet-kezelés egyéni szabályzatok használatával
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan kezelheti az egyszeri bejelentkezéses munkameneteket a Azure AD B2C egyéni házirendjeivel.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a64af5d2b19b05ec9a5eda97c43e278cdfb8b4ff
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189106"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Egyszeri bejelentkezés munkamenet-kezelés a Azure Active Directory B2C-ben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az egyszeri bejelentkezés (SSO) munkamenet-kezelője Azure Active Directory B2C (Azure AD B2C) lehetővé teszi a rendszergazda számára, hogy a felhasználó már hitelesített hitelesítése után vezérelje a felhasználókkal való interakciót. A rendszergazda például megadhatja, hogy megjelenjenek-e az identitás-szolgáltatók, vagy hogy meg kell-e adni a helyi fiók adatait. Ez a cikk a Azure AD B2C egyszeri bejelentkezéses beállításainak konfigurálását ismerteti.

Az egyszeri bejelentkezéses munkamenetek kezelése két részből áll. Az első a felhasználó interakcióit a Azure AD B2C és a másikkal együtt a külső felekkel, például a Facebooktal folytatott interakciókkal foglalkozik. Azure AD B2C nem bírál felül vagy mellőzi az SSO-munkameneteket, amelyeket külső felek is tárolhatnak. Ahelyett, hogy a külső fél felé irányuló, Azure AD B2C útvonalon áthaladó útvonalat "megjegyezték", a felhasználónak nem kell újrakérnie a felhasználót, hogy válassza ki a közösségi vagy vállalati identitás-szolgáltatót. A végső SSO-döntés a külső fél számára is fennáll.

Az egyszeri bejelentkezéses munkamenet-kezelés ugyanazt a szemantikai kapcsolatot használja, mint bármely más technikai profil az egyéni házirendekben. Egy előkészítési lépés végrehajtásakor a lépéshez társított technikai profil lekérdezése `UseTechnicalProfileForSessionManagement`. Ha van ilyen, a rendszer ellenőrzi a hivatkozott SSO munkamenet-szolgáltatót, hogy a felhasználó munkamenet-résztvevő-e. Ha igen, az SSO-munkamenet-szolgáltató a munkamenet újrafeltöltésére szolgál. Hasonlóképpen, ha egy előkészítési lépés végrehajtása befejeződött, a szolgáltató a munkamenetben lévő információk tárolására szolgál, ha meg van adva egy egyszeri bejelentkezéses munkamenet-szolgáltató.

Azure AD B2C több SSO munkamenet-szolgáltatót definiált:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

Az egyszeri bejelentkezéses felügyeleti osztályok a technikai profil `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` eleme alapján vannak megadva.

## <a name="input-claims"></a>Bemeneti jogcímek

A `InputClaims` elem üres vagy hiányzik.

## <a name="persisted-claims"></a>Megőrzött jogcímek

Azokat a jogcímeket, amelyeket vissza kell adni az alkalmazásnak, vagy a későbbi lépésekben az előfeltételeket kell használni, a munkamenetben kell tárolni, vagy a felhasználó profiljából kell kibővíteni a címtárban. A megőrzött jogcímek használata biztosítja, hogy a hitelesítési útvonalak ne legyenek sikertelenek a hiányzó jogcímek esetében. Ha jogcímeket szeretne felvenni a munkamenetbe, használja a technikai profil `<PersistedClaims>` elemét. Ha a szolgáltató a munkamenet újrafeltöltésére szolgál, a rendszer hozzáadja a megőrzött jogcímeket a jogcímek táskához.

## <a name="output-claims"></a>Kimeneti jogcímek

A `<OutputClaims>` a jogcímek munkamenetből való beolvasására szolgál.

## <a name="session-providers"></a>Munkamenet-szolgáltatók

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Ahogy a név diktálja, a szolgáltató nem tesz semmit. Ez a szolgáltató használható egy adott technikai profil SSO-viselkedésének letiltására. A következő `SM-Noop` technikai profilt tartalmazza az [egyéni házirend-indító csomag](custom-policy-get-started.md#custom-policy-starter-pack).

```XML
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Ezt a szolgáltatót a jogcímek egy munkamenetben való tárolására lehet használni. Ez a szolgáltató általában a helyi fiókok kezeléséhez használt technikai profilban hivatkozik. A következő `SM-AAD` technikai profilt tartalmazza az [egyéni házirend-indító csomag](custom-policy-get-started.md#custom-policy-starter-pack).

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

A következő `SM-MFA` technikai profilt tartalmazza az [egyéni házirend-indító csomag](custom-policy-get-started.md#custom-policy-starter-pack) `SocialAndLocalAccountsWithMfa`. Ez a technikai profil a multi-Factor Authentication munkamenetet kezeli.

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

Ez a szolgáltató a "személyazonossági szolgáltató választása" képernyő letiltására szolgál. Általában egy külső identitás-szolgáltatóhoz, például a Facebookhoz konfigurált technikai profil hivatkozik rá. A következő `SM-SocialLogin` technikai profilt tartalmazza az [egyéni házirend-indító csomag](custom-policy-get-started.md#custom-policy-starter-pack).

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

| Attribútum | Szükséges | Leírás|
| --- | --- | --- |
| AlwaysFetchClaimsFromProvider | Nem | Jelenleg nincs használatban, figyelmen kívül hagyható. |

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Ez a szolgáltató az Azure AD B2C SAML-munkamenetek felügyeletére szolgál a függő entitás alkalmazása vagy egy összevont SAML-szolgáltató között. Ha az SSO-szolgáltatót használja az SAML-azonosító szolgáltatói munkamenetének tárolására, a `IncludeSessionIndex` és a `RegisterServiceProviders` `false`ra kell beállítani. Az [SAML technikai profil](saml-technical-profile.md)a következő `SM-Saml-idp` technikai profilt használja.

```XML
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IncludeSessionIndex">false</Item>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

Ha a B2C SAML-munkamenet tárolására szolgáltatót használ, a `IncludeSessionIndex`nak és a `RegisterServiceProviders`nak `true`értékre kell állítania. Az SAML-munkamenet kijelentkezéséhez szükség van a `SessionIndex`ra, és `NameID` a befejezéshez.

Az [SAML-kiállító technikai profilja](connect-with-saml-service-providers.md) a következő `SM-Saml-idp` technikai profilt használja

```XML
<TechnicalProfile Id="SM-Saml-sp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```
#### <a name="metadata"></a>Metaadatok

| Attribútum | Szükséges | Leírás|
| --- | --- | --- |
| IncludeSessionIndex | Nem | Azt jelzi, hogy a szolgáltatónak tárolnia kell a munkamenet-indexet. Lehetséges értékek: `true` (alapértelmezett) vagy `false`.|
| RegisterServiceProviders | Nem | Azt jelzi, hogy a szolgáltatónak regisztrálnia kell az összes olyan SAML-szolgáltatót, amely kiállított egy állítást. Lehetséges értékek: `true` (alapértelmezett) vagy `false`.|




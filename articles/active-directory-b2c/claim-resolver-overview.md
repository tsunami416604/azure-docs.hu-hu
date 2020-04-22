---
title: Igénylés-feloldók egyéni házirendekben
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan használhatja a jogcímfeloldókat egy egyéni szabályzatban az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0bdede482b79c82e6e05b1429cb7c17399bc2277
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756612"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Jogcímfeloldók az Azure Active Directory B2C egyéni szabályzataiban

Az Azure Active Directory B2C (Azure AD B2C) [egyéni szabályzatai](custom-policy-overview.md) ban a jogcímoldók környezeti információkat nyújtanak egy engedélyezési kérelemről, például a szabályzat nevéről, a kérelemkorrelációs azonosítójáról, a felhasználói felület nyelvéről és egyebekről.

Jogcímfeloldó bemeneti vagy kimeneti jogcímben való használatához definiáljon egy **karakterlánc-jogcímet**a [ClaimsSchema](claimsschema.md) elem alatt, majd állítsa be a **DefaultValue értéket** a jogcímfeloldóhoz a bemeneti vagy kimeneti jogcímösszetevőben. Az Azure AD B2C beolvassa a jogcímfeloldó értékét, és a technikai profilban használja az értéket.

A következő példában egy `correlationId` megnevezett jogcímtípus van `string`definiálva a **DataType (Adattípus)** segítségével.

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

A technikai profilban rendelje hozzá a jogcímfeloldót a jogcímtípushoz. Az Azure AD B2C feltölti a jogcímfeloldó `{Context:CorrelationId}` értékét a jogcímcím, `correlationId` és elküldi a jogcímet a technikai profilba.

```XML
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Jogcímfeloldó típusai

A következő szakaszok felsorolják az elérhető jogcímfeloldókat.

### <a name="culture"></a>Kulturális környezet

| Jogcím | Leírás | Példa |
| ----- | ----------- | --------|
| {Kulturális:LanguageName} | A nyelv kétbetűs ISO-kódja. | hu |
| {Kulturális:LCID}   | A nyelvkód LCID-je. | 1038 |
| {Kultúra:RegionName} | A régió kétbetűs ISO-kódja. | USA |
| {Kultúra:RFC5646} | Az RFC5646 nyelvkód. | en-US |

### <a name="policy"></a>Szabályzat

| Jogcím | Leírás | Példa |
| ----- | ----------- | --------|
| {Házirend:Házirendazonosító} | A függő entitás házirendjének neve. | B2C_1A_signup_signin |
| {Házirend:RelyingPartyTenantId} | A függő entitás házirendjének bérlői azonosítója. | your-tenant.onmicrosoft.com |
| {Házirend:TenantObjectId} | A függő entitásházirend bérlői objektumazonosítója. | 00000000-0000-0000-0000-000000000000 |
| {Házirend:TrustFrameworkTenantId} | A megbízhatósági keretrendszer bérlői azonosítója. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Jogcím | Leírás | Példa |
| ----- | ----------- | --------|
| {OIDC:AuthenticationContextReferences} |A `acr_values` lekérdezési karakterlánc paraméter. | N/A |
| {OIDC:Ügyfélazonosító} |A `client_id` lekérdezési karakterlánc paraméter. | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |A `domain_hint` lekérdezési karakterlánc paraméter. | facebook.com |
| {OIDC:LoginHint} |  A `login_hint` lekérdezési karakterlánc paraméter. | someone@contoso.com |
| {OIDC:MaxAge} | A `max_age`. | N/A |
| {OIDC:Nonce} |A `Nonce` lekérdezési karakterlánc paraméter. | defaultNonce |
| {OIDC:Jelszó}| Az erőforrás-tulajdonos i jelszó hitelesítő adatai a felhasználó jelszavát [áramtalanítók.](ropc-custom.md)| jelszó1| 
| {OIDC:Kérdés} | A `prompt` lekérdezési karakterlánc paraméter. | bejelentkezés |
| {OIDC:RedirectUri} |A `redirect_uri` lekérdezési karakterlánc paraméter. | https://jwt.ms |
| {OIDC:Erőforrás} |A `resource` lekérdezési karakterlánc paraméter. | N/A |
| {OIDC:Hatókör} |A `scope` lekérdezési karakterlánc paraméter. | Openid |
| {OIDC:Felhasználónév}| Az [erőforrás-tulajdonos jelszóhitelesítő adatai a](ropc-custom.md) felhasználó felhasználónevét áramtalanítódulják.| emily@contoso.com| 

### <a name="context"></a>Környezet

| Jogcím | Leírás | Példa |
| ----- | ----------- | --------|
| {Context:BuildNumber} | Az Identitáskezelési keretrendszer verziója (buildszáma).  | 1.0.507.0 |
| {Context:CorrelationId} | A korrelációs azonosító.  | 00000000-0000-0000-0000-000000000000 |
| {Környezet:DateTimeInUtc} |A dátum időpontja UTC-ben.  | 2018.10.10. 12:00:00 |
| {Context:DeploymentMode} |A házirend telepítési módja.  | Production |
| {Környezet:IPAddress} | A felhasználó IP-címe. | 11.111.111.11 |
| {Környezet:KMSI} | Azt jelzi, hogy [a Be jelentkezve maradjon](custom-policy-keep-me-signed-in.md) jelölőnégyzet be jelölve. |  igaz |

### <a name="claims"></a>Jogcímek 

| Jogcím | Leírás | Példa |
| ----- | ----------- | --------|
| {Jogcím:jogcím típusa} | A házirendfájl vagy a szülőházirend-fájl ClaimsSchema szakaszában már definiált jogcímtípus azonosítója.  Például: `{Claim:displayName}`vagy `{Claim:objectId}`. | Jogcímtípus értéke.|


### <a name="oauth2-key-value-parameters"></a>OAuth2 kulcs-érték paraméterek

Az OIDC vagy OAuth2 kérelem részeként szereplő bármely paraméternév leképezhető egy jogcímhez a felhasználói út során. Az alkalmazástól érkező kérelem tartalmazhat például egy lekérdezési `app_session` `loyalty_number`karakterlánc-paramétert , vagy bármely egyéni lekérdezési karakterláncot.

| Jogcím | Leírás | Példa |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | Lekérdezési karakterlánc paraméter. | Hawaii |
| {OAUTH-KV:app_session} | Lekérdezési karakterlánc paraméter. | A3C5R |
| {OAUTH-KV:loyalty_number} | Lekérdezési karakterlánc paraméter. | 1234 |
| {OAUTH-KV:bármely egyéni lekérdezési karakterlánc} | Lekérdezési karakterlánc paraméter. | N/A |

### <a name="oauth2"></a>OAuth2

| Jogcím | Leírás | Példa |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | A hozzáférési jogkivonat. | N/A |


### <a name="saml"></a>SAML

| Jogcím | Leírás | Példa |
| ----- | ----------- | --------|
| {SAML:AuthnContextClassReferences} | Az `AuthnContextClassRef` elem értéke az SAML-kérelemből. | urn:oázis:nevek:tc:SAML:2.0:ac:classes:PasswordProtectedTransport |
| {SAML:NameIdPolicyFormat} | Az `Format` attribútum, az `NameIDPolicy` SAML-kérelem eleméből. | urn:oázis:nevek:tc:SAML:1.1:nameid-format:emailAddress |
| {SAML:Kiállító} |  Az SAML-kérelem SAML `Issuer` elemértéke.| `https://contoso.com` |
| {SAML:AllowCreate} | Az `AllowCreate` attribútum értéke az `NameIDPolicy` SAML-kérelem eleméből. | True (Igaz) |
| {SAML:ForceAuthn} | Az `ForceAuthN` attribútum értéke az `AuthnRequest` SAML-kérelem eleméből. | True (Igaz) |
| {SAML:Szolgáltatónév} | Az `ProviderName` attribútum értéke az `AuthnRequest` SAML-kérelem eleméből.| Contoso.com |
| {SAML:RelayState} | A `RelayState` lekérdezési karakterlánc paraméter.| 

## <a name="using-claim-resolvers"></a>Jogcímfeloldók használata

A jogcímfeloldókat a következő elemekkel használhatja:

| Elem | Elem | Beállítások |
| ----- | ----------------------- | --------|
|Az Application Insights technikai profilja |`InputClaim` | |
|[Az Azure Active Directory](active-directory-technical-profile.md) technikai profilja| `InputClaim`, `OutputClaim`| 1, 2|
|[OAuth2](oauth2-technical-profile.md) technikai profil| `InputClaim`, `OutputClaim`| 1, 2|
|[OpenID Connect](openid-connect-technical-profile.md) technikai profil| `InputClaim`, `OutputClaim`| 1, 2|
|[Jogcímek átalakítása](claims-transformation-technical-profile.md) technikai profil| `InputClaim`, `OutputClaim`| 1, 2|
|[RESTful szolgáltató](restful-technical-profile.md) technikai profilja| `InputClaim`| 1, 2|
|[SAML2](saml-technical-profile.md) technikai profil| `OutputClaim`| 1, 2|
|[Önérvényesítő](self-asserted-technical-profile.md) technikai profil| `InputClaim`, `OutputClaim`| 1, 2|
|[Tartalomdefiníció](contentdefinitions.md)| `LoadUri`| |
|[ContentDefinitionParameters (ContentDefinitionParameters)](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|[RelyingParty](relyingparty.md#technicalprofile) technikai profil| `OutputClaim`| 2 |

Beállítások:
1. A `IncludeClaimResolvingInClaimsHandling` metaadatokat a `true`ikonra kell állítani.
1. A bemeneti vagy `AlwaysUseDefaultValue` kimeneti jogcímek attribútumot a -ra `true`kell állítani.

## <a name="claim-resolvers-samples"></a>Jogcímfeloldók mintái

### <a name="restful-technical-profile"></a>RESTful műszaki profil

A [RESTful](restful-technical-profile.md) technikai profilban érdemes lehet elküldeni a felhasználói nyelvet, a házirend nevét, a hatókört és az ügyfélazonosítót. A jogcímek alapján a REST API futtathatja az egyéni üzleti logikát, és szükség esetén feltud vetni egy honosított hibaüzenetet.

A következő példa egy RESTful technikai profilt mutat be ezzel a forgatókönyvvel:

```XML
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:Scope}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" AlwaysUseDefaultValue="true" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Közvetlen bejelentkezés

A jogcímfeloldók használatával előre feltöltheti a bejelentkezési nevet, vagy közvetlen bejelentkezést adhat egy adott közösségi identitásszolgáltatónak, például a Facebooknak, a LinkedInnek vagy egy Microsoft-fióknak. További információ: [Közvetlen bejelentkezés beállítása az Azure Active Directory B2C használatával](direct-signin.md)című témakörben talál.

### <a name="dynamic-ui-customization"></a>A felhasználói felület dinamikus testreszabása

Az Azure AD B2C lehetővé teszi, hogy lekérdezési karakterlánc-paramétereket a HTML-tartalom definíciós végpontok dinamikusan megjeleníteni az oldal tartalmát. Ez a funkció például lehetővé teszi, hogy módosítsa a háttérképet az Azure AD B2C regisztrációs vagy bejelentkezési oldalon egy egyéni paraméter, amely átad a webről vagy mobilalkalmazásból. További információ: [Dinamikusan konfigurálja a felhasználói felületet az Azure Active Directory B2C egyéni szabályzatai használatával.](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri) A HTML-lapot nyelvi paraméter alapján is honosíthatja, vagy módosíthatja a tartalmat az ügyfélazonosító alapján.

A következő példa a **campaignId** nevű lekérdezési `Hawaii`karakterlánc-paraméterben halad át , a nyelvkódéssal **language** `en-US`és az ügyfélazonosítót képviselő **alkalmazással:**

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

Ennek eredményeképpen az Azure AD B2C elküldi a fenti paramétereket a HTML-tartalomlapra:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="content-definition"></a>Tartalom definíciója

A [ContentDefinition](contentdefinitions.md) `LoadUri`alkalmazásban a használt paraméterek alapján jogcímfeloldókat küldhet a különböző helyekről származó tartalom lekérése céljából.

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

### <a name="application-insights-technical-profile"></a>Az Application Insights technikai profilja

Az Azure Application Insights és a jogcímfeloldók segítségével betekintést nyerhet a felhasználók viselkedéséről. Az Application Insights technikai profilban olyan bemeneti jogcímeket küld, amelyek megmaradnak az Azure Application Insights számára. További információ: [A felhasználói viselkedés nyomon követése az Azure AD B2C-utazások ban az Application Insights használatával.](analytics-with-application-insights.md) A következő példa elküldi a szabályzatazonosítót, a korrelációs azonosítót, a nyelvet és az ügyfélazonosítót az Azure Application Insightsnak.

```XML
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Alternate Email</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
    <InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
    <InputClaim ClaimTypeReferenceId="AppId" PartnerClaimType="{property:App}" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
</TechnicalProfile>
```

### <a name="relying-party-policy"></a>A függő entitás irányelvei

A [függő entitás](relyingparty.md) technikai profilban érdemes lehet a bérlői azonosítót vagy a korrelációs azonosítót a függő entitás alkalmazásának a JWT-n belül küldeni.

```XML
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
        <OutputClaim ClaimTypeReferenceId="correlationId" AlwaysUseDefaultValue="true" DefaultValue="{Context:CorrelationId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
```

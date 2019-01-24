---
title: Az Azure Active Directory B2C-vel egyéni szabályzatok feloldók készül jogcím |} A Microsoft Docs
description: Ismerje meg a jogcímek feloldók az Azure Active Directory B2C egy egyéni szabályzat használata.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/08/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6b7f3dc79e3b4c06b2b974e0cdca0bf20221c3ad
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845010"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Tudnivalók az Azure Active Directory B2C-vel egyéni szabályzatok jogcím feloldók

Az Azure Active Directory (Azure AD) B2C feloldók jogcím [egyéni szabályzatok](active-directory-b2c-overview-custom.md) egy engedélyezési kérést, például a szabályzat nevét, kérelem korrelációs azonosítója, a felhasználói felület nyelvének és további környezeti ismertetik.

Szeretne használni a jogcímszabály-feloldó egy bemeneti vagy kimeneti jogcím, megadhat egy karakterláncot **takar**alatt a [ClaimsSchema](claimsschema.md) elemet, és ezt állítsa be a **DefaultValue** a jogcímek a bemeneti vagy kimeneti feloldó jogcím elemet. Az Azure AD B2C beolvassa a jogcím-feloldó értékét, és a technikai profilban értéket használja. 

A következő példában egy jogcímtípust nevű `correlationId` van definiálva egy **DataType** , `string`.  

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

A jogcím típusa a jogcím-feloldó leképezése a technikai profilban. Az Azure AD B2C tölti fel a jogcím-feloldó értékét `{Context:CorrelationId}` az a jogcím `correlationId` , és elküldi a kérelmet a technikai profil.

```XML
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Feloldási jogcímtípusok

Az alábbi szakaszok tartalmazzák a rendelkezésre álló jogcímek feloldók.

### <a name="culture"></a>Kulturális környezet

| Jogcím | Leírás | Példa |
| ----- | ----------- | --------|
| {Kulturális környezet: LanguageName} | A kétbetűs ISO-kódja a nyelvhez. | hu |
| {Culture:LCID}   | A nyelvi kód LCID. | 1038 |
| {Kulturális környezet: RegionName} | A kétbetűs ISO-kódja a régióban. | USA |
| {Culture:RFC5646} | A RFC5646 nyelvi kódot. | en-US |

### <a name="policy"></a>Szabályzat

| Jogcím | Leírás | Példa |
| ----- | ----------- | --------|
| {Policy:PolicyId} | A függő entitás szabályzat neve. | B2C_1A_signup_signin |
| {Policy:RelyingPartyTenantId} | A függő entitás házirend Bérlőazonosítója. | your-tenant.onmicrosoft.com |
| {Policy:TenantObjectId} | A függő entitás házirend bérlői objektum azonosítója. | 00000000-0000-0000-0000-000000000000 |
| {Policy:TrustFrameworkTenantId} | A bizalmi keretrendszer Bérlőazonosítója. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Jogcím | Leírás | Példa |
| ----- | ----------- | --------|
| {OIDC:AuthenticationContextReferences} |A `acr_values` lekérdezési karakterlánc paraméter. | – |
| {OIDC:ClientId} |A `client_id` lekérdezési karakterlánc paraméter. | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |A `domain_hint` lekérdezési karakterlánc paraméter. | facebook.com |
| {OIDC:LoginHint} |  A `login_hint` lekérdezési karakterlánc paraméter. | someone@contoso.com |
| {OIDC:MaxAge} | A `max_age`. | – |
| {OIDC:Nonce} |A `Nonce` lekérdezési karakterlánc paraméter. | defaultNonce |
| {OIDC:Prompt} | A `prompt` lekérdezési karakterlánc paraméter. | bejelentkezés |
| {OIDC:Resource} |A `resource` lekérdezési karakterlánc paraméter. | – |
| {OIDC:scope} |A `scope` lekérdezési karakterlánc paraméter. | openid |

### <a name="context"></a>Környezet

| Jogcím | Leírás | Példa |
| ----- | ----------- | --------|
| {Környezet: BuildNumber} | Az identitás-kezelőfelületi keretrendszer verziója (build száma).  | 1.0.507.0 |
| {Context:CorrelationId} | A korrelációs azonosítót.  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeInUtc} |A dátum idő (UTC).  | 10-ES/10/2018. 12:00:00 PM |
| {Context:DeploymentMode} |A házirend üzembe helyezési mód.  | Production |
| {Context:IPAddress} | A felhasználói IP-cím. | 11.111.111.11 |


### <a name="non-protocol-parameters"></a>Nem-protokoll-paraméterek

A felhasználói interakciósorozatban szereplő jogcím bármely paraméternév egy OIDC vagy OAuth2-kérés részét is le lehet képezni. Például az alkalmazásból érkező kérelem tartalmazhat egy lekérdezési karakterlánc paramétereként nevű `app_session`, `loyalty_number`, vagy bármilyen egyéni lekérdezés-karakterlánc.

| Jogcím | Leírás | Példa |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | Lekérdezési karakterlánc paramétereként. | hawaii |
| {OAUTH-KV:app_session} | Lekérdezési karakterlánc paramétereként. | A3C5R |
| {OAUTH-KV:loyalty_number} | Lekérdezési karakterlánc paramétereként. | 1234 |
| {OAUTH-KV: minden olyan egyéni lekérdezési karakterlánc} | Lekérdezési karakterlánc paramétereként. | – |


## <a name="how-to-use-claim-resolvers"></a>Jogcím feloldók használata

### <a name="restful-technical-profile"></a>REST-alapú technikai profilban

Az egy [RESTful](restful-technical-profile.md) technikai profilban, előfordulhat, hogy szeretne küldeni a felhasználó nyelvét, a házirend nevét, a hatókör és a ügyfél-azonosítót. Ezeket a jogcímeket, a REST API egyéni üzleti logika futtathatja, és szükség esetén előléptetése honosított hibaüzenet alapján. 

Az alábbi példa bemutatja egy REST-alapú technikai profilban:

```XML
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:scope}" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Közvetlen bejelentkezés

Jogcím feloldók használja, megadhatja a bejelentkezési név vagy közvetlen jelentkezzen be egy adott közösségi identitásszolgáltató, például a Facebook, a LinkedIn, vagy a Microsoft-fiókkal. További információkért lásd: [állítsa be a közvetlen bejelentkezés az Azure Active Directory B2C használatával](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Felhasználói felület dinamikus testreszabása

Azure AD B2C lehetővé teszi, hogy adja át a lekérdezési karakterlánc paraméterei a HTML tartalomdefiníció-végpontokra, hogy dinamikusan jelennek meg az oldal tartalmát. Ha például a háttérkép, amelyeket átad a webes vagy mobilalkalmazás egyéni paraméterérték alapján az Azure AD B2C-vel regisztrálási vagy bejelentkezési oldalon módosíthatja. További információkért lásd: [dinamikus konfigurálásához a felhasználói felület az Azure Active Directory B2C-vel egyéni szabályzatok használatával](active-directory-b2c-ui-customization-custom-dynamic.md). A HTML-oldalt, a nyelv paraméter alapján honosítható, vagy módosíthatja a tartalom alapján az ügyfél-azonosítót.

Az alábbi példában a rendszer átadja a lekérdezési karakterlánc nevű paraméter **campaignId** értékkel `hawaii`, amely egy **nyelvi** kódját `en-US`, és **alkalmazás** az ügyfél-Azonosítóját jelölő:

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

Ennek eredményeképpen az Azure AD B2C-t a fenti paraméterek küldi el a HTML-tartalom lap:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="application-insights-technical-profile"></a>Application Insights-technikai profilban

Az Azure Application Insights és a jogcím feloldók mélyebb bepillantást nyerhet a felhasználói viselkedés. Az Application Insights technikai profilban küldeni az Azure Application Insights bemeneti jogcímek között, amelyek megmaradnak. További információkért lásd: [Application Insights segítségével nyomon követheti felhasználói viselkedés az Azure AD B2C Journey](active-directory-b2c-custom-guide-eventlogger-appins.md). Az alábbi példa a házirend-azonosító, korrelációs Azonosítót, nyelv és az ügyfél-Azonosítót küld az Azure Application Insights.

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

---
title: Igénylések feloldói az egyéni házirendekben
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan használhatók a jogcímek feloldói a Azure Active Directory B2C egyéni házirendjében.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e3a80628e5729813e1d405e58ecb623925b63076
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77193379"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Tudnivalók a jogcímek feloldóinak Azure Active Directory B2C egyéni házirendekben

A Azure Active Directory B2C (Azure AD B2C) [Egyéni szabályzatok](custom-policy-overview.md) által felismert feloldók az engedélyezési kérelemre vonatkozó környezeti információkat biztosítanak, például a szabályzat nevét, a kérelem KORRELÁCIÓs azonosítóját, a felhasználói felület nyelvét és egyebeket.

Ha a jogcím-feloldót egy bemeneti vagy kimeneti jogcímben szeretné használni, Definiáljon egy karakterlánc- **claimType**a [ClaimsSchema](claimsschema.md) elem alatt, majd állítsa be a **DefaultValue** -feloldót a bemeneti vagy kimeneti jogcím elemben. Azure AD B2C beolvassa a jogcím-feloldó értékét, és a technikai profilban szereplő értéket használja.

A következő példában egy `correlationId` nevű jogcím-típus `string`**adattípussal** van definiálva.

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

A technikai profilban rendelje hozzá a jogcím feloldóját a jogcím típusához. Azure AD B2C feltölti a jogcím-feloldó értékét `{Context:CorrelationId}` a jogcím `correlationId`, és elküldi a jogcímet a technikai profilnak.

```XML
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Jogcím-feloldási típusok

A következő fejezetei az elérhető jogcímek feloldóit sorolja fel.

### <a name="culture"></a>Kulturális környezet

| Jogcím | Leírás | Példa |
| ----- | ----------- | --------|
| {Kulturális környezet: LanguageName} | A nyelv két betűs ISO-kódja. | hu |
| {Kulturális környezet: LCID}   | A nyelvi kód LCID-je. | 1033 |
| {Kulturális környezet: RegionName} | A régió két betűs ISO-kódja. | USA |
| {Kulturális környezet: RFC5646} | A RFC5646 nyelvének kódja | hu-HU |

### <a name="policy"></a>Szabályzat

| Jogcím | Leírás | Példa |
| ----- | ----------- | --------|
| {Policy:PolicyId} | A függő entitás házirendjének neve. | B2C_1A_signup_signin |
| {Policy:RelyingPartyTenantId} | A függő entitás házirendjének bérlői azonosítója. | your-tenant.onmicrosoft.com |
| {Policy:TenantObjectId} | A függő entitás házirendjének bérlői objektumának azonosítója. | 00000000-0000-0000-0000-000000000000 |
| {Policy:TrustFrameworkTenantId} | A megbízhatósági keretrendszer bérlői azonosítója. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Jogcím | Leírás | Példa |
| ----- | ----------- | --------|
| {OIDC: AuthenticationContextReferences} |A `acr_values` lekérdezési karakterlánc paraméter. | N/A |
| {OIDC:ClientId} |A `client_id` lekérdezési karakterlánc paraméter. | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |A `domain_hint` lekérdezési karakterlánc paraméter. | facebook.com |
| {OIDC:LoginHint} |  A `login_hint` lekérdezési karakterlánc paraméter. | someone@contoso.com |
| {OIDC:MaxAge} | A `max_age`. | N/A |
| {OIDC:Nonce} |A `Nonce` lekérdezési karakterlánc paraméter. | defaultNonce |
| {OIDC:Prompt} | A `prompt` lekérdezési karakterlánc paraméter. | bejelentkezés |
| {OIDC: erőforrás} |A `resource` lekérdezési karakterlánc paraméter. | N/A |
| {OIDC: hatókör} |A `scope` lekérdezési karakterlánc paraméter. | OpenID |

### <a name="context"></a>Környezet

| Jogcím | Leírás | Példa |
| ----- | ----------- | --------|
| {Context: BuildNumber} | Az Identity Experience Framework verziószáma (Build száma).  | 1.0.507.0 |
| {Context:CorrelationId} | A korrelációs azonosítót.  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeInUtc} |A dátum és idő (UTC).  | 10/10/2018 12:00:00 PM |
| {Context: Deploymentmode beállítása} |A házirend-telepítési mód.  | Production |
| {Context:IPAddress} | A felhasználó IP-címe. | 11.111.111.11 |


### <a name="non-protocol-parameters"></a>Nem protokoll paraméterek

Egy OIDC-vagy OAuth2-kérelem részeként megadott paraméter neve a felhasználói úton lévő jogcímek számára képezhető le. Előfordulhat például, hogy az alkalmazástól érkező kérelem tartalmaz egy lekérdezési karakterlánc paramétert `app_session`, `loyalty_number`vagy bármely egyéni lekérdezési karakterlánc nevével.

| Jogcím | Leírás | Példa |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | Egy lekérdezési karakterlánc paraméter. | Hawaii |
| {OAUTH-KV:app_session} | Egy lekérdezési karakterlánc paraméter. | A3C5R |
| {OAUTH-KV:loyalty_number} | Egy lekérdezési karakterlánc paraméter. | 1234 |
| {OAUTH-KV: bármely egyéni lekérdezési karakterlánc} | Egy lekérdezési karakterlánc paraméter. | N/A |

### <a name="oauth2"></a>OAuth2

| Jogcím | Leírás | Példa |
| ----- | ----------------------- | --------|
| {oauth2: access_token} | A hozzáférési jogkivonat. | N/A |

## <a name="using-claim-resolvers"></a>Jogcímek feloldóinak használata 

A jogcímek feloldóit a következő elemekkel használhatja: 

| Elem | Elem | Beállítások |
| ----- | ----------------------- | --------|
|Application Insights műszaki profil |`InputClaim` | |
|[Azure Active Directory](active-directory-technical-profile.md) műszaki profil| `InputClaim`, `OutputClaim`| 1, 2|
|[OAuth2](oauth2-technical-profile.md) műszaki profil| `InputClaim`, `OutputClaim`| 1, 2|
|[OpenID Connect](openid-connect-technical-profile.md) – technikai profil| `InputClaim`, `OutputClaim`| 1, 2|
|[Jogcím-átalakítási](claims-transformation-technical-profile.md) technikai profil| `InputClaim`, `OutputClaim`| 1, 2|
|[Rest-szolgáltató](restful-technical-profile.md) technikai profilja| `InputClaim`| 1, 2|
|[Egy saml2](saml-technical-profile.md) műszaki profil| `OutputClaim`| 1, 2|
|[Önérvényesített](self-asserted-technical-profile.md) technikai profil| `InputClaim`, `OutputClaim`| 1, 2|
|[ContentDefinition](contentdefinitions.md)| `LoadUri`| |
|[ContentDefinitionParameters](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|[RelyingParty](relyingparty.md#technicalprofile) műszaki profil| `OutputClaim`| 2 |

Beállítások 
1. A `IncludeClaimResolvingInClaimsHandling` metaadatokat a következőre kell beállítani `true`
1. A bemeneti vagy kimeneti jogcímek attribútumának `AlwaysUseDefaultValue` értéknek kell lennie `true`

## <a name="how-to-use-claim-resolvers"></a>A jogcím-feloldók használata

### <a name="restful-technical-profile"></a>REST-technikai profil

A [Rest](restful-technical-profile.md) -alapú műszaki profilokban érdemes lehet elküldeni a felhasználó nyelvét, a szabályzat nevét, a hatókört és az ügyfél-azonosítót. Ezen jogcímek alapján a REST API képes egyéni üzleti logikát futtatni, és ha szükséges, honosított hibaüzenetet kell létrehoznia.

Az alábbi példa egy REST-technikai profilt mutat be:

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

A jogcímek feloldóinak használatával előre feltöltheti a bejelentkezési nevet vagy a közvetlen bejelentkezést egy adott közösségi identitás-szolgáltatóba, például a Facebook, a LinkedIn vagy egy Microsoft-fiók. További információ: [közvetlen bejelentkezés beállítása Azure Active Directory B2C használatával](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Dinamikus felhasználói felület testreszabása

A Azure AD B2C lehetővé teszi a lekérdezési karakterlánc paramétereinek átadását a HTML-tartalom definíciós végpontjai számára, így dinamikusan megjelenítheti az oldal tartalmát. Megváltoztathatja például a háttérképet a Azure AD B2C regisztrációs vagy bejelentkezési oldalon a webes vagy mobil alkalmazásából származó egyéni paraméter alapján. További információ: [a felhasználói felület dinamikus konfigurálása egyéni házirendek használatával Azure Active Directory B2Cban](custom-policy-ui-customization-dynamic.md). A HTML-lapot nyelvi paraméter alapján is honosíthatja, vagy az ügyfél-azonosító alapján módosíthatja a tartalmat.

A következő példa egy **campaignId** nevű paramétert ad át a lekérdezési karakterláncban, amelynek értéke `hawaii`, a `en-US`**nyelvi** kódja és az ügyfél-azonosítót jelképező **alkalmazás** :

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

Ennek eredményeképpen a Azure AD B2C elküldi a fenti paramétereket a HTML-tartalom oldalának:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="application-insights-technical-profile"></a>Application Insights műszaki profil

Az Azure Application Insights és a jogcím-feloldók segítségével elemzéseket nyerhet a felhasználói viselkedésről. A Application Insights technikai profilban elküldheti azokat a bemeneti jogcímeket, amelyeket az Azure Application Insights tart fenn. További információ: a [felhasználói viselkedés nyomon követése Azure ad B2C-útvonalakon Application Insights használatával](analytics-with-application-insights.md). Az alábbi példa a házirend-azonosítót, a korrelációs azonosítót, a nyelvet és az ügyfél-azonosítót küldi az Azure Application Insightsnak.

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

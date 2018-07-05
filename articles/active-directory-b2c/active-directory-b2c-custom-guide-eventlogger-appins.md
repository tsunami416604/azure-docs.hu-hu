---
title: Események az Application Insights az Azure Active Directory B2C használatával a felhasználók viselkedésének nyomon követése |} A Microsoft Docs
description: Lépésenkénti útmutató, amellyel az eseménynaplókat az Application insights szolgáltatásban az Azure AD B2C felhasználói utak engedélyezése egyéni házirendekkel (előzetes verzió) használatával
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 04/16/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 300a8a15552fe1e8ec9d6b434a14a31b3d827f19
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445585"
---
# <a name="track-user-behavior-in-azure-ad-b2c-journeys-by-using-application-insights"></a>Application Insights segítségével nyomon követheti a felhasználói viselkedés az Azure AD B2C-vel Journey

Az Azure Active Directory B2C az Azure Application Insights jól működik (Azure AD B2C-vel). Azok az egyénileg létrehozott felhasználói utak részletes és testre szabott eseménynaplók adja meg. Ez a cikk bemutatja, hogyan kezdheti el, így:

* Felhasználói viselkedéssel kapcsolatos információkhoz.
* Hárítsa el a megfelelő saját házirendeket fejlesztési vagy éles környezetben.
* Teljesítmény mérésére.
* Értesítések létrehozása az Application Insights szolgáltatásból.

> [!NOTE]
> Ez a funkció előzetes verzióban érhető el.

## <a name="how-it-works"></a>Működés

Az identitás-kezelőfelületi keretrendszer Azure AD B2C-ben mostantól tartalmazza a szolgáltatót `Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0`.  A kialakítási kulcsot a megadott Azure AD B2C használatával közvetlenül az Application Insights küld eseményadatokat.

A technikai profil a szolgáltató használ egy esemény a B2C meghatározására.  A profil megadja az esemény lesz rögzítve a jogcímeket és a kialakítási kulcs neve.  Az esemény közzétételéhez majd hozzáadja a technikai profil egy `orchestration step` vagy mint egy `validation technical profile` egy egyéni felhasználói interakciósorozatban szereplő.

Az Application Insights is használja őket egységes előtérrendszerként az eseményeket rögzítse a felhasználói munkamenet egy korrelációs azonosító használatával. Az Application Insights elérhetővé teszi a események és munkamenetek másodpercen belül, és megadja a sok vizualizációt, exportálása és analitikai eszközök.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a [Ismerkedés az egyéni szabályzatok](active-directory-b2c-get-started-custom.md). Ez a cikk azt feltételezi, hogy az egyéni házirend alapszintű csomagot használ. De az alapszintű csomag nincs szükség.

## <a name="step-1-create-an-application-insights-resource-and-get-the-instrumentation-key"></a>1. lépés Hozzon létre egy Application Insights-erőforrást, és a kialakítási kulcs beszerzése

Az Application Insights az Azure AD B2C-vel használ, egyetlen követelménye esetén hozzon létre egy erőforrást, és a egy kialakítási kulcs beszerzése. Az erőforrás létrehozása a [Azure Portalon.](https://portal.azure.com)

1. Válassza ki az Azure Portalon, az előfizetés bérlőn belül **+ erőforrás létrehozása**. Ezen a bérlőn nem áll az Azure AD B2C-bérlőben.  
2. Keresse meg és válassza **Application Insights**.  
3. Hozzon létre egy erőforrást, amely használja **ASP.NET-alkalmazás** , **alkalmazástípus**, a beállításokat az előfizetéshez.
4. Miután létrehozta az Application Insights-erőforrást, nyissa meg és jegyezze fel a kialakítási kulcsot.

![Az Application Insights áttekintése és a kialakítási kulcsot](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-key.png)

## <a name="step-2-add-new-claimtype-definitions-to-your-trust-framework-extension-file"></a>2. lépés A bizalmi keretrendszer bővítményfájl hozzáadása új takar definíciók

Nyissa meg a bővítmény fájlt az alapszintű csomagból, és adja hozzá a következő elemeket a `<BuildingBlocks>` csomópont. A fájlnév formátuma általában `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

```xml
<ClaimsSchema>
  <ClaimType Id="EventType">
    <DisplayName>EventType</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="PolicyId">
    <DisplayName>PolicyId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="Culture">
    <DisplayName>Culture</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="CorrelationId">
    <DisplayName>CorrelationId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <!--Additional claims used for passing claims to Application Insights Provider -->
  <ClaimType Id="federatedUser">
    <DisplayName>federatedUser</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="parsedDomain">
    <DisplayName>Parsed Domain</DisplayName>
    <DataType>string</DataType>
    <UserHelpText>The domain portion of the email address.</UserHelpText>
  </ClaimType>
  <ClaimType Id="userInLocalDirectory">
    <DisplayName>userInLocalDirectory</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
</ClaimsSchema>
```

## <a name="step-3-add-new-technical-profiles-that-use-the-application-insights-provider"></a>3. lépés Adja hozzá az Application Insights-szolgáltató használó új technikai profilok

Az az identitás élmény keretrendszer az Azure AD B2C funkciók technikai profilok lehessen venni. Ebben a példában öt technikai profilok munkamenetet nyit meg, és az események közzététele határozza meg:

| Technikai profil | Tevékenység |
| ----------------- | -----|
| AzureInsights-Common | Létrehoz egy közös csoportot minden AzureInsights technikai profilban szereplő paraméterek | 
| JourneyContextForInsights | A munkamenet megnyitása az Application Insights és a egy korrelációs Azonosítót küld |
| AzureInsights-SignInRequest | Létrehoz egy `SignIn` jogcímeket, amikor egy bejelentkezési kérelmet kapott esemény | 
| AzureInsights-UserSignup | Amikor a felhasználó elindítja a regisztrációs lehetőség a regisztrálási-regisztrálási vagy bejelentkezési út UserSignup eseményt hoz létre | 
| AzureInsights-SignInComplete | A hitelesítés sikeres befejezése rögzíti, amikor a rendszer elküldte a jogkivonatot a függő gyártótól származó alkalmazás | 

A profilok hozzáadása a bővítményfájl az alapszintű csomag hozzáadásával ezeknek az elemeknek a `<ClaimsProviders>` csomópont.  A fájlnév formátuma általában `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

> [!IMPORTANT]
> A kialakítási kulcsot az módosítása a `ApplicationInsights-Common` technikai profil az Application Insights-erőforrást biztosító GUID.

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="JourneyContextForInsights">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="CorrelationId" />
      </OutputClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-UserSignup">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignup" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-Common">
      <DisplayName>Alternate Email</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <!-- A Boolean that indicates whether developer mode is enabled. This controls how events are buffered. In a development environment with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights. -->
        <Item Key="DeveloperMode">false</Item>
        <!-- A Boolean that indicates whether telemetry should be enabled or not. -->
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-4-add-the-technical-profiles-for-application-insights-as-orchestration-steps-in-an-existing-user-journey"></a>4. lépés A technikai profilok hozzáadása az Application Insights egy meglévő felhasználói interakciósorozatban szereplő lépéseket vezénylési

Hívás `JournyeContextForInsights` , orchestration 1. lépés:

```xml
<!-- Initialize a session with Application Insights -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="JourneyContextForInsights" TechnicalProfileReferenceId="JourneyContextForInsights" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Hívás `Azure-Insights-SignInRequest` vezénylési, 2. lépés: nyomon követésére, hogy a bejelentkezési-a vagy a regisztráláshoz kérelmet kapott:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Azonnal *előtt* a `SendClaims` vezénylési lépés, adjon hozzá egy új lépés, amely meghívja ezt `Azure-Insights-UserSignup`. Ez akkor aktiválódik, ha a felhasználó kiválaszt egy regisztrálási-regisztrálási vagy bejelentkezési utazás a regisztráció gombra.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="9" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
      <Value>newUser</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>newUser</Value>
      <Value>false</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
  </ClaimsExchanges>
```

Után azonnal a `SendClaims` vezénylési. lépés: a hívás `Azure-Insights-SignInComplete`. Ez a lépés sikeresen befejeződött az út tükrözi.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="11" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Miután hozzáadta az új vezénylési lépésekből, áthelyezni a lépések sorrendben anélkül, hogy a rendszer kihagyja bármely egész számok 1-től N. a


## <a name="step-5-upload-your-modified-extensions-file-run-the-policy-and-view-events-in-application-insights"></a>5. lépés Töltse fel a módosított bővítmények fájlt, a szabályzat futtatása és események megtekintése az Application insights szolgáltatásban

Mentse, és töltse fel az új megbízhatósági keretrendszer kiterjesztésű fájlt. Ezután a függő entitás házirend hívása használatát vagy az alkalmazás `Run Now` az Azure AD B2C-felületen. (Másodpercben) az eseményeket az Application Insights érhető el.

1. Nyissa meg a **Application Insights** erőforrást az Azure Active Directory-bérlőben.
2. Válassza ki **használati** > **események**.
3. Állítsa be **során** való **utolsó óra** és **által** való **3 perc**.  Előfordulhat, hogy ki kell választania **frissítése** eredmények megtekintéséhez.

![Application Insights használati-események Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

##  <a name="next-steps"></a>További lépések

Jogcímtípusok és események hozzáadása a felhasználói interakciósorozat saját igényei szerint. Egy lehetséges jogcímek segítségével további jogcímek feloldók listája

### <a name="culture-specific-claims"></a>Kulturális környezet-specifikus jogcímek

```xml
Referenced using: {Culture:One of the property names below}
```

| Jogcím | Meghatározás | Példa |
| ----- | -----------| --------|
| LanguageName | A kétbetűs ISO-kódja, a nyelv | hu |
| RegionName | A kétbetűs ISO-kódja a régió | USA |
| RFC5646 | A RFC5646 nyelvi kód | hu-HU |
| LCID   | A nyelvi kód LCID | 1038 |

### <a name="policy-specific-claims"></a>A házirend-specifikus jogcímek

```xml
Referenced using {Policy:One of the property names below}
```

| Jogcím | Meghatározás | Példa |
| ----- | -----------| --------|
| TrustFrameworkTenantId | A trustframework bérlő azonosítója | – |
| RelyingPartyTenantId | A bérlő azonosítóját, a függő entitás | – |
| PolicyId | A házirend azonosítója | – |
| TenantObjectId | A szabályzat objektum bérlőazonosítója | – |

### <a name="openid-connect-specific-claims"></a>OpenID Connect-specifikus jogcímek

```xml
Referenced using {OIDC:One of the property names below}
```

| Jogcím | OpenIdConnect paraméter | Példa |
| ----- | ----------------------- | --------|
| parancssor | parancssor | – |
| LoginHint |  login_hint | – |
| DomainHint | domain_hint | – |
|  MaxAge | max_age | – |
| ClientId | client_id | – |
| Felhasználónév | login_hint | – |
|  Erőforrás | erőforrás| – |
| AuthenticationContextReferences | acr_values | – |

### <a name="non-protocol-parameters-included-with-oidc--oauth2-requests"></a>OIDC & OAuth2 kérelmek mellékelt nem-protokoll-paraméterek

```xml
Referenced using { OAUTH-KV:Querystring parameter name }
```

A felhasználói interakciósorozatban szereplő jogcím bármely paraméternév egy OIDC vagy OAuth2-kérés részét is le lehet képezni. Ezután rögzítheti azt az eseményt. Például az alkalmazásból érkező kérelem tartalmazhat egy lekérdezési karakterlánc paramétereként nevű `app_session`, `loyalty_number` vagy `any_string`.

Íme egy példa az alkalmazásból érkező kérelmet:

```
https://login.microsoftonline.com/sampletenant.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_signup_signin&client_id=e1d2612f-c2bc-4599-8e7b-d874eaca1ae1&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&app_session=0a2b45c&loyalty_number=1234567

```
Ezután hozzáadhatja a jogcímeket hozzáadja egy `Input Claim` elem az Application Insights eseményre. Az esemény tulajdonságainak kerülnek {TULAJDONSÁGNÉV:}-szintaxis használatával where NAME tulajdonság ad hozzá az esemény. Példa:

```
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
```

### <a name="other-system-claims"></a>Egyéb system jogcímek

Néhány rendszer jogcímeket hozzá kell adnia a jogcímek csomagban érhetők el a eseményként is rögzíti. A technikai profil `SimpleUJContext` előtt meg kell hívni egy vezénylési lépés vagy egy érvényesítési technikai profil érhetők el ezeket a jogcímeket.

```xml
<ClaimsProvider>
  <DisplayName>User Journey Context Provider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SimpleUJContext">
      <DisplayName>User Journey Context Provide</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="IP-Address" />
        <OutputClaim ClaimTypeReferenceId="CorrelationId" />
        <OutputClaim ClaimTypeReferenceId="DateTimeInUtc" />
        <OutputClaim ClaimTypeReferenceId="Build" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```



---
title: Felhasználó viselkedésének nyomon eseményeket az Application Insights az Azure AD B2C használatával |} Microsoft Docs
description: Részletes útmutató az Azure AD B2C felhasználói utak az Application Insightsban eseménynaplók engedélyezése egyéni házirendek (előzetes verzió) használatával
services: active-directory-b2c
documentationcenter: dev-center-name
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 3/15/2018
ms.author: davidmu
ms.openlocfilehash: 28c4cefdd7604dbddf6887dcf494ecea65d658f1
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="track-user-behavior-in-azure-ad-b2c-journeys-by-using-application-insights"></a>Az Azure AD B2C útvonal be a felhasználó viselkedésének nyomon az Application Insights használatával

Az Azure Active Directory B2C Azure Application Insights jól működik (Azure AD B2C-vel). A felhasználó egyénileg létrehozott teljesített részletes és testre szabott eseménynaplók biztosítanak. Ez a cikk bemutatja, hogyan első lépésként, a következőket teheti: 
* A felhasználói viselkedés megismerésében.
* Végezzen hibaelhárítást a megfelelő saját házirendeket a fejlesztési és éles környezetben.
* Mérték teljesítményét.
* Az Application Insights értesítések létrehozása

> [!NOTE]
> A funkció jelenleg előzetes verzió.

## <a name="how-it-works"></a>Működés
Az Azure AD B2C identitás élmény keretében mostantól tartalmazza a szolgáltató `Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0`. Esemény adatokat küld közvetlenül az Application Insights az Azure AD B2C megadott instrumentation kulcs használatával. 

Műszaki profil ezt a szolgáltatót használ egy esemény B2C meghatározására. A profil határozza meg az esemény, a jogcímeket lesznek rögzítve és a rendszerállapot-kulcs neve. Elküldeni egy eseményt, a műszaki profil kerül egy *vezénylési lépés* vagy regisztrációja, mivel egy *érvényesítési műszaki profil* az egyéni felhasználói út. 

Az Application Insights is egyesítése, ezáltal az események rögzítése a felhasználói munkamenet a korrelációs azonosító használatával. Az Application Insights elérhetővé teszi a esemény- és a másodperc, és sok képi megjelenítés és exportálása analitikai eszközök mutatja be.



## <a name="prerequisites"></a>Előfeltételek
Hajtsa végre a [Ismerkedés az egyéni házirendek](active-directory-b2c-get-started-custom.md). Ez a cikk feltételezi, hogy az egyéni házirend kezdő szintű csomagot használ. De az alapszintű csomag nem szükséges.



## <a name="step-1-create-an-application-insights-resource-and-get-the-instrumentation-key"></a>1. lépés Az Application Insights-erőforrás létrehozása és a rendszerállapot-kulcs beszerzése

Az Application Insights Azure AD B2C használata, amikor egyetlen követelménye, hogy hozzon létre egy erőforrást és egy instrumentation kulcs beszerzése. Az erőforrás létrehozása a [Azure-portálon](https://portal.azure.com).

1. Válassza ki az Azure portálon, az előfizetés-bérlő belül **+ hozzon létre egy erőforrást**. A bérlő nincs az Azure AD B2C-bérlő.  
2. Keresse meg és jelölje ki **Application Insights**.  
3. Hozzon létre egy erőforrást használó **ASP.NET-webalkalmazás** , **alkalmazástípus**, igény szerint az adott előfizetésen belül.
4. Az Application Insights-erőforrás létrehozása után nyissa meg azt, és jegyezze fel a instrumentation kulcs. 

![Application Insights – áttekintés és instrumentation kulcs](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-key.png)

További információkért lásd: a [Application Insights dokumentációja teljes](https://docs.microsoft.com/azure/application-insights/).

## <a name="step-2-add-new-claimtype-definitions-to-your-trust-framework-extension-file"></a>2. lépés Új ClaimType definíciók hozzáadása a megbízható keretrendszer bővítményfájl

Nyissa meg a bővítményfájl alapszintű csomagból, és adja hozzá a következő elemek a `<BuildingBlocks>` csomópont. A fájlnév pedig általában `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`.

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
      <!-- Additional claims used for passing claims to the Application Insights provider. -->
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

## <a name="step-3-add-new-technical-profiles-that-use-the-application-insights-provider"></a>3. lépés Adja hozzá az új műszaki profilokat az Application Insights-szolgáltató

Műszaki profilok az identitás élmény keretrendszer az Azure AD B2C funkciók tekinthető meg. Ebben a példában egy munkamenet megnyitását és események közzétételéhez öt műszaki profil határozza meg:

| Műszaki profil       | Tevékenység |
| ----------------------------- |:---------------------------------------------|
| AzureInsights-Common | Jönnek létre a közös paraméterek összes szereplő `AzureInsights` műszaki profilok     | 
| JourneyContextForInsights   | A munkamenet megnyílik az Application Insights, és elküldi a korrelációs azonosító |
| AzureInsights-SignInRequest     | Létrehoz egy `SignIn` a jogcímeket, ha egy bejelentkezési kérelmet kapott esemény      | 
| AzureInsights-UserSignup | Létrehoz egy `UserSignup` eseményt, amikor a felhasználó elindítja az előfizetési beállítás a sign-Close-Up/sign-in út     | 
| AzureInsights-SignInComplete | a hitelesítés sikeres befejezését rögzíti, amikor el lett küldve egy jogkivonatot a függő entitás alkalmazás     | 

A profilok a bővítményfájl származó hozzáadása az alapszintű csomag ezen elemek hozzáadásával a `<ClaimsProviders>` csomópont. A fájlnév pedig általában `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`.

> [!IMPORTANT]
> A rendszerállapot-kulcsot a módosítása a `ApplicationInsights-Common` műszaki profilt, amely az Application Insights-erőforrás GUID.

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
            <!-- An input claim with PartnerClaimType="eventName" is required. The Application Insights provider uses it to create an event with the specified value. -->
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
            <!-- The Application Insights instrumentation key that will be used for logging the events. -->
            <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
            <!-- A Boolean that indicates whether developer mode is enabled. This controls how events are buffered. In a development environment with minimal event volume, enabling developer mode results in events being sent immediately to Application Insights.   -->
            <Item Key="DeveloperMode">false</Item>
            <!-- A Boolean that indicates whether telemetry should be enabled or not.   -->
            <Item Key="DisableTelemetry ">false</Item>
          </Metadata>
          <InputClaims>
            <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is the name of the property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
            <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
            <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
            <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
          </InputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

```

## <a name="step-4-add-the-technical-profiles-for-application-insights-as-orchestration-steps-in-an-existing-user-journey"></a>4. lépés Adja hozzá a műszaki profilok az Application Insights, egy meglévő felhasználó út vezénylési szükséges lépések

Hívás `JournyeContextForInsights` , orchestration 1. lépés:

```xml
<!-- Initialize a session with Application Insights. -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="JourneyContextForInsights" TechnicalProfileReferenceId="JourneyContextForInsights" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

Hívás `Azure-Insights-SignInRequest` vezénylési, nyomon követheti, hogy a bejelentkezési-a/regisztrációs kérelmet kapott a 2. lépés:

```xml
<!-- Track that we have received a sign-in request. -->
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

Azonnal *előtt* a `SendClaims` vezénylési. lépés:, adjon hozzá egy új lépést, amely behívja `Azure-Insights-UserSignup`. Ha a felhasználó a regisztráció gombra a sign-Close-Up/sign-in út adatbázistáblába.

```xml
        <!-- Handles the user selecting the sign-up link in the local account sign-in page. -->
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

Után azonnal a `SendClaims` vezénylési lépés, hívás `Azure-Insights-SignInComplete`. Ez a lépés sikeresen befejeződött az út tükrözi.

```xml
        <!-- Track that we have successfully sent a token. -->
        <OrchestrationStep Order="11" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
          </ClaimsExchanges>
        </OrchestrationStep>

```

> [!IMPORTANT]
> Miután az új vezénylési lépésekből, áthelyezni a lépéseket egymás után nélkül kihagyásával bármely egész számok 1-n


## <a name="step-5-upload-your-modified-extensions-file-run-the-policy-and-view-events-in-application-insights"></a>5. lépés A módosított bővítmények-fájl feltöltése, futtassa a házirend, és tekintse meg az eseményeket az Application Insightsban

Mentse, majd töltse fel az új megbízhatósági keretrendszer kiterjesztésű fájlt. Ezután hívja a függő entitás házirend használatát vagy az alkalmazás **Futtatás most** az Azure AD B2C-felületen. (Másodpercben) az események érhetők el az Application Insights.

1. Nyissa meg az Application Insights-erőforrást az Azure Active Directory-bérlő.
2. Válassza ki **események** a a **használati** almenü.
3. Állítsa be **során** való **elmúlt egy órában** és **által** való **3 perc**. Előfordulhat, hogy kell kiválasztania **frissítése** eredmények megtekintése.

![Az Application Insights használati események diagramhoz](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)





## <a name="next-steps"></a>További lépések

Jogcímtípusok és események hozzáadása a felhasználói használatában az igényeihez. Lehetséges jogcímek, további jogcímek feloldókat listája itt található.

### <a name="culture-specific-claims"></a>Kulturális környezet-specifikus jogcímek

```xml
Culture-specific Claims
            Referenced using {Culture:One of the property names below}
 
        /// An enumeration of the claims supported by the <see cref="JourneyCultureDefaultClaimProcessor"/>
        public enum SupportedClaim
        {
             /// The two letter ISO code for the language i.e. en
            LanguageName
             
            /// The two letter ISO code for the region i.e. US
            RegionName,
 
            /// The RFC5646 language code i.e. en-US
            RFC5646,

            /// The LCID of language code i.e. 1033
            LCID
        }

```

### <a name="policy-specific-claims"></a>Házirend-specifikus jogcímek

```xml
Policy-specific Claims
Referenced using {Policy:One of the property names below}
 
        /// An enumeration of the claims supported by the <see cref="PolicyDefaultClaimProcessor"/> 
        public enum SupportedClaim
        {
            /// The trustframework tenant id
            TrustFrameworkTenantId,
  
            /// The tenant id of the relying party
            RelyingPartyTenantId,
 
            /// The policy id of the policy
            PolicyId,
 
            /// The tenant object id of the policy
            TenantObjectId
}

```

### <a name="openid-connect-specific-claims"></a>OpenID Connect-specifikus jogcímek

```xml
OpenIDConnect Specific Claims
Referenced using {OIDC:One of the property names below}
 
/// 
        /// An enumeration of the claims supported by the <see cref="OpenIdConnectDefaultClaimProcessor"/>

        public enum SupportedClaim
        {
            /// The OpenIdConnect prompt parameter
            Prompt,
 
            /// The OpenIdConnect login_hint parameter
            LoginHint,

            /// The OpenIdConnect domain_hint parameter
            DomainHint,
 
             /// The OpenIdConnect max_age parameter
            MaxAge,
 
            /// The OpenIdConnect client_id parameter
            ClientId,
 
            /// The OpenIdConnect username parameter
            Username,

            /// The OpenIdConnect password parameter
            Password,
 
            /// The OpenIdConnect resource type parameter
            Resource,
 
            /// The OpendIdConext acr_values parameter
             AuthenticationContextReferences
        }
 
```

### <a name="non-protocol-parameters-included-with-oidc-and-oauth2-requests"></a>A nem protokollparaméterekhez a OIDC és OAuth2 részét képező kérelmek

```xml
Referenced using { OAUTH-KV:Querystring parameter name }
```

A paraméter neve OIDC vagy OAuth2 kérelmet részét képező egy jogcímet a felhasználó út rendelhetők. Ezután rögzítheti az esemény. Például az alkalmazás a kérelem állhatnak-e a lekérdezési karakterlánc paraméterként nevű `app_session`, `loyalty_number`, vagy `any_string`.

Íme egy minta kérelem az alkalmazásból:
```
https://login.microsoftonline.com/sampletenant.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_signup_signin&client_id=e1d2612f-c2bc-4599-8e7b-d874eaca1ae1&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&app_session=0a2b45c&loyalty_number=1234567

```
A jogcímek hozzáadásával Ezután felvehet egy `InputClaim` elemben, amely az Application Insights esemény:
```
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="app_session" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="loyalty_number" DefaultValue="{OAUTH-KV:loyalty_number}" />
```

### <a name="other-system-claims"></a>Egyéb rendszer jogcímek

Néhány rendszer jogcímeket kell felvenni a jogcímek tulajdonságcsomagjait ahhoz, azok elérhetők eseményként is rögzíti. A műszaki profil `SimpleUJContext` előtt meg kell hívni az orchestration lépés vagy egy érvényesítési műszaki profil érhetők el ezeket a jogcímeket.

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




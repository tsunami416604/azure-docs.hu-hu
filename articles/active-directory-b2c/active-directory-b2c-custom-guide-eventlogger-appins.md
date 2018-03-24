---
title: Hogyan eseményeket az Application Insights az Azure AD B2C használ a felhasználó viselkedésének nyomon |} Microsoft Docs
description: Részletes útmutató az Azure AD B2C felhasználói utak egyéni házirendekkel – előzetes az Application Insightsban eseménynaplók engedélyezése
services: active-directory-b2c
documentationcenter: dev-center-name
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 3/15/2018
ms.author: davidmu
ms.openlocfilehash: 1e8c4a53266072db71952ee35b15e5de54fabb95
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="tracking-user-behavior-inside-azure-ad-b2c-journeys-using-application-insights"></a>Az Application Insights használata az Azure AD B2C utak belül a felhasználó viselkedésének nyomon követése

Az Azure Active Directory B2C jól Azure Application Insights működik.  Az egyéni felhasználói teljesített részletes és testre szabott eseménynaplók biztosítanak.  Ez az útmutató bemutatja, hogyan első lépésként, a következőket teheti: 
* a felhasználói viselkedés megismerésében
* a fejlesztési és éles környezetben a saját szabályzatokkal kapcsolatos problémák elhárítása
* mérték teljesítmény
* az Application Insights értesítések létrehozása

## <a name="how-it-works"></a>Működés
Új szolgáltató hozzá lett adva az Azure AD B2C identitás élmény keretrendszer: `Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0`.  Esemény adatokat küld közvetlenül az Application Insights az Azure AD B2C megadott Instrumentation kulcs használatával.  Műszaki profil ezt a szolgáltatót használ egy esemény B2C meghatározására.  A profil határozza meg az esemény, a jogcímeket lesznek rögzítve és a rendszerállapot-kulcs neve.  Az esemény közzétételéhez a műszaki profil kerül, a perceké 'M `orchestration step` vagy regisztrációja, mivel egy `validation technical profile` az egyéni felhasználói út.  Az eseményeket az Application Insights korrelációs azonosító használatával rögzítheti a felhasználói munkamenet által egységes is.  Az Application Insights elérhetővé teszi a esemény- és a másodpercen belül, és sok képi megjelenítés és exportálása analitikai eszközök mutatja be.



## <a name="prerequisites"></a>Előfeltételek
Hajtsa végre a a [Ismerkedés az egyéni házirendek](active-directory-b2c-get-started-custom.md).  Ez az útmutató feltételezi, hogy az egyéni házirend alapszintű csomag használatban van.  Azonban nincs szükség.



## <a name="step-1-create-an-application-insights-resource-and-get-the-instrumentation-key"></a>1. lépés Az Application Insights-erőforrás létrehozása és a rendszerállapot-kulcs beszerzése

Az Application Insights egy olyan erőteljes eszköz. Az Azure AD B2C használata esetén egyetlen követelménye, hogy hozzon létre egy erőforrást és egy Instrumentation kulcs beszerzése.  Az Application Insights kell létrehozni a [Azure-portálon.](https://portal.azure.com)

[Az Application Insights teljes dokumentációja](https://docs.microsoft.com/azure/application-insights/)

1. Kattintson a `+ Create a resource` az Azure portálon, az előfizetés-bérlő belül.  A bérlő nincs az Azure AD B2C-bérlő.  
2. Megkereséséhez és kijelöléséhez. `Application Insights`  
3. Az erőforrás létrehozása `Application Type` `ASP.NET web application` igény szerint az adott előfizetésen belül.
4. Létrehozása után, nyissa meg az Application Insights-erőforrás és a Megjegyzés: a  `Instrumentation Key` 

![Application Insights – áttekintés és Instrumentation kulcs](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-key.png)

## <a name="step-2-add-new-claimtype-definitions-to-your-trust-framework-extension-file"></a>2. lépés Új ClaimType definíciók hozzáadása a megbízható keretrendszer bővítményfájl

### <a name="open-the-extension-file-from-the-starter-pack-and-add-the-following-elements-to-the-buildingblocks-node--the-extensions-filename-is-typically-yourtenantonmicrosoftcom-b2c1atrustframeworkextensionsxml"></a>Nyissa meg a bővítményfájl alapszintű csomagból, és adja hozzá a következő elemek a `<BuildingBlocks>` csomópont.  A bővítmények fájlnév általában van `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

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

## <a name="step-3-add-new-technical-profiles-that-use-the-application-insights-provider"></a>3. lépés Adja hozzá az új műszaki profilokat az Application Insights-szolgáltató

Műszaki profilok az Azure AD B2C-identitás élmény keretrendszer funkciók tekinthetők meg.  Ez a példa egy munkamenet megnyitását és események közzétételéhez öt műszaki profilok vannak definiálva.

| Műszaki profil       | Tevékenység |
| ----------------------------- |:---------------------------------------------|
| AzureInsights-Common | minden Azure-elemzések műszaki profilban szereplő paraméterek közös készlete     | 
| JourneyContextForInsights   | a munkamenet során App Insights megnyílik, és elküldi a korrelációs azonosító |
| AzureInsights-SignInRequest     | hozza létre a "Bejelentkezés" esemény a jogcímek egy készletét, amikor a bejelentkezési kérelem érkezett      | 
| AzureInsights-UserSignup | hoz létre eseményt, "UserSignup" meghívva, amikor a felhasználó a regisztráció vagy bejelentkezés az út az előfizetési beállítást lett elindítva     | 
| AzureInsights-SignInComplete | a hitelesítés sikeres befejezését rögzíti, amikor el lett küldve egy jogkivonatot a függő entitás alkalmazás     | 

A profilok a bővítményfájl származó hozzáadása az alapszintű csomag ezen elemek hozzáadásával a `<ClaimsProviders>` csomópont.  A bővítmények fájlnév általában van `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

> [!IMPORTANT]
> Módosítsa a `Instrumentation Key` a a `ApplicationInsights-Common` műszaki az Application Insights-erőforrás által megadott GUID-profilt.

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
            <!-- 
                            An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider
                            to create an event with the specified value.
                        -->
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
            <!-- 
                            A boolean indicating whether delevoper mode is enabled. This controls how events are buffered. In a development environment
                            with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights.
                        -->
            <Item Key="DeveloperMode">false</Item>
            <!-- 
                            A boolean indicating whether telemetry should be enabled or not.
                        -->
            <Item Key="DisableTelemetry ">false</Item>
          </Metadata>
          <InputClaims>
            <!--
                            Properties of an event are added using the syntax {property:NAME} where NAME is the name of the property being added
                            to the event. DefaultValue can be either a static value or one resolved by one of the supported DefaultClaimResolvers.
                        -->
            <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
            <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
            <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
          </InputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

```

## <a name="step-4-add-the-technical-profiles-for-application-insights-as-orchestration-steps-in-an-existing-user-journey"></a>4. lépés A műszaki profilok vezénylési lépéseket egy meglévő felhasználó út az alkalmazás-elemzések hozzáadása

Hívás `JournyeContextForInsights` vezénylési lépéseként 1

```xml
<!-- Initialize a session with Application Insights -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="JourneyContextForInsights" TechnicalProfileReferenceId="JourneyContextForInsights" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

Hívás `Azure-Insights-SignInRequest` vezénylési, nyomon követheti, hogy a bejelentkezési-a/regisztrációs kérelmet kapott a 2. lépés.

```xml
<!-- Track that we have received a sign in request -->
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

Azonnal **előtt** a `SendClaims` vezénylési. lépés:, adjon hozzá egy új lépést, amely behívja `Azure-Insights-UserSignup`. Akkor lesz kiváltva, ha a regisztráció gombra kattintott a regisztráció vagy bejelentkezés út.

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

Után azonnal a `SendClaims` vezénylési lépés, hívás `Azure-Insights-SignInComplete`.   Ebben a lépésben fogja tartalmazni az út sikeresen befejeződött.

```xml
        <!-- Track that we have successfully sent a token -->
        <OrchestrationStep Order="11" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
          </ClaimsExchanges>
        </OrchestrationStep>

```

> [!IMPORTANT]
> Az új vezénylési lépésekből adásakor áthelyezni a lépéseket egymás után nélkül kihagyásával bármely egész számok 1-N


## <a name="step-5-upload-your-modified-extensions-file-run-the-policy-and-view-events-in-application-insights"></a>5. lépés A módosított bővítmények-fájl feltöltése, futtassa a házirend, és tekintse meg az eseményeket az Application Insightsban

Mentse, és töltse fel az új megbízhatósági keretrendszer bővítmények fájlt.  Ezután hívja a függő entitás házirend használatát vagy az alkalmazás `Run Now` az Azure AD B2C-felületen.  Néhány másodpercen belül az események az Application Insights elérhető lesz.

1. Nyissa meg az Application Insights-erőforrást az Azure Active Directory-bérlő.
2. Kattintson a `Events` a a `USAGE` almenü.
3. Állítsa be `During` való `Last hour` és `By` való `3 minutes`.  Kattintson a szeretne `Refresh` eredmények megtekintése

![Application Insights használati-események Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)





##  <a name="next-steps"></a>TOVÁBBI LÉPÉSEK

További jogcímtípusok és események hozzáadása a felhasználói használatában az igényeihez.  További jogcímek feloldókat lehetséges jogcímek listája itt található.

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

### <a name="openidconnect-specific-claims"></a>OpenIDConnect megadott jogcímek

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

### <a name="non-protocol-parameters-included-with-oidc--oauth2-requests"></a>A nem protokollparaméterekhez részét képező OIDC & OAuth2 kérelmek

```xml
Referenced using { OAUTH-KV:Querystring parameter name }
```

A paraméter neve OIDC vagy OAuth2 kérelmet részét képező egy jogcímet a felhasználó út rendelhetők.  Majd rögzíthetők az eseményben. Például az alkalmazás a kérelem tartalmazhatják a lekérdezési karakterlánc paraméterként nevű `app_session`, `loyalty_number` vagy `any_string`.

Az alkalmazás kérelemmintát:
```
https://login.microsoftonline.com/sampletenant.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_signup_signin&client_id=e1d2612f-c2bc-4599-8e7b-d874eaca1ae1&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&app_session=0a2b45c&loyalty_number=1234567

```
A jogcímek felveheti egy bemeneti jogcímet elem felvétele az Application Insights esemény használatával:
```
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="app_session" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="loyalty_number" DefaultValue="{OAUTH-KV:loyalty_number}" />
```

### <a name="other-system-claims"></a>Egyéb rendszer jogcímek

Néhány rendszer jogcímek hozzá kell adnia a jogcímek tulajdonságcsomagjait számára érhetők el a eseményként is rögzíti. A műszaki profil `SimpleUJContext` előtt meg kell hívni az orchestration lépés vagy egy érvényesítési műszaki profil érhetők el ezeket a jogcímeket.

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




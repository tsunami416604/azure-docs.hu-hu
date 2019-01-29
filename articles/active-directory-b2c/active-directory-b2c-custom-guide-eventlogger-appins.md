---
title: Események az Application Insights az Azure Active Directory B2C használatával a felhasználók viselkedésének nyomon követése |} A Microsoft Docs
description: Ismerje meg az eseménynaplókat az Application insights szolgáltatásban az Azure AD B2C felhasználói utak engedélyezése egyéni házirendek (előzetes verzió) használatával.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5bc9e44cc1f1759231d405e64fab097d73d48486
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55160526"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Nyomon követheti a felhasználói viselkedés az Azure Active Directory B2C az Application Insights használatával

Azure Active Directory (Azure AD) B2C-vel együtt az Azure Application Insights használata esetén is részletes, és a felhasználói utak eseménynaplók igényeire szabva. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Felhasználói viselkedéssel kapcsolatos információkhoz.
* Hárítsa el a megfelelő saját házirendeket fejlesztési vagy éles környezetben.
* Teljesítmény mérésére.
* Értesítések létrehozása az Application Insights szolgáltatásból.

> [!NOTE]
> Ez a funkció előzetes verzióban érhető el.

## <a name="how-it-works"></a>Működés

Az Azure AD B2C az identitás-kezelőfelületi keretrendszer magában foglalja a szolgáltató `Handler="Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0`. A kialakítási kulcsot a megadott Azure AD B2C használatával közvetlenül az Application Insights küld eseményadatokat.

A technikai profil a szolgáltató használ egy esemény az Azure AD B2C-ből meghatározására. A profil megadja az esemény, a jogcímeket, které jsou zaznamenány és a kialakítási kulcs neve. Az esemény közzétételéhez majd hozzáadja a technikai profil egy `orchestration step`, vagy mint egy `validation technical profile` egy egyéni felhasználói interakciósorozatban szereplő.

Az Application Insights is használja őket egységes előtérrendszerként az eseményeket rögzítse a felhasználói munkamenet egy korrelációs azonosító használatával. Az Application Insights elérhetővé teszi a események és munkamenetek másodpercen belül, és megadja a sok vizualizációt, exportálása és analitikai eszközök.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a [egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md). Ez a cikk azt feltételezi, hogy az egyéni házirend alapszintű csomagot használ. De az alapszintű csomag nincs szükség.

## <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása

Az Application Insights az Azure AD B2C-vel használ, meg kell esetén hozzon létre egy erőforrást, és a kialakítási kulcs beszerzése.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Ellenőrizze, hogy a könyvtárat, amely tartalmazza az Azure-előfizetés kattintva használ a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza az előfizetés kiválasztásával. Ezen a bérlőn nem áll az Azure AD B2C-bérlőben.
3. Válasszon **erőforrás létrehozása** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Application Insights**.
4. Kattintson a **Create** (Létrehozás) gombra.
5. Adjon meg egy **neve** az erőforráshoz.
6. A **alkalmazástípus**válassza **ASP.NET-alkalmazás**.
7. A **erőforráscsoport**, válasszon egy meglévő csoportot, vagy adjon meg egy új csoport nevét.
8. Kattintson a **Create** (Létrehozás) gombra.
4. Miután létrehozta az Application Insights-erőforrást, nyissa meg azt, bontsa ki a **Essentials**, és másolja a kialakítási kulcsot.

![Az Application Insights áttekintése és a kialakítási kulcsot](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-insights.png)

## <a name="add-new-claimtype-definitions"></a>Adja hozzá az új takar definíciók

Nyissa meg a *TrustFrameworkExtensions.xml* az alapszintű csomagból fájlt, és adja hozzá a következő elemeket az [BuildingBlocks](buildingblocks.md) elem:

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

## <a name="add-new-technical-profiles"></a>Adja hozzá az új technikai profilok

Az az identitás élmény keretrendszer az Azure AD B2C funkciók technikai profilok lehessen venni. Ez a táblázat a munkamenetet nyit meg, és az események közzététele használható technikai profilok határozza meg.

| Technikai profil | Tevékenység |
| ----------------- | -----|
| AzureInsights-Common | Minden AzureInsights technikai profilban szereplő paraméterek készletének hoz létre. | 
| AzureInsights-SignInRequest | Jogcímek egy készletét bejelentkezési eseményt hoz létre, amikor egy bejelentkezési kérelmet kapott. | 
| AzureInsights-UserSignup | Amikor a felhasználó elindítja a regisztrációs lehetőség a regisztrálási-regisztrálási vagy bejelentkezési út UserSignup eseményt hoz létre. | 
| AzureInsights-SignInComplete | Amikor a rendszer elküldte a jogkivonatot a függő gyártótól származó alkalmazás rögzíti a hitelesítés sikeres befejezését. | 

Adja hozzá a profilt a *TrustFrameworkExtensions.xml* az alapszintű csomag fájlt. Adja hozzá a ezeknek az elemeknek a **ClaimsProviders** elem:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
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

> [!IMPORTANT]
> A kialakítási kulcsot az módosítása a `ApplicationInsights-Common` technikai profil az Application Insights-erőforrást biztosító GUID.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Adja hozzá a technikai profilok vezénylési lépésekből

Hívás `Azure-Insights-SignInRequest` vezénylési, 2. lépés: nyomon követésére, hogy a bejelentkezési-a vagy a regisztráláshoz kérelmet kapott:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Azonnal *előtt* a `SendClaims` vezénylési lépés, adjon hozzá egy új lépés, amely meghívja ezt `Azure-Insights-UserSignup`. Ez akkor aktiválódik, ha a felhasználó kiválaszt egy regisztrálási-regisztrálási vagy bejelentkezési utazás a regisztráció gombra.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="8" Type="ClaimsExchange">
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
</OrchestrationStep>
```

Után azonnal a `SendClaims` vezénylési. lépés: a hívás `Azure-Insights-SignInComplete`. Ez a lépés bemutatja az út sikeresen befejeződött.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Miután hozzáadta az új vezénylési lépésekből, áthelyezni a lépések sorrendben anélkül, hogy a rendszer kihagyja bármely egész számok 1-től N. a


## <a name="upload-your-file-run-the-policy-and-view-events"></a>A fájl feltöltése, a szabályzat futtatása és események megtekintése

Mentse, és töltse fel a *TrustFrameworkExtensions.xml* fájlt. Ezután a függő entitás házirend hívása használatát vagy az alkalmazás **Futtatás most** az Azure Portalon. (Másodpercben) az eseményeket az Application Insights érhető el.

1. Nyissa meg a **Application Insights** erőforrást az Azure Active Directory-bérlőben.
2. Válassza ki **használati** > **események**.
3. Állítsa be **során** való **utolsó óra** és **által** való **3 perc**.  Előfordulhat, hogy ki kell választania **frissítése** eredmények megtekintéséhez.

![Application Insights USAGE-Events Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

## <a name="next-steps"></a>További lépések

Jogcímtípusok és események hozzáadása a felhasználói interakciósorozat saját igényei szerint. Használhat [feloldók jogcím](claim-resolver-overview.md) vagy bármilyen karakterlánc jogcím típusa, adja hozzá a jogcímeket hozzáadja egy **bemeneti jogcímek** elem az Application Insights-esemény vagy a AzureInsights-közös technikai profil. 

- **ClaimTypeReferenceId** egy jogcímtípushoz a hivatkozás.
- **PartnerClaimType** Azure Insights megjelenő tulajdonság neve. A szintaxissal, `{property:NAME}`, ahol `NAME` az esemény hozzáadandó tulajdonság. 
- **DefaultValue** bármilyen karakterlánc típusú értéket vagy a jogcím-feloldó. 

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```


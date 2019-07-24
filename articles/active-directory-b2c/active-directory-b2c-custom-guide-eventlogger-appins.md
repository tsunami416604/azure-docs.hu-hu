---
title: A felhasználói viselkedés nyomon követése a Application Insightsban lévő események használatával Azure Active Directory B2C | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti az eseménynaplókat a Application Insights Azure AD B2C felhasználói úton egyéni szabályzatok használatával (előzetes verzió).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e18157c95dac0de90c50b4b7e8591e32c5b76aaf
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227235"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>A felhasználó viselkedésének nyomon követése Azure Active Directory B2C a Application Insights használatával

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Ha a Azure Active Directory (Azure AD) B2C-t az Azure Application Insights-mel együtt használja, részletes és testreszabott eseménynaplókat kaphat a felhasználói utazásokhoz. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* A felhasználói viselkedés megállapítása.
* Saját házirendek fejlesztése a fejlesztésben vagy éles környezetben.
* Teljesítmény mérése.
* Értesítések létrehozása Application Insightsból.

## <a name="how-it-works"></a>Működés

A Azure AD B2C identitás-keretrendszer része a szolgáltatót `Handler="Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0`is tartalmazza. Az eseményre vonatkozó adatokat közvetlenül az Application Insights számára küldi el a Azure AD B2C számára megadott rendszerállapot-kulcs használatával.

A technikai profil ezt a szolgáltatót használja a Azure AD B2C eseményének definiálásához. A profil megadja az esemény nevét, a rögzített jogcímeket és a kialakítási kulcsot. Egy esemény közzétételéhez a technikai profilt hozzáadjuk `orchestration step`, vagy `validation technical profile` egy egyéni felhasználói úton.

A Application Insights az eseményeket egy korrelációs azonosító használatával egyesítheti egy felhasználói munkamenet rögzítéséhez. A Application Insights másodpercek alatt elérhetővé teszi az eseményt és a munkamenetet, és számos vizualizációs, exportálási és analitikai eszközt megjelenít.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre az [Ismerkedés az egyéni szabályzatokkal](active-directory-b2c-get-started-custom.md)című témakör lépéseit. Ez a cikk azt feltételezi, hogy az egyéni szabályzatok alapszintű csomagja van használatban. Az alapszintű csomag azonban nem kötelező.

## <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása

Ha a Azure AD B2C használatával Application Insightst használ, mindössze annyit kell tennie, hogy létrehoz egy erőforrást, és beolvassa a kialakítási kulcsot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy az Azure-előfizetését tartalmazó könyvtárat használja, majd a felső menüben kattintson a **címtár és előfizetés szűrőre** , és válassza ki az előfizetést tartalmazó könyvtárat. Ez a bérlő nem az Ön Azure AD B2C bérlője.
3. Válassza az **erőforrás létrehozása** lehetőséget a Azure Portal bal felső sarkában, majd keresse meg és válassza a **Application Insights**lehetőséget.
4. Kattintson a **Create** (Létrehozás) gombra.
5. Adja meg az erőforrás **nevét** .
6. Az **alkalmazás típusa**mezőben válassza a **ASP.net webalkalmazás**lehetőséget.
7. Az **erőforráscsoport**területen válasszon egy meglévő csoportot, vagy adjon meg egy új csoport nevét.
8. Kattintson a **Create** (Létrehozás) gombra.
4. A Application Insights erőforrás létrehozása után nyissa meg, bontsa ki az **Essentials**csomópontot, és másolja a kialakítási kulcsot.

![Application Insights áttekintése és kialakítási kulcsa](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-insights.png)

## <a name="add-new-claimtype-definitions"></a>Új ClaimType-definíciók hozzáadása

Nyissa meg a *TrustFrameworkExtensions. XML* fájlt az alapszintű csomagból, és adja hozzá a következő elemeket a [BuildingBlocks](buildingblocks.md) elemhez:

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

## <a name="add-new-technical-profiles"></a>Új technikai profilok hozzáadása

A technikai profilok a Azure AD B2C identitási élményének keretrendszerében tekinthetők meg. Ez a táblázat a munkamenetek megnyitásához és az események közzétételéhez használt technikai profilokat határozza meg.

| Technikai profil | Tevékenység |
| ----------------- | -----|
| AzureInsights-Common | Közös paramétereket hoz létre az összes AzureInsights-technikai profilban. | 
| AzureInsights-SignInRequest | Egy bejelentkezési eseményt hoz létre, amely jogcímeket tartalmaz, ha bejelentkezési kérés érkezett. | 
| AzureInsights-UserSignup | Létrehoz egy UserSignup-eseményt, amikor a felhasználó aktiválja a bejelentkezési lehetőséget egy regisztrációs vagy bejelentkezési úton. | 
| AzureInsights-SignInComplete | A hitelesítés sikeres befejezését rögzíti, ha a rendszer jogkivonat küldése a függő entitás alkalmazásának. | 

Adja hozzá a profilokat a *TrustFrameworkExtensions. XML* fájlhoz az alapszintű csomagból. Adja hozzá ezeket az elemeket a **ClaimsProviders** elemhez:

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
> Módosítsa a kialakítási kulcsot `AzureInsights-Common` a technikai profilban a Application Insights erőforrás által biztosított GUID azonosítóra.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Technikai profilok hozzáadása előkészítési lépésként

Hívja `Azure-Insights-SignInRequest` meg a 2. lépést a beléptetési/regisztrációs kérések nyomon követéséhez:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Közvetlenül  a `SendClaims` előkészítési lépés előtt adjon hozzá egy új lépést, amely `Azure-Insights-UserSignup`meghívja a-t. Akkor aktiválódik, ha a felhasználó kiválasztja a regisztrációs vagy bejelentkezési útvonalon a regisztráció gombot.

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

Közvetlenül a előkészítési lépés után hívja `SendClaims` `Azure-Insights-SignInComplete`meg a következőt:. Ez a lépés egy sikeres befejezett utazást mutat be.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Az új előkészítési lépések hozzáadása után a lépéseket szekvenciálisan számozva, az 1 és N közötti egész számok kihagyása nélkül.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Töltse fel a fájlt, futtassa a szabályzatot, és tekintse meg az eseményeket

Mentse és töltse fel a *TrustFrameworkExtensions. XML* fájlt. Ezután hívja meg a függő entitásra vonatkozó házirendet az alkalmazásból, vagy használja a **Futtatás most parancsot** a Azure Portal. Másodpercek alatt az események Application Insightsban érhetők el.

1. Nyissa meg a **Application Insights** erőforrást a Azure Active Directory-bérlőben.
2. Válassza ki a **használati** > **eseményeket**.
3. Állítsa  be az **elmúlt órában** és  **3 percen belül**.  Előfordulhat, hogy a **frissítés** elemre kell kattintania az eredmények megtekintéséhez.

![HASZNÁLAT Application Insights-események Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

## <a name="next-steps"></a>További lépések

Az igényeinek megfelelően adja hozzá a jogcím-típusokat és az eseményeket a felhasználói útra. Használhatja a [jogcím](claim-resolver-overview.md) -feloldókat vagy bármely karakterlánc-jogcím típusát, hozzáadhatja a jogcímeket egy **bemeneti jogcím** elem hozzáadásával a Application Insights eseményhez vagy a AzureInsights-közös technikai profilhoz. 

- A **ClaimTypeReferenceId** a jogcím típusára mutató hivatkozás.
- A **PartnerClaimType** az Azure-adatfelismerésekben megjelenő tulajdonság neve. Használja a szintaxisát `{property:NAME}`, ahol `NAME` a tulajdonságot az eseményhez adja a rendszer. 
- A **DefaultValue** bármilyen karakterlánc-értéket vagy a jogcím feloldóját használja. 

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```


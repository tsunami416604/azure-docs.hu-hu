---
title: A felhasználó viselkedésének nyomon követése Application Insights
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan engedélyezheti az eseménynaplókat a Application Insights Azure AD B2C felhasználói úton egyéni házirendek használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 688bf4526ad287955231358ab0b64036e5480713
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85201429"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>A felhasználó viselkedésének nyomon követése Azure Active Directory B2C a Application Insights használatával

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

A Azure Active Directory B2C (Azure AD B2C) a Azure AD B2C számára biztosított rendszerállapot-kulcs használatával támogatja az esemény-adatok közvetlenül a [Application Insightsre](../azure-monitor/app/app-insights-overview.md) való küldését.  Application Insights technikai profillal részletes és testreszabott eseménynaplókat kaphat a felhasználói számára:

* A felhasználói viselkedés megállapítása.
* Saját házirendek fejlesztése a fejlesztésben vagy éles környezetben.
* Teljesítmény mérése.
* Értesítések létrehozása Application Insightsból.

## <a name="how-it-works"></a>Működés

A [Application Insights](application-insights-technical-profile.md) műszaki profil egy eseményt határoz meg Azure ad B2Cból. A profil megadja az esemény nevét, a rögzített jogcímeket és a kialakítási kulcsot. Egy esemény elküldéséhez a technikai profilt a rendszer a [felhasználói úton](userjourneys.md)történő előkészítési lépésként adja hozzá.

A Application Insights az eseményeket egy korrelációs azonosító használatával egyesítheti egy felhasználói munkamenet rögzítéséhez. A Application Insights másodpercek alatt elérhetővé teszi az eseményt és a munkamenetet, és számos vizualizációs, exportálási és analitikai eszközt megjelenít.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre az [Ismerkedés az egyéni szabályzatokkal](custom-policy-get-started.md)című témakör lépéseit. A bejelentkezéshez és a helyi fiókokkal való bejelentkezéshez egyéni szabályzatot kell használnia.

## <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása

Ha a Azure AD B2C használatával Application Insightst használ, mindössze annyit kell tennie, hogy létrehoz egy erőforrást, és beolvassa a kialakítási kulcsot. További információ: [Application Insights erőforrás létrehozása](../azure-monitor/app/create-new-resource.md)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy az Azure-előfizetését tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki az előfizetést tartalmazó könyvtárat. Ez a bérlő nem az Ön Azure AD B2C bérlője.
3. Válassza az **erőforrás létrehozása** lehetőséget a Azure Portal bal felső sarkában, majd keresse meg és válassza a **Application Insights**lehetőséget.
4. Kattintson a **Létrehozás** lehetőségre.
5. Adja meg az erőforrás **nevét** .
6. Az **alkalmazás típusa**mezőben válassza a **ASP.net webalkalmazás**lehetőséget.
7. Az **erőforráscsoport**területen válasszon egy meglévő csoportot, vagy adjon meg egy új csoport nevét.
8. Kattintson a **Létrehozás** lehetőségre.
4. A Application Insights erőforrás létrehozása után nyissa meg, bontsa ki az **Essentials**csomópontot, és másolja a kialakítási kulcsot.

![Application Insights áttekintése és kialakítási kulcsa](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Jogcímek meghatározása

A jogcím a Azure AD B2C szabályzat végrehajtása során ideiglenes adattárolást biztosít. A [jogcím-séma](claimsschema.md) az a hely, ahol deklarálja a jogcímeket.

1. Nyissa meg a szabályzat Extensions (bővítmények) fájlját. Például: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Keresse meg a [BuildingBlocks](buildingblocks.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Keresse meg a [ClaimsSchema](claimsschema.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá a következő jogcímeket a **ClaimsSchema** elemhez. 

```xml
<ClaimType Id="EventType">
  <DisplayName>Event type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="EventTimestamp">
  <DisplayName>Event timestamp</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="PolicyId">
  <DisplayName>Policy Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="Culture">
  <DisplayName>Culture ID</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="CorrelationId">
  <DisplayName>Correlation Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="federatedUser">
  <DisplayName>Federated user</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
<ClaimType Id="parsedDomain">
  <DisplayName>Domain name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>The domain portion of the email address.</UserHelpText>
</ClaimType>
<ClaimType Id="userInLocalDirectory">
  <DisplayName>userInLocalDirectory</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
```

## <a name="add-new-technical-profiles"></a>Új technikai profilok hozzáadása

A technikai profilok a Azure AD B2C identitási élményének keretrendszerében tekinthetők meg. Ez a táblázat a munkamenetek megnyitásához és az események közzétételéhez használt technikai profilokat határozza meg.

| Technikai profil | Tevékenység |
| ----------------- | -----|
| AppInsights – gyakori | Az Azure-beli összes technikai profilban szerepeltetni kívánt paraméterek közös készlete. |
| AppInsights – SignInRequest | Egy olyan eseményt rögzít, amely `SignInRequest` jogcímeket tartalmaz, ha bejelentkezési kérés érkezett. |
| AppInsights – UserSignUp | Egy `UserSignUp` eseményt jegyez be, amikor a felhasználó elindítja a bejelentkezési lehetőséget egy bejelentkezési vagy bejelentkezési úton. |
| AppInsights – SignInComplete | Egy `SignInComplete` hitelesítés sikeres befejezésére vonatkozó eseményt rögzít, ha a rendszer elküldje egy jogkivonatot a függő entitás alkalmazásnak. |

Adja hozzá a profilokat a *TrustFrameworkExtensions.xml* fájlhoz az alapszintű csomagból. Adja hozzá ezeket az elemeket a **ClaimsProviders** elemhez:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="EventTimestamp" PartnerClaimType="{property:EventTimestamp}" DefaultValue="{Context:DateTimeInUtc}" />
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-UserSignUp">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignUp" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
    
    <TechnicalProfile Id="AppInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Módosítsa a kialakítási kulcsot a `AppInsights-Common` technikai profilban a Application Insights erőforrás által biztosított GUID azonosítóra.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Technikai profilok hozzáadása előkészítési lépésként

Hívja meg a `AppInsights-SignInRequest` 2. lépést a beléptetési/regisztrációs kérések nyomon követéséhez:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Közvetlenül a előkészítési lépés *előtt* `SendClaims` adjon hozzá egy új lépést, amely meghívja a-t `AppInsights-UserSignup` . Akkor aktiválódik, ha a felhasználó kiválasztja a regisztrációs vagy bejelentkezési útvonalon a regisztráció gombot.

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
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AppInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Közvetlenül a előkészítési lépés után hívja meg a következőt: `SendClaims` `AppInsights-SignInComplete` . Ez a lépés egy sikeres befejezett utazást mutat be.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Az új előkészítési lépések hozzáadása után a lépéseket szekvenciálisan számozva, az 1 és N közötti egész számok kihagyása nélkül.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Töltse fel a fájlt, futtassa a szabályzatot, és tekintse meg az eseményeket

Mentse és töltse fel a *TrustFrameworkExtensions.xml* fájlt. Ezután hívja meg a függő entitásra vonatkozó házirendet az alkalmazásból, vagy használja a **Futtatás most parancsot** a Azure Portal. Másodpercek alatt az események Application Insightsban érhetők el.

1. Nyissa meg a **Application Insights** erőforrást a Azure Active Directory-bérlőben.
2. Válassza ki a **használati**  >  **eseményeket**.
3. Állítsa **be** az **elmúlt órában** és **By** **3 percen belül**.  Előfordulhat, hogy a **frissítés** elemre kell kattintania az eredmények megtekintéséhez.

![HASZNÁLAT Application Insights-események Blase](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="optional-collect-more-data"></a>Választható További adatok gyűjtése

Az igényeinek megfelelően adja hozzá a jogcím-típusokat és az eseményeket a felhasználói útra. Használhatja a [jogcím-feloldókat](claim-resolver-overview.md) vagy bármely karakterlánc-jogcím típusát, hozzáadhatja a jogcímeket egy **bemeneti jogcím** elem hozzáadásával a Application Insights eseményhez vagy a AppInsights-közös technikai profilhoz.

- A **ClaimTypeReferenceId** a jogcím típusára mutató hivatkozás.
- A **PartnerClaimType** az Azure-adatfelismerésekben megjelenő tulajdonság neve. Használja a szintaxisát `{property:NAME}` , ahol a `NAME` tulajdonságot az eseményhez adja a rendszer.
- A **DefaultValue** bármilyen karakterlánc-értéket vagy a jogcím feloldóját használja.

```xml
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

## <a name="next-steps"></a>További lépések

- További információ a [Application Insights](application-insights-technical-profile.md) technikai profilról a IEF-referenciában. 

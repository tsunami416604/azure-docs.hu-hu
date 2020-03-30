---
title: A felhasználói viselkedés nyomon követése az Application Insights segítségével
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan engedélyezheti az eseménynaplókat az Application Insightsban az Azure AD B2C felhasználói utazásaiból egyéni szabályzatok használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 03/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 687c9620ae70f7bca2b95a94dd8fe411d7348b30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246483"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Felhasználói viselkedés nyomon követése az Azure Active Directory B2C-ben az Application Insights használatával

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Az Azure Active Directory B2C (Azure AD B2C) támogatja az eseményadatok közvetlen küldését az [Application Insightsnak](../azure-monitor/app/app-insights-overview.md) az Azure AD B2C-nek biztosított instrumentációs kulcs használatával.  Az Application Insights technikai profiljával részletes és személyre szabott eseménynaplókat kaphat a felhasználói utakhoz:

* Betekintést nyerhet a felhasználók viselkedéséről.
* Saját szabályzatok elhárítása fejlesztés vagy éles környezetben.
* A teljesítmény mérése.
* Értesítéseket hozhat létre az Application Insightsból.

## <a name="how-it-works"></a>Működés

Az [Application Insights](application-insights-technical-profile.md) technikai profil határozza meg az Azure AD B2C esemény. A profil megadja az esemény nevét, a rögzített jogcímeket és a instrumentation kulcsot. Esemény közzétételéhez a technikai profil ezután vezénylési lépésként kerül hozzáadásra a [felhasználói útban.](userjourneys.md)

Az Application Insights egyesítheti az eseményeket egy korrelációs azonosító használatával egy felhasználói munkamenet rögzítéséhez. Az Application Insights másodperceken belül elérhetővé teszi az eseményt és a munkamenetet, és számos vizualizációs, exportálási és elemzési eszközt jelenít meg.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre az Egyéni házirendek – Első lépések című [lépéseit.](custom-policy-get-started.md) Rendelkeznie kell egy működő egyéni szabályzat a regisztrációhoz és a helyi fiókkal való bejelentkezéshez.

## <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása

Amikor az Azure Insights ot használja az Azure AD B2C-vel, mindössze annyit kell tennie, hogy létrehoz egy erőforrást, és beszerzi a műszerezési kulcsot. További információ: [Application Insights-erőforrás létrehozása](../azure-monitor/app/create-new-resource.md)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Győződjön meg arról, hogy az Azure-előfizetést tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetés szűrőt választja, és kiválasztja az előfizetést tartalmazó könyvtárat. Ez a bérlő nem az Azure AD B2C-bérlő.
3. Válassza az **Erőforrás létrehozása lehetőséget** az Azure Portal bal felső sarkában, majd keresse meg és válassza az Application **Insights**lehetőséget.
4. Kattintson **a Létrehozás gombra.**
5. Adja meg az erőforrás **nevét.**
6. Az **Alkalmazástípus mezőben**válassza **ASP.NET webalkalmazás**lehetőséget.
7. Az **Erőforráscsoport**csoport ban jelöljön ki egy meglévő csoportot, vagy adjon meg egy nevet egy új csoportnak.
8. Kattintson **a Létrehozás gombra.**
4. Az Application Insights-erőforrás létrehozása után nyissa meg, **bontsa ki az Essentials csomópontot,** és másolja a instrumentation kulcsot.

![Az Application Insights áttekintése és a műszerezési kulcs](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Jogcímek definiálása

A jogcím az Adatok ideiglenes tárolását biztosítja az Azure AD B2C-szabályzat végrehajtása során. A [jogcímséma](claimsschema.md) az a hely, ahol deklarálja a jogcímeket.

1. Nyissa meg a házirend bővítményfájlját. <em> `SocialAndLocalAccounts/` </em>Például.
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

A technikai profilok az Azure AD B2C identitáskezelési keretrendszerében függvényként használhatók. Ez a táblázat azokat a technikai profilokat határozza meg, amelyek munkamenetek megnyitásához és események közzétételéhez használatosak.

| Műszaki profil | Tevékenység |
| ----------------- | -----|
| AppInsights-gyakori | Az Azure Insights összes technikai profiljában szerepelnifogó közös paraméterek. |
| Appinsights-SignInRequest | Rekordokat `SignInRequest` rögzít egy jogcímekkel rendelkező eseményt, ha bejelentkezési kérelem érkezett. |
| AppInsights-UserSignUp | Rögzít `UserSignUp` egy eseményt, amikor a felhasználó elindítja a regisztrációs lehetőséget egy regisztrációs/bejelentkezési úton. |
| AppInsights-SignIn Complete | Egy `SignInComplete` hitelesítés sikeres befejezésekor rögzít egy eseményt, ha egy jogkivonatot elküldtek a függő entitás alkalmazásnak. |

Adja hozzá a profilokat a *TrustFrameworkExtensions.xml* fájlhoz az indítócsomagból. Adja hozzá ezeket az elemeket a **ClaimsProviders** elemhez:

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
> Módosítsa a technikai profil `AppInsights-Common` ban lévő instrumentation kulcsot az Application Insights-erőforrás által biztosított GUID azonosítóra.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>A technikai profilok hozzáadása vezénylési lépésként

A `AppInsights-SignInRequest` 2.

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Közvetlenül *before* a `SendClaims` vezénylési lépés `AppInsights-UserSignup`előtt adjon hozzá egy új lépést, amely meghívja a . Akkor aktiválódik, amikor a felhasználó kiválasztja a regisztrációs gombot egy regisztrációs/bejelentkezési utazássorán.

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

Közvetlenül a `SendClaims` vezénylési lépés után hívja meg a hívást. `AppInsights-SignInComplete` Ez a lépés egy sikeresen befejezett utazást jelenít meg.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Az új vezénylési lépések hozzáadása után számozza újra a lépéseket egymás után anélkül, hogy 1-től N-ig kihagyna minden egész számot.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Töltse fel a fájlt, futtassa a házirendet, és tekintse meg az eseményeket

Mentse és töltse fel a *TrustFrameworkExtensions.xml* fájlt. Ezután hívja meg a függő entitás szabályzatát az alkalmazásból, vagy használja a **Futtatás most** az Azure Portalon. Másodpercek alatt az események elérhetők az Application Insightsban.

1. Nyissa meg az **Application Insights-erőforrást** az Azure Active Directory-bérlőben.
2. Válassza a **Használati** > **események lehetőséget.**
3. Állítsa **alatt** **az utolsó óra** és **a** 3 **perc .**  Előfordulhat, hogy az eredmények megtekintéséhez a Frissítés lehetőséget kell **választania.**

![Application Insights HASZNÁLATI események Blase](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="optional-collect-more-data"></a>[Nem kötelező] További adatok gyűjtése

Adja hozzá a jogcímtípusokat és -eseményeket a felhasználói úthoz, hogy megfeleljen az igényeinek. [Jogcímfeloldók](claim-resolver-overview.md) vagy bármely karakterlánc jogcímtípus, add hozzá a jogcímeket hozzáadásával **inputjogcím-elem** az Application Insights esemény vagy az AppInsights-közös technikai profil.

- **A ClaimTypeReferenceId** egy jogcímtípusra mutató hivatkozás.
- **PartnerClaimType** az Azure Insightsban megjelenő tulajdonság neve. Használja a szintaxisát, `{property:NAME}`ahol `NAME` a tulajdonság hozzáadódik az eseményhez.
- **A DefaultValue** bármely karakterlánc-értéket vagy a jogcímfeloldót használja.

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

## <a name="next-steps"></a>További lépések

- További információ az [Application Insights](application-insights-technical-profile.md) technikai profiljáról az IEF-hivatkozásban. 
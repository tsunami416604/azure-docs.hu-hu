---
title: 'Az Azure Active Directory B2C: REST API jogcím cseréje egy vezénylési lépés |} Microsoft Docs'
description: 'Témakör: Azure Active Directory B2C egyéni házirendek, amelyek az API-k integrálása'
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/24/2017
ms.author: davidmu
ms.openlocfilehash: 0a3f69f8262e67de624d4cd97e0cad49c6a96ef0
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-an-orchestration-step"></a>Forgatókönyv: Integrálása az Azure AD B2C felhasználói út a REST API jogcímek cseréjét egy vezénylési lépés

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az identitás élmény keretrendszer (IEF) Azure Active Directory B2C alapjául szolgáló (az Azure AD B2C) lehetővé teszi, hogy a tevékenységet egy felhasználó út RESTful API-t integrálja a identitás fejlesztő.  

Ez az útmutató végén lesz létrehozása az Azure AD B2C felhasználói út, amely együttműködik a RESTful-szolgáltatásokat.

A IEF adatok jogcímekben adatokat küld és fogad vissza a jogcímeket. A REST API-exchange jogcímek:

- Egy vezénylési lépés tervezhető.
- Egy külső műveletet is elindíthatja. Például a külső adatbázis azt is naplózhat egy eseményt.
- Olyan érték beolvasása, és majd tárolja a felhasználói adatbázis használható.

A kapott jogcímek később segítségével módosíthatja a végrehajtási folyamat.

A kapcsolati érvényesítési profil tervezhet. További információkért lásd: [forgatókönyv: integrálja a REST API-t cseréjét használják az Azure AD B2C felhasználói út a jogcímeket, a felhasználói bevitel ellenőrzése](active-directory-b2c-rest-api-validation-custom.md).

A például az is, hogy amikor egy felhasználó egy profil szerkesztése végez, szeretnénk:

1. Keresse meg a felhasználó egy külső rendszerben.
2. A város, amelyen regisztrálva van-e az, hogy a felhasználó beolvasása.
3. Térjen vissza az alkalmazás jogcímként ezt az attribútumot.

## <a name="prerequisites"></a>Előfeltételek

- Egy helyi fiókot sign-Close-Up/sign-in befejezéséhez, a konfigurált Azure AD B2C-bérlő [bevezetés](active-directory-b2c-get-started-custom.md).
- REST API-végpont kommunikál. Ez az útmutató egy egyszerű Azure függvény app webhook használja példaként.
- *Ajánlott*: végezze el a [REST API-jogcímek exchange forgatókönyv érvényesítési lépésként](active-directory-b2c-rest-api-validation-custom.md).

## <a name="step-1-prepare-the-rest-api-function"></a>1. lépés: Felkészülés a REST API-függvénye

> [!NOTE]
> Ez a cikk hatókörén kívül található a REST API-függvények telepítése. [Az Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) biztosít egy kiváló eszközkészlet RESTful szolgáltatás létrehozásához a felhőben.

Beállítjuk van egy Azure függvény, amely megkapja a jogcím nevű `email`, majd visszatér az a jogcím `city` hozzárendelt értékét `Redmond`. A minta Azure függvény megtalálható [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

A `userMessage` jogcímet, amely az Azure függvény nem kötelező ebben a környezetben, és a IEF figyelmen kívül hagyja azt. Potenciálisan akár is használhatja az alkalmazásnak átadott, és később a felhasználó számára megjelenő üzenet.

```csharp
if (requestContentAsJObject.email == null)
{
    return request.CreateResponse(HttpStatusCode.BadRequest);
}

var email = ((string) requestContentAsJObject.email).ToLower();

return request.CreateResponse<ResponseContent>(
    HttpStatusCode.OK,
    new ResponseContent
    {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        userMessage = "User Found",
        city = "Redmond"
    },
    new JsonMediaTypeFormatter(),
    "application/json");
```

Egy Azure függvény alkalmazás megkönnyíti az első a függvény URL-cím, az adott függvény azonosítóval. Ebben az esetben a URL-je: https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==. Tesztelési használhatja.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworextensionsxml-file"></a>2. lépés: A RESTful API jogcímek az exchange konfigurálása a TrustFrameworExtensions.xml fájlban műszaki profil

Műszaki profilt a RESTful szolgáltatás szükséges az exchange teljes konfigurációjának. Nyissa meg a TrustFrameworkExtensions.xml fájlt, és adja hozzá a következő XML-részletet belül a `<ClaimsProvider>` elemet.

> [!NOTE]
> A következő XML-kódban, RESTful szolgáltató `Version=1.0.0.0` protokoll leírását. Vegye figyelembe azt, a függvény, amely a külső szolgáltatással együtt fog működni. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```XML
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-LookUpLoyaltyWebHook">
            <DisplayName>Check LookUpLoyalty Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==</Item>
                <Item Key="AuthenticationType">None</Item>
                <Item Key="SendClaimsIn">Body</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
            </InputClaims>
            <OutputClaims>
                <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
            </OutputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

A `<InputClaims>` elem definiálja a jogcímeket, amely a REST-szolgáltatást a IEF a kapnak. Ebben a példában a jogcím tartalmát `givenName` a REST-szolgáltatást a jogcímként való küldésének `email`.  

A `<OutputClaims>` elem definiálja a IEF várható fog a többi szolgáltatás jogcímeket. Kapott jogcímek száma, függetlenül a IEF használja csak azonosított itt. Ebben a példában a jogcím érkezett `city` kell hozzárendelni egy IEF nevű jogcím `city`.

## <a name="step-3-add-the-new-claim-city-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>3. lépés:, Adja hozzá az új jogcímet `city` a sémának a TrustFrameworkExtensions.xml fájl

A jogcímek `city` nincs még definiálva a sémában. Igen, hozzáadása a elemen belül `<BuildingBlocks>`. Ez az elem a TrustFrameworkExtensions.xml fájl elején található.

```XML
<BuildingBlocks>
    <!--The claimtype city must be added to the TrustFrameworkPolicy-->
    <!-- You can add new claims in the BASE file Section III, or in the extensions file-->
    <ClaimsSchema>
        <ClaimType Id="city">
            <DisplayName>City</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your city</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-4-include-the-rest-service-claims-exchange-as-an-orchestration-step-in-your-profile-edit-user-journey-in-trustframeworkextensionsxml"></a>4. lépés: A profil szerkesztése felhasználói megtett út TrustFrameworkExtensions.xml lépést az orchestration közé a többi szolgáltatás jogcímek exchange

Adjon hozzá egy lépést, amely a profil szerkesztése felhasználói út után a felhasználó hitelesítése (vezénylési lépésekből 1-4 a következő XML-kódban), és a felhasználó már rendelkezik a frissített profil adatait (5. lépés).

> [!NOTE]
> Nincsenek a REST API-hívás helyének egy vezénylési lépés számos használhatók. Az orchestration lépésként használat egy frissítést adunk ki a külső rendszerek, a felhasználó sikeresen befejezte egy feladat, például az első regisztráció után, akár egy profil frissítéséhez megőrizheti az adatok szinkronizálása. Ebben az esetben szolgál a profil szerkesztése után az alkalmazás foglalt információk révén.

Másolás a profil szerkesztése felhasználói út XML-kódot a TrustFrameworkBase.xml fájlból a TrustFrameworkExtensions.xml fájl belül a `<UserJourneys>` elemet. Végezze el a módosítást, a 6. lépés.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
    <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
    </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Ha a sorrend nem egyezik meg a verziójával, győződjön meg arról, hogy lépése előtt helyezze be a kódját a `ClaimsExchange` típus `SendClaims`.

A felhasználó útra végső XML kell kinéznie:

```XML
<UserJourney Id="ProfileEdit">
    <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
                <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
                <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
                <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="ClaimsExchange">
            <Preconditions>
                <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
                    <Value>authenticationSource</Value>
                    <Value>localAccountAuthentication</Value>
                    <Action>SkipThisOrchestrationStep</Action>
                </Precondition>
            </Preconditions>
            <ClaimsExchanges>
                <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="ClaimsExchange">
            <Preconditions>
                <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
                    <Value>authenticationSource</Value>
                    <Value>socialIdpAuthentication</Value>
                    <Action>SkipThisOrchestrationStep</Action>
                </Precondition>
            </Preconditions>
            <ClaimsExchanges>
                <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="5" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <!-- Add a step 6 to the user journey before the JWT token is created-->
        <OrchestrationStep Order="6" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    </OrchestrationSteps>
    <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="step-5-add-the-claim-city-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>5. lépés:, Vegye fel a jogcím `city` a függő entitás házirend fájl, a jogcím kap az alkalmazáshoz

Szerkessze a ProfileEdit.xml függő entitásonkénti (RP) fájlt, és módosítsa a `<TechnicalProfile Id="PolicyProfile">` adja hozzá a következő elem: `<OutputClaim ClaimTypeReferenceId="city" />`.

Miután az új jogcím, a műszaki profil néz ki:

```XML
<DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="step-6-upload-your-changes-and-test"></a>6. lépés: Töltse fel a módosításokat, és tesztelése

A házirend meglévő verzióinak felülírását.

1.  (Nem kötelező:) Mentse a meglévő verziót (Letöltés) a bővítmények fájl folytatás előtt. Kezdeti összetettségét alacsony megtartásához, javasoljuk, hogy nem töltsön a bővítmények fájl több verziója.
2.  (Nem kötelező:) Nevezze át az új verziót a házirend-azonosító a házirend szerkesztése fájl módosításával `PolicyId="B2C_1A_TrustFrameworkProfileEdit"`.
3.  A bővítmények-fájl feltöltése.
4.  A házirend szerkesztése RP-fájl feltöltése.
5.  Használjon **Futtatás most** tesztelése a házirendet. Tekintse át a jogkivonatot, amely a IEF visszaadja az alkalmazásnak.

Ha minden megfelelően van beállítva, a jogkivonat tartalmazza az új jogcímet `city`, a értékű `Redmond`.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>További lépések

[A REST API-t használják a ellenőrzési lépés](active-directory-b2c-rest-api-validation-custom.md)

[Módosítsa a profil szerkesztése további információkat kell gyűjteni a felhasználóktól](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

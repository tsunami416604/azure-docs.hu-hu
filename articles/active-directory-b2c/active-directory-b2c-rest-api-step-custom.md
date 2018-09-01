---
title: REST API-jogcímek cseréje, egy vezénylési lépés az Azure Active Directory B2C |} A Microsoft Docs
description: A témakör az Azure Active Directory B2C-vel egyéni szabályzatok, amelyek integrálhatók az API-k.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/24/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: dddb42f53d4bb59113df937799bd4de10d31491c
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338779"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-an-orchestration-step"></a>Forgatókönyv: Integrálása az Azure AD B2C felhasználói interakciósorozatban szereplő REST API-val jogcím cseréje egy vezénylési lépés

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az identitás élmény keretrendszer (IEF) alapjául szolgáló Azure Active Directory B2C (Azure AD B2C-vel) lehetővé teszi, hogy az identitás fejlesztői integrálható a RESTful API-t a felhasználói út használata közben.  

Ez az útmutató végén lesz egy Azure AD B2C felhasználói interakciósorozat, együttműködő létrehozása a REST-alapú szolgáltatások.

A IEF adatokat küld a jogcímeket, valamint adatokat fogad újra a jogcím. A REST API-val jogcímcsere:

- Megtervezhetők úgy, mint egy vezénylési lépés.
- Egy külső műveletet is indíthat. Például a külső adatbázis azt is naplózhat egy eseményt.
- Olyan érték beolvasása, és tárolja a felhasználói adatbázis használható.

A kapott jogcímek később segítségével módosítsa a folyamat végrehajtása.

A kapcsolati érvényesítési profil is tervezhet. További információkért lásd: [forgatókönyv: integrálása a REST API-t az Azure AD B2C felhasználói interakciósorozatban szereplő cseréje jogcímek a felhasználói bevitel auditáló](active-directory-b2c-rest-api-validation-custom.md).

A forgatókönyv a következő, hogy a felhasználók a profilszerkesztés hajt végre, ha azt szeretné:

1. Keresse meg a felhasználó egy külső rendszerben.
2. A város, ahol regisztrálva van-e az adott felhasználó kaphat.
3. Ezt az attribútumot térjen vissza az alkalmazás jogcímként.

## <a name="prerequisites"></a>Előfeltételek

- Az Azure AD B2C-bérlő egy helyi fiók regisztrálási-regisztrálási vagy bejelentkezési, végrehajtásához leírtak szerint konfigurálva [bevezetés](active-directory-b2c-get-started-custom.md).
- REST API-végpont használatával kommunikálhat. Ez az útmutató egy egyszerű Azure-függvény alkalmazás webhook használja példaként.
- *Ajánlott*: végezze el a [REST API-ellenőrzésként exchange forgatókönyv jogcímek](active-directory-b2c-rest-api-validation-custom.md).

## <a name="step-1-prepare-the-rest-api-function"></a>1. lépés: Készítse elő a REST API-függvénye

> [!NOTE]
> REST API-függvények, a telepítő nem ez a cikk foglalkozik. [Az Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) biztosít egy kiváló eszközkészlet RESTful szolgáltatás létrehozásához a felhőben.

Nem kell beállítani egy Azure-függvényt, amely megkapja a nevű jogcím `email`, majd visszaadja a jogcím `city` hozzárendelt értékét `Redmond`. A minta Azure-függvény megtalálható [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

A `userMessage` jogcímet, amelyet az Azure-függvény adja vissza. nem kötelező ebben a környezetben, és a IEF figyelmen kívül hagyja azt. Potenciálisan használhatja azt az alkalmazásnak átadott és jelenik meg a felhasználó újabb üzenetnek számít.

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

Azure-függvényalkalmazás megkönnyíti a függvény URL-címére, amely tartalmazza az azonosító az adott függvény. Ebben az esetben a URL-je: https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==. Használhatja a teszteléshez.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworextensionsxml-file"></a>2. lépés: Az RESTful API jogcímcsere konfigurálása TrustFrameworExtensions.xml fájlban technikai profil

Egy technikai profil a RESTful szolgáltatás kívánt exchange teljes konfigurációját. Nyissa meg a TrustFrameworkExtensions.xml fájlt, és adja hozzá a következő XML-részletet belül a `<ClaimsProvider>` elemet.

> [!NOTE]
> A következő XML formátumú, RESTful szolgáltató `Version=1.0.0.0` protokoll leírását. Érdemes használni, mint a függvényt, amely a külső szolgáltatás használni fog. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

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

A `<InputClaims>` elem definiálja a jogcímeket, amely a REST-szolgáltatás, a IEF kapnak. Ebben a példában a jogcím tartalmát `givenName` küld a REST-szolgáltatás, mint a jogcím `email`.  

A `<OutputClaims>` elem definiálja a jogcímeket a IEF rendszer várható a REST-szolgáltatást. A jogcímszolgáltatótól kapott száma, függetlenül a IEF fogja használni csak azok azonosított itt. Ebben a példában egy jogcímet kapott `city` lesz rendelve egy IEF nevű jogcím `city`.

## <a name="step-3-add-the-new-claim-city-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>3. lépés: Adja hozzá az új jogcímet `city` a sémának a TrustFrameworkExtensions.xml fájl

Az igényt `city` még nem határozott bármilyen környezetben a sémában. Adjunk hozzá egy definíciót a elemen belül `<BuildingBlocks>`. Ez az elem a TrustFrameworkExtensions.xml fájl elején annak.

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

## <a name="step-4-include-the-rest-service-claims-exchange-as-an-orchestration-step-in-your-profile-edit-user-journey-in-trustframeworkextensionsxml"></a>. 4. lépés: Egy vezénylési lépés a a profil szerkesztése felhasználói interakciósorozatban szereplő TrustFrameworkExtensions.xml közé a REST-szolgáltatás jogcímcsere

Adjon hozzá egy lépéssel a profil szerkesztése felhasználói interakciósorozat, ha a felhasználó már hitelesített (vezénylési lépésekből 1 – 4 a következő XML formátumú), és a felhasználó már rendelkezik a frissített profil adatait (5. lépés).

> [!NOTE]
> Nincsenek számos használati esetek, amikor egy vezénylési lépés használható a REST API-hívás. Egy vezénylési lépésként használat frissítés után a felhasználó sikeresen végrehajtotta egy feladat, például az első regisztráció külső rendszerre, vagy a profil frissítése, hogy az adatok szinkronizálását. Ebben az esetben szolgál, mivel megvédi a profil szerkesztése után az alkalmazásnak megadott információkat.

Másolás a profil szerkesztése felhasználói interakciósorozat XML kódot a TrustFrameworkBase.xml fájlból a TrustFrameworkExtensions.xml fájllal belül a `<UserJourneys>` elemet. Végezze el a módosítást, a 6. lépés.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
    <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
    </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Ha a rendelés nem egyezik meg a verziót, győződjön meg arról, hogy Ön a kódot is beilleszthet a lépés előtt a `ClaimsExchange` típus `SendClaims`.

A felhasználói út tartalomdefinícióinak végső XML kell kinéznie:

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

## <a name="step-5-add-the-claim-city-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>5. lépés: Adja hozzá a jogcím `city` a függő házirend-fájlba a jogcímet küld az alkalmazás

Szerkessze a ProfileEdit.xml függő entitásonkénti (RP) fájlt, és módosítsa a `<TechnicalProfile Id="PolicyProfile">` adja hozzá a következő elem: `<OutputClaim ClaimTypeReferenceId="city" />`.

Miután az új jogcímet ad hozzá, a technikai profil néz ki:

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

Írja felül a meglévő verziói a szabályzatot.

1.  (Nem kötelező:) Mentse a meglévő verziót (Letöltés) a bővítmények fájl folytatás előtt. A kezdeti összetettsége alacsony megtartani, azt javasoljuk, ne töltse fel a bővítmények fájl több verzióját.
2.  (Nem kötelező:) Nevezze át az új verziójának a házirend-azonosító, a házirend szerkesztése fájl módosításával `PolicyId="B2C_1A_TrustFrameworkProfileEdit"`.
3.  Töltse fel a bővítmények fájlt.
4.  A szabályzat szerkesztése RP-fájl feltöltése.
5.  Használat **Futtatás most** a szabályzat teszteléséhez. Tekintse át a jogkivonatot, amely a IEF adja vissza az alkalmazásba.

Ha minden helyesen van beállítva, a jogkivonat tartalmazza az új jogcímet `city`, a következő értékkel `Redmond`.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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

[Ellenőrzésként egy REST API használata](active-directory-b2c-rest-api-validation-custom.md)

[További információkat kell gyűjteni a felhasználók a profilszerkesztés módosítása](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

---
title: Az erőforrás tulajdonosának jelszavas hitelesítő adatainak folyamata konfigurálása az Azure Active Directory B2C |} A Microsoft Docs
description: Ismerje meg, hogy az erőforrás tulajdonosának jelszavas hitelesítő adatainak folyamata konfigurálása az Azure Active Directory B2C-t.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7dffa1480be73f1dbf5e99d11fd8d33eb2ab9038
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55196412"
---
# <a name="configure-the-resource-owner-password-credentials-flow-in-azure-active-directory-b2c-using-a-custom-policy"></a>Az erőforrás tulajdonosának jelszavas hitelesítő adatainak folyamata az Azure Active Directory B2C egy egyéni házirend használatával konfigurálja

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory (Azure AD) B2C-vel az erőforrás tulajdonosának jelszavas hitelesítő adatainak (ROPC) folyamata egy OAuth standard hitelesítési folyamatát. Ezt a folyamatot, az alkalmazás, más néven a függő entitás cseréje jogkivonatokat érvényes hitelesítő adatokat. A hitelesítő adatok közé tartozik a felhasználói Azonosítót és jelszót. A visszaadott jogkivonatok egy azonosító jogkivonat, a hozzáférési jogkivonatot és a egy frissítési jogkivonatot. 

A következő beállítások támogatottak a ROPC folyamatban:

- **Natív ügyfél** -felhasználói beavatkozás során a hitelesítés során történik, ha a felhasználó ügyféloldali eszközön fut kódja.
- **Nyilvános client flow** – csak a felhasználó hitelesítő adatait, amely gyűjti az adatokat az alkalmazások által az API-hívás küldi el. A hitelesítő adatokat, az alkalmazás nem küld.
- **Új jogcímeket adhatnak hozzá** – új jogcímeket adhatnak hozzá az azonosító jogkivonat tartalma módosítható. 

A következő folyamatok nem támogatottak:

- **Kiszolgálók közötti** – az identity protection rendszer kell egy megbízható IP-címet a hívó (a natív ügyfél) a interakció részeként összegyűjtött. Kiszolgálóoldali API-hívással, csak a kiszolgáló IP-címet használja. Ha túl sok bejelentkezések nem sikerül, az identity protection rendszer előfordulhat, hogy tekintse meg egy ismétlődő IP-cím egy támadó.
- **Egyetlen lap alkalmazás** – egy előtér-alkalmazás, amely elsősorban a JavaScript nyelven írt. Az alkalmazás gyakran, például az AngularJS, az Ember.js vagy a Durandal keretrendszer használatával írt.
- **Bizalmas client flow** – az alkalmazás ügyfél-azonosító érvényességét, de az alkalmazás titkos nem.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a [az Azure Active Directory B2C-vel egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Egy alkalmazás regisztrálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**. 
4. Válassza ki **alkalmazások**, majd válassza ki **Hozzáadás**.
5. Adja meg egy nevet az alkalmazásnak, például *ROPC_Auth_app*.
6. Válassza ki **nem** a **Web App/Web API**, majd válassza ki **Igen** a **natív ügyfél**.
7. Az összes többi értéket hagyja, és válassza ki **létrehozás**.
8. Válassza ki az új alkalmazást, és jegyezze fel az Alkalmazásazonosítót későbbi használatra.

##  <a name="create-a-resource-owner-policy"></a>Erőforrás tulajdonosának szabályzat létrehozása

1. Nyissa meg a *TrustFrameworkExtensions.xml* fájlt.
2. Ha már nem létezik, adjon hozzá egy **ClaimsSchema** elem és az alárendelt elemei alapján első elemeként a **BuildingBlocks** elem:

    ```XML
    <ClaimsSchema>
      <ClaimType Id="logonIdentifier">
        <DisplayName>User name or email address that the user can use to sign in</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="resource">
        <DisplayName>The resource parameter passes to the ROPC endpoint</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokenIssuedOnDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokensValidFromDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
    </ClaimsSchema>
    ```

3. Után **ClaimsSchema**, adjon hozzá egy **ClaimsTransformations** elem és az alárendelt elemei, a **BuildingBlocks** elem:

    ```XML
    <ClaimsTransformations>
      <ClaimsTransformation Id="CreateSubjectClaimFromObjectID" TransformationMethod="CreateStringClaim">
        <InputParameters>
          <InputParameter Id="value" DataType="string" Value="Not supported currently. Use oid claim." />
        </InputParameters>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="sub" TransformationClaimType="createdClaim" />
        </OutputClaims>
      </ClaimsTransformation>
    
      <ClaimsTransformation Id="AssertRefreshTokenIssuedLaterThanValidFromDate" TransformationMethod="AssertDateTimeIsGreaterThan">
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" TransformationClaimType="leftOperand" />
          <InputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" TransformationClaimType="rightOperand" />
        </InputClaims>
        <InputParameters>
          <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
          <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
        </InputParameters>
      </ClaimsTransformation>
    </ClaimsTransformations>
    ```

4. Keresse meg a **ClaimsProvider** elem, amely rendelkezik egy **DisplayName** , `Local Account SignIn` , és adja hozzá a következő technikai profil:

    ```XML
    <TechnicalProfile Id="ResourceOwnerPasswordCredentials-OAUTH2">
      <DisplayName>Local Account SignIn</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
        <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
        <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
        <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
        <Item Key="response_types">id_token</Item>
        <Item Key="response_mode">query</Item>
        <Item Key="scope">email openid</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="logonIdentifier" PartnerClaimType="username" Required="true" DefaultValue="{OIDC:Username}"/>
        <InputClaim ClaimTypeReferenceId="password" Required="true" DefaultValue="{OIDC:Password}" />
        <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
        <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
        <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
        <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="00000000-0000-0000-0000-000000000000" />
        <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="00000000-0000-0000-0000-000000000000" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
      </OutputClaimsTransformations>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    ```

    Cserélje le a **DefaultValue** , **client_id** és **erőforrás_azonosítója** ProxyIdentityExperienceFramework alkalmazás, amelyet az alkalmazás azonosítójával a az előfeltételként szolgáló oktatóanyagot.

5. Adjon hozzá következő **ClaimsProvider** , a technikai profilok olyan elemet a **ClaimsProviders** elem:

    ```XML
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserReadUsingObjectId-CheckRefreshTokenDate">
          <Metadata>
            <Item Key="Operation">Read</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="AssertRefreshTokenIssuedLaterThanValidFromDate" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
          </OutputClaimsTransformations>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-RefreshTokenReadAndSetup">
          <DisplayName>Trustframework Policy Engine Refresh Token Setup Technical Profile</DisplayName>
          <Protocol Name="None" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="JwtIssuer">
          <Metadata>
            <!-- Point to the redeem refresh token user journey-->
            <Item Key="RefreshTokenUserJourneyId">ResourceOwnerPasswordCredentials-RedeemRefreshToken</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>    
    ```

6. Adjon hozzá egy **UserJourneys** elem és az alárendelt elemei, a **TrustFrameworkPolicy** elem:

    ```XML
    <UserJourney Id="ResourceOwnerPasswordCredentials">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
        <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="ResourceOwnerFlow" TechnicalProfileReferenceId="ResourceOwnerPasswordCredentials-OAUTH2" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    <UserJourney Id="ResourceOwnerPasswordCredentials-RedeemRefreshToken">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="RefreshTokenSetupExchange" TechnicalProfileReferenceId="SM-RefreshTokenReadAndSetup" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="CheckRefreshTokenDateFromAadExchange" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId-CheckRefreshTokenDate" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    ```

7. Az a **egyéni szabályzatok** az Azure AD B2C-bérlő, válassza a lap **szabályzat feltöltése**.
8. Engedélyezése **szabályzat felülírása, ha létezik**, és keresse meg és válassza ki a *TrustFrameworkExtensions.xml* fájlt.
9. Kattintson a **Feltöltés** gombra.

## <a name="create-a-relying-party-file"></a>Hozzon létre egy függő entitás fájlt

Ezután frissítse a függő entitás fájlt, amely az Ön által létrehozott felhasználói interakciósorozat kezdeményezi:

1. Készítsen másolatot *SignUpOrSignin.xml* a munkakönyvtárban fájlt, és nevezze át, hogy *ROPC_Auth.xml*.
2. Nyissa meg az új fájlt, és módosítsa az értéket, a **PolicyId** az attribútum **TrustFrameworkPolicy** egy egyedi értékre. A házirend-azonosító a házirend nevét. Ha például **B2C_1A_ROPC_Auth**.
3. Módosítsa a **referenceid megadása** attribútum **DefaultUserJourney** való `ResourceOwnerPasswordCredentials`.
4. Módosítsa a **OutputClaims** elem csak a következő jogcímeket tartalmaz:
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. Az a **egyéni szabályzatok** az Azure AD B2C-bérlő, válassza a lap **szabályzat feltöltése**.
6. Engedélyezése **szabályzat felülírása, ha létezik**, és keresse meg és válassza ki a *TrustFrameworkExtensions.xml* fájlt.
7. Kattintson a **Feltöltés** gombra.

## <a name="test-the-policy"></a>A házirend tesztelése

A kedvenc API-fejlesztési alkalmazás használatával létrehozhat egy olyan API-hívás, és tekintse át a válasz lehet hibákat keresni a házirend. Ebben a példában az alábbi információkkal, mint a POST-kérés törzse például egy hívás össze:

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- Cserélje le `your-tenant-name` az Azure AD B2C-bérlő nevével.
- Cserélje le `B2C_1A_ROPC_Auth` az erőforrás tulajdonosa hitelesítő adatok jelszóházirend teljes nevét.

| Kulcs | Value |
| --- | ----- |
| felhasználónév | `user-account` |
| jelszó | `password1` |
| grant_type | jelszó |
| scope | openid `application-id` offline_access |
| client_id | `application-id` |
| response_type | token id_token |

- Cserélje le `user-account` egy felhasználói fiók a bérlő nevével.
- Cserélje le `password1` a felhasználói fiókhoz jelszót.
- Cserélje le `application-id` az alkalmazás azonosítójával, a *ROPC_Auth_app* regisztráció. 
- *Offline_access* nem kötelező, ha szeretne kapni a frissítési jogkivonatot.

A tényleges POST-kérés a következő példához hasonlóan néz ki:

```HTTPS
POST /yourtenant.onmicrosoft.com/oauth2/v2.0/token?B2C_1_ROPC_Auth HTTP/1.1
Host: yourtenant.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```


Offline hozzáférés a sikeres válasz az alábbi példához hasonlóan néz ki:

```JSON
{ 
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...", 
    "token_type": "Bearer", 
    "expires_in": "3600", 
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...", 
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..." 
} 
```

## <a name="redeem-a-refresh-token"></a>A frissítési jogkivonat beváltása

Itt látható egy hasonló a POST híváson hozhatnak létre. Az adatokat használja az alábbi táblázatban a kérelem törzse:

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- Cserélje le `your-tenant-name` az Azure AD B2C-bérlő nevével.
- Cserélje le `B2C_1A_ROPC_Auth` az erőforrás tulajdonosa hitelesítő adatok jelszóházirend teljes nevét.

| Kulcs | Value |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | `application-id` |
| erőforrás | `application-id` |
| refresh_token | `refresh-token` |

- Cserélje le `application-id` az alkalmazás azonosítójával, a *ROPC_Auth_app* regisztráció.
- Cserélje le `refresh-token` együtt a **refresh_token** , amely az előző válaszban küldték. 

A sikeres válasz az alábbi példához hasonlóan néz ki:

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhT...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQn...",
    "token_type": "Bearer",
    "not_before": 1533672990,
    "expires_in": 3600,
    "expires_on": 1533676590,
    "resource": "bef2222d56-552f-4a5b-b90a-1988a7d634c3",
    "id_token_expires_in": 3600,
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI1MTZmYzA2NS1mZjM2LTRiOTMtYWE1YS1kNmVlZGE3Y2JhYzgiLCJzdWIiOm51bGwsIm5hbWUiOiJEYXZpZE11IiwicHJlZmVycmVkX3VzZXJuYW1lIjpudWxsLCJpZHAiOiJMb2NhbEFjY291bnQifQ",
    "refresh_token": "eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMCIsInppcCI6IkRlZmxhdGUiLCJzZXIiOiIxLjAi...",
    "refresh_token_expires_in": 1209600
}
```

## <a name="use-a-native-sdk-or-app-auth"></a>Egy natív SDK-t vagy alkalmazás-alapú hitelesítés használata

Az Azure AD B2C megfelel-e nyilvános ügyfél erőforrás tulajdonosának jelszavas hitelesítő adatai az OAuth 2.0-s előírásoknak, és a legtöbb ügyfél SDK-k kompatibilisnek kell lennie. A legfrissebb információkért lásd: [natív App SDK az OAuth 2.0 és OpenID Connect modern ajánlott eljárások végrehajtására](https://appauth.io/).

## <a name="next-steps"></a>További lépések

- Az ebben a forgatókönyvben egy teljes példa a [egyéni házirendet kezdőcsomag az Azure Active Directory B2C](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/source/aadb2c-ief-ropc).
- További információ az Azure Active Directory B2C a által használt jogkivonatokat a [jogkivonat-referencia](active-directory-b2c-reference-tokens.md).



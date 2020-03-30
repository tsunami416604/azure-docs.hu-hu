---
title: Az erőforrás-tulajdonos jelszóhitelesítő adatainak konfigurálása egyéni házirendekkel
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan konfigurálhatja az erőforrás-tulajdonos jelszó hitelesítő adatok (ROPC) folyamat egyéni szabályzatok használatával az Azure Active Directory B2C használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 55b4750d2c601a4d3c66bcd8235a9718d6daaf9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186997"
---
# <a name="configure-the-resource-owner-password-credentials-flow-in-azure-active-directory-b2c-using-a-custom-policy"></a>Az erőforrás-tulajdonos jelszóhitelesítő adatainak konfigurálása az Azure Active Directory B2C szolgáltatásában egyéni házirend használatával

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Az Azure Active Directory B2C (Azure AD B2C) az erőforrás-tulajdonos jelszó hitelesítő adatok (ROPC) folyamat egy OAuth szabványos hitelesítési folyamat. Ebben a folyamatban egy alkalmazás, más néven a függő entitás, a jogkivonatok érvényes hitelesítő adatait cseréli. A hitelesítő adatok közé tartozik a felhasználói azonosító és a jelszó. A visszaadott jogkivonatok egy azonosító jogkivonat, hozzáférési jogkivonat és egy frissítési jogkivonat.

[!INCLUDE [active-directory-b2c-ropc-notes](../../includes/active-directory-b2c-ropc-notes.md)]

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre az [Egyéni szabályzatok első lépéseit az Azure Active Directory B2C-ben](custom-policy-get-started.md)című részben.

## <a name="register-an-application"></a>Egy alkalmazás regisztrálása

[!INCLUDE [active-directory-b2c-appreg-ropc](../../includes/active-directory-b2c-appreg-ropc.md)]

##  <a name="create-a-resource-owner-policy"></a>Erőforrás-tulajdonosi házirend létrehozása

1. Nyissa meg a *TrustFrameworkExtensions.xml* fájlt.
2. Ha még nem létezik, adjon hozzá egy **ClaimsSchema** elemet és annak gyermekelemeit a **BuildingBlocks** elem első elemeként:

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

3. A **ClaimsSchema**után adjon hozzá egy **ClaimsTransformations** elemet és annak gyermekelemeit a **BuildingBlocks** elemhez:

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

4. Keresse meg a **DisplayName nevű** `Local Account SignIn` **ClaimsProvider** elemet, és adja hozzá a következő technikai profilt:

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

    Cserélje le a **DefaultValue** client_id az előfeltételként létrehozott ProxyIdentityExperienceFramework alkalmazás alkalmazásazonosítójára. **client_id** Ezután cserélje le a **DefaultValue** of **resource_id-t** az előfeltételként szolgáló oktatóanyagban is létrehozott IdentityExperienceFramework alkalmazás alkalmazásazonosítójára.

5. Adja hozzá a következő **ClaimsProvider** elemeket a technikai profiljukkal a **ClaimsProviders** elemhez:

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

6. **UserJourneys** elem és gyermekelemeinek hozzáadása a **TrustFrameworkPolicy** elemhez:

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

7. Az Azure AD B2C-bérlő **egyéni szabályzatok** lapján válassza **a Feltöltési szabályzat lehetőséget.**
8. Engedélyezze **a házirend felülírását, ha létezik,** majd keresse meg és jelölje ki a *TrustFrameworkExtensions.xml* fájlt.
9. Kattintson a **Feltöltés** gombra.

## <a name="create-a-relying-party-file"></a>Függő entitásfájl létrehozása

Ezután frissítse a létrehozott felhasználói utat kezdeményező függő entitásfájlt:

1. Készítsen másolatot a *SignUpOrSignin.xml* fájlról a munkakönyvtárában, és nevezze át *ROPC_Auth.xml*fájlra.
2. Nyissa meg az új fájlt, és módosítsa a **TrustFrameworkPolicy** **PolicyId** attribútumának értékét egyedi értékre. A házirend azonosítója a házirend neve. Például **B2C_1A_ROPC_Auth**.
3. Módosítsa a `ResourceOwnerPasswordCredentials` **DefaultUserJourney (DefaultUserJourney)** **ReferenceId** attribútumának értékét .
4. Módosítsa úgy a **OutputClaims** elemet, hogy csak a következő jogcímeket tartalmazza:

    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. Az Azure AD B2C-bérlő **egyéni szabályzatok** lapján válassza **a Feltöltési szabályzat lehetőséget.**
6. Engedélyezze **a házirend felülírása, ha létezik,** majd tallózással keresse meg és jelölje ki a *ROPC_Auth.xml* fájlt.
7. Kattintson a **Feltöltés** gombra.

## <a name="test-the-policy"></a>A szabályzat tesztelése

Használja a kedvenc API-fejlesztő alkalmazás api-hívás létrehozásához, és tekintse át a választ a szabályzat hibakereséséhez. A mint ez a példa, a következő információkkal hozhat létre a POST-kérelem törzseként:

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- Cserélje `your-tenant-name` le az Azure AD B2C-bérlő nevét.
- Cserélje `B2C_1A_ROPC_Auth` le az erőforrás-tulajdonos jelszóhitelesítő házirendjének teljes nevét.

| Kulcs | Érték |
| --- | ----- |
| felhasználónév | `user-account` |
| jelszó | `password1` |
| grant_type | jelszó |
| scope | nyitott `application-id` offline_access |
| client_id | `application-id` |
| response_type | token id_token |

- Cserélje `user-account` le a bérlőben lévő felhasználói fiók nevét.
- Cserélje `password1` le a felhasználói fiók jelszavával.
- Cserélje `application-id` le a *ROPC_Auth_app* regisztrációból származó alkalmazásazonosítóra.
- *Offline_access* nem kötelező, ha frissítési jogkivonatot szeretne kapni.

A tényleges POST kérelem a következő példához hasonlóan néz ki:

```HTTPS
POST /yourtenant.onmicrosoft.com/oauth2/v2.0/token?B2C_1_ROPC_Auth HTTP/1.1
Host: yourtenant.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```

Az offline hozzáféréssel rendelkező sikeres válasz a következő példához hasonlóan néz ki:

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...",
    "token_type": "Bearer",
    "expires_in": "3600",
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..."
}
```

## <a name="redeem-a-refresh-token"></a>Frissítési token beváltása

Építsünk egy POST hívást, mint az itt látható. A kérelem törzseként használja az alábbi táblázatban szereplő információkat:

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- Cserélje `your-tenant-name` le az Azure AD B2C-bérlő nevét.
- Cserélje `B2C_1A_ROPC_Auth` le az erőforrás-tulajdonos jelszóhitelesítő házirendjének teljes nevét.

| Kulcs | Érték |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | `application-id` |
| Erőforrás | `application-id` |
| refresh_token | `refresh-token` |

- Cserélje `application-id` le a *ROPC_Auth_app* regisztrációból származó alkalmazásazonosítóra.
- Cserélje `refresh-token` le az előző válaszban visszaküldött **refresh_token.**

A sikeres válasz a következő példához hasonlóan néz ki:

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

## <a name="use-a-native-sdk-or-app-auth"></a>Natív SDK vagy App-Auth használata

Az Azure AD B2C megfelel az OAuth 2.0 szabványoknak a nyilvános ügyfél-erőforrás-tulajdonosi jelszó hitelesítő adatokhoz, és kompatibilisnek kell lennie a legtöbb ügyfél SDK-val. A legfrissebb információkért lásd: [Native App SDK for OAuth 2.0 és OpenID Connect végrehajtási modern gyakorlati tanácsok](https://appauth.io/).

## <a name="next-steps"></a>További lépések

- Tekintse meg a forgatókönyv teljes példáját az [Azure Active Directory B2C egyéni szabályzatkezdő csomagjában.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/source/aadb2c-ief-ropc)
- Az Azure Active Directory B2C által használt jogkivonatokról a Token hivatkozásban olvashat [bővebben.](tokens-overview.md)

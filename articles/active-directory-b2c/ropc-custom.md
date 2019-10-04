---
title: Az erőforrás-tulajdonos jelszava hitelesítő adatainak konfigurálása a Azure Active Directory B2Cban | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja az erőforrás-tulajdonosi jelszó hitelesítő adatait a Azure Active Directory B2Cban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2f3eb2c0071eecb20bbf5616a01c80e55645207a
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71678139"
---
# <a name="configure-the-resource-owner-password-credentials-flow-in-azure-active-directory-b2c-using-a-custom-policy"></a>Az erőforrás-tulajdonosi jelszó hitelesítő adatainak konfigurálása Azure Active Directory B2C egyéni házirend használatával

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure Active Directory B2C (Azure AD B2C) esetében az erőforrás-tulajdonos jelszava hitelesítő adatai (ROPC) folyamata egy szabványos OAuth-hitelesítési folyamat. Ebben a folyamatban egy alkalmazás, más néven a függő entitás, érvényes hitelesítő adatokat cserél a tokenekhez. A hitelesítő adatok tartalmazzák a felhasználói azonosítót és a jelszót. A visszaadott tokenek azonosító tokenek, hozzáférési tokenek és frissítési tokenek.

A ROPC folyamat a következő beállításokat támogatja:

- **Natív ügyfél** -felhasználói interakció a hitelesítés során akkor történik meg, amikor a kód egy felhasználói oldali eszközön fut.
- Az API-hívásban az alkalmazás által összegyűjtött **nyilvános ügyféloldali** felhasználói hitelesítő adatok lesznek elküldve. Az alkalmazás hitelesítő adatai nem lesznek elküldve.
- **Új jogcímek hozzáadása** – az azonosító jogkivonat tartalma módosítható új jogcímek hozzáadásához.

A következő folyamatok nem támogatottak:

- **Kiszolgáló – kiszolgáló** – az Identity Protection rendszernek a kapcsolat részeként a hívótól (a natív ügyféltől) gyűjtött megbízható IP-címet kell tartalmaznia. Kiszolgálóoldali API-hívás esetén csak a kiszolgáló IP-címe van használatban. Ha túl sok bejelentkezés meghiúsul, az Identity Protection rendszer egy ismétlődő IP-címet is megvizsgálhat támadóként.
- **Egyoldalas alkalmazás** – az előtér-alkalmazás, amely elsősorban JavaScript nyelven íródott. Az alkalmazást gyakran olyan keretrendszerrel kell megírni, mint például a AngularJS, az izzó. js vagy a Durand.
- **Bizalmas ügyféloldali folyamat** – az alkalmazás ügyfél-azonosítója érvényesítve van, de az alkalmazás titkos kulcsa nem.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a következő témakörben ismertetett lépéseket: Ismerkedés az [Egyéni szabályzatokkal Azure Active Directory B2Cban](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Alkalmazás regisztrálása

[!INCLUDE [active-directory-b2c-appreg-ropc](../../includes/active-directory-b2c-appreg-ropc.md)]

##  <a name="create-a-resource-owner-policy"></a>Erőforrás-tulajdonosi szabályzat létrehozása

1. Nyissa meg a *TrustFrameworkExtensions. XML* fájlt.
2. Ha még nem létezik, vegyen fel egy **ClaimsSchema** elemet és annak alárendelt elemeit az **BuildingBlocks** elem alatti első elemként:

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

3. **ClaimsSchema**után adjon hozzá egy **ClaimsTransformations** elemet és annak alárendelt elemeit a **BuildingBlocks** elemhez:

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

4. Keresse meg azt a `Local Account SignIn` **ClaimsProvider** -elemet, amely a **DisplayName** paraméterrel rendelkezik, és adja hozzá a következő technikai profilt:

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

    Cserélje le a **Client_id** **DefaultValue** értékét az előfeltételként megadott oktatóanyagban létrehozott PROXYIDENTITYEXPERIENCEFRAMEWORK alkalmazás alkalmazás-azonosítójával. Ezután cserélje le az **Resource_id** **DefaultValue** értékét az előfeltételként szükséges oktatóanyagban létrehozott IDENTITYEXPERIENCEFRAMEWORK alkalmazás alkalmazás-azonosítójával.

5. Adja hozzá a következő **ClaimsProvider** elemeket a technikai profiljaihoz a **ClaimsProviders** elemhez:

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

6. Adjon hozzá egy **UserJourneys** elemet és annak alárendelt elemeit a **TrustFrameworkPolicy** elemhez:

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

7. A Azure AD B2C-bérlő **Egyéni házirendek** lapján válassza a **házirend feltöltése**lehetőséget.
8. **Ha létezik, engedélyezze a házirend felülírását**, majd keresse meg és válassza ki a *TrustFrameworkExtensions. XML* fájlt.
9. Kattintson a **Feltöltés** gombra.

## <a name="create-a-relying-party-file"></a>Függő entitás fájljának létrehozása

Következő lépésként frissítse a függő entitás fájlját, amely kezdeményezi a létrehozott felhasználói utat:

1. Készítsen másolatot a *SignUpOrSignin. XML* fájlról a munkakönyvtárában, és nevezze át a *ROPC_Auth. XML*fájlba.
2. Nyissa meg az új fájlt, és módosítsa a **PolicyId** attribútum értékét a **TrustFrameworkPolicy** egyedi értékre. A házirend-azonosító a szabályzat neve. Például: **B2C_1A_ROPC_Auth**.
3. Módosítsa a **DefaultUserJourney** ReferenceId attribútum értékét a következőre: `ResourceOwnerPasswordCredentials`.
4. Módosítsa a **OutputClaims** elemet úgy, hogy csak a következő jogcímeket tartalmazza:

    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. A Azure AD B2C-bérlő **Egyéni házirendek** lapján válassza a **házirend feltöltése**lehetőséget.
6. **Ha létezik, engedélyezze a házirend felülírását**, majd keresse meg és válassza ki a *ROPC_Auth. XML* fájlt.
7. Kattintson a **Feltöltés** gombra.

## <a name="test-the-policy"></a>A szabályzat tesztelése

Egy API-hívás létrehozásához használja kedvenc API-fejlesztési alkalmazását, és tekintse át a szabályzat hibakeresésére adott választ. A POST kérelem törzsének a következő információk alapján hozhat létre egy hívást:

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- Cserélje le `your-tenant-name` az Azure AD B2C-bérlő nevével.
- A `B2C_1A_ROPC_Auth` helyére írja be az erőforrás-tulajdonosi jelszó hitelesítő adatainak teljes nevét.

| Kulcs | Value |
| --- | ----- |
| username | `user-account` |
| password | `password1` |
| grant_type | password |
| scope | OpenID @no__t – 0 offline_access |
| client_id | `application-id` |
| response_type | token id_token |

- Cserélje `user-account` le a nevet a bérlő felhasználói fiókjának nevére.
- Cserélje `password1` le a-t a felhasználói fiók jelszavára.
- Cserélje le a `application-id` értéket a *ROPC_Auth_app* -regisztráció alkalmazás-azonosítójával.
- A *Offline_access* nem kötelező, ha frissítési tokent szeretne kapni.

A tényleges POST-kérelem a következő példához hasonlóan néz ki:

```HTTPS
POST /yourtenant.onmicrosoft.com/oauth2/v2.0/token?B2C_1_ROPC_Auth HTTP/1.1
Host: yourtenant.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```

Az offline-hozzáférés sikeres válasza a következő példához hasonlóan néz ki:

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...",
    "token_type": "Bearer",
    "expires_in": "3600",
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..."
}
```

## <a name="redeem-a-refresh-token"></a>Frissítési jogkivonat beváltása

Állítson össze egy POST hívást, amely az itt láthatóhoz hasonló. Használja a következő táblázatban szereplő információkat a kérelem törzse:

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- Cserélje le `your-tenant-name` az Azure AD B2C-bérlő nevével.
- A `B2C_1A_ROPC_Auth` helyére írja be az erőforrás-tulajdonosi jelszó hitelesítő adatainak teljes nevét.

| Kulcs | Value |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | `application-id` |
| resource | `application-id` |
| refresh_token | `refresh-token` |

- Cserélje le a `application-id` értéket a *ROPC_Auth_app* -regisztráció alkalmazás-azonosítójával.
- Cserélje le a `refresh-token` értéket az előző válaszban visszaküldött **refresh_token** .

A sikeres válasz a következő példához hasonlít:

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

## <a name="use-a-native-sdk-or-app-auth"></a>Natív SDK vagy alkalmazás-Auth használata

Azure AD B2C megfelel a OAuth 2,0 szabványoknak a nyilvános ügyfél erőforrás-tulajdonos jelszavának hitelesítő adataihoz, és kompatibilisnek kell lennie a legtöbb ügyféloldali SDK-val. A legfrissebb információkért tekintse meg a [OAuth 2,0-hez készült natív app SDK-t és az OpenID connectet a modern ajánlott eljárások megvalósításához](https://appauth.io/).

## <a name="next-steps"></a>További lépések

- Tekintse meg a forgatókönyv teljes példáját a [Azure Active Directory B2C egyéni házirend alapszintű csomagban](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/source/aadb2c-ief-ropc).
- További információ a Azure Active Directory B2C által használt jogkivonatokról a [jogkivonat-hivatkozásban](active-directory-b2c-reference-tokens.md).

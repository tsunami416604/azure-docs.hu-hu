---
title: Jelszómódosítás konfigurálása egyéni házirendekkel
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan engedélyezheti a felhasználóknak, hogy az Azure Active Directory B2C egyéni szabályzatokkal módosítsák a jelszavukat.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2c351f8a95110a32c53c68c5eb6095918578bc5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189174"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Jelszómódosítás konfigurálása egyéni házirendekkel az Azure Active Directory B2C-ben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) szolgáltatásban engedélyezheti, hogy a helyi fiókkal bejelentkezett felhasználók anélkül módosíthassák jelszavukat, hogy e-mailben ellenőriznék a hitelességüket. Ha a munkamenet lejár, mire a felhasználó eljut a jelszómódosítási folyamathoz, a rendszer kéri, hogy jelentkezzen be újra. Ez a cikk bemutatja, hogyan konfigurálhatja a jelszóváltozást az [egyéni házirendekben.](custom-policy-overview.md) Önkiszolgáló [jelszó-visszaállítás](user-flow-self-service-password-reset.md) konfigurálása is konfigurálható a felhasználói folyamatokhoz.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre az [Egyéni házirendek ismerkedése az Active Directory B2C szolgáltatásában](custom-policy-get-started.md)című lépéseit.

## <a name="add-the-elements"></a>Az elemek hozzáadása

1. Nyissa meg a *TrustframeworkExtensions.xml* fájlt, és adja `oldPassword` hozzá a következő **ClaimType** elemet a [ClaimsSchema](claimsschema.md) elemazonosítójával:

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. A [ClaimsProvider](claimsproviders.md) elem tartalmazza a felhasználót hitelesítő technikai profilt. Adja hozzá a következő jogcímszolgáltatókat a **ClaimsProviders** elemhez:

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <Protocol Name="OpenIdConnect" />
            <Metadata>
              <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
              <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
              <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
              <Item Key="ProviderName">https://sts.windows.net/</Item>
              <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
              <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
              <Item Key="response_types">id_token</Item>
              <Item Key="response_mode">query</Item>
              <Item Key="scope">email openid</Item>
              <Item Key="UsePolicyInRedirectUri">false</Item>
              <Item Key="HttpBinding">POST</Item>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="username" Required="true" />
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
              <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
              <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
              <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
              <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
              <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
              <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
              <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
              <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

    Cserélje `IdentityExperienceFrameworkAppId` le az előfeltételi oktatóanyagban létrehozott IdentityExperienceFramework alkalmazás azonosítójával. Cserélje `ProxyIdentityExperienceFrameworkAppId` le a korábban is létrehozott ProxyIdentityExperienceFramework alkalmazás azonosítóját.

3. A [UserJourney](userjourneys.md) elem határozza meg az elérési utat, amelyet a felhasználó az alkalmazással való interakció során vesz igénybe. Adja hozzá a **UserJourneys** elemet, ha az nem `PasswordChange`létezik a **UserJourney** azonosító val:

    ```XML
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. Mentse a *TrustFrameworkExtensions.xml* házirendfájlt.
5. Másolja a programcsomaggal letöltött *ProfileEdit.xml* fájlt, és nevezze el *ProfileEditPasswordChange.xml*néven.
6. Nyissa meg az új fájlt, és frissítse a **PolicyId** attribútumot egyedi értékkel. Ez az érték a házirend neve. Például *B2C_1A_profile_edit_password_change*.
7. Módosítsa a **ReferenceId** `<DefaultUserJourney>` attribútumot úgy, hogy az megfeleljen a létrehozott új felhasználói út azonosítójának. Például *PasswordChange*.
8. Mentse a módosításokat.

A mintaszabályzatot [itt](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change)találja.

## <a name="test-your-policy"></a>A szabályzat tesztelése

Amikor az alkalmazások at Azure AD B2C tesztelése, hasznos lehet, hogy `https://jwt.ms` az Azure AD B2C jogkivonat ot vissza, hogy képes legyen áttekinteni a benne lévő jogcímek.

### <a name="upload-the-files"></a>A fájlok feltöltése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja a bérlőt tartalmazó könyvtárat.
3. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
4. Válassza **az Identitáskezelési keretrendszert**.
5. Az Egyéni házirendek lapon kattintson a **Feltöltési szabályzat gombra.**
6. Válassza **a Házirend felülírása, ha létezik lehetőséget,** majd keresse meg és jelölje ki a *TrustframeworkExtensions.xml* fájlt.
7. Kattintson a **Feltöltés** gombra.
8. Ismételje meg az 5–7. *ProfileEditPasswordChange.xml*

### <a name="run-the-policy"></a>A házirend futtatása

1. Nyissa meg a módosított házirendet. Például *B2C_1A_profile_edit_password_change*.
2. Az **Alkalmazás területen**válassza ki a korábban regisztrált alkalmazást. A token megtekintéséhez a Válasz `https://jwt.ms` **URL-címének** meg kell jelennie.
3. Kattintson a **Futtatás most** parancsra. Jelentkezzen be a korábban létrehozott acouunt-tal. Most lehetősége van a jelszó módosítására.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogy miként [konfigurálhatja a jelszó összetettségét az Azure Active Directory B2C egyéni szabályzatai használatával.](custom-policy-password-complexity.md)

---
title: Jelszó módosításának beállítása egyéni szabályzatok használatával
titleSuffix: Azure AD B2C
description: Ismerje meg, hogy a felhasználók miként módosíthatják a jelszavukat egyéni szabályzatok használatával Azure Active Directory B2Cban.
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189174"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>A jelszó módosításának konfigurálása egyéni házirendek használatával Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) esetében engedélyezheti, hogy a helyi fiókkal bejelentkezett felhasználók a jelszavukat úgy változtassák meg, hogy az e-mail-ellenőrzésen alapuló eredetiségük bizonyítása nélkül is megváltozzon. Ha a munkamenet lejár, amikor a felhasználó megkapja a jelszó módosítási folyamatát, a rendszer kéri, hogy jelentkezzen be újra. Ez a cikk bemutatja, hogyan konfigurálhatja a jelszó módosítását az [Egyéni házirendekben](custom-policy-overview.md). A felhasználói folyamatok önkiszolgáló [jelszó-visszaállítását](user-flow-self-service-password-reset.md) is konfigurálhatja.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a következő témakörben ismertetett lépéseket: Ismerkedés az [Egyéni szabályzatokkal Active Directory B2Cban](custom-policy-get-started.md).

## <a name="add-the-elements"></a>Elemek hozzáadása

1. Nyissa meg a *TrustframeworkExtensions. XML* fájlt, és adja hozzá a következő **claimType** elemet `oldPassword` azonosítójának megadásával az [ClaimsSchema](claimsschema.md) elemhez:

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

2. A [ClaimsProvider](claimsproviders.md) elem tartalmazza azt a technikai profilt, amely hitelesíti a felhasználót. Adja hozzá a következő jogcím-szolgáltatókat a **ClaimsProviders** elemhez:

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

    Cserélje le a `IdentityExperienceFrameworkAppId`t az előfeltételként megadott oktatóanyagban létrehozott IdentityExperienceFramework alkalmazás alkalmazás-azonosítójával. Cserélje le a `ProxyIdentityExperienceFrameworkAppId`t a korábban létrehozott ProxyIdentityExperienceFramework alkalmazás alkalmazás-azonosítójával.

3. A [UserJourney](userjourneys.md) elem azt az elérési utat határozza meg, amelyet a felhasználó az alkalmazással való interakció során használ. Adja hozzá a **UserJourneys** elemet, ha az nem létezik a `PasswordChange`azonosított **UserJourney** :

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

4. Mentse a *TrustFrameworkExtensions. XML* házirend-fájlt.
5. Másolja az alapszintű csomaggal letöltött *ProfileEdit. XML* fájlt, és nevezze el a *ProfileEditPasswordChange. XML*néven.
6. Nyissa meg az új fájlt, és frissítse a **PolicyId** attribútumot egyedi értékkel. Ez az érték a szabályzat neve. Például *B2C_1A_profile_edit_password_change*.
7. Módosítsa a `<DefaultUserJourney>` **ReferenceId** attribútumát úgy, hogy az megfeleljen a létrehozott új felhasználói út azonosítójának. Például: *PasswordChange*.
8. Mentse a módosításokat.

[Itt](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change)megtekintheti a minta szabályzatot.

## <a name="test-your-policy"></a>A szabályzat tesztelése

Az alkalmazások Azure AD B2C-ben történő tesztelésekor hasznos lehet, ha az Azure AD B2C-jogkivonat visszaadott `https://jwt.ms`, hogy át tudja tekinteni a benne lévő jogcímeket.

### <a name="upload-the-files"></a>A fájlok feltöltése

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza az **identitási élmény keretrendszert**.
5. Az egyéni házirendek lapon kattintson a **házirend feltöltése**elemre.
6. Ha létezik, válassza a **házirend felülírása**lehetőséget, majd keresse meg és válassza ki a *TrustframeworkExtensions. XML* fájlt.
7. Kattintson a **Feltöltés** gombra.
8. Ismételje meg az 5 – 7. lépést a függő entitás fájljánál (például *ProfileEditPasswordChange. XML*).

### <a name="run-the-policy"></a>A házirend futtatása

1. Nyissa meg a módosított szabályzatot. Például *B2C_1A_profile_edit_password_change*.
2. **Alkalmazás**esetén válassza ki a korábban regisztrált alkalmazást. A token megjelenítéséhez a **Válasz URL-címének** `https://jwt.ms`nak kell megjelennie.
3. Kattintson a **Futtatás most** parancsra. Jelentkezzen be a korábban létrehozott acouunt. Most lehetősége van a jelszó módosítására.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [konfigurálhatja a jelszó-bonyolultságot a Azure Active Directory B2C lévő egyéni szabályzatok használatával](custom-policy-password-complexity.md).

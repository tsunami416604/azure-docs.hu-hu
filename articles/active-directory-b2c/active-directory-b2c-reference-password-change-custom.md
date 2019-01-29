---
title: Konfigurálja a jelszó módosítása az Azure Active Directory B2C-vel egyéni szabályzatok használatával |} A Microsoft Docs
description: Ismerje meg, hogyan engedélyezhet felhasználókat az Azure Active Directory B2C-vel egyéni szabályzatok használatával jelszó módosítására.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: a9e58139d248daed19f4fe35c7d33ede9dfe64b0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55195970"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurálja a jelszó módosítása az Azure Active Directory B2C-vel egyéni szabályzatok használatával

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory (Azure AD) B2C-vel engedélyezheti a felhasználók, akik a helyi fiókkal jelentkezik be jelszómódosításra hitelességét nélkül által aláírt e-mailes ellenőrzés. Ha a munkamenet lejárt az idő a felhasználó által a folyamat módosítsa a jelszót, jelentkezzen be újra kell adnia. Ez a cikk bemutatja, hogyan konfigurálja a jelszó módosítása [egyéni szabályzatok](active-directory-b2c-overview-custom.md). Akkor is konfigurálhatja a [önkiszolgáló jelszó-visszaállítási](active-directory-b2c-reference-sspr.md) felhasználókövetési adatai számára.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a [az Active Directory B2C-vel egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md).

## <a name="add-the-elements"></a>Az elemek hozzáadása 

1. Nyissa meg a *TrustframeworkExtensions.xml* fájlt, és adja hozzá a következő **takar** elem azonosítója, amelyet az `oldPassword` , a [ClaimsSchema](claimsschema.md) elem: 

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

2. A [ClaimsProvider](claimsproviders.md) elem tartalmazza a technikai profil, amely hitelesíti a felhasználót. Adja hozzá a következő jogcím-szolgáltatók számára a **ClaimsProviders** elem:

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

    Cserélje le `IdentityExperienceFrameworkAppId` IdentityExperienceFramework alkalmazás, amelyet az előfeltételnek számító oktatóanyagot az alkalmazás azonosítójával. Cserélje le `ProxyIdentityExperienceFrameworkAppId` ProxyIdentityExperienceFramework alkalmazás, amelyet korábban hozott létre az alkalmazás azonosítójával.

3. A [UserJourney](userjourneys.md) elem definiálja az útvonal, amely a felhasználó az alkalmazás való interakció során. Adja hozzá a **UserJourneys** Ha még nem létezik az elem a **UserJourney** azonosította az eseményt `PasswordChange`:

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

4. Mentse a *TrustFrameworkExtensions.xml* szabályzatot tartalmazó fájlt.
5. Másolás a *ProfileEdit.xml* fájlt, hogy letöltötte az alapszintű csomaggal, és adja neki *ProfileEditPasswordChange.xml*.
6. Nyissa meg az új fájlt, és frissítse a **PolicyId** attribútumnak egyedi értékkel. Ez az érték a házirend nevét. Ha például *B2C_1A_profile_edit_password_change*.
7. Módosítsa a **hivatkozásazonosító** attribútum `<DefaultUserJourney>` megfelelő az Ön által létrehozott új felhasználói interakciósorozat azonosítója. Ha például *PasswordChange*.
8. Mentse a módosításokat.

A minta házirendet annak [Itt](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change). 

## <a name="test-your-policy"></a>A házirend tesztelése

Ha teszteli az alkalmazások Azure AD B2C-ben, hasznos lehet az Azure AD B2C jogkivonat vissza lehet `https://jwt.ms` lehet majd tekinteni a jogcímek, az.

### <a name="upload-the-files"></a>A fájlok feltöltése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
4. Válassza ki **identitás-kezelőfelületi keretrendszer**.
5. Egyéni szabályzatok lapon kattintson **szabályzat feltöltése**.
6. Válassza ki **szabályzat felülírása, ha létezik**, és keressen rá, és válassza ki a *TrustframeworkExtensions.xml* fájlt.
7. Kattintson a **Feltöltés** gombra.
8. Ismételje meg az 5 – 7 a függő entitás fájlt például *ProfileEditPasswordChange.xml*.

### <a name="run-the-policy"></a>Futtassa a szabályzat

1. Nyissa meg a módosított szabályzatot. Ha például *B2C_1A_profile_edit_password_change*.
2. A **alkalmazás**, válassza ki az alkalmazását, amely korábban regisztrálva. A jogkivonatot, hogy a **válasz URL-cím** megjelennie `https://jwt.ms`.
3. Kattintson a **Futtatás most** parancsra. Jelentkezzen be a acouunt korábban létrehozott. Érdemes most már lehetősége van a jelszó módosítására. 

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan zajlik [állítsa be az Azure Active Directory B2C-vel egyéni szabályzatok használatával, a jelszó erősségét](active-directory-b2c-reference-password-complexity-custom.md). 

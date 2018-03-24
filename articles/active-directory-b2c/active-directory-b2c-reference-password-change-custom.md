---
title: 'Az Azure Active Directory B2C: Az önkiszolgáló jelszóváltoztatáshoz |} Microsoft Docs'
description: A témakör bemutatja, hogyan lehet beállítani a felhasználók az Azure Active Directory B2C az önkiszolgáló jelszó módosítása
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 09/05/2016
ms.author: davidmu
ms.openlocfilehash: 4c54abe8c3375b49b786c7849c7e963e5849fa3a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-configure-password-change-in-custom-policies"></a>Az Azure Active Directory B2C: A jelszó módosítása konfigurálja az egyéni házirendek  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A jelszó módosítása szolgáltatással bejelentkezett felhasználók (helyi fiókok használatát) módosíthatják jelszavukat anélkül, hogy a hitelességét igazolásához által e-mailek ellenőrzése a [önkiszolgáló jelszó-átállítási folyamata.](active-directory-b2c-reference-sspr.md) Ha a munkamenet lejár, az ügyfél kap a időpontjára jelszót folyamata módosításához felhasználótól jelentkezzen be újra. 

## <a name="prerequisites"></a>Előfeltételek

Egy helyi fiókot sign-Close-Up/sign-in befejezéséhez, a konfigurált Azure AD B2C-bérlő [bevezetés](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-change-in-custom-policy"></a>Egyéni házirendek konfigurálása a jelszó módosítása

Konfigurálása egyéni házirendet a jelszó módosítása a következő módosításokat a megbízhatósági keretrendszer bővítmények házirend 

## <a name="define-a-claimtype-oldpassword"></a>Adja meg a következő ClaimType ismeretlen: "Régi_jelszó"

Az egyéni házirend struktúrájának tartalmaznia kell egy `ClaimsSchema`, és adja meg egy új `ClaimType` alábbi, "Régi_jelszó" 

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

Ezek az elemek célja a következőképpen:

- A `ClaimsSchema` határozza meg, milyen jogcímek érvényesítésre kerül.  Ebben az esetben a "jelszó" érvényesíti. 

## <a name="add-a-password-change-claims-provider-with-its-supporting-elements"></a>A jelszó módosítása az támogató elemei a Jogcímszolgáltatók hozzáadása

Jelszó módosítása a jogcímeket szolgáltató lesz

1. A régi jelszó alapján hitelesíteni a felhasználót
2. És "új" egyezik 'új jelszót', ha ez az érték B2C adattárolóban tárolja, és ezért a jelszó sikeresen módosítva. 

![kép](images/passwordchange.jpg)

Adja hozzá a következő jogcímszolgáltató a bővítmények házirendhez. 

```XML
<ClaimsProviders>
    <ClaimsProvider>
      <DisplayName>Local Account SignIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="login-NonInteractive">
          <Metadata>
           <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
           <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
          </InputClaims>
        </TechnicalProfile>
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



### <a name="add-the-application-ids-to-your-custom-policy"></a>Az alkalmazás azonosítók hozzá az egyéni házirend

Adja hozzá a Alkalmazásazonosítók a bővítmények fájlhoz (`TrustFrameworkExtensions.xml`):

1. A bővítmények fájlban (TrustFrameworkExtensions.xml), az elem található `<TechnicalProfile Id="login-NonInteractive">` és `<TechnicalProfile Id="login-NonInteractive-PasswordChange">`

2. Cserélje le az összes példányát `IdentityExperienceFrameworkAppId` az identitás élmény keretrendszer alkalmazás leírtak szerint az alkalmazás azonosítójával [bevezetés](active-directory-b2c-get-started-custom.md). Például:

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. Cserélje le az összes példányát `ProxyIdentityExperienceFrameworkAppId` a Proxy identitás élmény keretrendszer alkalmazás leírtak szerint az alkalmazás azonosítójával [bevezetés](active-directory-b2c-get-started-custom.md).

4. A bővítmények fájl mentéséhez.



## <a name="create-a-password-change-user-journey"></a>Hozzon létre felhasználói út jelszó módosítása

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

Befejezte a bővítmény fájl módosítása. Mentse, majd töltse fel ezt a fájlt. Győződjön meg arról, hogy az összes érvényesítések sikeres.



## <a name="create-a-relying-party-rp-file"></a>Hozzon létre egy függő entitásonkénti (RP) fájlt

Következő lépésként frissítse a függő entitásonkénti (RP) fájl, amely kezdeményezi a létrehozott felhasználói út:

1. Készítsen másolatot a ProfileEdit.xml a munkakönyvtárat. Nevezze át (például PasswordChange.xml).
2. Nyissa meg az új fájlt, és frissítse a `PolicyId` az attribútum `<TrustFrameworkPolicy>` egyedi értékkel. Azt a nevet a házirend (például PasswordChange).
3. Módosítsa a `ReferenceId` attribútumnak `<DefaultUserJourney>` kell egyeznie a `Id` az új felhasználói út (például PasswordChange) létrehozott.
4. A módosítások mentéséhez, és majd feltölteni a fájlt.
5. Az egyéni házirend feltöltött, tesztelje az Azure portálon, nyissa meg a házirend paneljét, és kattintson **futtatása most**.




## <a name="link-to-password-change-sample-policy"></a>Jelszó módosítása minta szabályzatra hivatkozó

A minta házirend található [Itt](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change). 











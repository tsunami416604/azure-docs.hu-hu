---
title: Az önkiszolgáló jelszó módosítása az Azure Active Directory B2C |} A Microsoft Docs
description: A témakör bemutatja, hogyan lehet beállítani az önkiszolgáló jelszómódosítás a felhasználók az Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 491b3988a6581387c71b4214907e689119fcb979
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344989"
---
# <a name="azure-active-directory-b2c-configure-password-change-in-custom-policies"></a>Az Azure Active Directory B2C: Egyéni szabályzatok konfigurálása a jelszó módosítása  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A jelszó módosítása szolgáltatással a bejelentkezett felhasználók (helyi fiókok használatával) jelszavuk módosításához leírtak szerint hitelességét igazolásához által e-mailes ellenőrzés nélkül a [önkiszolgáló jelszó-visszaállítási folyamatot.](active-directory-b2c-reference-sspr.md) Ha a munkamenet lejár, az ügyfél lekéri a ideje jelszó módosítása a flow, a felhasználó kéri, hogy jelentkezzen be újra. 

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD B2C-bérlő egy helyi fiók regisztrálási-regisztrálási vagy bejelentkezési, végrehajtásához leírtak szerint konfigurálva [bevezetés](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-change-in-custom-policy"></a>Egyéni házirendek konfigurálása a jelszó módosítása

Egyéni házirendek jelszó módosítása, hogy a következő módosításokat a bizalmi keretrendszer bővítmények házirendjében 

## <a name="define-a-claimtype-oldpassword"></a>Egy "oldPassword" takar definiálása

Az egyéni házirend általános szerkezete tartalmaznia kell egy `ClaimsSchema`és a egy új definiálása `ClaimType` , az alábbi, "oldPassword" 

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

- A `ClaimsSchema` határozza meg, melyik jogcím van kell kiértékelni.  Ebben az esetben a "régi jelszót' érvényesíti. 

## <a name="add-a-password-change-claims-provider-with-its-supporting-elements"></a>Adjon hozzá egy jelszó módosítása az támogató elemei a jogcímszolgáltatótól

Jelszó módosítása a jogcímeket szolgáltató fog

1. Hitelesíteni a felhasználót a régi jelszó
2. És ha az "új jelszó" megfelel a "új jelszó megerősítése", ezt az értéket a B2C-adattár tárolja, és ezért a jelszó sikeresen módosítva. 

![kép](images/passwordchange.jpg)

Adja hozzá a következő jogcímszolgáltató a bővítmények szabályzatot. 

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
            <Item Key="METADATA">https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/.well-known/openid-configuration</Item>
            <Item Key="authorization_endpoint">https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/token</Item>
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



### <a name="add-the-application-ids-to-your-custom-policy"></a>Az alkalmazás-azonosítók hozzáadása az egyéni házirend

Vegye fel az alkalmazásazonosítót a bővítmények fájlba (`TrustFrameworkExtensions.xml`):

1. A bővítmények fájlt (TrustFrameworkExtensions.xml), keresse meg az elem `<TechnicalProfile Id="login-NonInteractive">` és `<TechnicalProfile Id="login-NonInteractive-PasswordChange">`

2. Cserélje le az összes példányát `IdentityExperienceFrameworkAppId` az identitás-kezelőfelületi keretrendszer alkalmazás leírtak szerint az alkalmazás azonosítójával [bevezetés](active-directory-b2c-get-started-custom.md). Például:

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. Cserélje le az összes példányát `ProxyIdentityExperienceFrameworkAppId` az identitás-kezelőfelületi keretrendszer Proxy alkalmazás leírtak szerint az alkalmazás azonosítójával [bevezetés](active-directory-b2c-get-started-custom.md).

4. Mentse a bővítmények fájlt.



## <a name="create-a-password-change-user-journey"></a>Hozzon létre egy jelszó módosítása felhasználói interakciósorozat

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

Elkészült a kiterjesztésű fájl módosítása. Mentse, és töltse fel ezt a fájlt. Győződjön meg arról, hogy az összes ellenőrzés sikeres.



## <a name="create-a-relying-party-rp-file"></a>Hozzon létre egy függő entitásonkénti (RP) fájlt

Ezután frissítse a függő entitásonkénti (RP) fájl, amely az Ön által létrehozott felhasználói interakciósorozat kezdeményezi:

1. A munkakönyvtárban ProfileEdit.xml másolatának elkészítéséhez. Nevezze (például PasswordChange.xml).
2. Nyissa meg az új fájlt, és frissítse a `PolicyId` az attribútum `<TrustFrameworkPolicy>` egyedi értékkel. Ez az a a szabályzat nevét (például PasswordChange).
3. Módosítsa a `ReferenceId` attribútum `<DefaultUserJourney>` megfelelően a `Id` az új felhasználói út (például PasswordChange) létrehozott.
4. Mentse a módosításokat, majd feltölti a fájlt.
5. Ha tesztelni szeretné az egyéni házirend feltöltött, az Azure Portalon, nyissa meg a szabályzat paneljén, és kattintson **Futtatás most**.




## <a name="link-to-password-change-sample-policy"></a>Jelszó módosítása minta szabályzat csatolása

A minta házirendet annak [Itt](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change). 











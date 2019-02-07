---
title: Az Azure Active Directory B2C-ben bejelentkezve szeretnék maradni |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be tartsa Me aláírva a (KMSI) az Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: e2aa52e8ad19274d45f648978e7b2f021139fe4a
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812301"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Engedélyezze a bejelentkezve maradás (KMSI) az Azure Active Directory B2C-vel

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tartsa meg aláírt a (KMSI) funkció a webes és natív alkalmazások Azure Active Directory (Azure AD) B2C-ben is engedélyezheti. Ez a funkció a visszatérő felhasználókat az alkalmazás újbóli felhasználónevüket és jelszavukat anélkül hozzáférést. Ezt a hozzáférést a felhasználó kijelentkezésekor visszavonták. 

Felhasználók ne engedélyezze ezt a kapcsolót található nyilvános számítógépeket. 

![A bejelentkezve maradás engedélyezése](./media/active-directory-b2c-reference-kmsi-custom/kmsi.PNG)

## <a name="prerequisites"></a>Előfeltételek

Azure AD B2C-bérlő, amely engedélyezi a helyi fiók, előfizetési, és jelentkezzen be. Ha nem rendelkezik bérlővel, létrehozhat egyet a lépések használatával [oktatóanyag: Az Azure Active Directory B2C-bérlő létrehozása](tutorial-create-tenant.md).

## <a name="add-a-content-definition-element"></a>Adjon hozzá egy tartalomdefiníció elemet 

Alatt a **BuildingBlocks** eleme a bővítményfájl hozzáadása egy **ContentDefinitions** elemet. 

1. Alatt a **ContentDefinitions** elemben adjon hozzá egy **ContentDefinition** elem azonosítója, amelyet az `api.signuporsigninwithkmsi`.
2. Alatt a **ContentDefinition** elemben adja hozzá a **LoadUri**, **RecoveryUri**, és **DataUri** elemeket. A `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` értékét a **DataUri** elem egy gép érthető azonosítója, amely megjelenik a bejelentkezési oldalak KMSI jelölőnégyzetből. Ez az érték nem kell módosítani. 

    ```XML
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsigninwithkmsi">
          <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>                       
    ```

## <a name="add-a-sign-in-claims-provider-for-a-local-account"></a>Egy helyi fiók bejelentkezési jogcímeket szolgáltató hozzáadása  

Helyi fiók bejelentkezési használatával Jogcímszolgáltatók, meghatározhatja a **ClaimsProvider** elem a bővítmény fájlban a szabályzat:

1. Nyissa meg a *TrustFrameworkExtensions.xml* fájlt a munkakönyvtárban. 
2. Keresse meg a **ClaimsProviders** elemet. Ha még nem létezik, adja hozzá a legfelső szintű elem alatt. A [alapszintű csomagja](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) magában foglalja a helyi fiókkal jelentkezzen be a jogcím-szolgáltatóktól. 
3. Adjon hozzá egy **ClaimsProvider** elemet a **DisplayName** és **TechnicalProfile** az alábbi példában látható módon:

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
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

### <a name="add-the-application-identifiers-to-your-custom-policy"></a>Az alkalmazás-azonosítók hozzáadása az egyéni házirend

Az alkalmazás azonosítók hozzáadása az *TrustFrameworkExtensions.xml* fájlt.

1. Az a *TrustFrameworkExtensions.xml* fájlt és keresse meg a **TechnicalProfile** elem azonosítóját az `login-NonInteractive` és a **TechnicalProfile** elemmel azonosítója, amelyet `login-NonInteractive-PasswordChange` , és cserélje le az összes értéket `IdentityExperienceFrameworkAppId` az identitás-kezelőfelületi keretrendszer alkalmazás leírtak szerint az alkalmazás-azonosítóval rendelkező [bevezetés](active-directory-b2c-get-started-custom.md).

    ```
    <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
    ```

2. Cserélje le az összes értéket `ProxyIdentityExperienceFrameworkAppId` az identitás-kezelőfelületi keretrendszer Proxy alkalmazás leírtak szerint az alkalmazás azonosítóval rendelkező [bevezetés](active-directory-b2c-get-started-custom.md).
3. Mentse a bővítmények fájlt.

## <a name="create-a-kmsi-enabled-user-journey"></a>Hozzon létre egy KMSI engedélyezett felhasználói interakciósorozat

Adja hozzá a felhasználói út a bejelentkezési jogcímszolgáltató egy helyi fiók. 

1. Nyissa meg a szabályzat alapszintű fájlt. Ha például *TrustFrameworkBase.xml*.
2. Keresse meg a **UserJourneys** elemet, és másolja a teljes tartalmát a **UserJourney** elem azonosítóját használó `SignUpOrSignIn`.
3. Nyissa meg a kiterjesztésű fájlt. Ha például *TrustFrameworkExtensions.xml* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, adjon hozzá egyet.
4. Illessze be a teljes **UserJourney** gyermekeként kimásolt elem a **UserJourneys** elemet.
5. Az új felhasználói interakciósorozat esetében az azonosító értékének módosítása. Például: `SignUpOrSignInWithKmsi`.
6. Végül az első lépésben vezénylési értékének módosítása **ContentDefinitionReferenceId** való `api.signuporsigninwithkmsi`. Ezt az értéket a beállítás lehetővé teszi, hogy a felhasználói interakciósorozatban szereplő be a jelölőnégyzetet. 
7. Mentés és a bővítmény-fájl feltöltése, és győződjön meg arról, hogy az összes ellenőrzés sikeres.

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignInWithKmsi">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsigninwithkmsi">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <Preconditions>
              <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
                <Value>objectId</Value>
                <Action>SkipThisOrchestrationStep</Action>
              </Precondition>
            </Preconditions>
            <ClaimsExchanges>
              <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <!-- This step reads any user attributes that we may not have received when in the token. -->
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

## <a name="create-a-relying-party-file"></a>Hozzon létre egy függő entitás fájlt

Frissítse a függő entitásonkénti (RP) fájl, amely az Ön által létrehozott felhasználói interakciósorozat kezdeményezi.

1. Készítsen másolatot *SignUpOrSignIn.xml* a munkakönyvtárban fájlt, és nevezze át. Ha például *SignUpOrSignInWithKmsi.xml*.
2. Nyissa meg az új fájlt, és frissítse a **PolicyId** az attribútum a **TrustFrameworkPolicy** egyedi értékkel. Ez az a házirend nevét. Például: `SignUpOrSignInWithKmsi`.
3. Módosítsa a **referenceid megadása** az attribútum a **DefaultUserJourney** elem megfelelő az Ön által létrehozott új felhasználói interakciósorozat azonosítóját. Például: `SignUpOrSignInWithKmsi`.

    KMSI használatára van konfigurálva, a **UserJourneyBehaviors** elemet **SingleSignOn**, **SessionExpiryType**, és **SessionExpiryInSeconds** , az első alárendelt elemei. A **KeepAliveInDays** attribútum meghatározza, hogy mennyi ideig a felhasználó bejelentkezve marad. A következő példában a KMSI munkamenet automatikusan lejárata után `7` nap, függetlenül attól, hogy milyen gyakran a felhasználói beavatkozás nélküli hitelesítést hajt végre. Beállítás a **KeepAliveInDays** értéket a következőre `0` KMSI funkciót kikapcsol. Alapértelmezés szerint ez az érték a `0`. Ha az értéke **SessionExpiryType** van `Rolling`, a KMSI munkamenet bővített `7` nappal minden alkalommal, amikor a felhasználó csendes hitelesítést hajt végre.  Ha `Rolling` van kijelölve, érdemes megtartani a napok számát minimális. 

    Az érték **SessionExpiryInSeconds** az egyszeri bejelentkezés munkamenet lejárati idejét jelzi. Ez belső használatára szolgál az Azure AD B2C-vel ellenőrizze, hogy KMSI a munkamenet lejárt-e vagy sem. Az érték **KeepAliveInDays** meghatározza, hogy az egyszeri bejelentkezési cookie-t a böngésző Expires és Max-Age értékét. Ellentétben **SessionExpiryInSeconds**, **KeepAliveInDays** szolgál, hogy a böngészőben a cookie-k törlése, ha be van zárva. A felhasználói beavatkozás nélkül jelentkezhetnek be, csak akkor, ha az egyszeri bejelentkezés munkamenet cookie-k létezik, amelyek vezérlik **KeepAliveInDays**, és nem járt le, amelyek vezérlik **SessionExpiryInSeconds**. 
    
    Ha a felhasználó nem engedélyezi az **bejelentkezve szeretnék maradni** a regisztrációs és bejelentkezési oldalon a munkamenet által jelzett idő után jár le **SessionExpiryInSeconds** megfelelt vagy a böngésző be van zárva. Ha a felhasználó engedélyezi **bejelentkezve szeretnék maradni**, értékét **KeepAliveInDays** értékét felülírja **SessionExpiryInSeconds** és előírja a munkamenet lejárati ideje. A felhasználók még akkor is, zárja be a böngészőt, és nyissa meg újra, is csendes továbbra is be, amíg idején belül **KeepAliveInDays**. Javasoljuk, hogy az értékét állítsa be **SessionExpiryInSeconds** rövid időn (1200-as másodperc), miközben értékét kell **KeepAliveInDays** értékre lehet beállítani egy viszonylag hosszú ideje (7 nap), ahogyan az a Példa:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
      <UserJourneyBehaviors>
        <SingleSignOn Scope="Tenant" KeepAliveInDays="7" />
        <SessionExpiryType>Absolute</SessionExpiryType>
        <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
      </UserJourneyBehaviors>
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName" />
          <OutputClaim ClaimTypeReferenceId="givenName" />
          <OutputClaim ClaimTypeReferenceId="surname" />
          <OutputClaim ClaimTypeReferenceId="email" />
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

4. Mentse a módosításokat, és majd feltöltjük a fájlt.
5. Ha tesztelni szeretné az egyéni házirend feltöltött, az Azure Portalon, nyissa meg a szabályzat lapját, és válassza **Futtatás most**.

A minta házirendet annak [Itt](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).









---
title: Bejelentkezve maradok Azure Active Directory B2C
description: Megtudhatja, hogyan állíthatja be a bejelentkezett lépést (KMSI) a Azure Active Directory B2Cban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 29cdf5e7723113b4673945bf5db3158680a44b79
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147038"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>A bejelentkezett (KMSI) Azure Active Directory B2C használatának engedélyezése

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Engedélyezheti a bejelentkezett (KMSI) funkciót azon webes és natív alkalmazások felhasználói számára, akik helyi fiókkal rendelkeznek a Azure Active Directory B2C (Azure AD B2C) címtárban. Ez a szolgáltatás hozzáférést biztosít az alkalmazásnak visszaadott felhasználóknak anélkül, hogy újra meg kellene adnia felhasználónevét és jelszavát. A rendszer visszavonja ezt a hozzáférést, amikor egy felhasználó kijelentkezik.

A felhasználók nem engedélyezhetik ezt a lehetőséget a nyilvános számítógépeken.

![Példa a bejelentkezési bejelentkezési oldalára, amely egy bejelentkezve marad jelölőnégyzetet jelenít meg](./media/active-directory-b2c-reference-kmsi-custom/kmsi.PNG)

## <a name="prerequisites"></a>Előfeltételek

Azure AD B2C bérlő, amely a helyi fiók bejelentkezésének engedélyezésére van konfigurálva. A KMSI nem támogatott külső identitás-szolgáltatói fiókok esetében.

Ha nem rendelkezik Bérlővel, létrehozhat egyet az [oktatóanyag lépéseinek használatával: Hozzon létre egy](tutorial-create-tenant.md)Azure Active Directory B2C bérlőt.

## <a name="add-a-content-definition-element"></a>Tartalom-definíciós elem hozzáadása

A fájlkiterjesztés **BuildingBlocks** eleme alatt adjon hozzá egy **ContentDefinitions** elemet.

1. A **ContentDefinitions** elem alatt adjon hozzá egy **ContentDefinition** elemet az azonosítóval `api.signuporsigninwithkmsi`.
2. A **ContentDefinition** elem alatt adja hozzá a **tartalomdefinícióban**, a **RecoveryUri**és a **DataUri** elemet. A **DataUri** elem értékeegyszámítógépáltalértelmezhetőazonosító,amelylétrehozegyKMSIjelölőnégyzetetabejelentkezésilapokon.`urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` Ez az érték nem változtatható meg.

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

## <a name="add-a-sign-in-claims-provider-for-a-local-account"></a>Bejelentkezési jogcím-szolgáltató hozzáadása helyi fiókhoz

A helyi fiók bejelentkezését jogcím-szolgáltatóként megadhatja a **ClaimsProvider** elem használatával a szabályzat bővítmény fájljában:

1. Nyissa meg a *TrustFrameworkExtensions. XML* fájlt a munkakönyvtárból.
2. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez. Az [alapszintű csomag](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) tartalmazza a helyi fiók bejelentkezési jogcímek szolgáltatóját.
3. Adjon hozzá egy **ClaimsProvider** elemet a **DisplayName** és a **kivonatjogcím** , ahogy az a következő példában látható:

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

### <a name="add-the-application-identifiers-to-your-custom-policy"></a>Az alkalmazás-azonosítók hozzáadása az egyéni szabályzathoz

Adja hozzá az alkalmazás azonosítóit a *TrustFrameworkExtensions. XML* fájlhoz.

1. A *TrustFrameworkExtensions. XML* fájlban keresse meg a **kivonatjogcím** elemet `login-NonInteractive` a és a **kivonatjogcím** elem `login-NonInteractive-PasswordChange` azonosítójával, és cserélje le az összes értéket a `IdentityExperienceFrameworkAppId` az Identity Experience Framework alkalmazás az [első lépések](active-directory-b2c-get-started-custom.md)című témakörben leírtak szerint.

    ```XML
    <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
    ```

2. Cserélje le az összes `ProxyIdentityExperienceFrameworkAppId` értéket a proxy Identity Experience Framework alkalmazás azonosítójának az [első lépések](active-directory-b2c-get-started-custom.md)című témakörben leírtak szerint.
3. Mentse a bővítmények fájlt.

## <a name="create-a-kmsi-enabled-user-journey"></a>KMSI-kompatibilis felhasználói út létrehozása

Adja hozzá a bejelentkezési jogcímek szolgáltatóját egy helyi fiókhoz a felhasználói útra.

1. Nyissa meg a szabályzat alapfájlját. Például: *TrustFrameworkBase. XML*.
2. Keresse meg a **UserJourneys** elemet, és másolja a **UserJourney** elem teljes tartalmát, amely `SignUpOrSignIn`az azonosítót használja.
3. Nyissa meg a kiterjesztési fájlt. Például a *TrustFrameworkExtensions. xml fájlt* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, vegyen fel egyet.
4. Illessze be a teljes **UserJourney** elemet, amelyet a **UserJourneys** elem gyermekeiként másolt.
5. Módosítsa az új felhasználói út azonosítójának értékét. Például: `SignUpOrSignInWithKmsi`.
6. Végül az első előkészítési lépésben módosítsa a **ContentDefinitionReferenceId** `api.signuporsigninwithkmsi`értékét. Ennek az értéknek a beállításával engedélyezheti a jelölőnégyzetet a felhasználói úton.
7. Mentse és töltse fel a bővítmény fájlját, és győződjön meg arról, hogy az összes érvényesítés sikeres.

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

## <a name="create-a-relying-party-file"></a>Függő entitás fájljának létrehozása

Frissítse a függő entitás (RP) fájlját, amely kezdeményezi a létrehozott felhasználói utat.

1. Készítsen másolatot a *SignUpOrSignIn. XML* fájlról a munkakönyvtárában, majd nevezze át. Például: *SignUpOrSignInWithKmsi. XML*.
2. Nyissa meg az új fájlt, és frissítse a **PolicyId** attribútumot a **TrustFrameworkPolicy** egyedi értékkel. Ez a szabályzat neve. Például: `SignUpOrSignInWithKmsi`.
3. Módosítsa a **DefaultUserJourney** elem **ReferenceId** attribútumát úgy, hogy az megfeleljen a létrehozott új felhasználói út azonosítójának. Például: `SignUpOrSignInWithKmsi`.

    A KMSI a **SingleSignon**, a **SessionExpiryType**és a **SessionExpiryInSeconds** **UserJourneyBehaviors** elemmel van konfigurálva első alárendelt elemeiként. A **KeepAliveInDays** attribútum azt határozza meg, hogy mennyi ideig marad a felhasználó bejelentkezve. A következő példában a KMSI-munkamenet automatikusan lejár nap után `7` , attól függetlenül, hogy a felhasználó milyen gyakran hajt végre csendes hitelesítést. A **KeepAliveInDays** értékének `0` beállításával kikapcsolhatja a KMSI funkciót. Alapértelmezés szerint ez az érték `0`. Ha a **SessionExpiryType** `Rolling`értéke, akkor a KMSI- `7` munkamenetet a rendszer minden alkalommal kiterjeszti, amikor a felhasználó csendes hitelesítést végez.  Ha `Rolling` be van jelölve, a minimum napok számát kell megtartania.

    A **SessionExpiryInSeconds** értéke az egyszeri bejelentkezéses munkamenet lejárati idejét jelöli. Ezt a belső használat Azure AD B2C annak ellenőrzéséhez, hogy a KMSI-munkamenet lejárt-e vagy sem. A **KeepAliveInDays** értéke határozza meg az egyszeri bejelentkezési cookie lejárati/maximális élettartamát a böngészőben. A **SessionExpiryInSeconds**-től eltérően a **KeepAliveInDays** használatával megakadályozható, hogy a böngésző törölje a cookie-t, amikor bezárult. A felhasználók csak akkor tudnak bejelentkezni, ha az egyszeri bejelentkezési munkamenet cookie-je létezik, amelyet a **KeepAliveInDays**szabályoz, és nem járt le, amelyet a **SessionExpiryInSeconds**vezérel.

    Ha a felhasználó nem engedélyezi a bejelentkezett üzenet bekapcsolását a regisztrációs és bejelentkezési oldalon, a munkamenet a **SessionExpiryInSeconds** által jelzett idő lejárta után lejár, vagy a böngésző bezárult. Ha a felhasználó engedélyezia bejelentkezést, a **KeepAliveInDays** értéke felülbírálja a **SessionExpiryInSeconds** értékét, és megdiktálja a munkamenet lejárati idejét. Még ha a felhasználók is lezárták a böngészőt, és újra megnyitják, akkor is csendesen jelentkezhetnek be, ha a **KeepAliveInDays**időpontján belül van. Javasoljuk, hogy a **SessionExpiryInSeconds** értékét rövid időtartamra (1200 másodpercre) állítsa be, míg a **KeepAliveInDays** értéke viszonylag hosszú lehet (7 nap), az alábbi példában látható módon:

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

4. Mentse a módosításokat, majd töltse fel a fájlt.
5. A feltöltött egyéni szabályzat teszteléséhez a Azure Portal lépjen a szabályzat lapra, majd válassza a **Futtatás most**lehetőséget.

[Itt](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)megtekintheti a minta szabályzatot.

---
title: Az Azure Active Directory B2C-ben bejelentkezve szeretnék maradni |} A Microsoft Docs
description: A témakör bemutatja, hogyan lehet "a bejelentkezve maradás tarthatja" beállítása.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6bad6e1f2b204f76b075652a9d3f27367a8de49f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441317"
---
# <a name="azure-active-directory-b2c-enable-keep-me-signed-in-kmsi"></a>Az Azure Active Directory B2C: Engedélyezése "Bejelentkezve szeretnék maradni (KMSI)"  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure AD B2C most már lehetővé teszi a webes és natív alkalmazások funkcióinak "Bejelentkezve szeretnék maradni (KMSI)". Ez a funkció hozzáférést a felhasználók visszatérő alkalmazás nem kéri a felhasználót, a felhasználónevet és jelszót adja meg újból. Amikor a felhasználó kijelentkezik a hozzáférés vissza lett vonva. 

Javasoljuk, hogy ezen beállítás található nyilvános számítógépeket a felhasználókkal szemben. 

![kép](images/kmsi.PNG)


## <a name="prerequisites"></a>Előfeltételek

Az Azure AD B2C-bérlő konfigurálva, hogy a helyi fiók regisztrálási-regisztrálási vagy bejelentkezési, leírtak szerint [bevezetés](active-directory-b2c-get-started-custom.md).

## <a name="how-to-enable-kmsi"></a>KMSI engedélyezése

A következő módosításokat a bizalmi keretrendszer bővítmények szabályzatban.

## <a name="adding-a-content-definition-element"></a>Egy tartalomdefiníció elem felvétele 

A `BuildingBlocks` csomópont a bővítmény fájl tartalmaznia kell egy `ContentDefinitions` elemet. 

1. Az a `ContentDefinitions` területén adja meg egy új `ContentDefinition` azonosítójú `api.signuporsigninwithkmsi`.
2. Az új `ContentDefinition` tartalmaznia kell egy `LoadUri`, `RecoveryUri` és `DataUri` módon.
3. Datauri`urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` gép érthető azonosítója, amely megjelenik a bejelentkezési oldalak KMSI jelölőnégyzetből. Ellenőrizze, hogy nem módosíthatja ezt az értéket. 

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



## <a name="add-a--local-account-sign-in-claims-provider"></a>Helyi fiókkal jelentkezzen be a jogcímeket szolgáltató hozzáadása 

Megadhatja a helyi fiókba egy jogcímszolgáltatótól, mint a `<ClaimsProvider>` csomópont a szabályzat a bővítmény fájlban:

1. A bővítményfájl (TrustFrameworkExtensions.xml) nyissa meg a munkakönyvtár. 
2. Keresse meg a `<ClaimsProviders>` szakaszban. Ha nem létezik, adja hozzá a legfelső szintű csomópontja alatt.
3. Az alapszintű csomag [bevezetés](active-directory-b2c-get-started-custom.md) helyi fiókba Jogcímszolgáltatók is tartalmaz. 
4. Ha nem, akkor vegyen fel egy új `<ClaimsProvider>` csomópont az alábbiak szerint:

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
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
           </InputClaims>
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

## <a name="create-a-kmsi-in-enabled-user-journey"></a>Hozzon létre egy KMSI engedélyezett felhasználói interakciósorozat

Most szüksége helyi fiókba jogcím-szolgáltató hozzáadása a felhasználói interakciósorozat. 

1. Nyissa meg a szabályzat (például TrustFrameworkBase.xml) alapszintű fájlt.
2. Keresse meg a `<UserJourneys>` elem és a teljes másolási `<UserJourney>` tartalmazó csomópont `Id="SignUpOrSignIn"`.
3. Nyissa meg a kiterjesztésű fájlt (például TrustFrameworkExtensions.xml), és keresse meg a `<UserJourneys>` elemet. Ha az elem nem létezik, adjon hozzá egyet.
4. Illessze be a teljes `<UserJourney>` csomópont gyermekeként kimásolt a `<UserJourneys>` elemet.
5. Nevezze át az új felhasználói interakciósorozat azonosítója (mint például nevezze át `SignUpOrSignInWithKmsi`).
6. Végül a `OrchestrationStep 1` módosítsa a `ContentDefinitionReferenceId` való `api.signuporsigninwithkmsi` , meg kell definiálni a korábbi lépésekben. Ez lehetővé teszi a felhasználói interakciósorozatban szereplő be a jelölőnégyzetet. 
7. Elkészült a kiterjesztésű fájl módosítása. Mentse, és töltse fel ezt a fájlt. Győződjön meg arról, hogy az összes ellenőrzés sikeres.

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

## <a name="create-a-relying-party-rp-file"></a>Hozzon létre egy függő entitásonkénti (RP) fájlt

Ezután frissítse a függő entitásonkénti (RP) fájl, amely az Ön által létrehozott felhasználói interakciósorozat kezdeményezi:

1. A munkakönyvtárban SignUpOrSignIn.xml másolatának elkészítéséhez. Nevezze (például SignUpOrSignInWithKmsi.xml).

2. Nyissa meg az új fájlt, és frissítse a `PolicyId` az attribútum `<TrustFrameworkPolicy>` egyedi értékkel. Ez az a a szabályzat nevét (például SignUpOrSignInWithKmsi).

3. Módosítsa a `ReferenceId` attribútum `<DefaultUserJourney>` megfelelően a `Id` az új felhasználói út (például SignUpOrSignInWithKmsi) létrehozott.

4. KMSI van konfigurálva a `UserJourneyBehaviors`. 

5. **`KeepAliveInDays`** azt szabályozza, hogy mennyi ideig a felhasználó bejelentkezve marad. A következő példában KMSI munkamenet automatikusan függetlenül attól, hogy milyen gyakran a felhasználói beavatkozás nélküli hitelesítést hajt végre 14 nap elteltével jár le.

   Beállítás `KeepAliveInDays` 0 érték kikapcsolja a KMSI funkciót. Alapértelmezés szerint ez az érték a 0

6. Ha **`SessionExpiryType`** van *működés közbeni*, majd a KMSI munkamenet bővített 14 nappal minden alkalommal, amikor a felhasználó csendes hitelesítést hajt végre.  Ha *működés közbeni* van kijelölve, javasoljuk, hogy ahhoz, hogy legalább hány napig. 

       <RelyingParty>
       <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
       <UserJourneyBehaviors>
         <SingleSignOn Scope="Tenant" KeepAliveInDays="14" />
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

7. Mentse a módosításokat, majd feltölti a fájlt.

8. Ha tesztelni szeretné az egyéni házirend feltöltött, az Azure Portalon, nyissa meg a szabályzat paneljén, és kattintson **Futtatás most**.


## <a name="link-to-sample-policy"></a>Minta szabályzat csatolása

A minta házirendet annak [Itt](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).









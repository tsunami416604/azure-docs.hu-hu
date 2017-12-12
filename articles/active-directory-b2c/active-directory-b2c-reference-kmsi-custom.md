---
title: 'Az Azure Active Directory B2C: KMSI |} Microsoft Docs'
description: "A témakör bemutatja, hogyan lehet beállítani a \"me bejelentkezve megőrzése\""
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: mtillman
ms.assetid: 926e9711-71c0-49e8-b658-146ffb7386c0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2016
ms.author: vigunase
ms.openlocfilehash: a3d78945f862d1ae12cec05da0cf0ea7df511f43
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-enable-keep-me-signed-in-kmsi"></a>Az Azure Active Directory B2C: Engedélyezése "Megtartása (KMSI) bejelentkezve"  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure AD B2C mostantól lehetővé teszi, hogy a webes és a natív alkalmazásokhoz, hogy a "Szeretnék maradni (KMSI) megtartása" funkciót. Ez a szolgáltatás hozzáférést a felhasználók Visszatérés az alkalmazás a felhasználónév és jelszó újbóli értesítése nélkül. A hozzáférés vissza lett vonva, amikor a felhasználó kijelentkezik. 

Azt javasoljuk, a beállítás a nyilvános számítógépeken felhasználókkal szemben. 

![kép](images/kmsi.PNG)


## <a name="prerequisites"></a>Előfeltételek

Az Azure AD B2C-bérlő konfigurálva ahhoz, hogy a helyi fiók sign-Close-Up/sign-in a [bevezetés](active-directory-b2c-get-started-custom.md).

## <a name="how-to-enable-kmsi"></a>KMSI engedélyezése

A következő módosításokat a megbízhatósági keretrendszer bővítmények házirend.

## <a name="adding-a-content-definition-element"></a>A tartalom jelentésdefiníciós elem hozzáadása 

A `BuildingBlocks` csomópont a kiterjesztés fájl tartalmaznia kell egy `ContentDefinitions` elemet. 

1. Az a `ContentDefinitions` területen adja meg egy új `ContentDefinition` azonosítójú `api.signuporsigninwithkmsi`.
2. Az új `ContentDefinition` tartalmaznia kell egy `LoadUri`, `RecoveryUri` és `DataUri` az alábbiak szerint.
3. Datauri`urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` gép érthető azonosítója, amely megjelenik a bejelentkezési lapok KMSI jelölőnégyzet. Győződjön meg arról, hogy ne módosítsa ezt az értéket. 

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



## <a name="add-a--local-account-sign-in-claims-provider"></a>Adja hozzá egy helyi fiók bejelentkezési jogcím-szolgáltató 

A jogcímszolgáltató, megadhatja a helyi fiókkal bejelentkezik a `<ClaimsProvider>` csomópont házirend bővítmény fájlban:

1. A bővítményfájl (TrustFrameworkExtensions.xml) megnyitása a munkakönyvtárat. 
2. Keresés a `<ClaimsProviders>` szakasz. Ha még nem létezik, adja hozzá a legfelső szintű csomópontja alatt.
3. Az alapszintű csomag [bevezetés](active-directory-b2c-get-started-custom.md) tartalmaz egy "Helyi fiók SignIn" jogcím-szolgáltató. 
4. Ha nem, adjon hozzá egy új `<ClaimsProvider>` csomópont az alábbiak szerint:

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

### <a name="add-the-application-ids-to-your-custom-policy"></a>Az alkalmazás azonosítók hozzá az egyéni házirend

Adja hozzá a Alkalmazásazonosítók a bővítmények fájlhoz (`TrustFrameworkExtensions.xml`):

1. A bővítmények fájlban (TrustFrameworkExtensions.xml), az elem található `<TechnicalProfile Id="login-NonInteractive">` és`<TechnicalProfile Id="login-NonInteractive-PasswordChange">`

2. Cserélje le az összes példányát `IdentityExperienceFrameworkAppId` az identitás élmény keretrendszer alkalmazás leírtak szerint az alkalmazás azonosítójával [bevezetés](active-directory-b2c-get-started-custom.md). Például:

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. Cserélje le az összes példányát `ProxyIdentityExperienceFrameworkAppId` a Proxy identitás élmény keretrendszer alkalmazás leírtak szerint az alkalmazás azonosítójával [bevezetés](active-directory-b2c-get-started-custom.md).

4. A bővítmények fájl mentéséhez.

## <a name="create-a-kmsi-in-enabled-user-journey"></a>Hozzon létre egy KMSI engedélyezett felhasználók utazás

Most kell a helyi fiókkal bejelentkezik jogcím-szolgáltató hozzáadása a felhasználói használatában. 

1. Nyissa meg a házirend (például TrustFrameworkBase.xml) Alap fájlt.
2. Keresés a `<UserJourneys>` elemet, és másolja a teljes `<UserJourney>` tartalmazó csomópont `Id="SignUpOrSignIn"`.
3. Nyissa meg a bővítmény (például TrustFrameworkExtensions.xml) fájlt, és keresse a `<UserJourneys>` elemet. Ha az elem nem létezik, vegyen fel egyet.
4. Illessze be a teljes `<UserJourney>` csomópont gyermekeként másolt a `<UserJourneys>` elemet.
5. Nevezze át az új felhasználói út azonosítója (például a módon nevezze át `SignUpOrSignInWithKmsi`).
6. Végezetül a `OrchestrationStep 1` módosítása a `ContentDefinitionReferenceId` való `api.signuporsigninwithkmsi` , elvégezhessék a korábbi lépésekben. Ez lehetővé teszi, hogy a CheckBox osztály a felhasználói út. 
7. Befejezte a bővítmény fájl módosítása. Mentse, majd töltse fel ezt a fájlt. Győződjön meg arról, hogy az összes érvényesítések sikeres.

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

Következő lépésként frissítse a függő entitásonkénti (RP) fájl, amely kezdeményezi a létrehozott felhasználói út:

1. Készítsen másolatot a SignUpOrSignIn.xml a munkakönyvtárat. Nevezze át (például SignUpOrSignInWithKmsi.xml).

2. Nyissa meg az új fájlt, és frissítse a `PolicyId` az attribútum `<TrustFrameworkPolicy>` egyedi értékkel. Azt a nevet a házirend (például SignUpOrSignInWithKmsi).

3. Módosítsa a `ReferenceId` attribútumnak `<DefaultUserJourney>` kell egyeznie a `Id` az új felhasználói út (például SignUpOrSignInWithKmsi) létrehozott.

4. KMSI van konfigurálva a `UserJourneyBehaviors`. 

5. **`KeepAliveInDays`**Meghatározza, mennyi ideig felhasználó bejelentkezett marad. A következő példában KMSI munkamenet automatikusan függetlenül attól, milyen gyakran a felhasználói beavatkozás nélküli hitelesítést hajt végre 14 nap után jár le.

   Beállítás `KeepAliveInDays` 0 érték kikapcsolja a KMSI funkciót. Alapértelmezés szerint ez az érték: 0

6. Ha  **`SessionExpiryType`**  van *működés közbeni*, majd a KMSI munkamenet ki van bővítve 14 nap minden alkalommal, amikor a felhasználói beavatkozás nélküli hitelesítést hajt végre.  Ha *működés közbeni* van kijelölve, azt javasoljuk, hogy legalább hány nap nyomon. 

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

7. A módosítások mentéséhez, és majd feltölteni a fájlt.

8. Az egyéni házirend feltöltött, tesztelje az Azure portálon, nyissa meg a házirend paneljét, és kattintson **futtatása most**.


## <a name="link-to-sample-policy"></a>A minta szabályzatra hivatkozó

A minta házirend található [Itt](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).









---
title: Jogcímek hozzáadása és felhasználói bevitel testreszabása az egyéni házirendekben
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan szabhatja testre a felhasználói bevitelt, és hogyan adhat hozzá jogcímeket a regisztrációs vagy bejelentkezési utazáshoz az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 85f2ab6f8c3e5edda027e44eeda13a3279a88321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473676"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>Jogcímek hozzáadása és felhasználói bevitel testreszabása egyéni szabályzatok használatával az Azure Active Directory B2C-ben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ebben a cikkben egy új attribútumot gyűjt az Azure Active Directory B2C (Azure AD B2C) előfizetési folyamat során. Beszerzi a felhasználók városát, legördülő menüként konfigurálja, és meghatározza, hogy szükséges-e a megadása.

> [!NOTE]
> Ez a minta a beépített "város" követelést használja. Ehelyett választhat a támogatott [Azure AD B2C beépített attribútumok](user-profile-attributes.md) vagy egy egyéni attribútum. Egyéni attribútum használatához engedélyezze az [egyéni attribútumokat a házirendben.](custom-policy-custom-attributes.md) Ha egy másik beépített vagy egyéni attribútumot szeretne használni, cserélje le a "város" szót az Ön által választott attribútumra, például a beépített *jobTitle* attribútumra vagy egy olyan egyéni attribútumra, mint *extension_loyaltyId*.  

A felhasználóktól kezdeti adatokat gyűjthet a regisztrációs vagy bejelentkezési felhasználói úton. További jogcímek később is összegyűjthetők egy profil szerkesztése felhasználói út használatával. Bármikor, amikor az Azure AD B2C interaktív módon közvetlenül a felhasználótól gyűjt információkat, az Identitásélmény-keretrendszer az [önérvényesítő technikai profilját](self-asserted-technical-profile.md)használja. Ebben a példában a következők:

1. "Város" jogcím definiálása. 
1. Kérdezze meg a felhasználót a városát.
1. A város az Azure AD B2C címtárban a felhasználói profil t.
1. Olvassa el a városi jogcímet az Azure AD B2C címtárból minden bejelentkezéskor.
1. A bejelentkezés vagy a regisztráció után küldje vissza a várost a függő entitás alkalmazásába.  

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre az Egyéni házirendek – Első lépések című [lépéseit.](custom-policy-get-started.md) Rendelkeznie kell egy működő egyéni szabályzat a regisztrációhoz és a bejelentkezéshez a közösségi és helyi fiókok.

## <a name="define-a-claim"></a>Jogcím definiálása

A jogcím az Adatok ideiglenes tárolását biztosítja az Azure AD B2C-szabályzat végrehajtása során. A [jogcímséma](claimsschema.md) az a hely, ahol deklarálja a jogcímeket. A jogcím meghatározásához a következő elemek et használjuk:

- **DisplayName** - A felhasználó felé néző címkét meghatározó karakterlánc.
- [DataType](claimsschema.md#datatype) - A jogcím típusa.
- **UserHelpText** - Segít a felhasználónak megérteni, mi szükséges.
- [UserInputType](claimsschema.md#userinputtype) - A beviteli vezérlő típusa, például szövegdoboz, rádiókijelölés, legördülő lista vagy többszörös kijelölés.

Nyissa meg a házirend bővítményfájlját. <em> `SocialAndLocalAccounts/` </em>Például.

1. Keresse meg a [BuildingBlocks](buildingblocks.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Keresse meg a [ClaimsSchema](claimsschema.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá a városjogcíma a **ClaimsSchema** elem.  

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

## <a name="add-a-claim-to-the-user-interface"></a>Jogcím hozzáadása a felhasználói felülethez

A következő technikai profilok [saját érvényesítésre](self-asserted-technical-profile.md)kerülnek, akkor hívnak meg, ha a felhasználónak bemenetet kell megadnia:

- **LocalAccountSignUpWithLogonEmail** – Helyi fiók regisztrációs folyamata.
- **SelfAsserted-Social** - Federated fiók első alkalommal a felhasználó bejelentkezés.
- **SelfAsserted-ProfileUpdate** - Profilfolyamat szerkesztése.

A városjogcím beszedéséhez a regisztráció során hozzá kell `LocalAccountSignUpWithLogonEmail` adni a technikai profil kimeneti jogcímként. Felülbírálja ezt a technikai profilt a bővítményfájlban. Adja meg a kimeneti jogcímek teljes listáját a jogcímek képernyőn megjelenő sorrendjének szabályozásához. Keresse meg a **ClaimsProviders** elemet. Adjon hozzá új kárrendezési szolgáltatókat az alábbiak szerint:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
<ClaimsProvider>
```

A város jogcímének összegyűjtése után a kezdeti bejelentkezés egy összevont fiókkal, `SelfAsserted-Social` hozzá kell adni, mint egy kimeneti jogcím a technikai profilhoz. Ahhoz, hogy a helyi és összevont fiók felhasználók később szerkeszthessék `SelfAsserted-ProfileUpdate` a profiladataikat, adja hozzá a kimeneti jogcímet a technikai profilhoz. Felülbírálja ezeket a technikai profilokat a bővítményfájlban. Adja meg a kimeneti jogcímek teljes listáját a jogcímek képernyőn megjelenő sorrendjének szabályozásához. Keresse meg a **ClaimsProviders** elemet. Adjon hozzá új kárrendezési szolgáltatókat az alábbiak szerint:

```xml
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>Jogcím olvasása és írása

A következő technikai profilok [az Active Directory technikai profiljai,](active-directory-technical-profile.md)amelyek adatokat olvasnak és írnak az Azure Active Directoryba.  
Adatok `PersistedClaims` írása a felhasználói `OutputClaims` profilba, valamint az adott Active Directory technikai profilon belüli felhasználói profiladatainak olvasása.

Felülbírálja ezeket a technikai profilokat a bővítményfájlban. Keresse meg a **ClaimsProviders** elemet.  Adjon hozzá új kárrendezési szolgáltatókat az alábbiak szerint:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>Jogcím felvétele a jogkivonatba 

Ha vissza szeretné küldeni a városjogcímeket a függő <em> `SocialAndLocalAccounts/` </em> entitás alkalmazásnak, adjon hozzá egy kimeneti jogcímet a fájlhoz. A kimeneti jogcím sikeres felhasználói út után hozzáadódik a jogkivonathoz, és az alkalmazásnak kerül elküldésre. Módosítsa a technikai profil elem a függő entitás szakaszban, hogy adja hozzá a várost, mint egy kimeneti jogcím.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Az egyéni házirend tesztelése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja az Azure AD-bérlőt tartalmazó könyvtárat.
3. Válassza az **Összes szolgáltatás lehetőséget** az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Alkalmazásregisztrációk lehetőséget.**
4. Válassza **az Identitáskezelési keretrendszert**.
5. Válassza **az Egyéni házirend feltöltése**lehetőséget, majd töltse fel a két módosított házirendfájlt.
2. Jelölje ki a feltöltött regisztrációs vagy bejelentkezési szabályzatot, és kattintson a **Futtatás gombra.**
3. Önnek képesnek kell lennie arra, hogy iratkozzon fel egy e-mail címet.

A regisztrációs képernyőnek az alábbi képernyőképhez hasonlóan kell kinéznie:

![Képernyőkép a módosított előfizetési lehetőségről](./media/custom-policy-configure-user-input/signup-with-city-claim-dropdown-example.png)

Az alkalmazásnak küldött jogkivonat `city` tartalmazza a jogcímet.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Bellevue"
  ...
}
```

## <a name="next-steps"></a>További lépések

- További információ a [ClaimsSchema](claimsschema.md) elemről az IEF-hivatkozásban.
- Megtudhatja, hogy [miként használhatja az egyéni attribútumokat egyéni profilszerkesztési házirendben.](custom-policy-custom-attributes.md)

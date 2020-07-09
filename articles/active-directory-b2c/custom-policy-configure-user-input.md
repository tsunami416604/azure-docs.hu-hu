---
title: Jogcímek hozzáadása és felhasználói bevitel testreszabása Egyéni házirendekben
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan szabhatja testre a felhasználói adatokat, és hogyan adhat hozzá jogcímeket a regisztrálási vagy bejelentkezési utazáshoz Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 47fdf445fa11693dd3a998b8c73ac0c3ed8452a8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85389360"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>Jogcímek hozzáadása és felhasználói bevitel testreszabása Egyéni házirendek használatával Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ebben a cikkben egy új attribútumot gyűjt a regisztráció során Azure Active Directory B2C (Azure AD B2C). Megszerezheti a felhasználók városát, legördülőként konfigurálhatja, és meghatározhatja, hogy meg kell-e adni.

> [!NOTE]
> Ez a példa a beépített "City" jogcímet használja. Ehelyett kiválaszthatja az egyik támogatott [Azure ad B2C beépített attribútumot](user-profile-attributes.md) vagy egy egyéni attribútumot is. Ha egyéni attribútumot szeretne használni, [engedélyezze az egyéni attribútumokat a házirendben](custom-policy-custom-attributes.md). Ha más beépített vagy egyéni attribútumot szeretne használni, cserélje le a "City" kulcsszót az Ön által választott attribútumra, például a beépített attribútum *beosztás* vagy egy olyan egyéni attribútumra, mint a *extension_loyaltyId*.  

A felhasználóktól származó kezdeti adatokat a regisztrációs vagy bejelentkezési felhasználói úton gyűjtheti be. A további jogcímek később is összegyűjthetők, ha a felhasználói úton szerkeszti a profilt. A bármikor Azure AD B2C az adatokat közvetlenül a felhasználótól gyűjti össze interaktív módon, az Identity Experience Framework saját [maga által megadott műszaki profilt](self-asserted-technical-profile.md)használja. Ebben a példában a következőket látja:

1. Adja meg a "város" jogcímet. 
1. Kérje meg a felhasználót a városra.
1. A Azure AD B2C könyvtárban maradjon a város a felhasználói profilhoz.
1. Olvassa el a városi jogcímet az Azure AD B2C könyvtárából minden bejelentkezéskor.
1. A bejelentkezés vagy a regisztráció után adja vissza a várost a függő entitás alkalmazásához.  

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre az [Ismerkedés az egyéni szabályzatokkal](custom-policy-get-started.md)című témakör lépéseit. A közösségi és helyi fiókokkal való bejelentkezéshez és a bejelentkezéshez egy működő egyéni szabályzatot kell használnia.

## <a name="define-a-claim"></a>Jogcím definiálása

A jogcím a Azure AD B2C szabályzat végrehajtása során ideiglenes adattárolást biztosít. A [jogcím-séma](claimsschema.md) az a hely, ahol deklarálja a jogcímeket. A jogcím definiálásához a következő elemek használhatók:

- **DisplayName** – a felhasználó felé irányuló címkét meghatározó karakterlánc.
- [Adattípus](claimsschema.md#datatype) – a jogcím típusa.
- **UserHelpText** – segít a felhasználónak megérteni, hogy mi szükséges.
- [UserInputType](claimsschema.md#userinputtype) – a beviteli vezérlőelem típusa, például a szövegmező, a választógomb, a legördülő lista vagy több lehetőség.

Nyissa meg a szabályzat Extensions (bővítmények) fájlját. Például: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Keresse meg a [BuildingBlocks](buildingblocks.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Keresse meg a [ClaimsSchema](claimsschema.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá a város jogcímet a **ClaimsSchema** elemhez.  

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

A következő műszaki profilok [önmagukban vannak érvényesítve](self-asserted-technical-profile.md), amikor a felhasználónak meg kell adnia a bemenetet:

- **LocalAccountSignUpWithLogonEmail** – helyi fiók regisztrálási folyamata.
- **SelfAsserted – közösségi** összevont fiók – az első alkalommal bejelentkezett felhasználói bejelentkezés.
- **SelfAsserted-ProfileUpdate** – a profil folyamatának szerkesztése.

Ahhoz, hogy regisztrálni lehessen a városi jogcímet a regisztráció során, kimeneti jogcímként hozzá kell adni a `LocalAccountSignUpWithLogonEmail` technikai profilhoz. Bírálja felül ezt a technikai profilt a kiterjesztési fájlban. Adja meg a kimeneti jogcímek teljes listáját, hogy szabályozni lehessen a jogcímek megjelenítésének sorrendjét a képernyőn. Keresse meg a **ClaimsProviders** elemet. Vegyen fel egy új ClaimsProviders a következőképpen:

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

A városi jogcímek összevont fiókkal való első bejelentkezés után történő összegyűjtéséhez hozzá kell adni a technikai profil kimeneti jogcímeként `SelfAsserted-Social` . Ahhoz, hogy a helyi és összevont fiókok a felhasználók később szerkesszék a profiljaikat, adja hozzá a kimeneti jogcímet a `SelfAsserted-ProfileUpdate` technikai profilhoz. Bírálja felül ezeket a technikai profilokat a kiterjesztési fájlban. Adja meg a kimeneti jogcímek teljes listáját, hogy szabályozni lehessen a jogcímek megjelenítésének sorrendjét a képernyőn. Keresse meg a **ClaimsProviders** elemet. Vegyen fel egy új ClaimsProviders a következőképpen:

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

A következő műszaki profilok [Active Directory műszaki profilok](active-directory-technical-profile.md), amelyek az Azure Active Directoryba való adatolvasást és-írást írják le.  
A használatával `PersistedClaims` adatok írhatók a felhasználói profilba, valamint az `OutputClaims` adatok beolvasása a felhasználói profilból a megfelelő Active Directory technikai profilokban.

Bírálja felül ezeket a technikai profilokat a kiterjesztési fájlban. Keresse meg a **ClaimsProviders** elemet.  Vegyen fel egy új ClaimsProviders a következőképpen:

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

## <a name="include-a-claim-in-the-token"></a>Jogcím belefoglalása a jogkivonatba 

Ha vissza szeretné állítani a város jogcímet a függő entitás alkalmazásba, adjon hozzá egy kimeneti jogcímet a <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> fájlhoz. A kimeneti jogcím a sikeres felhasználói út után lesz hozzáadva a jogkivonathoz, és a rendszer elküldi az alkalmazásnak. Módosítsa a technikai profil elemet a függő entitás szakaszban a város kimeneti jogcímként való hozzáadásához.
 
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

## <a name="test-the-custom-policy"></a>Egyéni szabályzat tesztelése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki az Azure ad-bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Alkalmazásregisztrációk**.
4. Válassza az **identitási élmény keretrendszert**.
5. Válassza az **egyéni házirend feltöltése**lehetőséget, majd töltse fel a módosított két házirendet.
2. Válassza ki a feltöltött regisztrációs vagy bejelentkezési szabályzatot, majd kattintson a **Futtatás most** gombra.
3. Regisztrálnia kell egy e-mail-cím használatával.

A regisztrációs képernyőnek az alábbi képernyőképhez hasonlóan kell kinéznie:

![A módosított regisztrációs lehetőség képernyőképe](./media/custom-policy-configure-user-input/signup-with-city-claim-dropdown-example.png)

Az alkalmazásnak visszaadott jogkivonat tartalmazza a `city` jogcímet.

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

- További információ a [ClaimsSchema](claimsschema.md) elemről a IEF-hivatkozásban.
- Megtudhatja, hogyan használhatja az egyéni [attribútumokat egyéni profil szerkesztése házirendben](custom-policy-custom-attributes.md).

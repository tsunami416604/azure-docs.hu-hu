---
title: Módosítsa a bejelentkezési be az egyéni házirendek és önkiszolgáló magas szolgáltató konfigurálása |} Microsoft Docs
description: Egy általános bemutató hozzáadása regisztrálhat és konfigurálása a felhasználó által megadott jogcímek
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/29/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9d8f644e819ceb83f0b436789d6d8610ed01f6a6
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "34710797"
---
# <a name="azure-active-directory-b2c-modify-sign-up-to-add-new-claims-and-configure-user-input"></a>Az Azure Active Directory B2C: A módosítás jelentkezzen be új jogcímeket adhatnak hozzá, és konfigurálja a felhasználói bevitel.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ebben a cikkben egy új felhasználó által megadott bejegyzés (a jogcímek) fogja hozzáadni a bejelentkezési felhasználói használatában.  A bejegyzés konfigurálása, a legördülő menüből, és szükség esetén adja meg.

## <a name="prerequisites"></a>Előfeltételek

* Hajtsa végre a cikk a [Ismerkedés az egyéni házirendek](active-directory-b2c-get-started-custom.md).  A regisztráció vagy bejelentkezés felhasználói utazás előfizetési egy új helyi fiók a folytatás előtt tesztelje.


A felhasználók kezdeti adatgyűjtés az előfizetési/signin keresztül érhető el.  További jogcímek később gyűjthetők profil szerkesztése felhasználói utak keresztül. Az Azure AD B2C gyűjtse össze az adatokat közvetlenül a felhasználó interaktív módon, bármikor identitás élmény keretében használja a `selfasserted provider`. Az alábbi lépéseket alkalmazni, bármikor ezt a szolgáltatót használja.


## <a name="define-the-claim-its-display-name-and-the-user-input-type"></a>A jogcímek, a megjelenített név és a felhasználó bemeneti típus megadása
Lehetővé teszi, hogy azok városhoz kérnie a felhasználót.  A következő elem hozzáadása a `<ClaimsSchema>` a TrustFrameworkBase házirend fájlban:

```xml
<ClaimType Id="city">
  <DisplayName>city</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```
Nincsenek további lehetőségek tehet itt testre szabhatja a jogcímek.  A teljes séma, tekintse meg a **identitás élmény keretrendszer a műszaki referencia-útmutató**.  Ez az útmutató az útmutató szakaszban a közeljövőben lesznek közzétéve.

* `<DisplayName>` egy karakterlánc, amely meghatározza a felhasználók számára is elérhető *címke*

* `<UserHelpText>` a rendszer kötelező felhasználó segítségével

* `<UserInputType>` a következő négy beállítás alatt van kiemelve:
    * `TextBox`
```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

    * `RadioSingleSelectduration` -Egyszeres kijelölésnél érvényesíti.
```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

    * `DropdownSingleSelect` -Lehetővé teszi, hogy a kijelölés csak érvényes érték.

![Képernyőfelvétel a legördülő lista lehetőséget](./media/active-directory-b2c-configure-signup-self-asserted-custom/dropdown-menu-example.png)


```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```


* `CheckboxMultiSelect` Lehetővé teszi a kijelölt egy vagy több értéket.

![Képernyőkép a multiselect beállítás](./media/active-directory-b2c-configure-signup-self-asserted-custom/multiselect-menu-example.png)


```xml
<ClaimType Id="city">
  <DisplayName>Receive updates from which cities?</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

## <a name="add-the-claim-to-the-sign-upsign-in-user-journey"></a>Vegye fel a kérelmet a bejelentkezési felhasználói út felfelé vagy bejelentkezés

1. Adja hozzá a jogcímek, az `<OutputClaim ClaimTypeReferenceId="city"/>` a TechnicalProfile való `LocalAccountSignUpWithLogonEmail` (a TrustFrameworkBase házirend fájlban található).  Vegye figyelembe a TechnicalProfile használja a SelfAssertedAttributeProvider.

  ```xml
  <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
    <DisplayName>Email signup</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
      <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
      <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
      <Item Key="language.button_continue">Create</Item>
    </Metadata>
    <CryptographicKeys>
      <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
    </CryptographicKeys>
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" />
      <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
      <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
      <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
      <OutputClaim ClaimTypeReferenceId="authenticationSource" />
      <OutputClaim ClaimTypeReferenceId="newUser" />
      <!-- Optional claims, to be collected from the user -->
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surName" />
      <OutputClaim ClaimTypeReferenceId="city"/>
    </OutputClaims>
    <ValidationTechnicalProfiles>
      <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
    </ValidationTechnicalProfiles>
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
  </TechnicalProfile>
  ```

2. Az AAD-UserWriteUsingLogonEmail, a jogcím hozzáadása egy `<PersistedClaim ClaimTypeReferenceId="city" />` az AAD-címtárában, a felhasználó összegyűjtése után a jogcím írni. Ezt a lépést kihagyhatja, ha nem szeretné megőrizni a későbbi használatra a könyvtárban a jogcímet.

  ```xml
  <!-- Technical profiles for local accounts -->
  <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
    <Metadata>
      <Item Key="Operation">Write</Item>
      <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    </Metadata>
    <IncludeInSso>false</IncludeInSso>
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" Required="true" />
    </InputClaims>
    <PersistedClaims>
      <!-- Required claims -->
      <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
      <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password" />
      <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
      <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
      <!-- Optional claims. -->
      <PersistedClaim ClaimTypeReferenceId="givenName" />
      <PersistedClaim ClaimTypeReferenceId="surname" />
      <PersistedClaim ClaimTypeReferenceId="city" />
    </PersistedClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" />
      <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
      <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
      <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
      <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    </OutputClaims>
    <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
  </TechnicalProfile>
  ```

3. Az olvasó a könyvtárból, amikor a felhasználó jelentkezik be a TechnicalProfile jogcím hozzáadása egy `<OutputClaim ClaimTypeReferenceId="city" />`

  ```xml
  <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
    <Metadata>
      <Item Key="Operation">Read</Item>
      <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">An account could not be found for the provided user ID.</Item>
    </Metadata>
    <IncludeInSso>false</IncludeInSso>
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames" Required="true" />
    </InputClaims>
    <OutputClaims>
      <!-- Required claims -->
      <OutputClaim ClaimTypeReferenceId="objectId" />
      <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
      <!-- Optional claims -->
      <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="otherMails" />
      <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  </TechnicalProfile>
  ```

4. Adja hozzá a `<OutputClaim ClaimTypeReferenceId="city" />` SignUporSignIn.xml RP szabályzat fájl, ezért ezt a kérelmet küld az alkalmazás a jogkivonat a felhasználó sikeres út után.

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
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ```

## <a name="test-the-custom-policy-using-run-now"></a>Az egyéni házirend használatával "Futtatás most" tesztelése

1. Nyissa meg a **panel az Azure AD B2C** , és keresse meg **identitás élmény keretrendszer > egyéni házirendek**.
2. Válassza ki az egyéni házirendet, feltöltött, majd kattintson a **futtatása most** gombra.
3. Iratkozhat fel e-mail cím használatával kell lennie.

A regisztráció képernyő tesztmódban ehhez hasonlóan kell kinéznie:

![Képernyőkép a módosított előfizetési lehetőség](./media/active-directory-b2c-configure-signup-self-asserted-custom/signup-with-city-claim-dropdown-example.png)

  A jogkivonat az alkalmazás most már tartalmazza a `city` jogcím a lent látható módon
```json
{
  "exp": 1493596822,
  "nbf": 1493593222,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "9c2a3a9e-ac65-4e46-a12d-9557b63033a9",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustf_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1493593222,
  "auth_time": 1493593222,
  "email": "joe@outlook.com",
  "given_name": "Joe",
  "family_name": "Ras",
  "city": "Bellevue",
  "name": "unknown"
}
```

## <a name="optional-remove-email-verification-from-signup-journey"></a>Választható lehetőség: Távolítsa el e-mail ellenőrzése az előfizetési út

E-mail-ellenőrzés kihagyása a házirend Szerző beállíthatja úgy a eltávolítása `PartnerClaimType="Verified.Email"`. Az e-mail cím lesz szükség, de nincs ellenőrizve, kivéve, ha a "Kötelező" = true törlődik.  Alaposan fontolja meg, hogy ez a beállítás a használati esetek számára megfelelő!

Alapértelmezés szerint engedélyezve van a e-mailek ellenőrzése a `<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">` az alapszintű csomag TrustFrameworkBase házirend fájlban:
```xml
<OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
```

## <a name="next-steps"></a>További lépések

Adja hozzá a közösségi fiók bejelentkezések során a viszonylatában új jogcímet a lenti TechnicalProfiles módosításával. Ezek használhatók társadalombiztosítási/összevont fiók bejelentkezések által írható és olvasható a felhasználói adatokat a alternativeSecurityId használja, mint a lokátor.
```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```

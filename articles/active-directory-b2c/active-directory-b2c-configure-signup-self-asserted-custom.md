---
title: Módosítása jelentkezzen be az egyéni házirendek és a saját kiszolgáló által megerősített szolgáltató konfigurálása |} A Microsoft Docs
description: Regisztráljon, és a felhasználó által megadott adatok konfigurálása jogcímek hozzáadását bemutató
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1a949007750ae9607ac31f02d23e39204b9f58e4
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440501"
---
# <a name="azure-active-directory-b2c-modify-sign-up-to-add-new-claims-and-configure-user-input"></a>Az Azure Active Directory B2C: Bejelentkezés módosítása be új jogcímeket adhatnak hozzá és felhasználó által megadott adatok konfigurálása.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk egy új felhasználó által megadott bejegyzés (jogcím) adnak hozzá a regisztráció felhasználói interakciósorozat.  A bejegyzés konfigurálása, a legördülő listából, és határozza meg, ha szükséges.

## <a name="prerequisites"></a>Előfeltételek

* A cikkben leírtak elvégzése [Ismerkedés az egyéni szabályzatok](active-directory-b2c-get-started-custom.md).  Tesztelje a folytatás előtt egy új helyi fiókot regisztrálni szeretne a regisztráció/bejelentkezés felhasználói interakciósorozat.


A regisztráció/bejelentkezés keresztül érhető el a felhasználók kezdeti adatgyűjtést.  További jogcímek később gyűjthetők profil szerkesztése felhasználói utak keresztül. Bármikor az Azure AD B2C-vel információt gyűjt közvetlenül a felhasználó interaktív módon, az identitás-kezelőfelületi keretrendszer használ annak `selfasserted provider`. Az alábbi lépéseket bármikor ezt a szolgáltatót használja a alkalmazni.


## <a name="define-the-claim-its-display-name-and-the-user-input-type"></a>A jogcím, a megjelenítendő nevét és a felhasználói adatbevitel típusa
Lehetővé teszi, hogy a felhasználó kérése az városa.  Adja hozzá a következő elemet, a `<ClaimsSchema>` elem a TrustFrameworkBase házirend fájlban:

```xml
<ClaimType Id="city">
  <DisplayName>city</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```
Nincsenek további választható lehetőségeket Itt szabhatja testre a jogcímet.  A teljes séma, tekintse meg a **identitás élmény keretrendszer műszaki referencia-útmutató**.  Ez az útmutató hamarosan közzé lesz téve a hivatkozási szakaszban.

* `<DisplayName>` egy karakterlánc, amely meghatározza a felhasználó által használt *felirat*

* `<UserHelpText>` segít a szükséges ismertetése

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

    * `RadioSingleSelectduration` – Egyetlen kijelölésre érvénybe lépteti.
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

    * `DropdownSingleSelect` – Csak az érvényes érték a kiválasztását teszi lehetővé.

![Képernyőfelvétel a lehetőségéről](./media/active-directory-b2c-configure-signup-self-asserted-custom/dropdown-menu-example.png)


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


* `CheckboxMultiSelect` Lehetővé teszi, hogy a kijelölt egy vagy több értéket.

![Képernyőkép a többszörös kiválasztási lehetőséget](./media/active-directory-b2c-configure-signup-self-asserted-custom/multiselect-menu-example.png)


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

## <a name="add-the-claim-to-the-sign-upsign-in-user-journey"></a>Adja hozzá a bejelentkezést a jogcím up/sign felhasználói interakciósorozatban szereplő

1. A jogcím, adjon hozzá egy `<OutputClaim ClaimTypeReferenceId="city"/>` , a TechnicalProfile `LocalAccountSignUpWithLogonEmail` (a TrustFrameworkBase házirend fájlban található).  Vegye figyelembe a TechnicalProfile a SelfAssertedAttributeProvider használja.

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

2. Az AAD-UserWriteUsingLogonEmail, mint a jogcím hozzáadása egy `<PersistedClaim ClaimTypeReferenceId="city" />` az AAD-címtárában, a felhasználó összegyűjtése után a jogcím írni. Ezt a lépést kihagyhatja, ha nem szeretné megőrizni a jogcím későbbi használatra a könyvtárban.

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

3. Adja hozzá a TechnicalProfile, amely a könyvtárból olvassa be, amikor egy felhasználó bejelentkezik a jogcím- `<OutputClaim ClaimTypeReferenceId="city" />`

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

4. Adja hozzá a `<OutputClaim ClaimTypeReferenceId="city" />` SignUporSignIn.xml az RP-házirendhez fájlt, így a sikeres felhasználói út után az alkalmazás a jogkivonatban a jogcím megkap.

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

## <a name="test-the-custom-policy-using-run-now"></a>Tesztelje a egyéni szabályzat "Futtatás most" segítségével

1. Nyissa meg a **Azure AD B2C paneljén** , és keresse meg **identitás-kezelőfelületi keretrendszer > egyéni szabályzatok**.
2. Válassza ki az egyéni házirend feltöltött, majd kattintson a **Futtatás most** gombra.
3. Regisztráció e-mail-címmel kell lennie.

A regisztrációs képernyő tesztmódban ehhez hasonlóan kell kinéznie:

![Képernyőkép a módosított regisztrációs lehetőség](./media/active-directory-b2c-configure-signup-self-asserted-custom/signup-with-city-claim-dropdown-example.png)

  Most már tartalmazza a tokent, az alkalmazásnak a `city` jogcím, ahogy az alábbi
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

## <a name="optional-remove-email-verification-from-signup-journey"></a>Választható lehetőség: Távolítsa el e-mailes ellenőrzés az előfizetési utazás

E-mail-ellenőrzés kihagyása, hogy a szabályzat Szerző lehet váltani, távolítsa el `PartnerClaimType="Verified.Email"`. Az e-mail-cím lesz szükség, de nem ellenőrzi, kivéve, ha a "Kötelező" = true törlődik.  Alaposan gondolja át, ha ez a beállítás akkor megfelelő, a használati esetek!

Ellenőrzött e-mail alapértelmezés szerint engedélyezve van a `<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">` az alapszintű csomag TrustFrameworkBase házirend fájlban:
```xml
<OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
```

## <a name="next-steps"></a>További lépések

Adja hozzá az új jogcímet a flow közösségi fiók bejelentkezések az alább felsorolt TechnicalProfiles módosításával. Ezek írása és olvasása a felhasználói adatokat a alternativeSecurityId használja, mint a lokátor fiók társadalombiztosítási/összevont bejelentkezéseket használják.
```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```

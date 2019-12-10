---
title: Önérvényesített technikai profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: Önérvényesített műszaki profilt definiálhat egy egyéni szabályzatban Azure Active Directory B2Cban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bfa8982fb49b31540d1926bdeb75a96dc1d79cf0
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950901"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Önérvényesített technikai profil definiálása egy Azure Active Directory B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) összes olyan interakciója, ahol a felhasználónak meg kell adnia a bemenetet, saját maga által megadott technikai profilok. Például egy regisztrációs oldal, bejelentkezési oldal vagy jelszó-visszaállítási oldal.

## <a name="protocol"></a>Protocol (Protokoll)

A **protokoll** elem **Name** attribútumát `Proprietary`értékre kell állítani. A **kezelő** attribútumnak tartalmaznia kell a Azure ad B2C által használt protokollkezelő-szerelvény teljes nevét, amely a következő: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Az alábbi példa egy önérvényesített technikai profilt mutat be az e-mailek regisztrálásához:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Bemeneti jogcímek

Önérvényesített technikai profilban a **szabályzattípushoz** és a **InputClaimsTransformations** elemek használatával előre feltöltheti az önérvényesített lapon megjelenő jogcímek értékét (kimeneti jogcímek). Például a profil szerkesztése területen a felhasználói út először beolvassa a felhasználói profilt a Azure AD B2C Directory szolgáltatásból, majd az önérvényesített technikai profil beállítja a bemeneti jogcímeket a felhasználói profilban tárolt felhasználói adatokkal. Ezeket a jogcímeket a rendszer a felhasználói profilból gyűjti be, majd bemutatja azt a felhasználót, aki szerkesztheti a meglévő adatokat.

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```


## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem tartalmazza azon jogcímek listáját, amelyeket be kell mutatni az adatoknak a felhasználótól való összegyűjtéséhez. A kimeneti jogcímek egyes értékekkel való előzetes feltöltéséhez használja a korábban leírt bemeneti jogcímeket. Az elem tartalmazhat alapértelmezett értéket is. A **OutputClaims** lévő jogcímek sorrendje vezérli azt a sorrendet, amely Azure ad B2C a jogcímeket a képernyőn jeleníti meg. A **DefaultValue** attribútum csak akkor lép életbe, ha a jogcímet korábban még nem állították be. Ha azonban korábban már be van állítva egy korábbi előkészítési lépésben, akkor is, ha a felhasználó üresen hagyja az értéket, az alapértelmezett érték nem lép érvénybe. Az alapértelmezett érték használatának kényszerítéséhez állítsa `true`értékre a **AlwaysUseDefaultValue** attribútumot. Ha szeretné kényszeríteni a felhasználót, hogy adjon meg egy értéket egy adott kimeneti jogcím számára, állítsa a **OutputClaims** elem **kötelező** attribútumát `true`értékre.

A **OutputClaims** -gyűjtemény **claimType** elemének a **UserInputType** elemet kell beállítania a Azure ad B2C által támogatott összes felhasználói beviteli típushoz, például `TextBox` vagy `DropdownSingleSelect`. Vagy a **OutputClaim** elemnek egy **DefaultValue**-t kell beállítania.

A **OutputClaimsTransformations** elem olyan **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosítására vagy újak előállítására szolgálnak.

A következő kimeneti jogcím mindig `live.com`ra van beállítva:

```XML
<OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" AlwaysUseDefaultValue="true" />
```

### <a name="use-case"></a>Használati eset

A kimeneti jogcímek négy forgatókönyvből állnak:

- A **kimeneti jogcímek összegyűjtése a felhasználótól** – Ha adatokat kell gyűjtenie a felhasználótól, például a születési dátumtól, a jogcímet hozzá kell adnia a **OutputClaims** gyűjteményhez. A felhasználónak megjelenő jogcímeknek meg kell adniuk a **UserInputType**, például `TextBox` vagy `DropdownSingleSelect`. Ha az önérvényesített technikai profil egy olyan érvényesítési műszaki profilt tartalmaz, amely ugyanazt a jogcímet adja vissza, Azure AD B2C nem jelent jogcímet a felhasználónak. Ha nincs olyan kimeneti jogcím, amely a felhasználó felé mutat, Azure AD B2C kihagyja a technikai profilt.
- **Alapértelmezett érték beállítása a kimeneti jogcímek esetében** – a felhasználótól származó adatok begyűjtése vagy az érvényesítési technikai profilból való visszaküldése nélkül. A `LocalAccountSignUpWithLogonEmail` önérvényesített technikai profil állítja be a **végrehajtott SelfAsserted-bemeneti** jogcímet, hogy `true`.
- **Az érvényesítési technikai profil a kimeneti jogcímeket adja vissza** – a technikai profil egy érvényesítési műszaki profilt hívhat meg, amely visszaadja a jogcímeket. Előfordulhat, hogy fel kívánja készíteni a jogcímeket, és visszaadja azokat a felhasználói utazás következő hangolási lépéseihez. Ha például helyi fiókkal jelentkezik be, a `SelfAsserted-LocalAccountSignin-Email` nevű önérvényesített technikai profil meghívja a `login-NonInteractive`nevű érvényesítési technikai profilt. Ez a technikai profil ellenőrzi a felhasználói hitelesítő adatokat, és a felhasználói profilt is visszaadja. Ilyen például a "userPrincipalName", a "displayName", a "givenName" és a "vezetéknév".
- **Jogcímek kimeneti jogcímek átalakításán keresztüli kimenete**

A következő példában a `LocalAccountSignUpWithLogonEmail` önérvényesített technikai profil a kimeneti jogcímek használatát mutatja be, és a **SelfAsserted-input** parancsot a `true`. A `objectId`, `authenticationSource``newUser` jogcímek a `AAD-UserWriteUsingLogonEmail` érvényesítési technikai profil kimenetét jelentik, és nem jelennek meg a felhasználó számára.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
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
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surName" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>

```

## <a name="persist-claims"></a>Jogcímek fenntartása

Ha a **PersistedClaims** elem hiányzik, az önérvényesített technikai profil nem őrzi meg Azure ad B2C. Ehelyett egy olyan érvényesítési technikai profilra irányuló hívás történik, amely felelős az adatmegőrzésért. A regisztrálási szabályzat például a `LocalAccountSignUpWithLogonEmail` önérvényesített technikai profilt használja az új felhasználói profil összegyűjtéséhez. A `LocalAccountSignUpWithLogonEmail` műszaki profil az érvényesítési technikai profilt hívja meg a fiók létrehozásához Azure AD B2Cban.

## <a name="validation-technical-profiles"></a>Ellenőrzési technikai profilok

Egy érvényesítési műszaki profil a hivatkozó technikai profil kimeneti jogcímeinek érvényesítésére szolgál. Az érvényesítési technikai profil bemeneti jogcímeit az önérvényesített technikai profil kimeneti jogcímeiben kell megjelennie. Az érvényesítési technikai profil ellenőrzi a felhasználó által megadott adatokat, és hibát ad vissza a felhasználónak.

Az érvényesítési technikai profil a szabályzat bármely műszaki profilja lehet, például [Azure Active Directory](active-directory-technical-profile.md) vagy egy [REST API](restful-technical-profile.md) technikai profil. Az előző példában a `LocalAccountSignUpWithLogonEmail` technikai profil ellenőrzi, hogy a signinName nem létezik-e a könyvtárban. Ha nem, az érvényesítési technikai profil létrehoz egy helyi fiókot, és visszaadja a objectId, a authenticationSource, a newUser. A `SelfAsserted-LocalAccountSignin-Email` műszaki profil a felhasználói hitelesítő adatok ellenőrzéséhez meghívja a `login-NonInteractive` ellenőrzési technikai profilt.

Az üzleti logikával meghívhat egy REST API technikai profilt, felülírhatja a bemeneti jogcímeket, vagy gazdagíthatja a felhasználói adatokat a vállalati üzletági alkalmazások további integrálásával. További információ: [érvényesítési technikai profil](validation-technical-profile.md)

## <a name="metadata"></a>Metaadatok

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Setting. showContinueButton | Nem | Megjeleníti a folytatás gombot. Lehetséges értékek: `true` (alapértelmezett) vagy `false` |
| Setting. showCancelButton | Nem | Megjeleníti a Mégse gombot. Lehetséges értékek: `true` (alapértelmezett) vagy `false` |
| Setting. operatingMode | Nem | A bejelentkezési oldalon ez a tulajdonság szabályozza a Felhasználónév mező viselkedését, például a bemeneti ellenőrzés és a hibaüzenetek értékét. Várt értékek: `Username` vagy `Email`. |
| ContentDefinitionReferenceId | Igen | Az ehhez a technikai profilhoz társított [tartalom-definíció](contentdefinitions.md) azonosítója. |
| EnforceEmailVerification | Nem | A regisztrációhoz vagy a profil szerkesztéséhez érvényesíti az e-mailek ellenőrzését. Lehetséges értékek: `true` (alapértelmezett) vagy `false`. |
| Setting. showSignupLink | Nem | Megjeleníti a regisztrációs gombot. Lehetséges értékek: `true` (alapértelmezett) vagy `false` |
| Setting. retryLimit | Nem | Meghatározza, hogy a felhasználók hányszor próbálják meg megadni az érvényesítési technikai profilban ellenőrzött adatmennyiséget. Egy felhasználó például megpróbál egy olyan fiókkal regisztrálni, amely már létezik, és megtartja a próbálkozást, amíg el nem éri a korlátot.
| SignUpTarget | Nem | A regisztrációs cél Exchange-azonosítója. Amikor a felhasználó a feliratkozás gombra kattint, Azure AD B2C végrehajtja a megadott Exchange-azonosítót. |

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A **CryptographicKeys** elem nincs használatban.














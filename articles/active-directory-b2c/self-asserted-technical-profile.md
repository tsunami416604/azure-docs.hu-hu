---
title: Az önellenőrzött technikai profil meghatározása egy egyéni házirendek az Azure Active Directory B2C |} A Microsoft Docs
description: Adja meg az Azure Active Directory B2C-vel egyéni szabályzatok önellenőrzött technikai profil.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e9472f0fb6ca7c9924df57bb61a3f234bc7d4b13
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44381262"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Az önellenőrzött technikai profil meghatározása egy Azure Active Directory B2C egyéni házirendek

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az összes kommunikációt az Azure Active Directory (Azure AD) B2C, ahol a felhasználó várhatóan információk megadása a helyi rendszer magas technikai profilok. Például a regisztrációs oldalon, a bejelentkezési oldal vagy a jelszó alaphelyzetbe állítása az oldalon.

## <a name="protocol"></a>Protokoll

A **neve** attribútuma a **protokoll** elemet hozzá kell beállítani `Proprietary`. A **kezelő** attribútum kell tartalmaznia a teljes nevet, az Azure AD B2C által használt protokoll kezelő sestavení önálló kiszolgáló által megerősített: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Az alábbi példa bemutatja egy e-mailek önellenőrzött technikai profil előfizetési:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```
 
## <a name="input-claims"></a>A bemeneti jogcímek

Az önellenőrzött technikai profilban, használhatja a **InputClaims** és **InputClaimsTransformations** elemeket, hogy feltöltse az önellenőrzött oldalon (a kimeneti jogcímek értékét a jogcímek). Például a Profilszerkesztési szabályzatot, a felhasználói interakciósorozat először beolvassa a felhasználói profilt az Azure AD B2C-vel címtárból, majd az önellenőrzött technikai profilban beállítja a bemeneti jogcímek a felhasználó adatait a felhasználói profilban. Ezeket a jogcímeket a felhasználói profil gyűjtött és majd jelenik meg a felhasználó, aki majd szerkesztheti a meglévő adatokat.

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

A **OutputClaims** elem jogcímek adatokat gyűjteni a felhasználó számára megjelenő listáját tartalmazza. A kimeneti jogcímek bizonyos értékekkel előre, használja a bemeneti jogcímek között, amelyek korábban leírt. Az elem is tartalmazhat alapértelmezett értéket. A jogcímeket sorrendje **OutputClaims** szabályozza, hogy az Azure AD B2C-vel rendereli a jogcímeket a képernyőn sorrendjét. A **DefaultValue** életbe attribútum csak akkor, ha a jogcímek előtt soha nem lett állítva. De ha beállítása előtt az előző vezénylési lépés, még akkor is, ha a felhasználó az értéket üresen hagyja, az alapértelmezett érték nem lépnek érvénybe. Alapértelmezett érték használatának kényszerítéséhez állítsa a **AlwaysUseDefaultValue** attribútumot `true`. A felhasználó számára adjon meg egy értéket egy adott kimeneti jogcím kényszerítéséhez állítsa be a **szükséges** attribútuma a **OutputClaims** elem `true`.

A **takar** eleme a **OutputClaims** gyűjtemény be kell állítania a **UserInputType** bármely felhasználó elem típusa példáulazAzureADB2Cáltaltámogatottbemeneti`TextBox`vagy `DropdownSingleSelect`. Vagy a **kimeneti jogcím** az elemet be kell állítani egy **DefaultValue**.  

A **OutputClaimsTransformations** elemet tartalmazhat egy gyűjteményét **OutputClaimsTransformation** elemek, amelyek segítségével módosíthatja a kimeneti jogcímek, és hozzon létre újakat.

A következő kimeneti jogcím értéke mindig `live.com`:

```XML
<OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" AlwaysUseDefaultValue="true" />
```

### <a name="use-case"></a>Használati eset

Négyféleképpen kimeneti jogcímek:

- **A felhasználói jogcímeket a kimeneti gyűjtése** – Ha a felhasználó-fájlokból gyűjt adatokat kell például születési dátum, hozzá kell adnia a jogcím a **OutputClaims** gyűjtemény. A jogcímeket a felhasználó számára meg kell adnia a **UserInputType**, mint például `TextBox` vagy `DropdownSingleSelect`. Az önellenőrzött technikai profil tartalmaz egy érvényesítési technikai profil, amely ugyanazt a jogcímet, ha az Azure AD B2C-vel nem jelent-e a jogcímet a felhasználó számára. Ha nincs minden olyan kimeneti jogcím a felhasználóra, az Azure AD B2C-vel kihagyja a technikai profil.
- **Alapértelmezett érték a kimeneti jogcím** – adatok gyűjtését a felhasználó vagy az adatok visszaadása az érvényesítési technikai profil nélkül. A `LocalAccountSignUpWithLogonEmail` önálló kiszolgáló által megerősített technikai profil beállítása a **végrehajtott SelfAsserted-bemenet** jogcímet `true`.
- **Egy ellenőrzési technikai profil adja vissza a kimeneti jogcímek** – a technikai profil meghívhatja egy érvényesítési technikai profil, amely visszaadja az egyes jogcímek. Előfordulhat, hogy szeretné felülre kerülnek be a jogcímeket, és vissza kell a felhasználói út következő vezénylési lépéseit. Például egy helyi fiókkal történő bejelentkezés, ha az önellenőrzött technikai profilban nevű `SelfAsserted-LocalAccountSignin-Email` meghívja az érvényesítési nevű technikai profil `login-NonInteractive`. A technikai profil ellenőrzi a hitelesítő adatokat, és emellett adja vissza a felhasználói profil. Például a "userPrincipalName", "displayName", "givenName" és "Vezetéknév".
- **A jogcímeket a kimeneti jogcímek átalakítását keresztül kimeneti**

A következő példában a `LocalAccountSignUpWithLogonEmail` önálló kiszolgáló által megerősített technikai profil bemutatja, hogy a kimeneti jogcímek és a csoportok **végrehajtott SelfAsserted-bemenet** való `true`. A `objectId`, `authenticationSource`, `newUser` jogcímek olyan kimenetét a `AAD-UserWriteUsingLogonEmail` műszaki érvényesítési profilját, és nem jelennek meg a felhasználó számára.

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

## <a name="persist-claims"></a>Jogcímek megőrzése

Ha a **PersistedClaims** elem hiányzik, az önellenőrzött technikai profil nem tárol az Azure AD B2C az adatokat. Ehelyett egy kérés érkezett egy érvényesítési technikai profil felelős létrehozott adatok megőrzését. Például a regisztrálási szabályzat használja a `LocalAccountSignUpWithLogonEmail` önálló kiszolgáló által megerősített technikai profil az új felhasználói profil gyűjtéséhez. A `LocalAccountSignUpWithLogonEmail` technikai profil meghívja az érvényesítési technikai profilban, a fiók létrehozása az Azure AD B2C-t.

## <a name="validation-technical-profiles"></a>Érvényesítési technikai profilok

Egy ellenőrzési technikai profil ellenőrzése vagy azok egy részét a kimeneti jogcímek a hivatkozó technikai profil használható. A bemeneti jogcímek között, az érvényesítési technikai profil szerepelnie kell a kimeneti jogcímek az önellenőrzött technikai profil. Az érvényesítés technikai profilban a felhasználói bevitel érvényesíti, és hibát adhat vissza a felhasználó számára. 

Az érvényesítés technikai profilban lehet a szabályzat minden olyan technikai profil például [Azure Active Directory](active-directory-technical-profile.md) vagy egy [REST API-val](restful-technical-profile.md) technikai profilok. Az előző példában a `LocalAccountSignUpWithLogonEmail` technikai profil ellenőrzi, hogy a signinName nem létezik a címtárban. Ha nem, akkor az érvényesítési technikai profil egy helyi fiókot hoz létre, és az objectId, authenticationSource, Új_felhasználó adja vissza. A `SelfAsserted-LocalAccountSignin-Email` technikai profil hívások a `login-NonInteractive` ellenőrzési technikai profil a felhasználói hitelesítő adatok ellenőrzéséhez.

Technikai profil REST API-t hívja meg az üzleti logikát, felülírja a bemeneti jogcímek között, vagy felhasználói adatokat feldúsítani a vállalati üzleti alkalmazás további integrálásával. További információkért lásd: [ellenőrzési technikai profil](validation-technical-profile.md)

## <a name="metadata"></a>Metaadatok

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| setting.showContinueButton | Nem | Megjeleníti a Folytatás gombra. A lehetséges értékek: `true` (alapértelmezett), vagy `false` |
| setting.showCancelButton | Nem | Megjeleníti a Mégse gombra. A lehetséges értékek: `true` (alapértelmezett), vagy `false` |
| setting.operatingMode | Nem | A bejelentkezési oldal ezt a tulajdonságot, például a bemenet-ellenőrzést és hibaüzenetek a felhasználónév mező viselkedését szabályozza. Várt érték: `Username` vagy `Email`. |
| ContentDefinitionReferenceId | Igen | Azonosítóját a [definíciós tartalom](contentdefinitions.md) a műszaki profilhoz társított. |
| EnforceEmailVerification | Nem | A regisztrációs profil szerkesztése, érvénybe lépteti az e-mailes ellenőrzés vagy. A lehetséges értékek: `true` (alapértelmezett), vagy `false`. | 
| setting.showSignupLink | Nem | Megjeleníti a regisztráció gombra. A lehetséges értékek: `true` (alapértelmezett), vagy `false` |
| SignUpTarget | Nem | A regisztráció cél exchange azonosítója. Amikor a felhasználó a regisztráció gombra kattint, az Azure AD B2C-vel végrehajtja a megadott exchange-azonosító. |

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A **CryptographicKeys** elem nem szolgál.














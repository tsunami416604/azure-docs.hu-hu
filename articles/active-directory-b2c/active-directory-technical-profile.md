---
title: Azure AD technikai profil definiálása egyéni szabályzatban
titleSuffix: Azure AD B2C
description: Definiáljon egy Azure Active Directory technikai profilt egy egyéni szabályzatban az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7db47eda47850c1c080b6a49256c8a0b37bb0d3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330388"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory technikai profiljának definiálása az Azure Active Directory B2C egyéni szabályzatában

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) támogatja az Azure Active Directory felhasználói felügyeletét. Ez a cikk a műszaki profil sajátosságait ismerteti a szabványosprotokollt támogató jogcímszolgáltatóval való interakcióhoz.

## <a name="protocol"></a>Protocol (Protokoll)

A **Protokoll** elem **Name** attribútumát a `Proprietary`parancsra kell állítani. A **kezelő** attribútumnak tartalmaznia kell a protokollkezelő szerelvény `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`teljesen minősített nevét.

[Az egyéni szabályzat kezdőcsomagját](custom-policy-get-started.md#custom-policy-starter-pack) követően az Azure AD technikai profiljai közé tartozik az **AAD-Common** technikai profil. Az Azure AD technikai profilok nem adja meg a protokollt, mert a protokoll az **AAD-common** technikai profilban van konfigurálva:
 
- **AAD-UserReadUsingAlternativeSecurityId** és **AAD-UserReadUsingAlternativeSecurityId-NoError** - Keressen egy közösségi fiókot a címtárban.
- **AAD-UserWriteUsingAlternativeSecurityId** - Hozzon létre egy új közösségi fiókot.
- **AAD-UserReadUsingEmailAddress** - Helyi fiók kinézete a címtárban.
- **AAD-UserWriteUsingLogonEmail** - Hozzon létre egy új helyi fiókot.
- **AAD-UserWritePasswordUsingObjectId** - Helyi fiók jelszavának frissítése.
- **AAD-UserWriteProfileUsingObjectId** - Helyi vagy közösségi fiók felhasználói profiljának frissítése.
- **AAD-UserReadUsingObjectId** – Helyi vagy közösségi fiók felhasználói profiljának olvasása.
- **AAD-UserWritePhoneNumberUsingObjectId** - Írja be egy helyi vagy közösségi fiók MFA telefonszámát

A következő példa az **AAD-Common** technikai profilját mutatja be:

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>

  <!-- We need this here to suppress the SelfAsserted provider from invoking SSO on validation profiles. -->
  <IncludeInSso>false</IncludeInSso>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="inputclaims"></a>InputClaims (InputClaims)

Az InputClaims elem egy jogcímet tartalmaz, amely a címtárban lévő fiók megkeresésére vagy egy új létrehozására szolgál. Az összes Azure AD-technikai profil bemeneti jogcímgyűjteményében pontosan egy InputClaim-elemnek kell lennie. Előfordulhat, hogy le kell képeznie a házirendben definiált jogcím nevét az Azure Active Directoryban definiált névhez.

Egy meglévő felhasználói fiók olvasásához, frissítéséhez vagy törléséhez a bemeneti jogcím egy kulcs, amely egyedileg azonosítja a fiókot az Azure AD könyvtárban. Például **objectId**, **userPrincipalName**, **signInNames.emailAddress**, **signInNames.userName**vagy **alternativeSecurityId**. 

Új felhasználói fiók létrehozásához a bemeneti jogcím egy olyan kulcs, amely egyedileg azonosítja a helyi vagy összevont fiókot. Például helyi fiók: **signInNames.emailAddress**vagy **signInNames.userName**. Összevont fiók esetén: az **alternativeSecurityId**.

Az [InputClaimsTransformations](technicalprofiles.md#inputclaimstransformations) elem tartalmazhat bemeneti jogcímek átalakítási elemeinek gyűjteményét, amelyek a bemeneti jogcím módosítására vagy új létrehozására szolgálnak.

## <a name="outputclaims"></a>Kimeneti jogcímek

A **OutputClaims** elem az Azure AD technikai profil által visszaadott jogcímek listáját tartalmazza. Előfordulhat, hogy le kell képeznie a házirendben definiált jogcím nevét az Azure Active Directoryban definiált névhez. Az Azure Active Directory által vissza nem adott jogcímeket is `DefaultValue` megadhat, feltéve, hogy beállítja az attribútumot.

A [OutputClaimsTransformations](technicalprofiles.md#outputclaimstransformations) elem tartalmazhat **OutputClaimsTransformations** elemek gyűjteményét, amelyek a kimeneti jogcímek módosítására vagy újak létrehozására szolgálnak.

Például az **AAD-UserWriteUsingLogonEmail** technikai profil létrehoz egy helyi fiókot, és a következő jogcímeket adja vissza:

- **objectId**, amely az új fiók azonosítója
- **newUser**, amely azt jelzi, hogy a felhasználó új
- **authenticationSource**, amely a hitelesítést`localAccountAuthentication`
- **userPrincipalName**, amely az új fiók egyszerű felhasználóneve
- **signInNames.emailAddress ,** amely a fiók bejelentkezési neve, hasonlóan az **e-mail** bemeneti jogcíméhez

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>Tartós jogcímek

A **PersistedClaims** elem tartalmazza az összes olyan értéket, amelyet az Azure AD-nek meg kell persistnia a szabályzat [ClaimsSchema](claimsschema.md) szakaszában már definiált jogcímtípus és az Azure AD attribútum neve között.

Az **AAD-UserWriteUsingLogonEmail** technikai profil, amely új helyi fiókot hoz létre, a következő jogcímek szerint marad fenn:

```XML
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
```

A jogcím neve az Azure AD attribútum neve, kivéve, ha a **PartnerClaimType** attribútum meg van adva, amely tartalmazza az Azure AD attribútum nevét.

## <a name="requirements-of-an-operation"></a>A művelet követelményei

- Az összes Azure AD-technikai profilhoz pontosan egy **InputClaim-elemnek** kell lennie a jogcímtáskában.
- A [felhasználói profil attribútumok cikk](user-profile-attributes.md) ismerteti a támogatott Azure AD B2C felhasználói profil attribútumok a bemeneti jogcímek, kimeneti jogcímek és a megőrzött jogcímek. 
- Ha a `Write` művelet `DeleteClaims`vagy , akkor meg kell jelennie a **PersistedClaims** elemben is.
- A **userPrincipalName** jogcím értékének a `user@tenant.onmicrosoft.com`formátumának .
- A **displayName** jogcím kötelező, és nem lehet üres karakterlánc.

## <a name="azure-ad-technical-provider-operations"></a>Az Azure AD technikai szolgáltatói műveletei

### <a name="read"></a>Olvasás

Az **Olvasás** művelet egyetlen felhasználói fiók adatait olvassa be. A következő technikai profil a felhasználó objectId azonosítóját használó felhasználói fiók adatait olvassa be:

```XML
<TechnicalProfile Id="AAD-UserReadUsingObjectId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims>

    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />

    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="write"></a>Írás

Az **Írás** művelet egyetlen felhasználói fiókot hoz létre vagy frissít. A következő technikai profil új közösségi fiókot hoz létre:

```XML
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />

    <!-- Optional claims -->
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="deleteclaims"></a>Jogcímek törlése

A **DeleteClaims** művelet törli az adatokat a megadott jogcímek listájáról. A következő technikai profil törli a jogcímeket:

```XML
<TechnicalProfile Id="AAD-DeleteClaimsUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaims</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims />
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

A **DeleteClaimsPrincipal** művelet egyetlen felhasználói fiókot töröl a címtárból. A következő technikai profil töröl egy felhasználói fiókot a címtárból az egyszerű felhasználónév használatával:

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

A következő technikai profil törli a közösségi felhasználói fiókot **az alternativeSecurityId**használatával:

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```
## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Művelet | Igen | A végrehajtandó művelet. Lehetséges `Read`értékek: `Write` `DeleteClaims`, `DeleteClaimsPrincipal`, , vagy . |
| RaiseerrorifClaimsPrincipalDoesnotexist | Nem | Hiba, ha a felhasználói objektum nem létezik a címtárban. Lehetséges `true` értékek: `false`vagy . |
| RaiseErrorIfclaimsPrincipalmár létezik | Nem | Hiba, ha a felhasználói objektum már létezik. Lehetséges `true` értékek: `false`vagy .|
| ApplicationObjectId alkalmazásobjektum-azonosító | Nem | A bővítményattribútumok alkalmazásobjektum-azonosítója. Érték: Egy alkalmazás ObjectId azonosítója. További információt az [Egyéni attribútumok használata egyéni profilszerkesztési házirendben című témakörben talál.](custom-policy-custom-attributes.md) |
| ClientID | Nem | A bérlő harmadik félként való eléréséhez szolgáló ügyfélazonosító. További információ: [Egyéni attribútumok használata egyéni profilszerkesztési házirendben](custom-policy-custom-attributes.md) |
| IncludeClaimResolvingInClaimsHandling  | Nem | Bemeneti és kimeneti jogcímek esetén megadja, hogy a [jogcímek feloldása](claim-resolver-overview.md) szerepel-e a technikai profilban. Lehetséges értékek: `true` `false`  vagy (alapértelmezett). Ha a technikai profilban jogcímfeloldót szeretne `true`használni, állítsa ezt a beállításra. |

### <a name="ui-elements"></a>Felhasználói felület elemei
 
A következő beállításokkal konfigurálhatja a hiba esetén megjelenő hibaüzenetet. A metaadatokat az önérvényesítő technikai profilban kell [konfigurálni.](self-asserted-technical-profile.md) A hibaüzenetek [honosíthatók](localization.md).

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| UserMessageIfclaimsPrincipalMárexists | Nem | Ha egy hiba van -ból lenni -ból lenni előad ( bemutat RaiseErrorIfClaimsPrincipalMárExists attribútum leírás), adja meg az üzenetet, hogy a felhasználó, ha a felhasználói objektum már létezik. |
| UserMessageifClaimsPrincipalDoesnotExist | Nem | Ha egy hiba van -hoz lenni előad ( lát a RaiseErrorIfClaimsPrincipalDoesNotExist attribútum leírás), adja meg az üzenetet, hogy a felhasználó számára, ha a felhasználói objektum nem létezik. |


## <a name="next-steps"></a>További lépések

Tekintse meg a következő cikket, például az Azure AD technikai profiljának használatával:

- [Jogcímek hozzáadása és felhasználói bevitel testreszabása egyéni szabályzatok használatával az Azure Active Directory B2C-ben](custom-policy-configure-user-input.md)















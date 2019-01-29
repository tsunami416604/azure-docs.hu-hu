---
title: Az Azure Active Directory technikai profil meghatározása egy egyéni házirendek az Azure Active Directory B2C |} A Microsoft Docs
description: Adja meg az Azure Active Directory B2C-vel egyéni szabályzatok az Azure Active Directory technikai profil.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b8dac47d1aa91eb8a8ee1ef9515809607b267437
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190701"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Az Azure Active Directory technikai profil meghatározása az Azure Active Directory B2C egyéni házirendek

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory (Azure AD) B2C támogatást nyújt az Azure Active Directory-felhasználók kezelése. Ez a cikk ismerteti, amely támogatja a szabványos protokoll Jogcímszolgáltatók folytatott interakcióra szolgáló technikai profil adatait.

## <a name="protocol"></a>Protokoll

A **neve** attribútuma a **protokoll** elemet hozzá kell beállítani `Proprietary`. A **kezelő** attribútum kell tartalmaznia a teljes nevet, a protokoll-kezelő szerelvény `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Minden Azure AD technikai profil tartalmazza a **AAD-közös** technikai profil. A következő technikai profilok nem a protokoll megadását, mert a protokoll van beállítva a **AAD-közös** technikai profil:

- **AAD-UserReadUsingAlternativeSecurityId** és **AAD-UserReadUsingAlternativeSecurityId-NoError** – keresse fel egy közösségi hálózati fiókot a címtárban.
- **AAD-UserWriteUsingAlternativeSecurityId** -közösségi fiók létrehozása.
- **AAD-UserReadUsingEmailAddress** – keresse fel egy helyi fiókot a címtárban. 
- **AAD-UserWriteUsingLogonEmail** – hozzon létre egy új helyi fiókot.
- **AAD-UserWritePasswordUsingObjectId** – egy helyi fiók jelszavának módosítása.
- **AAD-UserWriteProfileUsingObjectId** – helyi vagy a közösségi fiók felhasználói profil frissítése.
- **AAD-UserReadUsingObjectId** – egy helyi vagy a közösségi fiók felhasználói profil olvasása.
- **AAD-UserWritePhoneNumberUsingObjectId** – a többtényezős hitelesítés telefonszám, egy helyi vagy a közösségi fiók írása

A következő példa bemutatja a **AAD-közös** technikai profil:

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

## <a name="input-claims"></a>A bemeneti jogcímek

A következő technikai profilok tartalmazzák **InputClaims** közösségi és a helyi fiókok esetében:

- A közösségi fiók technikai profilok **AAD-UserReadUsingAlternativeSecurityId** és **AAD-UserWriteUsingAlternativeSecurityId** magában foglalja a **AlternativeSecurityId** jogcím. Ez a jogcím a közösségi fiók felhasználói azonosítót tartalmaz.
- A helyi fiók technikai profilok **AAD-UserReadUsingEmailAddress** és **AAD-UserWriteUsingLogonEmail** magában foglalja a **e-mail** jogcím. Ez a jogcím a helyi fiók bejelentkezési nevét tartalmazza.
- Az egyesített (helyi és közösségi) technikai profilok **AAD-UserReadUsingObjectId**, **AAD-UserWritePasswordUsingObjectId**, **AAD-UserWriteProfileUsingObjectId**, és **AAD-UserWritePhoneNumberUsingObjectId** magában foglalja a **objectId** jogcím. A fiók egyedi azonosítója.

A **InputClaimsTransformations** elemet tartalmazhat egy gyűjteményét **InputClaimsTransformation** elemek, amelyek segítségével módosítsa a bemeneti jogcímek között, és hozzon létre újakat.

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem tartalmazza az Azure AD technikai profil által visszaküldött jogcímek listája. Szükség lehet a jogcímek, az Azure Active Directoryban megadott nevét a szabályzatban definiált nevének való leképezéséhez. Is használható az Azure Active Directory nem adott vissza jogcímeket, amennyiben beállította a `DefaultValue` attribútum.

A **OutputClaimsTransformations** elemet tartalmazhat egy gyűjteményét **OutputClaimsTransformation** elemek, amelyek segítségével módosíthatja a kimeneti jogcímek, és hozzon létre újakat.

Ha például a **AAD-UserWriteUsingLogonEmail** technikai profil egy helyi fiókot hoz létre, és a következő jogcímeket adja vissza:

- **objectId**, amely az, hogy az új fiók azonosítója
- **Új_felhasználó**, amely azt jelzi, hogy a felhasználó új
- **authenticationSource**, amelyek hitelesítés beállítása `localAccountAuthentication`
- **userPrincipalName**, vagyis az új fiók egyszerű felhasználónevét
- **signInNames.emailAddress**, azaz a fiók bejelentkezési neve, hasonlóan a **e-mail** bemeneti jogcímek

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims

A **PersistedClaims** elem tartalmazza az összes az értékeket, amelyeket érdemes őrizhető meg, az Azure AD között már definiálva a házirend- és az Azure AD-attribútum ClaimsSchema szakaszának jogcím típusa esetleges leképezés adatokkal név. 

A **AAD-UserWriteUsingLogonEmail** technikai profilban, új helyi fiókot hoz létre, amely továbbra is fennáll, jogcímek a következő:

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

A jogcím neve, az Azure AD-attribútum nevét, hacsak nem a **PartnerClaimType** attribútum van megadva, amely tartalmazza az Azure AD-attribútum neve.

## <a name="requirements-of-an-operation"></a>Egy művelet követelményeinek

- Kell pontosan egy **bemeneti jogcím** elem a jogcímek csomagban van az összes Azure AD technikai profil számára. 
- Ha a művelet `Write` vagy `DeleteClaims`, akkor azt is szerepelnie kell egy **PersistedClaims** elemet.
- Értékét a **userPrincipalName** jogcím formátumúnak kell lennie `user@tenant.onmicrosoft.com`.
- A **displayName** jogcím szükség, és nem lehet üres karakterlánc.

## <a name="azure-ad-technical-provider-operations"></a>Az Azure AD technikai szolgáltatói műveletek

### <a name="read"></a>Olvasás

A **olvasási** művelet beolvassa egy felhasználói fiókkal kapcsolatos adatokat. Felhasználói adatokat olvasni, meg kell adnia egy kulcs bemeneti jogcímet, mint például a **objectId**, **userPrincipalName**, **signInNames** (bármely típusú, felhasználói nevét és e-mail fiókkal), vagy **alternativeSecurityId**.  

A következő technikai profil beolvassa az adatokat egy felhasználói fiókot, a felhasználó objectId használatával kapcsolatban:

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

A **írási** létrehozása vagy frissítése egy felhasználói fiókkal egy műveletet. Írhat egy felhasználói fiókot, meg kell adnia egy kulcsot egy bemeneti jogcímet, mint például a **objectId**, **userPrincipalName**, **signInNames.emailAddress**, vagy  **alternativeSecurityId**.  

A következő technikai profil hoz létre új közösségi fiók:

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

### <a name="deleteclaims"></a>DeleteClaims

A **DeleteClaims** a művelet törli a jogcímek információit egy megadott listában. Törli az adatokat a jogcímeket, meg kell adnia egy kulcs bemeneti jogcímet, mint például a **objectId**, **userPrincipalName**, **signInNames.emailAddress** vagy **alternativeSecurityId**.  

A következő technikai profil jogcímek törli:

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

A **DeleteClaimsPrincipal** művelet töröl egy felhasználói fiókkal a címtárból. Egy felhasználói fiók törléséhez meg kell adnia egy kulcs bemeneti jogcímet, mint például a **objectId**, **userPrincipalName**, **signInNames.emailAddress** vagy  **alternativeSecurityId**.  

A következő technikai profil töröl egy felhasználói fiókot a címtárból, az egyszerű felhasználónév használatával:

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

A következő technikai profil töröl egy közösségi felhasználói fiók használatával **alternativeSecurityId**:

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

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Művelet | Igen | A végrehajtandó műveletet. A lehetséges értékek: `Read`, `Write`, `DeleteClaims`, vagy `DeleteClaimsPrincipal`. | 
| RaiseErrorIfClaimsPrincipalDoesNotExist | Nem | Emelje a hibát, ha a felhasználói objektum nem létezik a címtárban. A lehetséges értékek: `true` vagy `false`. | 
| UserMessageIfClaimsPrincipalDoesNotExist | Nem | A kiváltandó hiba esetén (lásd a RaiseErrorIfClaimsPrincipalDoesNotExist attribútum leírása), adja meg a felhasználói objektum nem létezik. Ha a felhasználó számára megjelenítendő üzenetet. Az érték lehet [honosított](localization.md).| 
| RaiseErrorIfClaimsPrincipalAlreadyExists | Nem | Emelje a hiba, ha a felhasználói objektum már létezik. A lehetséges értékek: `true` vagy `false`.| 
| UserMessageIfClaimsPrincipalAlreadyExists | Nem | A kiváltandó hiba esetén (lásd a RaiseErrorIfClaimsPrincipalAlreadyExists attribútum leírása), adja meg a felhasználói objektum már létezik-e a felhasználónak megjelenítendő üzenetet. Az érték lehet [honosított](localization.md).| 
| ApplicationObjectId | Nem | A bővítményattribútumok alkalmazás objektum azonosítója. Érték: Egy alkalmazás ObjectId. További információkért lásd: [egyéni attribútumok használata egyéni profil házirend szerkesztése](active-directory-b2c-create-custom-attributes-profile-edit-custom.md). | 
| ClientID | Nem | Az ügyfél-azonosítója egy harmadik féltől származó, a bérlő eléréséhez. További információkért lásd: [egyéni attribútumok használata egyéni profil szabályzat szerkesztése](active-directory-b2c-create-custom-attributes-profile-edit-custom.md) | 















---
title: Telefontényező-technikai profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: Az Azure Active Directory B2C-ben egy egyéni szabályzatban definiáljon egy telefontényező-technikai profilt.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3a0511a19477f3d76baf9c453316c5348cc31397
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332651"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Telefontényező-technikai profil definiálása az Azure Active Directory B2C egyéni szabályzatában

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) támogatja a telefonszámok regisztrálását és ellenőrzését. Ez a technikai profil:

- Felhasználói felületet biztosít a felhasználóval való együttműködéshez.
- A tartalomdefiníció segítségével szabályozhatja a megjelenést és a megjelenést.
- Támogatja a telefonhívásokat és a szöveges üzeneteket is a telefonszám érvényesítéséhez.
- Több telefonszámot támogat. A felhasználó kiválaszthatja az ellenőrizni kívánt telefonszámok egyikét.  
- Ha meg van adva telefonszám, a telefontényező felhasználói felülete megkéri a felhasználót, hogy ellenőrizze a telefonszámot. Ha nincs megadva, megkéri a felhasználót, hogy új telefonszámot regisztráljon.
- Jogcímet ad vissza, amely jelzi, hogy a felhasználó új telefonszámot adott-e meg. Ezzel a jogcímhasználatával eldöntheti, hogy a telefonszámot meg kell-e tartani az Azure AD felhasználói profil.  

## <a name="protocol"></a>Protocol (Protokoll)

A **Protokoll** elem **Name** attribútumát a `Proprietary`parancsra kell állítani. A **kezelő** attribútumnak tartalmaznia kell az Azure AD B2C által a telefontényezőhöz használt protokollkezelő szerelvény teljesen minősített nevét:`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

A következő példa egy telefonos tényező technikai profilját mutatja be a regisztrációhoz és az érvényesítéshez:

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Bemeneti jogcímek

Az InputClaims elemnek a következő jogcímeket kell tartalmaznia. A jogcím nevét a telefontényező technikai profiljában meghatározott névre is leképezheti. 

```XML
<InputClaims>
  <!--A unique identifier of the user. The partner claim type must be set to `UserId`. -->
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <!--A claim that contains the phone number. If the claim is empty, Azure AD B2C asks the user to enroll a new phone number. Otherwise, it asks the user to verify the phone number. -->
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
</InputClaims>
```

A következő példa több telefonszám használatát mutatja be. További információt a [mintaházirend című témakörben talál.](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa)

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

Az InputClaimsTransformations elem tartalmazhat InputClaimsTransformation elemek gyűjteményét, amelyek a bemeneti jogcímek módosítására vagy újak létrehozására szolgálnak, mielőtt a telefontényező-oldalra mutatnák be őket.

## <a name="output-claims"></a>Kimeneti jogcímek

A OutputClaims elem a telefontényező technikai profilja által visszaadott jogcímek listáját tartalmazza.

```xml
<OutputClaims>
  <!-- The verified phone number. The partner claim type must be set to `Verified.OfficePhone`. -->
  <OutputClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="Verified.OfficePhone" />
  <!-- Indicates whether the new phone number has been entered by the user. The partner claim type must be set to `newPhoneNumberEntered`. -->
  <OutputClaim ClaimTypeReferenceId="newPhoneNumberEntered" PartnerClaimType="newPhoneNumberEntered" />
</OutputClaims>
```

A OutputClaimsTransformations elem tartalmazhat OutputClaimsTransformations elemek gyűjteményét, amelyek a kimeneti jogcímek módosítására vagy újak létrehozására szolgálnak.

## <a name="cryptographic-keys"></a>Kriptográfiai kulcsok

A **Kriptográfiai kulcsok** elem nem használatos.


## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId azonosító | Igen | A technikai profilhoz társított [tartalomdefiníció](contentdefinitions.md) azonosítója. |
| ManualPhoneNumberEntryAllowed| Nem | Adja meg, hogy a felhasználó manuálisan beírhat-e egy telefonszámot. Lehetséges `true` értékek: `false` vagy (alapértelmezett).|

### <a name="ui-elements"></a>Felhasználói felület elemei

A telefontényező-hitelesítési lap felhasználói felületének elemei [honosodhatnak.](localization-string-ids.md#azure-mfa-error-messages)

## <a name="next-steps"></a>További lépések

- Ellenőrizze a [közösségi és helyi fiókokat az MFA kezdőcsomaggal.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa)


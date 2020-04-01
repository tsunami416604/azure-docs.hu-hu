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
ms.date: 03/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e2b30e8f6bcbe7c0e739455f4942712f68ff8404
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437453"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Telefontényező-technikai profil definiálása az Azure Active Directory B2C egyéni szabályzatában

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) támogatja a telefonszámok regisztrálását és ellenőrzését. Ez a technikai profil:

- Felhasználói felületet biztosít a felhasználóval a telefonszám ellenőrzéséhez vagy igényléséhez.
- Támogatja a telefonhívásokat és a szöveges üzeneteket a telefonszám érvényesítéséhez.
- Több telefonszámot támogat. A felhasználó kiválaszthatja az ellenőrizni kívánt telefonszámok egyikét.  
- Jogcímet ad vissza, amely jelzi, hogy a felhasználó új telefonszámot adott-e meg. Ezzel a jogcímhasználatával eldöntheti, hogy a telefonszámot meg kell-e tartani az Azure AD B2C felhasználói profil.  
- A megjelenés és a megjelenés szabályozására [tartalomdefiníciót](contentdefinitions.md) használ.

## <a name="protocol"></a>Protocol (Protokoll)

A **Protokoll** elem **Name** attribútumát a `Proprietary`parancsra kell állítani. A **kezelő** attribútumnak tartalmaznia kell az Azure AD B2C által a telefontényezőhöz használt protokollkezelő szerelvény teljesen minősített nevét:`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

A következő példa egy telefonos tényező technikai profilját mutatja be a regisztrációhoz és az érvényesítéshez:

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>Bemeneti jogcímek átalakítása

Az InputClaimsTransformations elem tartalmazhat bemeneti jogcímátalakítások gyűjteményét, amelyek a bemeneti jogcímek módosítására vagy újak létrehozására szolgálnak. A következő bemeneti jogcímek átalakítása létrehoz egy `UserId` jogcímet, amely később a bemeneti jogcímek gyűjtése.

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>Bemeneti jogcímek

Az InputClaims elemnek a következő jogcímeket kell tartalmaznia. A jogcím nevét a telefontényező technikai profiljában meghatározott névre is leképezheti. 

|  Adattípus| Kötelező | Leírás |
| --------- | -------- | ----------- | 
| sztring| Igen | A felhasználó egyedi azonosítója. A jogcím nevét vagy a PartnerClaimType címet a következőre `UserId`kell állítani: . Ez az állítás nem tartalmazhat személyazonosításra alkalmas adatokat.|
| sztring| Igen | Jogcímtípusok listája. Minden jogcím egy telefonszámot tartalmaz. Ha a bemeneti jogcímek bármelyike nem tartalmaz telefonszámot, a rendszer felkéri a felhasználót, hogy regisztráljon és ellenőrizzen egy új telefonszámot. Az érvényesített telefonszámot a kimeneti jogcím adja vissza kimeneti jogcímként. Ha a bemeneti jogcímek egyike telefonszámot tartalmaz, a rendszer felkéri a felhasználót, hogy ellenőrizze azt. Ha több bemeneti jogcím is tartalmaz egy telefonszámot, a rendszer felkéri a felhasználót, hogy válassza ki és ellenőrizze az egyik telefonszámot. |

A következő példa több telefonszám használatát mutatja be. További információt a [mintaházirend című témakörben talál.](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa)

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>Kimeneti jogcímek

A OutputClaims elem a telefontényező technikai profilja által visszaadott jogcímek listáját tartalmazza.

|  Adattípus| Kötelező | Leírás |
|  -------- | ----------- |----------- |
| logikai | Igen | Azt jelzi, hogy a felhasználó megadta-e az új telefonszámot. A jogcím nevét vagy a PartnerClaimType típust`newPhoneNumberEntered`|
| sztring| Igen | Az ellenőrzött telefonszám. A jogcím nevét vagy a PartnerClaimType címet a következőre `Verified.OfficePhone`kell állítani: .|

A OutputClaimsTransformations elem tartalmazhat OutputClaimsTransformations elemek gyűjteményét, amelyek a kimeneti jogcímek módosítására vagy újak létrehozására szolgálnak.

## <a name="cryptographic-keys"></a>Kriptográfiai kulcsok

A **Kriptográfiai kulcsok** elem nem használatos.


## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId azonosító | Igen | A technikai profilhoz társított [tartalomdefiníció](contentdefinitions.md) azonosítója. |
| ManualPhoneNumberEntryAllowed| Nem | Adja meg, hogy a felhasználó manuálisan beírhat-e egy telefonszámot. Lehetséges értékek: `true` `false` vagy (alapértelmezett).|
| setting.authenticationMode | Nem | A telefonszám érvényesítésének módja. Lehetséges `sms`értékek: `phone`, `mixed` vagy (alapértelmezett).|
| setting.autodial| Nem| Adja meg, hogy a műszaki profil automatikusan tárcsázzon-e vagy automatikusan küldjön SMS-t. Lehetséges értékek: `true` `false` vagy (alapértelmezett). Az automatikus `setting.authenticationMode` tárcsázáshoz a `sms`metaadatokat a vagy `phone`a parancsra kell állítani. A bemeneti jogcímek gyűjteményének egyetlen telefonszámmal kell rendelkeznie. |

### <a name="ui-elements"></a>Felhasználói felület elemei

A telefontényező-hitelesítési lap felhasználói felületének elemei [honosodhatnak.](localization-string-ids.md#azure-mfa-error-messages)

## <a name="next-steps"></a>További lépések

- Ellenőrizze a [közösségi és helyi fiókokat az MFA kezdőcsomaggal.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa)

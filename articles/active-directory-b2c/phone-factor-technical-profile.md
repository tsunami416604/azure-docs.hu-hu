---
title: A telefonos faktor technikai profiljának definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: Adjon meg egy telefonos faktor műszaki profilt egy egyéni szabályzatban Azure Active Directory B2Cban.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80437453"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>A telefonos faktor technikai profiljának definiálása egy Azure Active Directory B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A Azure Active Directory B2C (Azure AD B2C) támogatást nyújt a telefonszámok regisztrálásához és ellenőrzéséhez. Ez a technikai profil:

- Felhasználói felületet biztosít a felhasználóval, hogy ellenőrizze vagy regisztrálja a telefonszámot.
- A telefonos hívásokat és szöveges üzeneteket támogat a telefonszám ellenőrzéséhez.
- Több telefonszámot is támogat. A felhasználó választhatja ki az egyik telefonszámot az ellenőrzéshez.  
- Egy olyan jogcímet ad vissza, amely azt jelzi, hogy a felhasználó új telefonszámot adott-e meg. Ennek a jogcímnek a segítségével eldöntheti, hogy a telefonszámot meg kell-e őrizni a Azure AD B2C felhasználói profilban.  
- A [tartalom definícióját](contentdefinitions.md) használja a megjelenés és a működés szabályozására.

## <a name="protocol"></a>Protocol (Protokoll)

A **protokoll** elem `Proprietary` **Name** attribútumát be kell állítani. A **kezelő** attribútumnak tartalmaznia kell a Azure ad B2C által a telefonos faktorhoz használt protokollkezelő-szerelvény teljes nevét:`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

A következő példa egy telefonos faktor technikai profilt mutat be a regisztráláshoz és az ellenőrzéshez:

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>Bemeneti jogcím-átalakítások

A InputClaimsTransformations elem tartalmazhat olyan bemeneti jogcím-átalakítások gyűjteményét, amelyek a bemeneti jogcímek módosításához vagy újak létrehozásához használatosak. A következő bemeneti jogcímek átalakítása `UserId` olyan jogcímet hoz létre, amelyet később a bemeneti jogcímek gyűjteménye használ.

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>Bemeneti jogcímek

A Szabályzattípushoz elemnek tartalmaznia kell a következő jogcímeket. A jogcím nevét a telefonos faktor technikai profilban definiált névre is leképezheti. 

|  Adattípus| Kötelező | Leírás |
| --------- | -------- | ----------- | 
| sztring| Igen | A felhasználó egyedi azonosítója. A jogcím nevét vagy PartnerClaimType kell beállítani `UserId`. Ez a jogcím nem tartalmazhat személyes azonosításra alkalmas adatokat.|
| sztring| Igen | A jogcím-típusok listája. Minden jogcím egy telefonszámot tartalmaz. Ha a bemeneti jogcímek bármelyike nem tartalmaz telefonszámot, a rendszer megkéri a felhasználót, hogy regisztrálja és ellenőrizze az új telefonszámot. Az érvényesített telefonszámot kimeneti jogcímként adja vissza a rendszer. Ha a bemeneti jogcímek egyike telefonszámot tartalmaz, a rendszer megkéri a felhasználót, hogy ellenőrizze. Ha több bemeneti jogcím is tartalmaz telefonszámot, a rendszer megkéri a felhasználót, hogy válasszon ki és ellenőrizze a telefonszámok egyikét. |

Az alábbi példa több telefonszám használatát mutatja be. További információ: [Sample Policy](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa).

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>Kimeneti jogcímek

A OutputClaims elem a telefonos faktor technikai profil által visszaadott jogcímek listáját tartalmazza.

|  Adattípus| Kötelező | Leírás |
|  -------- | ----------- |----------- |
| logikai | Igen | Azt jelzi, hogy a felhasználó megadta-e az új telefonszámot. A jogcím nevét vagy PartnerClaimType úgy kell beállítani, hogy`newPhoneNumberEntered`|
| sztring| Igen | Az ellenőrzött telefonszám. A jogcím nevét vagy PartnerClaimType kell beállítani `Verified.OfficePhone`.|

A OutputClaimsTransformations elem olyan OutputClaimsTransformation-elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosításához vagy újak létrehozásához használatosak.

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A **CryptographicKeys** elem nincs használatban.


## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | Igen | Az ehhez a technikai profilhoz társított [tartalom-definíció](contentdefinitions.md) azonosítója. |
| ManualPhoneNumberEntryAllowed| Nem | Adja meg, hogy a felhasználó számára engedélyezett-e a telefonszám manuális megadása. Lehetséges értékek: `true`, vagy `false` (alapértelmezett).|
| Setting. Authenticationmode tulajdonsághoz | Nem | A telefonszám érvényesítésének módszere. Lehetséges értékek: `sms`, `phone`, vagy `mixed` (alapértelmezett).|
| az automatikus tárcsázás beállítása| Nem| Annak megadása, hogy a technikai profil automatikusan tárcsázjon-e, vagy automatikusan küldjön-e SMS-t. Lehetséges értékek: `true`, vagy `false` (alapértelmezett). Az automatikus tárcsázáshoz `setting.authenticationMode` a metaadatokat a `sms`vagy `phone`a értékre kell beállítani. A bemeneti jogcímek gyűjteményének egyetlen telefonszámmal kell rendelkeznie. |

### <a name="ui-elements"></a>Felhasználói felület elemei

A telefonos tényező hitelesítési lapja felhasználói felületi elemei [honosítható](localization-string-ids.md#azure-mfa-error-messages).

## <a name="next-steps"></a>További lépések

- A [közösségi és helyi fiókokat a MFA](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) Starter Pack csomaggal vizsgálhatja meg.

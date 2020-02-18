---
title: Alkalmazások és API-k migrálása a b2clogin.com-be
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan használhatja a b2clogin.com-t a Azure Active Directory B2C átirányítási URL-címeiben.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: aa1e6d8705cf4aed975ed0940087f243a06a9019
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2020
ms.locfileid: "77372698"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Átirányítási URL-címek beállítása b2clogin.com Azure Active Directory B2C

Ha a Azure Active Directory B2C (Azure AD B2C) alkalmazásban állít be egy identitás-szolgáltatót a regisztráláshoz és a bejelentkezéshez, meg kell adnia egy átirányítási URL-címet. Az alkalmazások és API-k már nem hivatkozhatnak *login.microsoftonline.com* . Ehelyett használja a *b2clogin.com* az összes új alkalmazáshoz, és telepítse át a meglévő alkalmazásokat a *login.microsoftonline.com* -ről a *b2clogin.com*-re.

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>Login.microsoftonline.com elavulása

December 2019-án bejelentettük a login.microsoftonline.com-támogatás ütemezett kivezetését Azure AD B2C, **2020. december 04**-én:

[A Azure Active Directory B2C elavult login.microsoftonline.com](https://azure.microsoft.com/updates/b2c-deprecate-msol/)

A login.microsoftonline.com elavulása az összes Azure AD B2C bérlőre érvényes, 2020. december 1-jén, és a meglévő bérlők számára egy (1) évet biztosít a b2clogin.com-re való Migrálás során. A 2019. december 04. után létrehozott új bérlők nem fogadják el a login.microsoftonline.com érkező kéréseket. Az összes funkció változatlan marad a b2clogin.com-végponton.

A login.microsoftonline.com elavulása nem érinti Azure Active Directory bérlőket. Ez a módosítás csak Azure Active Directory B2C bérlőket érint.

## <a name="benefits-of-b2clogincom"></a>A b2clogin.com előnyei

Ha a *b2clogin.com* -t használja átirányítási URL-címként:

* A Microsoft-szolgáltatások cookie-fejlécében felhasznált lemezterület csökken.
* Az átirányítási URL-címeknek már nem kell tartalmazniuk a Microsoftnak való hivatkozást.
* A JavaScript ügyféloldali kódja támogatott (jelenleg [előzetes](user-flow-javascript-overview.md)verzióban) a testreszabott lapokon. Biztonsági korlátozások miatt a rendszer eltávolítja a JavaScript-kódot és a HTML-űrlapok elemeit az egyéni lapokról, ha *login.microsoftonline.com*használ.

## <a name="overview-of-required-changes"></a>A szükséges módosítások áttekintése

Az alkalmazások *b2clogin.com*való áttelepíteni több módosítást is szükségessé tehet:

* Módosítsa az átirányítási URL-címet az identitás-szolgáltató alkalmazásaiban a *b2clogin.com*hivatkozására.
* Frissítse Azure AD B2C alkalmazásait a *b2clogin.com* használatára a felhasználói folyamat és a jogkivonat-végpont hivatkozásaiban.
* Frissítse a [felhasználói felület testreszabására](custom-policy-ui-customization.md)vonatkozó CORS-beállításokban definiált összes **engedélyezett eredetet** .

## <a name="change-identity-provider-redirect-urls"></a>Identitás-szolgáltató átirányítási URL-címeinek módosítása

Minden olyan identitás-szolgáltató webhelyén, amelyben létrehozott egy alkalmazást, módosítsa az összes megbízható URL-címet úgy, hogy a *login.microsoftonline.com*helyett átirányítsa a `your-tenant-name.b2clogin.com`.

A b2clogin.com-átirányítási URL-címekhez két formátumot használhat. Az első lehetőséget biztosít arra, hogy a bérlői tartománynév helyett a bérlői azonosító (GUID) használatával a "Microsoft" ne jelenjen meg az URL-ben.

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

A második lehetőség `your-tenant-name.onmicrosoft.com`formájában használja a bérlői tartománynevet. Például:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Mindkét formátum esetében:

* Cserélje le a `{your-tenant-name}`t a Azure AD B2C bérlő nevére.
* Távolítsa el `/te`, ha az szerepel az URL-címben.

## <a name="update-your-applications-and-apis"></a>Alkalmazások és API-k frissítése

A Azure AD B2C-kompatibilis alkalmazások és API-k kódja több helyen is hivatkozhat `login.microsoftonline.com`re. Előfordulhat például, hogy a kód a felhasználói folyamatokra és a jogkivonat-végpontokra mutató hivatkozásokat tartalmaz. Frissítse a következőt a hivatkozás helyett `your-tenant-name.b2clogin.com`:

* Engedélyezési végpont
* Jogkivonat-végpont
* Jogkivonat kiállítója

Például a contoso regisztrációs/bejelentkezési szabályzatának Authority végpontja a következő lesz:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

További információ a OWIN-alapú webalkalmazások b2clogin.com való áttelepítéséről: [OWIN-alapú webes API áttelepítése b2clogin.com](multiple-token-endpoints.md).

A Azure AD B2C által védett Azure API Management API-k áttelepítéséhez tekintse meg az [azure API Management API biztonságossá tétele a Azure ad B2C](secure-api-management.md)használatával című rész az [áttelepítés b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) című szakaszát.

## <a name="microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL)

### <a name="validateauthority-property"></a>ValidateAuthority tulajdonság

Ha a [MSAL.net][msal-dotnet] v2 vagy a korábbi verzióját használja, állítsa a **ValidateAuthority** tulajdonságot `false`re az ügyfél-példányon, hogy az átirányítást engedélyezze a *b2clogin.com*. Erre a beállításra nincs szükség a MSAL.NET v3 és újabb verziókhoz.

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

Ha [JavaScript-MSAL][msal-js]használ:

```JavaScript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```

## <a name="next-steps"></a>Következő lépések

További információ a OWIN-alapú webalkalmazások b2clogin.com való áttelepítéséről: [OWIN-alapú webes API áttelepítése b2clogin.com](multiple-token-endpoints.md).

A Azure AD B2C által védett Azure API Management API-k áttelepítéséhez tekintse meg az [azure API Management API biztonságossá tétele a Azure ad B2C](secure-api-management.md)használatával című rész az [áttelepítés b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) című szakaszát.

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md

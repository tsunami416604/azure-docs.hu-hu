---
title: Átirányítási URL-címek beállítása b2clogin.com – Azure Active Directory B2C
description: Ismerje meg, hogyan használhatja a b2clogin.com-t a Azure Active Directory B2C átirányítási URL-címeiben.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: dbc366daac89f44d4b084081590124f81ff9cc9c
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533739"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Átirányítási URL-címek beállítása b2clogin.com Azure Active Directory B2C

Ha a Azure Active Directory B2C (Azure AD B2C) alkalmazásban állít be egy identitás-szolgáltatót a regisztráláshoz és a bejelentkezéshez, meg kell adnia egy átirányítási URL-címet. Az alkalmazások és API-k már nem hivatkozhatnak *login.microsoftonline.com* . Ehelyett használja a *b2clogin.com* az összes új alkalmazáshoz, és telepítse át a meglévő alkalmazásokat a *login.microsoftonline.com* -ről a *b2clogin.com*-re.

## <a name="benefits-of-b2clogincom"></a>A b2clogin.com előnyei

Ha a *b2clogin.com* -t használja átirányítási URL-címként:

* A Microsoft-szolgáltatások cookie-fejlécében felhasznált lemezterület csökken.
* Az átirányítási URL-címeknek már nem kell tartalmazniuk a Microsoftnak való hivatkozást.
* A JavaScript ügyféloldali kódja támogatott (jelenleg [előzetes](user-flow-javascript-overview.md)verzióban) a testreszabott lapokon. Biztonsági korlátozások miatt a rendszer eltávolítja a JavaScript-kódot és a HTML-űrlapok elemeit az egyéni lapokról, ha *login.microsoftonline.com*használ.

## <a name="overview-of-required-changes"></a>A szükséges módosítások áttekintése

Az alkalmazások *b2clogin.com*való áttelepíteni több módosítást is szükségessé tehet:

* Módosítsa az átirányítási URL-címet az identitás-szolgáltató alkalmazásaiban a *b2clogin.com*hivatkozására.
* Frissítse Azure AD B2C alkalmazásait a *b2clogin.com* használatára a felhasználói folyamat és a jogkivonat-végpont hivatkozásaiban.
* Frissítse a [felhasználói felület testreszabására](active-directory-b2c-ui-customization-custom-dynamic.md)vonatkozó CORS-beállításokban definiált összes **engedélyezett eredetet** .

## <a name="change-identity-provider-redirect-urls"></a>Identitás-szolgáltató átirányítási URL-címeinek módosítása

Minden identitás-szolgáltató webhelyén, amelyben létrehozott egy alkalmazást, módosítsa az összes megbízható URL-címet úgy, hogy `your-tenant-name.b2clogin.com` átirányítsa a *login.microsoftonline.com*helyett.

A b2clogin.com-átirányítási URL-címekhez két formátumot használhat. Az első lehetőséget biztosít arra, hogy a bérlői tartománynév helyett a bérlői azonosító (GUID) használatával a "Microsoft" ne jelenjen meg az URL-ben.

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

A második lehetőség a bérlői tartománynevet használja a (z `your-tenant-name.onmicrosoft.com`) formában. Példa:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Mindkét formátum esetében:

* Cserélje le `{your-tenant-name}` az Azure AD B2C-bérlő nevével.
* Távolítsa el `/te` , ha az szerepel az URL-címben.

## <a name="update-your-applications-and-apis"></a>Alkalmazások és API-k frissítése

A Azure ad B2C-kompatibilis alkalmazások és API-k kódja több helyen is `login.microsoftonline.com` hivatkozhat. Előfordulhat például, hogy a kód a felhasználói folyamatokra és a jogkivonat-végpontokra mutató hivatkozásokat tartalmaz. Frissítse a következőt a hivatkozás `your-tenant-name.b2clogin.com`helyett:

* Engedélyezési végpont
* Jogkivonat-végpont
* Jogkivonat kibocsátója

Például a contoso regisztrációs/bejelentkezési szabályzatának Authority végpontja a következő lesz:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

## <a name="microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL)

### <a name="validateauthority-property"></a>ValidateAuthority tulajdonság

Ha a [MSAL.net][msal-dotnet] v2 vagy a korábbi verzióját használja, állítsa az **ValidateAuthority** tulajdonságot `false` az ügyfél-példányra, hogy engedélyezze az átirányítást a *b2clogin.com*. Erre a beállításra nincs szükség a MSAL.NET v3 és újabb verziókhoz.

```CSharp
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

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
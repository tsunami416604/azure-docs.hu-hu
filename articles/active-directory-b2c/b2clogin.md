---
title: Alkalmazások és API-k áttelepítése b2clogin.com
titleSuffix: Azure AD B2C
description: Ismerje meg b2clogin.com az Azure Active Directory B2C átirányítási URL-címeiben való használatát.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 64b440054795670b99a22e37dec7188f3e1cd74c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189990"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Url-ek átirányítása b2clogin.com az Azure Active Directory B2C-hez

Amikor beállít egy identitásszolgáltatót az Azure Active Directory B2C (Azure AD B2C) alkalmazásában, meg kell adnia egy átirányítási URL-címet. Az alkalmazásokban és API-kban a továbbiakban nem kell *login.microsoftonline.com* hivatkoznia. Ehelyett használja *a b2clogin.com* az összes új alkalmazáshoz, és telepítse át a meglévő alkalmazásokat *login.microsoftonline.com* *b2clogin.com.*

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>A login.microsoftonline.com eprektálása

2019. december 4-én bejelentettük az Azure AD B2C login.microsoftonline.com támogatásának tervezett nyugdíjazását **2020.**

[Az Azure Active Directory B2C elavulttá login.microsoftonline.com](https://azure.microsoft.com/updates/b2c-deprecate-msol/)

2020. december 4-én lép életbe az összes Azure AD B2C-bérlő login.microsoftonline.com e-eedesítése, így a meglévő bérlők egy (1) évben b2clogin.com. A 2019. december 4-e után létrehozott új bérlők nem fogadják el a login.microsoftonline.com. Minden funkció ugyanaz marad a b2clogin.com végponton.

A login.microsoftonline.com eprekation nem befolyásolja az Azure Active Directory-bérlők. Ez a módosítás csak az Azure Active Directory B2C-bérlőit érinti.

## <a name="benefits-of-b2clogincom"></a>A b2clogin.com előnyei

Ha *b2clogin.com* használ átirányítási URL-címként:

* A Microsoft-szolgáltatások cookie-fejlécében felhasznált terület csökken.
* Az átirányítási URL-címeknek már nem kell hivatkozást tartalmazniuk a Microsoftra.
* A JavaScript ügyféloldali kód jattot tartalmaz (jelenleg [előzetes verzióban)](user-flow-javascript-overview.md)a testreszabott oldalakon. Biztonsági korlátozások miatt a JavaScript-kód és a HTML-űrlapelemek törlődnek az egyéni lapokról, ha *login.microsoftonline.com*használ.

## <a name="overview-of-required-changes"></a>A szükséges módosítások áttekintése

Az alkalmazások *b2clogin.com*áttelepítéséhez több módosítást is végre kell végrehajtania:

* Módosítsa az átirányítási URL-címet az identitásszolgáltató alkalmazásaiban, hogy *hivatkozzon b2clogin.com.*
* Frissítse az Azure AD B2C-alkalmazásokat, hogy *b2clogin.com* használhassa a felhasználói folyamatés a tokenvégpont-hivatkozásokban.
* Frissítse a cors-beállításokban a [felhasználói felület testreszabásához](custom-policy-ui-customization.md)megadott **engedélyezett eredeteket.**

## <a name="change-identity-provider-redirect-urls"></a>Identitásszolgáltató átirányítási URL-címének módosítása

Minden identitásszolgáltató webhelyén, ahol létrehozott egy alkalmazást, módosítsa az összes megbízható URL-t úgy, hogy `your-tenant-name.b2clogin.com` *login.microsoftonline.com*helyett átirányítsa.

Két formátumot használhat a b2clogin.com átirányítási URL-ekhez. Az első azt az előnyt nyújtja, hogy a "Microsoft" nem jelenik meg az URL-cím ben a bérlői azonosító (GUID) használatával a bérlői tartománynév helyett:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

A második lehetőség a bérlői tartománynevet használja a formájában. `your-tenant-name.onmicrosoft.com` Példa:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Mindkét formátumesetében:

* Cserélje `{your-tenant-name}` le az Azure AD B2C-bérlő nevét.
* Távolítsa `/te` el, ha létezik az URL-ben.

## <a name="update-your-applications-and-apis"></a>Az alkalmazások és API-k frissítése

Az Azure AD B2C-kompatibilis alkalmazások és API-k kódja több `login.microsoftonline.com` helyen is hivatkozhat. Például a kód előfordulhat, hogy a felhasználói folyamatok és a token végpontok hivatkozásokkal rendelkezhet. Frissítse a következőket `your-tenant-name.b2clogin.com`inkább hivatkozásként:

* Engedélyezési végpont
* Token végpontja
* Token kibocsátója

Például a contoso-i regisztrációs/bejelentkezési szabályzat hatósági végpontja most a következő:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

Az OWIN-alapú webalkalmazások b2clogin.com való áttelepítéséről az [OWIN-alapú webes API áttelepítése b2clogin.com](multiple-token-endpoints.md)című témakörben talál.

Az Azure AD B2C által védett Azure API-k áttelepítéséről az [Azure AD B2C-vel rendelkező Azure API-k](secure-api-management.md) [áttelepítése b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) szakaszban található.

## <a name="microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL)

### <a name="validateauthority-property"></a>ValidateAuthority tulajdonság

Ha [MSAL.NET][msal-dotnet] v2-es vagy korábbi használatával használja, `false` állítsa a **ValidateAuthority** tulajdonságot az ügyfélpéldány-összeállításra, hogy az átirányítások *b2clogin.com.* Ez a beállítás nem szükséges MSAL.NET v3-as és újabb.

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

Ha [MSAL for JavaScript-et][msal-js]használ:

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

## <a name="next-steps"></a>További lépések

Az OWIN-alapú webalkalmazások b2clogin.com való áttelepítéséről az [OWIN-alapú webes API áttelepítése b2clogin.com](multiple-token-endpoints.md)című témakörben talál.

Az Azure AD B2C által védett Azure API-k áttelepítéséről az [Azure AD B2C-vel rendelkező Azure API-k](secure-api-management.md) [áttelepítése b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) szakaszban található.

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md

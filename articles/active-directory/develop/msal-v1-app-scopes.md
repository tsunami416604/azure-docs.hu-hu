---
title: Hatókörök a 1.0-s verziójú alkalmazásokhoz (MSAL) | Azure
description: Ismerje meg a Microsoft Authentication Library (MSAL) használatával egy v 1.0-alkalmazás hatóköreit.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 61d07c1ba912a0e24b2f4e5fa67243b4525db367
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "81536182"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>A webes API-k 1.0-s verziójának elfogadására szolgáló hatókörök

A OAuth2 engedélyek olyan jogosultsági hatókörök, amelyek egy Azure Active Directory (Azure AD) fejlesztői (v 1.0) web API (Resource) alkalmazás számára elérhetővé teszik az ügyfélalkalmazások számára. A jogosultsági hatókörök az ügyfélalkalmazások számára is megadhatók a hozzájárulás során. Tekintse meg az `oauth2Permissions` [Azure Active Directory alkalmazás jegyzékfájljának](reference-app-manifest.md#manifest-reference)ismertetése című szakaszt.

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>A v 1.0 alkalmazás adott OAuth2 engedélyeihez való hozzáférést kérő hatókörök

Ha jogkivonatokat szeretne beszerezni egy v 1.0 alkalmazás adott hatóköréhez (például a Microsoft Graph API-t, azaz a https://graph.microsoft.com) hatóköröket úgy kell létrehozni, hogy a kívánt erőforrás-azonosítót egy megfelelő OAuth2 engedéllyel társítsa az adott erőforráshoz.

Ha például a felhasználó nevében egy v 1.0 webes API-t szeretne elérni, amelyben az alkalmazás azonosítója URI `ResourceId` :

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Ha a MSAL.NET Azure AD-t a Microsoft Graph API-val (https: \/ /Graph.microsoft.com/) szeretné olvasni és írni, létre kell hoznia a hatókörök listáját az alábbi példákban látható módon:

```csharp
string ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.microsoft.com/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

A Azure Resource Manager API-nak (https: \/ /Management.Core.Windows.net/) megfelelő hatókör írásához a következő hatókört kell kérnie (jegyezze fel a két perjelet):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Két perjelet kell használnia, mivel a Azure Resource Manager API egy perjelet vár a célközönségi jogcímben (AUD), majd egy perjelet választ az API nevének a hatókörből való elkülönítésére.

Az Azure AD által használt logika a következő:

- A ADAL (Azure AD v 1.0) végpontja v 1.0 hozzáférési jogkivonattal (az egyetlen lehetséges), az AUD = erőforrás
- A MSAL (Microsoft Identity platform (v 2.0)) végpontja a v 2.0-tokeneket elfogadó erőforráshoz kér hozzáférési jogkivonatot. `aud=resource.AppId`
- A MSAL (v 2.0-végpont) esetében egy olyan erőforrás hozzáférési jogkivonatának megkérdezése, amely egy v 1.0 hozzáférési jogkivonatot fogad el (ez a fenti eset), az Azure AD a kért hatókörből elemezi a kívánt célközönséget, és az utolsó perjel előtt mindent megtesz az erőforrás-azonosítóként. Ezért ha a https: \/ /Database.Windows.net "https: \/ /Database.Windows.net/" célközönséget vár, a "https:/Database.Windows.net//.default" hatókört kell kérnie \/ . Lásd még: GitHub [-probléma #747: az erőforrás URL-címének záró perjele ki van hagyva, ami SQL-hitelesítési hibát okozott](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Hatókörök egy v 1.0 alkalmazás összes engedélyéhez való hozzáférés kérelmezéséhez

Ha egy v 1.0 alkalmazás összes statikus hatóköréhez tokent szeretne beszerezni, fűzze hozzá az ". default" elemet az API app ID URI-hoz:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>Ügyfél-hitelesítési folyamat/démon-alkalmazásra vonatkozó kérelmekre vonatkozó hatókörök

Az ügyfél-hitelesítési folyamat esetében az átadandó hatókör is a következő lesz: `/.default` . Ez azt jelzi, hogy az Azure AD: "minden olyan alkalmazás-szintű engedély, amelyet a rendszergazda beleegyezett az alkalmazás regisztrálására.

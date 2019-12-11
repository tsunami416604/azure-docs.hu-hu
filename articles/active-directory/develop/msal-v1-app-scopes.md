---
title: MSAL-hatókörök egy v 1.0-alkalmazáshoz | Azure
description: Ismerje meg a Microsoft Authentication Library (MSAL) használatával egy v 1.0-alkalmazás hatóköreit.
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0606376c455733ca90f1bff3d0295cbd1a075637
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963256"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>A webes API-k 1.0-s verziójának elfogadására szolgáló hatókörök

A OAuth2 engedélyek olyan jogosultsági hatókörök, amelyek egy Azure Active Directory (Azure AD) fejlesztői (v 1.0) web API (Resource) alkalmazás számára elérhetővé teszik az ügyfélalkalmazások számára. A jogosultsági hatókörök az ügyfélalkalmazások számára is megadhatók a hozzájárulás során. Tekintse meg a `oauth2Permissions` című szakaszt a [Azure Active Directory Application manifest-referenciában](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>A v 1.0 alkalmazás adott OAuth2 engedélyeihez való hozzáférést kérő hatókörök

Ha jogkivonatokat szeretne beszerezni egy v 1.0 alkalmazás adott hatóköréhez (például az Azure AD Graph, amely https://graph.windows.net), hozzon létre hatóköröket egy kívánt erőforrás-azonosítónak a kívánt OAuth2 engedéllyel való összefűzésével az adott erőforráshoz.

Ha például a felhasználó nevében egy v 1.0 webes API-t szeretne elérni, ahol az alkalmazás-azonosító URI-ja `ResourceId`:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Ha az Azure ad Graph API (https:\//graph.windows.net/) használatával szeretne beolvasni és írni az MSAL.NET Azure AD-t, létre kell hoznia a hatókörök listáját az alábbi példákban látható módon:

```csharp
string ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.windows.net/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

A Azure Resource Manager API-nak (https:\//management.core.windows.net/) megfelelő hatókör írásához a következő hatókört kell kérnie (jegyezze fel a két perjelet):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Két perjelet kell használnia, mivel a Azure Resource Manager API egy perjelet vár a célközönségi jogcímben (AUD), majd egy perjelet választ az API nevének a hatókörből való elkülönítésére.

Az Azure AD által használt logika a következő:

- A ADAL (Azure AD v 1.0) végpontja v 1.0 hozzáférési jogkivonattal (az egyetlen lehetséges), az AUD = erőforrás
- A MSAL (Microsoft Identity platform (v 2.0)) végpontja a v 2.0 jogkivonatokat elfogadó erőforráshoz kér hozzáférési jogkivonatot, `aud=resource.AppId`
- A MSAL (v 2.0-végpont) esetében egy olyan erőforrás hozzáférési jogkivonatának megkérdezése, amely egy v 1.0 hozzáférési jogkivonatot fogad el (ez a fenti eset), az Azure AD a kért hatókörből elemezi a kívánt célközönséget, és az utolsó perjel előtt mindent megtesz az erőforrás-azonosítóként. Ezért ha a https:\//database.windows.net "https:\//database.windows.net/" célközönséget vár, akkor a "https:\//database.windows.net//.default" hatókörét kell kérnie. Lásd még: GitHub [-probléma #747: az erőforrás URL-címének záró perjele ki van hagyva, ami SQL-hitelesítési hibát okozott](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

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

Az ügyfél-hitelesítési folyamat esetében az átadandó hatókör `/.default`is lesz. Ez azt jelzi, hogy az Azure AD: "minden olyan alkalmazás-szintű engedély, amelyet a rendszergazda beleegyezett az alkalmazás regisztrálására.

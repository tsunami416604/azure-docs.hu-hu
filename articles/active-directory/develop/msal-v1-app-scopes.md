---
title: V 1.0-alkalmazás hatókörök (Microsoft Authentication Library) | Azure
description: Ismerje meg a Microsoft Authentication Library (MSAL) használatával egy v 1.0-alkalmazás hatóköreit.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0590614e1c1bc7331246e76fa26a6567a05324e6
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532343"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>A webes API-k 1.0-s verziójának elfogadására szolgáló hatókörök

A OAuth2 engedélyek olyan engedélyezési hatókörök, amelyeket az Azure AD for Developers (v 1.0) web API (Resource) alkalmazás tesz elérhetővé az ügyfélalkalmazások számára. A jogosultsági hatókörök az ügyfélalkalmazások számára is megadhatók a hozzájárulás során. Tekintse `oauth2Permissions` meg az [Azure Active Directory alkalmazás jegyzékfájljának](reference-app-manifest.md#manifest-reference)ismertetése című szakaszt.

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>A v 1.0 alkalmazás adott OAuth2 engedélyeihez való hozzáférést kérő hatókörök
Ha egy v 1.0 alkalmazás adott hatóköréhez jogkivonatokat kíván beszerezni (például az Azure ad Graph (https:\//Graph.Windows.net), a hatóköröket a kívánt erőforrás-azonosító egy kívánt OAuth2 engedéllyel való összefűzésével kell létrehoznia. Ehhez az erőforráshoz.

Ha például a felhasználó nevében egy v 1.0 webes API-t `ResourceId`szeretne elérni, amelyben az alkalmazás azonosítója URI:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Ha a MSAL.net Azure Active Directory az Azure ad Graph API-val (https:\//Graph.Windows.net/) szeretné olvasni és írni, a hatókörök listáját a következő módon kell létrehoznia:

```csharp
string ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.windows.net/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Ha meg szeretné írni a Azure Resource Manager API-nak megfelelő hatókört (https:\//Management.Core.Windows.net/), akkor a következő hatókört kell megadnia (jegyezze fel a két perjelet):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Két perjelet kell használnia, mivel a Azure Resource Manager API egy perjelet vár a célközönségi jogcímben (AUD), majd egy perjelet választ az API nevének a hatókörből való elkülönítésére.

Az Azure AD által használt logika a következő:

- A ADAL (v 1.0) végponthoz egy v 1.0 hozzáférési jogkivonat (az egyetlen lehetséges), az AUD = erőforrás
- A MSAL (Microsoft Identity platform (v 2.0) végpont) egy hozzáférési jogkivonatot kér egy v 2.0 tokent elfogadó erőforráshoz, AUD = erőforrás. AppId
- A MSAL (v 2.0 Endpoint) esetében, ha egy olyan erőforrás hozzáférési jogkivonatát kérdezi le, amely egy v 1.0 hozzáférési jogkivonatot fogad el (ez a fenti eset), az Azure AD a kért hatókörből elemezi a kívánt célközönséget, így az utolsó perjel előtt mindent megtesz, és használja erőforrás-azonosítóként. Ezért ha a https\/:/Database.Windows.net "https:\//Database.Windows.net/" célközönséget vár, a "https:\//Database.Windows.net//.default" hatókört kell kérnie. Lásd még: GitHub [-probléma #747: Az erőforrás URL-címének záró perjele ki van hagyva, ami az](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)SQL-hitelesítési hibát okozta.

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

## <a name="scopes-to-request-for-client-credential-flow--daemon-app"></a>Ügyfél-hitelesítő adat-és démon-alkalmazásra vonatkozó kérelmekre vonatkozó hatókörök
Az ügyfél-hitelesítési folyamat esetében az átadandó hatókör is a következő lesz `/.default`:. Ez azt jelzi, hogy az Azure AD: "minden olyan alkalmazás-szintű engedély, amelyet a rendszergazda beleegyezett az alkalmazás regisztrálására.

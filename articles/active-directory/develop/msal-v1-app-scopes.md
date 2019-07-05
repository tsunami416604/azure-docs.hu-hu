---
title: Hatókörök (Microsoft-hitelesítési tár) 1.0-s verzió alkalmazásra vonatkozóan |} Az Azure
description: Ismerje meg a hatókörök 1.0-s verziójú alkalmazás használata a Microsoft-hitelesítési tár (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e43bc245a5908ba1bf91e7b4bee6df2f5cfc618
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514366"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Hatókörök webes API 1.0-s verziójú jogkivonatok elfogadása

Az OAuth2-engedélyek engedélyhatókörök, amely egy Azure AD-hez (1.0-s verzió) a fejlesztők webes API (erőforrás) alkalmazás közzéteszi az ügyfélalkalmazások számára. Ezek engedélyhatókörök hozzájárulási során adható az ügyfélalkalmazások számára. Című témakör `oauth2Permissions` a a [Azure Active Directory application manifest referencia](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Hatókörök kérelmezzenek hozzáférést a megadott OAuth2 engedélyek 1.0-s verziójú alkalmazások
Ha szeretné-e szerzi be a jogkivonatokat meghatározott hatókörök 1.0-s verziójú alkalmazások (például az Azure AD graph, amely a https:\//graph.windows.net), elkülönített változó összefűzésével előállítjuk a kívánt erőforrás-azonosítója egy kívánt OAuth2 engedéllyel rendelkező hatókörök létrehozásához szükséges az adott erőforráshoz.

Például, hogy a felhasználó, ahol az alkalmazás Alkalmazásazonosító URI-ja az 1.0-s verziójú webes API nevében hozzáférés `ResourceId`:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Ha szeretné olvasása és írása az MSAL.NET Azure Active Directoryban az Azure AD graph API használatával (https:\//graph.windows.net/), akkor hasonlóan a következő hatókörök listájának hozzunk létre:

```csharp
string ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.windows.net/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Ha szeretne írni az Azure Resource Manager API-nak megfelelő hatóköre (https:\//management.core.windows.net/), meg kell kérnie a következő hatókörrel (Megjegyzés: a két perjeleket):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Két perjeleket használni, mert az Azure Resource Manager API perjellel vár a célközönség jogcím (aud), és azután ott van az API-név a hatókörből külön perjellel kell.

Az Azure AD által használt logikai a következő:

- Adal-t (1.0-s verzió) végpont egy 1.0-s verziójú hozzáférési jogkivonattal (az egyetlen lehetséges), aud erőforrás =
- A hozzáférési jogkivonat kérése egy erőforrást, 2.0-s verziójú jogkivonatok, aud elfogadásával MSAL (a Microsoft identity platform (2.0-s verzió) végpont) erőforrást =. Alkalmazásazonosító
- Az MSAL (v2.0-végpontra) egy hozzáférési jogkivonatot kér egy erőforrást egy 1.0-s verziójú hozzáférési jogkivonat (amely a fenti eset) elfogadása az Azure AD a kért hatókörhöz a kívánt célközönség elemzi a minden, a legutóbbi / előtti és használhatná az erőforrás-azonosítója alapján. Ezért ha https:\//database.windows.net vár egy adott célközönségnek, a "https:\//database.windows.net/", szüksége lesz egy hatókörének kérése "https:\//database.windows.net//.default". Lásd még: GitHub probléma [#747: Erőforrás URL-cím, záró perjellel hiányzik, amely az sql-hitelesítési hiba okozza,](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Hatókörök igényelhet hozzáférést egy 1.0-s verziójú alkalmazás minden engedély
Ha azt szeretné, a statikus hatókörök 1.0-s verziójú alkalmazások jogkivonat-beszerzési, Hozzáfűzés ".default formátummá lesznek feloldva" a app ID URI-ját az API-hoz:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-client-credential-flow--daemon-app"></a>Hatókörök kérni az ügyfél hitelesítőadat-folyamat / démon alkalmazás
Ügyfél-hitelesítő adat folyamatát, esetén a hatókör át is lenne `/.default`. Azonban igen, az Azure AD: "minden az alkalmazásszintű engedélyek, amelyek a rendszergazda az alkalmazás regisztrálása a hozzájárult.

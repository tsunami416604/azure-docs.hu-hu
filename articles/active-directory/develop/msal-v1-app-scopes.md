---
title: Az 1.0-s v.0-s alkalmazások (MSAL) hatókörei | Azure
description: Ismerje meg a v1.0-s alkalmazások hatóköreit a Microsoft authentication library (MSAL) használatával.
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
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536182"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Hatókör egy web API-hoz, amely elfogadja a v1.0-s jogkivonatokat

Az OAuth2-engedélyek olyan engedélyható körök, amelyeket egy Azure Active Directory (Azure AD) a fejlesztők (1.0-s) webes API-alkalmazások számára az ügyfélalkalmazások számára elérhetővé tesz. Ezek az engedélyható körök a hozzájárulás során adhatók meg az ügyfélalkalmazásoknak. Tekintse meg `oauth2Permissions` az [Azure Active Directory alkalmazásjegyzék-hivatkozáscímű szakaszát.](reference-app-manifest.md#manifest-reference)

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Egy 1.0-s alkalmazás adott OAuth2 engedélyéhez való hozzáférés kérésének hatóhatókörei

Jogkivonatok beszerzése egy v1.0-s alkalmazás bizonyos hatóköreihez https://graph.microsoft.com)(például a Microsoft Graph API-hoz, amely, a szükséges erőforrás-azonosító összefűzésével hozzon létre egy kívánt erőforrás-azonosítót az adott erőforrás kívánt OAuth2 engedélyével.

Ha például a felhasználó nevében szeretne hozzáférni egy v1.0-s `ResourceId`webes API-hoz, ahol az alkalmazásazonosító URI-ja:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Ha MSAL.NET Azure AD-vel szeretne olvasni és\/írni a Microsoft Graph API használatával (https: /graph.microsoft.com/), létre kell hoznia a hatókörök listáját az alábbi példák szerint:

```csharp
string ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.microsoft.com/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Az Azure Resource Manager API-nak megfelelő\/hatókör (https: /management.core.windows.net/) írásához a következő hatókört kell kérnie (vegye figyelembe a két perjelet):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Két perjelet kell használnia, mert az Azure Resource Manager API perjelet vár a közönségjogcímében (aud), és van egy perjel, amely elválasztja az API-nevet a hatókörtől.

Az Azure AD által használt logika a következő:

- ADAL (Azure AD v1.0) végpont egy v1.0-s hozzáférési jogkivonattal (az egyetlen lehetséges), aud=resource
- MSAL (Microsoft identity platform (v2.0)) végpont, amely egy hozzáférési jogkivonatot kér egy 2.0-s jogkivonatot fogadó erőforráshoz,`aud=resource.AppId`
- Az MSAL (2.0-s végpont) egy hozzáférési jogkivonatot kér egy erőforrás, amely elfogadja a v1.0-s hozzáférési jogkivonatot (ami a fenti esetben), az Azure AD elemzi a kívánt közönséget a kért hatókörben azáltal, hogy mindent az utolsó perjel előtt, és erőforrás-azonosítóként használja. Ezért ha https:\//database.windows.net "https:\//database.windows.net/" célközönséget vár, akkor "https:\//database.windows.net//.default" hatókört kell kérnie. Lásd még: [GitHub-probléma #747: Az erőforrás url-cím záró perjelje kimarad, ami sql auth hibát okozott.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Hatókör a v1.0-s alkalmazás összes engedélyéhez való hozzáférés kéréséhez

Ha egy 1.0-s alkalmazás összes statikus hatóköréhez jogkivonatot szeretne beszerezni, fűzze hozzá a ".default" értéket az API alkalmazásazonosító URI-jához:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>Ügyfélhitelesítő adatfolyam-/démonalkalmazás igénylésére szolgáló hatókörök

Az ügyfél hitelesítő adatok áramlása esetén a `/.default`továbbítandó hatókör is . Ez azt mondja, hogy az Azure AD: "az összes alkalmazásszintű engedélyeket, amelyek a rendszergazda hozzájárult az alkalmazás regisztrációjában.

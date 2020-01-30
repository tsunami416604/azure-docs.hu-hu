---
title: Webes API meghívása egy Daemon-alkalmazásból – Microsoft Identity platform | Azure
description: Megtudhatja, hogyan hozhat létre webes API-kat meghívó Daemon-alkalmazást
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 338b638d6b33bcbbb5cf377643a96c71b0d314bd
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775195"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Webes API-kat meghívó Daemon-alkalmazás – webes API meghívása az alkalmazásból

A .NET Daemon-alkalmazások hívhatnak webes API-t. A .NET Daemon-alkalmazások számos előre jóváhagyott webes API-t is meghívhatnak.

## <a name="calling-a-web-api-from-a-daemon-application"></a>Webes API meghívása egy Daemon-alkalmazásból

A token használatával a következő módon hívhat meg API-t:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

---

## <a name="calling-several-apis"></a>Több API meghívása

A Daemon-alkalmazások esetében a hívott webes API-kat előzetesen jóvá kell hagyni. A Daemon-alkalmazásokhoz nem szükséges növekményes beleegyezni. (Nincs felhasználói beavatkozás.) A bérlői rendszergazdának előre meg kell adnia a belefoglalást az alkalmazáshoz és az összes API-engedélyhez. Ha több API-t szeretne meghívni, minden egyes erőforráshoz meg kell adnia egy jogkivonatot, minden alkalommal, amikor meghívja a `AcquireTokenForClient`. A MSAL az alkalmazás-jogkivonat gyorsítótárát fogja használni a szükségtelen szolgáltatási hívások elkerülése érdekében.

## <a name="next-steps"></a>Következő lépések

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon-alkalmazás – áttérés éles környezetbe](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon-alkalmazás – áttérés éles környezetbe](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon-alkalmazás – áttérés éles környezetbe](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---

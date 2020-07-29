---
title: Webes API meghívása egy Daemon-alkalmazásból – Microsoft Identity platform | Azure
description: Megtudhatja, hogyan hozhat létre webes API-kat meghívó Daemon-alkalmazást
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e440628526dada7655cc71f63fc9fff006cc5ef5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885446"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Webes API-kat meghívó Daemon-alkalmazás – webes API meghívása az alkalmazásból

A .NET Daemon-alkalmazások hívhatnak webes API-t. A .NET Daemon-alkalmazások számos előre jóváhagyott webes API-t is meghívhatnak.

## <a name="calling-a-web-api-from-a-daemon-application"></a>Webes API meghívása egy Daemon-alkalmazásból

A token használatával a következő módon hívhat meg API-t:

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="java"></a>[Java](#tab/java)

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

A Daemon-alkalmazások esetében a hívott webes API-kat előzetesen jóvá kell hagyni. A Daemon-alkalmazásokhoz nem szükséges növekményes beleegyezni. (Nincs felhasználói beavatkozás.) A bérlői rendszergazdának előre meg kell adnia a belefoglalást az alkalmazáshoz és az összes API-engedélyhez. Ha több API-t szeretne meghívni, minden alkalommal meg kell adnia egy jogkivonatot minden egyes erőforráshoz `AcquireTokenForClient` . A MSAL az alkalmazás-jogkivonat gyorsítótárát fogja használni a szükségtelen szolgáltatási hívások elkerülése érdekében.

## <a name="next-steps"></a>További lépések

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon-alkalmazás – áttérés éles környezetbe](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon-alkalmazás – áttérés éles környezetbe](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon-alkalmazás – áttérés éles környezetbe](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---

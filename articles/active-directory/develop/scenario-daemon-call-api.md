---
title: Daemon-alkalmazás, amely webes API-kat hív meg (webes API-k meghívása) – Microsoft Identity platform
description: Ismerje meg, hogyan hozhat létre olyan Daemon-alkalmazást, amely webes API-kat hív meg (webes API-k hívása)
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
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7a67e98d87850e2fc90df3ce7ba15e5e60da517
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596751"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Webes API-kat meghívó Daemon-alkalmazás – webes API meghívása az alkalmazásból

Egy démon-alkalmazás meghívhat egy webes API-t egy .NET Daemon-alkalmazásból, vagy meghívhat számos előre jóváhagyott webes API-t.

## <a name="calling-a-web-api-from-a-net-daemon-application"></a>Webes API meghívása .NET Daemon-alkalmazásból

Az alábbi témakörből megtudhatja, hogyan hívhat meg egy API-t a token használatával

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

A Daemon-alkalmazások esetében a hívott webes API-kat előzetesen jóvá kell hagyni. Nem lesz növekményes beleegyezik a Daemon-alkalmazásokkal (nincs felhasználói beavatkozás). A bérlői rendszergazdának előre jóvá kell hagynia az alkalmazást és az összes API-engedélyt. Ha több API-t szeretne meghívni, minden egyes erőforráshoz meg kell adnia egy jogkivonatot, minden alkalommal, amikor meghívja a `AcquireTokenForClient`. A MSAL az alkalmazás-jogkivonat gyorsítótárát fogja használni a szükségtelen szolgáltatási hívások elkerülése érdekében.

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

---
title: Webes API felhívása démonalkalmazásból – Microsoft identity platform | Azure
description: További információ a webes API-kat meghívjaó démonalkalmazások létrehozásáról
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885446"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>A webes API-kat meghívó Démonalkalmazás – webes API hívása az alkalmazásból

A .NET démonalkalmazások webes API-t hívhatnak. A .NET démonalkalmazások több előre jóváhagyott webes API-t is hívhatnak.

## <a name="calling-a-web-api-from-a-daemon-application"></a>Webes API hívása démonalkalmazásból

A jogkivonat ot az API-k hívására a következőképpen használhatja:

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

## <a name="calling-several-apis"></a>Több API hívása

A démonalkalmazások esetében a hívott webes API-kat előzetesen jóvá kell hagyni. A démonalkalmazások hoz nem járulhozzá növekményes hozzájárulás. (Nincs felhasználói beavatkozás.) A bérlői rendszergazdának előzetesen meg kell adnia a beleegyezést az alkalmazáshoz és az összes API-engedélyhez. Ha több API-t szeretne hívni, minden erőforráshoz be kell `AcquireTokenForClient`szereznie egy jogkivonatot, minden alkalommal, amikor meghívja. Az MSAL az alkalmazásjogkivonat-gyorsítótárat fogja használni a szükségtelen szolgáltatáshívások elkerülése érdekében.

## <a name="next-steps"></a>További lépések

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Démon alkalmazás – ugrás éles környezetre](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Démon alkalmazás – ugrás éles környezetre](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Démon alkalmazás – ugrás éles környezetre](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---

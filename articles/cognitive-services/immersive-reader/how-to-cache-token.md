---
title: A hitelesítési jogkivonat gyorsítótárazása
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan lehet gyorsítótárazni a hitelesítési jogkivonatot.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: e652aa29b1c1935fcc4887dbe13ef9b683a8bd05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946165"
---
# <a name="how-to-cache-the-authentication-token"></a>A hitelesítési jogkivonat gyorsítótárazása

Ez a cikk bemutatja, hogyan gyorsítótárazhatja a hitelesítési jogkivonatot az alkalmazás teljesítményének javítása érdekében.

## <a name="using-aspnet"></a>A ASP.NET használata

Importálja a **Microsoft.IdentityModel.Clients.ActiveDirectory** NuGet csomagot, amely a jogkivonat megszerzésére szolgál. Ezután a következő kód `AuthenticationResult`segítségével szerezzen be egy , a [Magával ragadó olvasó erőforrás létrehozásakor](./how-to-create-immersive-reader.md)kapott hitelesítési értékek használatával.

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

Az `AuthenticationResult` objektum `AccessToken` rendelkezik egy tulajdonsággal, amely a tényleges token fogja használni, amikor elindítja a magával ragadó olvasó segítségével sdk. Ez is `ExpiresOn` birtokol egy tulajdonság, amely jelzi, amikor a jogkivonat lejár. A Magával ragadó olvasó elindítása előtt ellenőrizheti, hogy a jogkivonat lejárt-e, és csak akkor szerezhet be egy új jogkivonatot, ha lejárt.

## <a name="using-nodejs"></a>A Node.JS használata

Adja hozzá a [**request**](https://www.npmjs.com/package/request) npm csomagot a projekthez. A következő kód segítségével szerezzen be egy jogkivonatot a [Immersive Reader erőforrás létrehozásakor](./how-to-create-immersive-reader.md)kapott hitelesítési értékek használatával.

```javascript
router.get('/token', function(req, res) {
    request.post(
        {
            headers: { 'content-type': 'application/x-www-form-urlencoded' },
            url: `https://login.windows.net/${TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: CLIENT_ID,
                client_secret: CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, json) {
            const result = JSON.parse(json);
            return res.send({
                access_token: result.access_token,
                expires_on: result.expires_on
            });
        }
    );
});
```

A `expires_on` tulajdonság az a dátum és időpont, amikor a token lejár, az 1970. Ezzel az értékkel határozza meg, hogy a jogkivonat lejárt-e, mielőtt megpróbálna újat beszerezni.

```javascript
async function getToken() {
    if (Date.now() / 1000 > CREDENTIALS.expires_on) {
        CREDENTIALS = await refreshCredentials();
    }
    return CREDENTIALS.access_token;
}
```

## <a name="next-steps"></a>További lépések

* Fedezze fel a [magával ragadó Reader SDK-referenciát](./reference.md)
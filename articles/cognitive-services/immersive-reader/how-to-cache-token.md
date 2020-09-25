---
title: A hitelesítési jogkivonat gyorsítótárazása
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan gyorsítótárazhatja a hitelesítési tokent.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 250fe6279288002fad1b6c12b158ffae3d83c264
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318857"
---
# <a name="how-to-cache-the-authentication-token"></a>A hitelesítési jogkivonat gyorsítótárazása

Ez a cikk bemutatja, hogyan gyorsítótárazhatja a hitelesítési tokent az alkalmazás teljesítményének növelése érdekében.

## <a name="using-aspnet"></a>A ASP.NET használata

Importálja a **Microsoft. IdentityModel. clients. ActiveDirectory** NuGet-csomagot, amely a jogkivonat beszerzésére szolgál. Ezután a következő kód használatával szerezzen be egy `AuthenticationResult` alkalmazást, amely a [magával ragadó olvasó erőforrás létrehozásakor](./how-to-create-immersive-reader.md)kapott hitelesítési értékeket használja.

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

Az `AuthenticationResult` objektumhoz tartozik egy olyan `AccessToken` tulajdonság, amely a valós token, amelyet a rendszer az SDK használatával fog használni. Emellett van egy tulajdonsága is, amely azt jelzi, hogy `ExpiresOn` mikor jár le a jogkivonat. A lebilincselő olvasó elindítása előtt megtekintheti, hogy a jogkivonat lejárt-e, és csak akkor szerezzen be egy új jogkivonatot, ha lejárt.

## <a name="using-nodejs"></a>Node.JS használata

Adja hozzá a [**kérelem**](https://www.npmjs.com/package/request) NPM-csomagját a projekthez. A következő kód használatával szerezheti be a jogkivonatot, és használhatja azokat a hitelesítési értékeket, amelyeket [az olvasói erőforrás létrehozásakor](./how-to-create-immersive-reader.md)kapott.

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

A `expires_on` tulajdonság az a dátum és időpont, amikor a jogkivonat lejár, a 1970 UTC óta eltelt másodpercek száma szerint. Ezzel az értékkel állapíthatja meg, hogy a jogkivonat lejárt-e, mielőtt megkísérelte egy új beszerzését.

```javascript
async function getToken() {
    if (Date.now() / 1000 > CREDENTIALS.expires_on) {
        CREDENTIALS = await refreshCredentials();
    }
    return CREDENTIALS.access_token;
}
```

## <a name="next-steps"></a>Következő lépések

* Ismerje meg az [olvasói SDK-referenciát](./reference.md)
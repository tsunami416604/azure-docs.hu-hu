---
title: Egyoldalas alkalmazás létrehozása webes API-hívással – Microsoft identity platform | Azure
description: Megtudhatja, hogyan hozhat létre egy egyoldalas alkalmazást, amely webes API-t hív meg
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: 5b70b109f43e80fc3ec68f52aef2dba6823033bb
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882131"
---
# <a name="single-page-application-call-a-web-api"></a>Egyoldalas alkalmazás: Webes API hívása

Azt javasoljuk, hogy `acquireTokenSilent` hívja meg a metódust egy hozzáférési jogkivonat beszerzéséhez vagy megújításához, mielőtt egy webes API-t hívna meg. Miután rendelkezik egy jogkivonatot, meghívhat egy védett webes API-t.

## <a name="call-a-web-api"></a>Webes API-hívás

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Használja a beszerzett hozzáférési jogkivonatot tulajdonosként egy HTTP-kérelemben bármely webes API, például a Microsoft Graph API hívásához. Például:

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://graph.microsoft.com/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

# <a name="angular"></a>[Angular](#tab/angular)

Az MSAL-gyűrűs kihasználja a HTTP interceptor automatikusan beszerzi a hozzáférési jogkivonatokat csendben, és csatolja őket a HTTP-kérelmek API-khoz. További információ: [Jogkivonat beszerzése API-hívásához.](scenario-spa-acquire-token.md)

---

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Átállás éles üzemre](scenario-spa-production.md)

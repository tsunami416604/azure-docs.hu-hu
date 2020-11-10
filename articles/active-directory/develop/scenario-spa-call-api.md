---
title: Webes API-t hívó egyoldalas alkalmazás létrehozása – Microsoft Identity platform | Azure
description: Ismerje meg, hogyan hozhat létre egy webes API-t meghívó egyoldalas alkalmazást
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
ms.openlocfilehash: 3b71f66dfcbd33cdecc1a6fea46871ba0cda687d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442888"
---
# <a name="single-page-application-call-a-web-api"></a>Egyoldalas alkalmazás: webes API meghívása

Javasoljuk, hogy a `acquireTokenSilent` webes API meghívása előtt hívja meg a metódust egy hozzáférési jogkivonat beszerzéséhez vagy megújításához. A jogkivonat meghívása után meghívhat egy védett webes API-t.

## <a name="call-a-web-api"></a>Webes API-hívás

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A beszerzett hozzáférési jogkivonatot tulajdonosként egy HTTP-kérelemben használhatja bármilyen webes API meghívásához, például Microsoft Graph API-t. Például:

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

A MSAL szögletes burkolója kihasználja a HTTP-Interceptor előnyeit, hogy a hozzáférési jogkivonatokat csendesen szerezze be, és csatolja őket a HTTP-kérésekhez API-khoz. További információ: [token beszerzése API meghívásához](scenario-spa-acquire-token.md).

---

## <a name="next-steps"></a>További lépések

Az ebben a forgatókönyvben lévő következő cikkre való áttéréssel váltson [éles környezetbe](scenario-spa-production.md).
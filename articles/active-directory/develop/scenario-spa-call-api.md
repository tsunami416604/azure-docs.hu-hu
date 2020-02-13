---
title: Webes API-t hívó egyoldalas alkalmazás létrehozása – Microsoft Identity platform | Azure
description: Ismerje meg, hogyan hozhat létre egy webes API-t meghívó egyoldalas alkalmazást
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: ccece6c840033913ec6d96b446dcb98c4befb32f
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159998"
---
# <a name="single-page-application-call-a-web-api"></a>Egyoldalas alkalmazás: webes API meghívása

Javasoljuk, hogy a webes API meghívása előtt hívja meg a `acquireTokenSilent` metódust egy hozzáférési jogkivonat beszerzéséhez vagy megújításához. A jogkivonat meghívása után meghívhat egy védett webes API-t.

## <a name="call-a-web-api"></a>Webes API-hívás

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="angulartabangular"></a>[Angular](#tab/angular)

A MSAL szögletes burkolója kihasználja a HTTP-Interceptor előnyeit, hogy a hozzáférési jogkivonatokat csendesen szerezze be, és csatolja őket a HTTP-kérésekhez API-khoz. További információ: [token beszerzése API meghívásához](scenario-spa-acquire-token.md).

---

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Áthelyezés éles környezetbe](scenario-spa-production.md)

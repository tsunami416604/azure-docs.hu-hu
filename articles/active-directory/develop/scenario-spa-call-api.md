---
title: Egyoldalas alkalmazás (egy webes API hívása) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy egyoldalas alkalmazás (egy webes API hívása)
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01f141a5374c0e794b264f6e0135ca3e15ff8359
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074845"
---
# <a name="single-page-application---call-a-web-api"></a>Egyoldalas alkalmazás – egy webes API hívása

Azt javasoljuk, hogy meghívja a `acquireTokenSilent` metódus hardvervásárlásra és a egy webes API hívása előtt hozzáférési jogkivonat megújításához. Miután megkapta a jogkivonatot, meghívhatja egy védett webes API-t.

## <a name="call-a-web-api"></a>Webes API-hívás

### <a name="javascript"></a>JavaScript

Egy HTTP-kérelem tulajdonosi minden webes API-k, például a Microsoft Graph API meghívásához a beszerezni a hozzáférési jogkivonatot használjuk. Példa:

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

### <a name="angular"></a>Angular

Említetteknek megfelelően az [beszerzése a tokenek szakaszban](scenario-spa-acquire-token.md), az MSAL Angular burkoló használja a HTTP elfogó automatikusan csendes beszerezni a hozzáférési jogkivonatok, és csatolja őket az API-k HTTP-kérelmekre.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Helyezze át az éles környezetbe](scenario-spa-production.md)

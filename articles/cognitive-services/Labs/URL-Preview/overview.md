---
title: Mi az a projekt URL-cím Preview? -Microsoft kognitív szolgáltatások |} Microsoft Docs
description: A projekt URL-cím Preview bemutatása.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 6b486e0ab4092bef4fe829a5f166311a572a2900
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348610"
---
# <a name="what-is-project-url-preview"></a>Mi az a projekt URL-cím Preview?
Az URL-cím előnézeti végpont URL-cím lekérdezési paramétert fogad, és visszaadja a cél erőforráson, rövid leírását és hivatkozást nevű JSON-választ a egy képen megjelenítendő kép. A válasz is magában foglalja a [isFamilyFriendly](url-preview-reference.md#query-parameters) jelzőt, amely jelzi, hogy az URL-címet tartalmaz felnőtt, hamisított vagy más érvénytelen tartalom. 

Ahhoz, hogy az URL-cím előnézettel, egy GET kérelmet, és tartalmazza a *Ocp-Apim-előfizetés-kulcs* fejléc a következő érvényes lexikális elem:  
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```
A válasz: 
````
HTTP Headers:
BingAPIs-TraceId: 3CC74C94769440C0851D9DF0869FCE7F
BingAPIs-SessionId: 52219085A6364692958C9C83983A0DBA
X-MSEdge-ClientID: 13D44DC2DE946B4B0F25460CDF036AD6
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 3CC74C94769440C0851D9DF0869FCE7F Ref B: CO1EDGE0315 Ref C: 2018-04-11T18:47:40Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https:\/\/swiftkey.com\/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https:\/\/swiftkey.com\/images\/og\/default.jpg"
  }
}

````
## <a name="scenarios"></a>Forgatókönyvek 

Az URL-cím előnézeti API támogatja a webes erőforrások rövid leírása. A fejlesztők használni, azt részletes előnézeti élmény létrehozásához.  Felhasználók megoszthatja és lássa el könyvjelzővel weblapok híreket, blogok, fórumok, stb. Ez az API tartalom moderálás is használható.    

Alkalmazás URL-cím Preview használja a webes kérelmeket küldeni a végpont URL-címet hozzárendelni előzetes lekérdezéssel.  A JSON-válasz preview információkat tartalmazza: név, leírás az erőforrás *familyFriendly* jelző, és hivatkozásokat tartalmaz, amelyek hozzáférést biztosít egy reprezentatív kép és a teljes erőforrás. 

## <a name="terms-of-use"></a>Használati feltételek
Csak a projekt URL-cím Preview adatok használatával a forrás webhelyek végfelhasználó által kezdeményezett URL-címben osztozik a közösségi média, csevegési botot vagy hasonló ajánlatok preview kódtöredékek és miniatűr képeket hiperhivatkozással ellátott megjelenítheti. D másolása, tárolásához, vagy projekt URL-cím Preview származó adatok gyorsítótárazásához. Tiszteletben webhelyére vagy a tartalmak tulajdonosai kapott előfordulhat, hogy előzetes verziójú funkciók letiltása minden kérést.

Vagy az Ön nevében, a harmadik fél előfordulhat, hogy nem használja, megőrizni, tárolásához, gyorsítótárazza, megosztásához vagy a tesztelési, fejleszt, betanítása, terjesztése, illetve elérhetővé teszi a nem Microsoft-szolgáltatás URL-cím előnézeti API Felületről adatokat terjeszteni vagy szolgáltatás. 

## <a name="throttling-requests"></a>Kérelmek szabályozása

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>További lépések
- [C# gyors üzembe helyezés](csharp.md)
- [Java gyors üzembe helyezés](java-quickstart.md)
- [JavaScript gyors üzembe helyezés](javascript.md)
- [Csomópont gyors üzembe helyezés](node-quickstart.md)
- [Python gyors üzembe helyezés](python-quickstart.md)

---
title: Mi az URL-cím előnézete projekt?
titlesuffix: Azure Cognitive Services
description: Az URL-cím előnézete projekt bemutatása.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-url-preview
ms.topic: overview
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: 5d0b8260bf1c58af915c1be18c32cec678f4f09c
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48869433"
---
# <a name="what-is-project-url-preview"></a>Mi az URL-cím előnézete projekt?
Az URL-cím előnézete végpont egy URL-címlekérdezési paramétert fogad, és egy JSON-választ ad vissza, amely tartalmazza a célerőforrás nevét, egy rövid leírást és egy, az előnézetben megjeleníteni kívánt képre mutató hivatkozást. A válasz tartalmazza az [isFamilyFriendly](url-preview-reference.md#query-parameters) jelzőt is, amely azt jelzi, hogy az URL-cím tartalmaz-e felnőtt-, hamisított vagy más illegális tartalmat. 

Az URL-cím előnézeti eredményeinek lekéréséhez küldeni kell egy GET-kérést, és bele kell foglalni az *Ocp-Apim-Subscription-Key* fejlécet egy érvényes tokennel:  
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

Az URL Preview API támogatja a webes erőforrások rövid leírásait. A fejlesztők információban gazdag előnézeti élmények megalkotásához használják.  A felhasználók megoszthatnak vagy könyvjelzővel jelölhetnek meg webhelyeket, híreket, blogokat, fórumokat stb. Ez az API használható tartalommoderáláshoz is.    

Az alkalmazások az URL-cím előnézetével webes kéréseket küldenek a végpontra egy, az előnézetben megtekinteni kívánt URL-címhez rendelt lekérdezéssel.  A JSON-válasz tartalmazza az előnézeti információkat: a nevet, az erőforrás leírását, a *familyFriendly* jelzőt és a címet jelölő képhez, valamint az egész online erőforráshoz hozzáférést biztosító hivatkozásokat. 

## <a name="terms-of-use"></a>Használati feltételek
Az URL-cím előnézete projektből kapott adatokat csak a forráshelyet hiperhivatkozással megjelölő előnézetek és miniatűrök megjelenítésére használhatja végfelhasználók által a közösségi médiában, csevegőrobotokban vagy hasonló ajánlatokban kezdeményezett URL-megosztásnál. Nem másolhatja, tárolhatja vagy gyorsítótárazhatja az URL-cím előnézete projektből kapott adatokat. Tiszteletben kell tartania a webhely- vagy tartalomtulajdonosoktól érkező, az előnézetek letiltására vonatkozó kéréseket.

Ön vagy az Ön nevében eljáró harmadik fél nem használhatja, őrizheti meg, tárolhatja, gyorsítótárazhatja, oszthatja meg vagy terjesztheti az URL Preview API-ról szerzett adatokat tesztelés, fejlesztés, képzés, terjesztés vagy bármely nem Microsoft szolgáltatás vagy funkció elérhetővé tétele céljából. 

## <a name="throttling-requests"></a>Kérelmek szabályozása

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>További lépések
- [C# – rövid útmutató](csharp.md)
- [Java – rövid útmutató](java-quickstart.md)
- [JavaScript – rövid útmutató](javascript.md)
- [Node – rövid útmutató](node-quickstart.md)
- [Python – rövid útmutató](python-quickstart.md)

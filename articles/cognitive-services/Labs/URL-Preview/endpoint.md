---
title: Projekt URL-cím előzetes verziójú végpont
titlesuffix: Azure Cognitive Services
description: Az URL-cím előnézete végpont összefoglalása.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 0464ac80c451ada46561de78b5ba0860c59a9e34
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868649"
---
# <a name="project-url-preview-endpoint"></a>Projekt URL-cím előzetes verziójú végpont

Az URL-cím előzetes API tartalmaz egy végpontot.

## <a name="endpoint"></a>Végpont
A következő végpont kérelmet küld egy URL-cím előnézete lekéréséhez. A fejlécek és URL-paraméterek használata egyéb beállításokat.

GET:
````
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

````

### <a name="query-parameters"></a>Lekérdezési paraméterek
|Name (Név)|Érték|Típus|Szükséges|  
|----------|-----------|----------|--------------|  
|válaszok|Az előzetes verzióra URL-címe|Sztring |Igen|
|biztonságos keresés|Érvénytelen felnőtt tartalom, vagy a hamisított tartalom le van tiltva, hibakód: 400, és a *isFamilyFriendly* jelző nem ad vissza. <p>Jogi felnőtt tartalom, az alábbi történik. Állapotkód: 200, adja vissza, és a *isFamilyFriendly* jelző false értékre van állítva.<ul><li>biztonságos keresés = szigorú: cím, leírás, URL-cím és a lemezkép nem állítható vissza.</li><li>biztonságos keresés = közepes; Cím, URL-cím és leírás, de nem a leíró képet kaphat.</li><li>biztonságos keresés kikapcsolása:; = Minden válasz objektumok/elemet – title, URL-címet, leírást és képet kaphat.</li></ul> |Sztring|Nem kötelező. </br> Biztonságos keresés alapértelmezés szerint szigorú =.| 

## <a name="response-object"></a>Válaszobjektum

A válasz HTTP-fejlécek és attribútumok weblap objektum tartalmazza a következő példában látható módon: `name`, `url`, `description`, `isFamilyFriendly`, és `primaryImageOfPage`.

````
BingAPIs-TraceId: 15AFE52A97AA422F960433A94803F6CE
BingAPIs-SessionId: 40587764F42142D3A8BA99F66B2B3BB6
X-MSEdge-ClientID: 0389E3EDED106B5E1424E82FEC436A56
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 15AFE52A97AA422F960433A94803F6CE Ref B: PAOEDGE0418 Ref C: 2018-03-30T16:36:27Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile ...",
  "url": "https://swiftkey.com/",
   "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

````

## <a name="next-steps"></a>További lépések
- [C# gyorsútmutató](csharp.md)
- [Java a rövid útmutató](java-quickstart.md)
- [JavaScript a rövid útmutató](javascript.md)
- [Csomópont a rövid útmutató](node-quickstart.md)
- [Python a rövid útmutató](python-quickstart.md)

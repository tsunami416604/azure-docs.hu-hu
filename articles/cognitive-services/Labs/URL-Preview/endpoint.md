---
title: Projekt URL-címének előnézeti végpontja
titlesuffix: Azure Cognitive Services
description: Az URL-cím előnézeti végpontjának összefoglalása.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 3ef5ebd4ec88deac8c49430f36956d3711c8c535
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706938"
---
# <a name="project-url-preview-endpoint"></a>Projekt URL-címének előnézeti végpontja

Az URL-cím előnézeti API egy végpontot tartalmaz.

## <a name="endpoint"></a>Végpont
URL-cím előnézetének beszerzéséhez küldjön egy kérelmet a következő végpontnak. Más specifikációk fejléceit és URL-paramétereit használhatja.

GET:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```

### <a name="query-parameters"></a>Lekérdezési paraméterek
|Name (Név)|Value|Type|Kötelező|  
|----------|-----------|----------|--------------|  
|válaszok|Előnézet URL-címe|Sztring |Igen|
|Biztonságos keresési|Az illegális felnőtt tartalmak vagy a kalóz tartalmak blokkolva vannak a 400 hibakód miatt, és a rendszer nem adja vissza a *isFamilyFriendly* jelzőt. <p>A jogi felnőtt tartalom esetében az alábbi viselkedést láthatja. Az állapotkód a 200 értéket adja vissza, és a *isFamilyFriendly* jelző hamis értékre van állítva.<ul><li>safeSearch=strict: A cím, a leírás, az URL-cím és a rendszerkép nem lesz visszaadva.</li><li>safeSearch = mérsékelt; Adja meg a címet, az URL-címet és a leírást, de ne a leíró képet.</li><li>safeSearch = kikapcsolva; Az összes válasz objektum/elem – cím, URL, leírás és rendszerkép lekérése.</li></ul> |Karakterlánc|Nem kötelező. </br> Alapértelmezés szerint a safeSearch = strict.| 

## <a name="response-object"></a>Válaszobjektum

A válasz tartalmazza a HTTP-fejléceket és a weboldal-objektumokat attribútumok szerint, a `name`következő példában `description`látható `isFamilyFriendly`módon: `primaryImageOfPage` `url`,,, és.

```
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

```

## <a name="next-steps"></a>További lépések
- [C# – rövid útmutató](csharp.md)
- [Java – rövid útmutató](java-quickstart.md)
- [JavaScript – rövid útmutató](javascript.md)
- [Node – rövid útmutató](node-quickstart.md)
- [Python – rövid útmutató](python-quickstart.md)

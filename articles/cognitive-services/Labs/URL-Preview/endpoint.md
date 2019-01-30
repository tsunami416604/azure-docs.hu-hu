---
title: Projekt URL-cím előzetes verziójú végpont
titlesuffix: Azure Cognitive Services
description: Az URL-cím előnézete végpont összefoglalása.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 985e08a9622e08d2a4b52dae996952cd6a7e7ad4
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207451"
---
# <a name="project-url-preview-endpoint"></a>Projekt URL-cím előzetes verziójú végpont

Az URL-cím előzetes API tartalmaz egy végpontot.

## <a name="endpoint"></a>Végpont
A következő végpont kérelmet küld egy URL-cím előnézete lekéréséhez. A fejlécek és URL-paraméterek használata egyéb beállításokat.

GET:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```

### <a name="query-parameters"></a>Lekérdezési paraméterek
|Name (Név)|Value|Typo|Szükséges|  
|----------|-----------|----------|--------------|  
|válaszok|Az előzetes verzióra URL-címe|Karakterlánc |Igen|
|biztonságos keresés|Érvénytelen felnőtt tartalom, vagy a hamisított tartalom le van tiltva, hibakód: 400, és a *isFamilyFriendly* jelző nem ad vissza. <p>Jogi felnőtt tartalom, az alábbi történik. Állapotkód: 200, adja vissza, és a *isFamilyFriendly* jelző false értékre van állítva.<ul><li>safeSearch=strict: Cím, leírás, URL-cím és a lemezkép nem állítható vissza.</li><li>biztonságos keresés = közepes; Cím, URL-cím és leírás, de nem a leíró képet kaphat.</li><li>biztonságos keresés kikapcsolása:; = Minden válasz objektumok/elemet – title, URL-címet, leírást és képet kaphat.</li></ul> |Karakterlánc|Nem kötelező. </br> Biztonságos keresés alapértelmezés szerint szigorú =.| 

## <a name="response-object"></a>Válaszobjektum

A válasz HTTP-fejlécek és attribútumok weblap objektum tartalmazza a következő példában látható módon: `name`, `url`, `description`, `isFamilyFriendly`, és `primaryImageOfPage`.

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

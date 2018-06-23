---
title: A projekt URL-cím előnézeti végpont - Microsoft kognitív szolgáltatások |} Microsoft Docs
description: Az URL-cím előnézeti végpont összegzését.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: ddd53aa49db01d7a6db397eb285d0854edc59388
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348615"
---
# <a name="project-url-preview-endpoint"></a>Projekt URL-cím előnézeti végpont

Az URL-cím előnézeti API-t egy végpontot tartalmaz.

## <a name="endpoint"></a>Végpont
Ahhoz, hogy egy URL-cím Preview, a következő végpont kérelmet küld. A fejlécek és URL-cím Paraméterek használata egyéb beállításokat.

GET:
````
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

````

### <a name="query-parameters"></a>Lekérdezési paraméterek
|Name (Név)|Érték|Típus|Szükséges|  
|----------|-----------|----------|--------------|  
|A Q|Az előzetes URL-címe|Sztring |Igen|
|biztonságos keresés|Felnőtt tartalom érvénytelen vagy hamisított tartalom le van tiltva, hibakód: 400, és a *isFamilyFriendly* jelző nem ad vissza. <p>Jogi felnőtt tartalom, az alábbi történik. Állapotkód adja vissza a 200 lesz, és a *isFamilyFriendly* jelző hamisra van állítva.<ul><li>biztonságos keresés = szigorú: cím, a leírás, a URL-cím és a lemezkép nem állítható vissza.</li><li>biztonságos keresés = mérsékelt; Cím, URL-cím és leírás, de nem a leíró kép lekérése.</li><li>biztonságos keresés = off; Minden válasz objektumok/elemet – cím, az URL-címet, a leírás és a kép lekérése.</li></ul> |Sztring|Nem szükséges. </br> Alapértelmezés szerint biztonságos keresés az = szigorú.| 

## <a name="response-object"></a>Válasz objektum

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
- [C# gyors üzembe helyezés](csharp.md)
- [Java gyors üzembe helyezés](java-quickstart.md)
- [JavaScript gyors üzembe helyezés](javascript.md)
- [Csomópont gyors üzembe helyezés](node-quickstart.md)
- [Python gyors üzembe helyezés](python-quickstart.md)

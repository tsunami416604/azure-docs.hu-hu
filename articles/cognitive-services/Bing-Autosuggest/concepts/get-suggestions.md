---
title: A Bing Autosuggest API keresési kifejezéseket javasol
titlesuffix: Azure Cognitive Services
description: Útmutató a Bing Autosuggest API használatához.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 293dcaadfc20116455983b3fc0069f9e9df3f843
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010548"
---
# <a name="suggesting-query-terms"></a>Lekérdezési kifejezések javaslása

Általában akkor hívható meg a Bing Autosuggest API minden alkalommal, amikor a felhasználó beír egy új karaktert a keresőmezőbe az alkalmazás. A lekérdezési sztring teljessége hatással van az API által visszaadott javasolt lekérdezési kifejezések relevanciájára. Minél teljesebb a lekérdezési sztring, annál relevánsabb lesz a javasolt lekérdezési kifejezések listája. Például, ha a javaslatok, amelyek az API-t a `s` valószínűleg kevésbé fontos, mint a adja vissza, a lekérdezések `sailing dinghies`.

## <a name="example-request"></a>Példakérelem

A következő példa egy olyan kérést mutat be, amely a *sail* sztringre adja vissza a javasolt lekérdezési sztringeket. Ne felejtse el URL-címként kódolni a felhasználó részleges lekérdezési kifejezését a [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#query) lekérdezési paraméter beállításakor. Ha például a felhasználó a *sailing les* sztringet adta meg, állítsa a `q` paramétert `sailing+les` vagy `sailing%20les` értékre.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Az alábbi válasz [SearchAction](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#searchaction)-objektumok egy listáját tartalmazza, amelyek a javasolt lekérdezési kifejezéseket tartalmazzák.

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

## <a name="using-suggested-query-terms"></a>Javasolt lekérdezési feltételek használatával

Minden javaslat tartalmaz egy `displayText`, `query` és `url` mezőt. A `displayText` mező tartalmazza a javasolt lekérdezést, amelyet a keresőmező legördülő listájának feltöltéséhez használ. A válaszban szereplő összes javaslatot meg kell jelenítenie az adott sorrendben.

Az alábbi példa bemutatja egy legördülő keresőmezőbe javasolt lekérdezés feltételeket, a Bing Autosuggest API.

![Autosuggest legördülő keresőmező-lista](../media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Ha a felhasználó kiválaszt egy javasolt lekérdezést a legördülő listából, használja a lekérdezési kifejezést a `query` mezőben a [Bing Web Search API](../../bing-web-search/search-the-web.md) meghívásához és a találatok megjelenítéséhez. Használhatja az URL-címet is az `url` mezőben, ha a felhasználót inkább a Bing keresési eredményeinek oldalára szeretné küldeni.

## <a name="next-steps"></a>További lépések

* [Mi az a Bing Autosuggest API?](../get-suggested-search-terms.md)
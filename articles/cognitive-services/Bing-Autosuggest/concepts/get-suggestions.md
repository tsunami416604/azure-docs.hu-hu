---
title: Keresési kifejezések rekedése a Bing Autosuggest API-val
titleSuffix: Azure Cognitive Services
description: Ez a cikk ismerteti a bing autojavaslati API-t használó lekérdezési kifejezések ajánlásának fogalmát, valamint a lekérdezés hosszának a relevanciára gyakorolt hatását.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 060dbd29ee4ddb78e8ae9b2ed4e7814da3c4eebf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072893"
---
# <a name="suggesting-query-terms"></a>Lekérdezési kifejezések javaslása

Általában a Bing Autosuggest API-t minden alkalommal meg kell hívnia, amikor egy felhasználó új karaktert gépel be az alkalmazás keresőmezőjébe. A lekérdezési sztring teljessége hatással van az API által visszaadott javasolt lekérdezési kifejezések relevanciájára. Minél teljesebb a lekérdezési sztring, annál relevánsabb lesz a javasolt lekérdezési kifejezések listája. Például a javaslatok, amelyek az `s` API-t visszaadhat, valószínűleg kevésbé relevánsak, mint a lekérdezések, amelyeket visszaad. `sailing dinghies`

## <a name="example-request"></a>Példakérelem

A következő példa egy olyan kérést mutat be, amely a *sail* sztringre adja vissza a javasolt lekérdezési sztringeket. Ne felejtse el URL-címként kódolni a felhasználó részleges lekérdezési kifejezését a [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#query) lekérdezési paraméter beállításakor. Ha például a felhasználó a *sailing les* sztringet adta meg, állítsa a `q` paramétert `sailing+les` vagy `sailing%20les` értékre.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Az alábbi válasz [SearchAction](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#searchaction)-objektumok egy listáját tartalmazza, amelyek a javasolt lekérdezési kifejezéseket tartalmazzák.

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

## <a name="using-suggested-query-terms"></a>Javasolt lekérdezési kifejezések használata

Minden javaslat tartalmaz egy `displayText`, `query` és `url` mezőt. A `displayText` mező tartalmazza a javasolt lekérdezést, amelyet a keresőmező legördülő listájának feltöltéséhez használ. A válaszban szereplő összes javaslatot meg kell jelenítenie az adott sorrendben.

A következő példa egy legördülő keresőmezőt mutat be a Bing Autosuggest API javasolt lekérdezési kifejezéseivel.

![Autosuggest legördülő keresőmező-lista](../media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Ha a felhasználó kiválaszt egy javasolt lekérdezést a legördülő listából, használja a lekérdezési kifejezést a `query` mezőben a [Bing Web Search API](../../bing-web-search/search-the-web.md) meghívásához és a találatok megjelenítéséhez. Használhatja az URL-címet is az `url` mezőben, ha a felhasználót inkább a Bing keresési eredményeinek oldalára szeretné küldeni.

## <a name="next-steps"></a>További lépések

* [Mi az a Bing Autosuggest API?](../get-suggested-search-terms.md)
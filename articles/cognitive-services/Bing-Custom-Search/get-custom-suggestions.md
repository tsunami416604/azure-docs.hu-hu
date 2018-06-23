---
title: 'Bing egyéni keresés: Egyéni automatikus kiegészítési javaslatokat kérhet |} Microsoft Docs'
description: Ismerteti, hogyan lehet egyéni automatikus javaslatokba javaslatok beolvasása
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 73059038018602f7aa76377bf7c98d4658576576
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347858"
---
# <a name="get-custom-suggestions"></a>Egyéni javaslatok kérése
Mielőtt elküldi a Bing egyéni keresés lekérdezések, hívja meg az egyéni automatikus kiegészítési API-t, a keresési kifejezés javaslatokat és a keresési élményt biztosít. Az egyéni automatikus kiegészítési API-t egy részleges lekérdezési karakterlánc, amely a felhasználó alapján javasolt lekérdezések listáját adja vissza. A javaslatok automatikus javaslatokba generáló előtt megadott vonatkozó egyéni lekérdezés feltételeket jelennek meg. További információkért lásd: [határozza meg az egyéni keresési javaslatok](define-custom-suggestions.md).

## <a name="endpoint"></a>Végpont
Ahhoz, hogy egyéni Bing keresési API használatával javasolt lekérdezések, küldjön egy `GET` kérelem a következő végponthoz.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

## <a name="response-json"></a>Válasz JSON
A válasz SearchAction objektumokat, amelyek a javasolt lekérdezési kifejezések listáját tartalmazza.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Minden javaslat tartalmaz egy `displayText` és `query` mező. A `displayText` mező tölti ki a keresőmezőbe legördülő listából válassza ki a javasolt lekérdezést tartalmaz.

Ha a felhasználó kijelöli a javasolt lekérdezés a legördülő listából, a lekérdezési kifejezés használata a `query` meghívásakor mezőben a [Bing egyéni Search API](overview.md).

## <a name="throttling-requests"></a>Kérelmek szabályozása

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>További lépések

- [Az egyéni keresési hívás](./search-your-custom-view.md)
- [A központi felhasználói felületi élmény konfigurálása](./hosted-ui.md)
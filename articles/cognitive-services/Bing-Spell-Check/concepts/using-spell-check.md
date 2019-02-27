---
title: A Bing Spell Check API használatával
titlesuffix: Azure Cognitive Services
description: További tudnivalók a Bing helyesírás-ellenőrzési mód, beállítások és az API-hoz kapcsolódó egyéb információkat.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 9544337ef1322e52cbdf123bb48d283485a8c7dd
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889952"
---
# <a name="using-the-bing-spell-check-api"></a>A Bing Spell Check API használatával

Ez a cikk segítségével hajtsa végre a környezetfüggő szintaxis és a helyesírás-ellenőrzés a Bing Spell Check API használatával. A legtöbb helyesírás-Pepita szótár-alapú szabálykészletek támaszkodnak, amíg a Bing helyesírás-ellenőrző kihasználja a gépi tanulás és statisztikai gépi fordítási biztosít pontos és környezetfüggő javításokat. 

## <a name="spell-check-modes"></a>Helyesírás-ellenőrzési módok

Az API kétféle ellenőrzési módot támogat: `Proof` és `Spell`.  Próbálja ki a példákat [itt](https://azure.microsoft.com/services/cognitive-services/spell-check/).

### <a name="proof---for-documents"></a>A koncepció - dokumentumok 

Az alapértelmezett mód a `Proof`. A `Proof` helyesírási mód biztosítja a legátfogóbb ellenőrzéseket, és a nagybetűs írásmód, az alapvető központozás és más funkciók hozzáadásával segíti a dokumentumok létrehozását. Azonban ez a mód kizárólag az en-US (angol-amerikai), es-ES (spanyol), pt-BR (portugál) piacokon érhető el (megjegyzés: a spanyol és portugál piac esetében csak béta verzióban). Minden más piachoz állítsa be a lekérdezési paramétert Spell módra. 

> [!NOTE]
> Ha a lekérdezés szövege hosszabb 4096, csonkolva lesz a 4096 karakternél, majd feldolgozni. 

### <a name="spell----for-web-searchesqueries"></a>Helyesírás - webes keresések és lekérdezések

A `Spell` agresszívabb, hogy jobb keresési eredményeket adhasson. A `Spell` mód megtalálja a legtöbb helyesírási hibát, de nem talál meg néhány olyan nyelvtani hibát, például a nagybetűket és a szóismétlést, amelyeket a `Proof` észlel.

> [!NOTE]
> * A lekérdezés a maximális támogatott hossz nem éri el. Ha a lekérdezés túllépi a maximális hosszúságú, a lekérdezés, és az eredményeket nem változik.
>    * a következő nyelvkódokról 130 karaktert: en, Németország, es, fr, pl, pt, sv, ru, nl, nb, tr-tr, it, zh, ko. 
>    * minden egyéb esetében: 65 karakter.
> * A helyesírás-mód nem támogatja a szögletes zárójelet (`[` és `]`) a lekérdezésekben és inkonzisztens eredményhez vezethet. Azt javasoljuk, hogy a helyesírás-mód használatakor eltávolítja a hatóköröket a lekérdezéseket.

## <a name="market-setting"></a>Piac beállítása

A [kód piaci](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#market-codes) együtt adható meg a `mkt` lekérdezési paraméter a kérelemből. Az API más módon fogja használni egy alapértelmezett piaci a kérés IP-címe alapján.


## <a name="http-post-and-get-support"></a>Támogatja a HTTP POST és a GET

Az API támogatja a HTTP POST és a HTTP GET metódust is. Az ellenőrizni kívánt szöveg hosszától függ, hogy melyiket érdemes használnia. Ha a sztringek mindig rövidebbek 1500 karakternél, használja a GET-et. Ha azonban 10 000 karakterig szeretné támogatni a sztringeket, akkor használja a POST-ot. A szöveges karakterlánc bármilyen érvényes UTF-8 karaktert tartalmazhat.

A következő példa egy szöveges karakterlánc helyesírását és nyelvtanát ellenőrző POST-kérelmet mutat. A teljesség kedvéért a példa tartalmazza a [mode](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#mode) lekérdezési paramétert (ezt ki lehetett volna hagyni, hiszen a `mode` alapértelmezett értéke a Proof). A [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#text) lekérdezési paraméter tartalmazza az ellenőrizni kívánt sztringet.
  
```  
POST https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?mode=proof&mkt=en-us HTTP/1.1  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 47  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
 
text=when+its+your+turn+turn,+john,+come+runing  
``` 

Ha a HTTP GET metódust használja, hozzáadhatja a `text` lekérdezési paramétert az URL lekérdezési sztringjéhez
  
Az alábbiakban az előző kérelemre adott válasz látható. A válasz tartalmaz egy [SpellCheck](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#spellcheck) objektumot. 
  
```json
{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  
  
A [flaggedTokens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#flaggedtokens) mező listázza a helyesírási és nyelvtani hibákat, amelyeket az API talált a [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#text) sztringben. A `token` mező tartalmazza a kicserélendő szót. A token megtalálásához a `text` sztringben használjon nullaalapú eltolást a `offset` mezőben. Majd kicserélheti az ott talált szót a `suggestion` mezőben található szóval. 

Ha a `type` mező RepeatedToken, továbbra is kicserélheti a tokent a `suggestion` mezővel, de emellett valószínűleg el kell távolítania a záró szóközt.

## <a name="throttling-requests"></a>Kérelmek szabályozása

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>További lépések

- [Mi az a Bing Spell Check API?](../overview.md)
- [A Bing Spell Check API 7-es verzió referenciája](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)

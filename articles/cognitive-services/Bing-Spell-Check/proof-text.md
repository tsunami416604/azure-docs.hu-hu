---
title: Mi a Bing Spell Check API?
titlesuffix: Azure Cognitive Services
description: A Bing Spell Check API gépi tanulást és statisztikai gépi fordítást használ a kontextusalapú helyesírás-ellenőrzéshez.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: overview
ms.date: 05/03/2018
ms.author: nolachar
ms.openlocfilehash: 48d9dee014d0759bd339c79811bb7b2fddecfe0b
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50214936"
---
# <a name="what-is-bing-spell-check-api"></a>Mi a Bing Spell Check API?

A Bing Spell Check API segítségével kontextusalapú nyelvtani és helyesírás-ellenőrzést végezhet egy szövegben.

Mi a különbség a szokásos helyesírás-ellenőrzők és a Bing harmadik generációs helyesírás-ellenőrzője között? A jelenlegi helyesírás-ellenőrzők a szótáralapú szabályrendszerekre támaszkodnak a helyesírás- és nyelvtani ellenőrzéskor, amelyek időszakosan frissülnek és bővülnek. Másként fogalmazva a helyesírás-ellenőrző csak annyira megbízható, amennyire az azt segítő szótár és annak összeállítói. Ez a helyesírás-ellenőrző típus lehet ismerős a Microsoft Wordből és más szövegszerkesztőkből.

Ezzel szemben a Bing kifejlesztett egy olyan webalapú helyesírás-ellenőrzőt, amely kihasználja a gépi tanulás és statisztikai gépi fordítás előnyeit egy állandóan fejlődő és a kontextus alapján működő algoritmust dinamikus betanítására. A helyesírás-ellenőrző a webes keresések és dokumentumok nagy tömegén alapul.

Ez a helyesírás-ellenőrző bármilyen szövegszerkesztési esetet képes kezelni:

- Felismeri a szlenget és az informális nyelvet
- Felismeri a hibákat a közneveknél
- Egyetlen jelzéssel javítja ki a szóelválasztáskor felmerülő problémákat
- Képes kijavítani kontextusban a homofónokat és más nehezen észrevehető hibákat
- Már a megjelenéstől támogatja az új márkákat, a digitális szórakoztatóipari és más népszerű kifejezéseket
- Azokat a szavakat, amelyek ugyanúgy hangzanak, de jelentésben és helyesírásban eltérnek, például „folyt” és „fojt”.

## <a name="spell-check-modes"></a>Helyesírás-ellenőrzési módok

Az API kétféle ellenőrzési módot támogat: `Proof` és `Spell`.  Próbálja ki a példákat [itt](https://azure.microsoft.com/services/cognitive-services/spell-check/).
### <a name="proof---for-documents-scenario"></a>Proof – a dokumentumokhoz
Az alapértelmezett mód a `Proof`. A `Proof` helyesírási mód biztosítja a legátfogóbb ellenőrzéseket, és a nagybetűs írásmód, az alapvető központozás és más funkciók hozzáadásával segíti a dokumentumok létrehozását. Azonban ez a mód kizárólag az en-US (angol-amerikai), es-ES (spanyol), pt-BR (portugál) piacokon érhető el (megjegyzés: a spanyol és portugál piac esetében csak béta verzióban). Minden más piachoz állítsa be a lekérdezési paramétert Spell módra. 
<br /><br/>**MEGJEGYZÉS:** Ha a lekérdezés szövegének hossza meghaladja a 4096 karaktert, feldolgozás előtt 4096 karakterre lesz csonkolva. 
### <a name="spell----for-web-searchesqueries-scenario"></a>Spell – a webes keresésekhez/lekérdezésekhez
A `Spell` agresszívabb, hogy jobb keresési eredményeket adhasson. A `Spell` mód megtalálja a legtöbb helyesírási hibát, de nem talál meg néhány olyan nyelvtani hibát, például a nagybetűket és a szóismétlést, amelyeket a `Proof` észlel.
<br /></br>**MEGJEGYZÉS:** A lekérdezések maximális támogatott hossza az alábbiak szerint alakul. Ha a lekérdezés túllépi a maximális hosszt, sem a lekérdezés, sem az eredmények nem változnak meg.
<ul><li>130 karakter az en, de, es, fr, pl, pt, sv, ru, nl, nb, tr-tr, it, zh, ko nyelvkódokhoz. </li>
<li>65 karakter a többi nyelvkódhoz</li></ul>

## <a name="market-setting"></a>Piac beállítása
A piacot meg kell határozni a lekérdezési paraméterben a kérelem URL-címénél, máskülönben a helyesírás-ellenőrző az IP-címből kiindulva az alapértelmezett piacot alkalmazza.


## <a name="post-vs-get"></a>POST vs. GET

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
  
```  
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

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>További lépések

Annak érdekében, hogy gyorsan nekiláthasson az első kérelem létrehozásának, olvassa el az [első kérelem létrehozását bemutató](quickstarts/csharp.md) cikket.

Ismerkedjen meg a [Bing Spell Check API referenciával](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference). A referencia olyan végpontok, fejlécek és lekérdezési paraméterek listáját tartalmazza, amelyekkel a keresési eredményeket és a válaszobjektumok definícióit kérheti le. 

Mindenképpen olvassa el a [Bing használati és megjelenítési előírásait,](./useanddisplayrequirements.md) hogy ne szegje meg az eredmények használatának szabályait.

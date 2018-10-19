---
title: 'Rövid útmutató: Bing Spell Check API, Python'
titlesuffix: Azure Cognitive Services
description: Információk és kódminták segítségével ismerkedhet meg a Bing Spell Check API használatának első lépéseivel.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: quickstart
ms.date: 09/14/2017
ms.author: v-jaswel
ms.openlocfilehash: 8614e4388f45c3bc9f71697bad44589e2165f6c8
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803290"
---
# <a name="quickstart-for-bing-spell-check-api-with-python"></a>Rövid útmutató a Bing Spell Check API és a Python használatához 

Ez a cikk bemutatja, hogyan használhatja a [Bing Spell Check API-t](https://azure.microsoft.com/services/cognitive-services/spell-check/) a Python segítségével. A Spell Check API visszaadja a fel nem ismert szavak listáját a javasolt cserekifejezésekkel együtt. Általános esetben küld egy szöveget az API-nak, majd végrehajtja a javasolt cseréket a szövegben, vagy megmutatja azokat az alkalmazás felhasználójának, hogy ő dönthesse el, végre szeretné-e hajtani a cserét. Ez a cikk bemutatja, hogyan küldhet el egy olyan kérést, amely a „Hollo, wrld!” szöveget tartalmazza. A javasolt cserekifejezések a „Hello” és a „world” lesznek.

## <a name="prerequisites"></a>Előfeltételek

A kód futtatásához [Python 3.x](https://www.python.org/downloads/) szükséges.

Rendelkeznie kell egy, a **Bing Spell Check API 7-es verzióját** tartalmazó [Cognitive Services API-fiókkal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Az [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/#lang) elegendő ehhez a rövid útmutatóhoz. Szüksége lesz az ingyenes próbaverzió aktiválásakor kapott hozzáférési kulcsra, vagy beszerezhet egy fizetős előfizetői azonosítót az Azure-irányítópultról.

## <a name="get-spell-check-results"></a>Spell Check-eredmények lekérése

1. Hozzon létre egy új Python-projektet kedvenc IDE-környezetében.
2. Adja hozzá az alábbi kódot.
3. A `subscriptionKey` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

```python
import http.client, urllib.parse, json

text = 'Hollo, wrld!'

data = {'text': text}

# NOTE: Replace this example key with a valid subscription key.
key = 'ENTER KEY HERE'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/spellcheck?'
params = 'mkt=en-us&mode=proof'

headers = {'Ocp-Apim-Subscription-Key': key,
'Content-Type': 'application/x-www-form-urlencoded'}

# The headers in the following example 
# are optional but should be considered as required:
#
# X-MSEdge-ClientIP: 999.999.999.999  
# X-Search-Location: lat: +90.0000000000000;long: 00.0000000000000;re:100.000000000000
# X-MSEdge-ClientID: <Client ID from Previous Response Goes Here>

conn = http.client.HTTPSConnection(host)
body = urllib.parse.urlencode (data)
conn.request ("POST", path + params, body, headers)
response = conn.getresponse ()
output = json.dumps(json.loads(response.read()), indent=4)
print (output)
```

**Válasz**

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon: 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bing Spell Check – oktatóanyag](../tutorials/spellcheck.md)

## <a name="see-also"></a>Lásd még

- [A Bing Spell Check áttekintése](../proof-text.md)
- [A Bing Spell Check API 7-es verzió referenciája](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)

---
title: Rövid útmutató – lekérdezés küldése az API-nak a Pythonban – Bing helyi üzleti keresés
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval megkezdheti a Bing local Business Search API használatát a Pythonban.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: c7e7ef7f052fccfea18b246f41109d5fa7528b4b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75379743"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Gyors útmutató: lekérdezés küldése a Bing helyi üzleti keresési API-nak a Pythonban

Ezzel a rövid útmutatóval megkezdheti a kérések küldését a Bing local Business Search API számára, amely egy Azure kognitív szolgáltatás. Habár ez az egyszerű alkalmazás Pythonban íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a HTTP-kérelmeket és a JSON-elemzést lehetővé tevő programozási nyelvekkel.

Ez a példában szereplő alkalmazás a keresési lekérdezés `hotel in Bellevue`API-ból érkező helyi válaszüzeneteket kéri le.

## <a name="prerequisites"></a>Előfeltételek

* [Python](https://www.python.org/) 2. x vagy 3. x
 
A Bing API-kkal [Cognitive Services API-fiókkal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) kell rendelkeznie. Az [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) elegendő ehhez a rövid útmutatóhoz. Használja az ingyenes próbaverzió által biztosított hozzáférési kulcsot.  Lásd még: [Cognitive Services díjszabása – BING Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-complete-application"></a>A teljes alkalmazás futtatása

A következő kód honosított eredményeket kap. Implementálására a következő lépésekben kerül sor:
1. Deklarálja a változókat a végpont gazdagép és útvonal szerinti megadásához.
2. A lekérdezési paramétert kell megadni. 
3. Definiálja a kérést létrehozó és az Ocp-Apim-Subscription-Key fejlécet hozzáadó Search függvényt.
4. Megadja az Ocp-Apim-Subscription-Key fejlécet. 
5. Hozza meg a kapcsolatokat, és küldje el a kérést.
6. Megjeleníti a JSON-eredményeket.

Az útmutatóban használt teljes kód a következő:

```python
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/localbusinesses/search'

query = 'restaurant in Bellevue'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

```

## <a name="next-steps"></a>További lépések
- [Helyi üzleti keresés – Java rövid útmutató](local-search-java-quickstart.md)
- [Helyi üzleti keresés C# rövid útmutató](local-quickstart.md)
- [Helyi üzleti keresési csomópont rövid útmutatója](local-search-node-quickstart.md)

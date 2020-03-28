---
title: Rövid útmutató – Lekérdezés küldése az API-nak pythonban – Bing helyi üzleti keresés
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval elkezdheti használni a Bing helyi üzleti keresési API-t a Pythonban.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: c7e7ef7f052fccfea18b246f41109d5fa7528b4b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75379743"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Rövid útmutató: Lekérdezés küldése a Bing helyi üzleti keresési API-nak pythonban

Ezzel a rövid útmutatóval megkezdheti a kérelmek küldését a Bing Helyi üzleti keresési API-nak, amely egy Azure Cognitive Service. Bár ez az egyszerű alkalmazás Pythonban íródott, az API egy RESTful webszolgáltatás, amely kompatibilis bármely programozási nyelvvel, amely képes HTTP-kérelmeket készíteni és json-t elemezni.

Ez a példaalkalmazás helyi válaszadatokat kér `hotel in Bellevue`le az API-ból a keresési lekérdezéshez.

## <a name="prerequisites"></a>Előfeltételek

* [Piton](https://www.python.org/) 2.x vagy 3.x
 
A Bing [API-kkal rendelkező Cognitive Services API-fiókkal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) kell rendelkeznie. Az [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) elegendő ehhez a rövid útmutatóhoz. Használja az ingyenes próbaverzió által biztosított hozzáférési kulcsot.  Lásd még: [Cognitive Services Pricing - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-complete-application"></a>A teljes alkalmazás futtatása

A következő kód honosított eredményeket kap. Implementálására a következő lépésekben kerül sor:
1. Deklarálja a változókat a végpont gazdagép és útvonal szerinti megadásához.
2. Adja meg a lekérdezési paramétert. 
3. Definiálja a kérést létrehozó és az Ocp-Apim-Subscription-Key fejlécet hozzáadó Search függvényt.
4. Megadja az Ocp-Apim-Subscription-Key fejlécet. 
5. Hozza létre a kapcsolatot, és küldje el a kérelmet.
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
- [Helyi üzleti keresés Java rövid útmutató](local-search-java-quickstart.md)
- [Helyi üzleti keresés C# Rövid útmutató](local-quickstart.md)
- [Rövid útmutató a helyi üzleti keresőcsomópontról](local-search-node-quickstart.md)

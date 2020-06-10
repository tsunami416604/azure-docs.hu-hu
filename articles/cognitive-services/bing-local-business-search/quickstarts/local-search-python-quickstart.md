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
ms.date: 05/12/2020
ms.author: aahi
ms.custom: tracking-python
ms.openlocfilehash: fcd3ab3cce74aa0ef021427904077c364de1f493
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84606275"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Gyors útmutató: lekérdezés küldése a Bing helyi üzleti keresési API-nak a Pythonban

Ebből a rövid útmutatóból megtudhatja, hogyan küldhet kéréseket a Bing helyi üzleti keresési API-nak, amely egy Azure kognitív szolgáltatás. Habár ez az egyszerű alkalmazás Pythonban íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a HTTP-kérelmeket és a JSON-elemzést lehetővé tevő programozási nyelvekkel.

Ez a példában szereplő alkalmazás helyi válaszüzeneteket olvas be az API-ból egy keresési lekérdezéshez.

## <a name="prerequisites"></a>Előfeltételek

* [Python](https://www.python.org/) 2. x vagy 3. x.
* Egy [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) , Bing Search API-k. Ebben a rövid útmutatóban az [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) elegendő. Mentse az ingyenes próbaverzió aktiválásához megadott API-kulcsot. További információ: [Cognitive Services díjszabása-BING Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-complete-application"></a>A teljes alkalmazás futtatása

Az alábbi példa honosított eredményeket kap, amelyeket a következő lépésekben kell megvalósítani:
1. Deklarálja a változókat a végpont gazdagép és útvonal szerinti megadásához.
2. A lekérdezési paramétert kell megadni. 
3. Adja meg a kérelmet létrehozó keresési függvényt, és adja meg a `Ocp-Apim-Subscription-Key` fejlécet.
4. Adja meg a `Ocp-Apim-Subscription-Key` fejlécet. 
5. Hozza meg a kapcsolatokat, és küldje el a kérést.
6. Megjeleníti a JSON-eredményeket.

A bemutató teljes kódja a következő:

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

## <a name="next-steps"></a>Következő lépések
- [Helyi üzleti keresés – Java rövid útmutató](local-search-java-quickstart.md)
- [Helyi üzleti keresés C# rövid útmutató](local-quickstart.md)
- [Helyi üzleti keresés Node. js rövid útmutató](local-search-node-quickstart.md)

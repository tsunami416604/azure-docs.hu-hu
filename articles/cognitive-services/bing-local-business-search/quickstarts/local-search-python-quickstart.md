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
ms.custom: devx-track-python
ms.openlocfilehash: 30e66260f0603139bf45f9a0f0b5f19539ae468e
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87843555"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Gyors útmutató: lekérdezés küldése a Bing helyi üzleti keresési API-nak a Pythonban

Ebből a rövid útmutatóból megtudhatja, hogyan küldhet kéréseket a Bing helyi üzleti keresési API-nak, amely egy Azure kognitív szolgáltatás. Habár ez az egyszerű alkalmazás Pythonban íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a HTTP-kérelmeket és a JSON-elemzést lehetővé tevő programozási nyelvekkel.

Ez a példában szereplő alkalmazás helyi válaszüzeneteket olvas be az API-ból egy keresési lekérdezéshez.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* [Python](https://www.python.org/) 2. x vagy 3. x.
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" hozzon létre egy Bing Search erőforrást, "  target="_blank"> és hozzon létre egy Bing Search-erőforrást <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése**elemre.

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
- [Helyi üzleti keresés Node.js rövid útmutató](local-search-node-quickstart.md)

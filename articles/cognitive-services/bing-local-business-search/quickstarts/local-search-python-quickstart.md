---
title: Rövid útmutató – a helyi vállalati keresési Bing-API a Pythonban lekérdezés küldése |} A Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Ez a cikk segítségével Python nyelven a helyi vállalati keresési Bing-API használatának megkezdéséhez.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 87cd18a60dc0c3d8affaaa69d1e9557fc10f6032
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748664"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Gyors útmutató: Egy lekérdezést küld a helyi vállalati keresési Bing-API a Pythonban

Ez a rövid útmutató segítségével megkezdheti a kérések küldését a Bing helyi üzleti Search API, azaz az Azure Cognitive Services-szolgáltatás. Bár ez az egyszerű alkalmazás pythonban írt, az API-t olyan kompatibilis HTTP-kérelem indítására és JSON-elemzés minden programozási nyelvet a webes RESTful szolgáltatás.

Ez a példa az alkalmazás helyi érkezett válasz adatait lekérdezi az API-val, a keresési lekérdezés `hotel in Bellevue`.

## <a name="prerequisites"></a>Előfeltételek

* [Python](https://www.python.org/) 2.x vagy 3.x
 
Rendelkeznie kell egy [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) Bing API-kkal. Az [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) elegendő ehhez a rövid útmutatóhoz. Az ingyenes próbaverzióra által biztosított hozzáférési kulcs használja.

## <a name="run-the-complete-application"></a>A teljes alkalmazás futtatása

A következő kód lekéri a honosított eredményeket. Implementálására a következő lépésekben kerül sor:
1. Deklarálja a változókat a végpont gazdagép és útvonal szerinti megadásához.
2. Adja meg a lekérdezési paraméter. 
3. Adja meg a keresési funkció, amely a kérést hoz létre, és hozzáadja az Ocp-Apim-Subscription-Key fejléc.
4. Állítsa be az Ocp-Apim-Subscription-Key fejléc. 
5. Hozza létre a kapcsolatot, és küldje el a kérelmet.
6. Megjeleníti a JSON-eredményeket.

Az útmutatóban használt teljes kód a következő:

````
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'www.bingapis.com'
path = '/api/v7/localbusinesses/search'

query = 'restaurant in Bellevue'

params = '?q=' + urllib.parse.quote (query) + '&appid=' + subscriptionKey + '&traffictype=Internal_monitor&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

````

## <a name="next-steps"></a>További lépések
- [Helyi vállalati keresési Java rövid útmutató](local-search-java-quickstart.md)
- [Helyi vállalati keresési C# a rövid útmutató](local-quickstart.md)
- [Helyi vállalati keresési Node-Quickstart](local-search-node-quickstart.md)
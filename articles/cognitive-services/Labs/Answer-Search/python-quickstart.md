---
title: Python gyors üzembe helyezés, a Microsoft kognitív szolgáltatásokban projekt válasz keresése |} Microsoft Docs
description: Python példa projekt válasz keresése, Microsoft Azure kognitív szolgáltatások használatának megkezdéséhez.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 9cb5406c616ed8e96d73c00c788a0d20f66dcabd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348571"
---
# <a name="project-answer-search-python-quickstart"></a>Projekt válasz keresési Python gyors üzembe helyezés

A következő Python példa hoz létre, és elküld egy kérést "Gibraltár Rock" kapcsolatos információkat.

## <a name="prerequisites"></a>Előfeltételek

Az ingyenes próbaidőszakra hozzáférési kulcs beszerzése [kognitív szolgáltatások Labs](https://aka.ms/answersearchsubscription)

Ez a példa Python 3.6.4

## <a name="code-scenario"></a>Kód forgatókönyv 

A következő kód egy URL-cím Preview hoz létre.
Az alábbi lépéseket a megvalósított:
1. Deklarálja a változókat, adja meg a végpont által állomás és az elérési utat.
2. Adja meg a lekérdezés URL-címe az előzetes, és adja hozzá a lekérdezési paramétert.  
3. Állítsa be a lekérdezési paraméter.
4. Adja meg a keresési funkció, amely hozza létre a kérelmet, és hozzáadja a *Ocp-Apim-előfizetés-kulcs* fejléc.
5. Állítsa be a *Ocp-Apim-előfizetés-kulcs* fejléc. 
6. A kapcsolathoz, és a kérelem küldése.
7. A JSON-eredmények nyomtatása.

A teljes kód látható ebben a bemutatóban a következőképpen:

````
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'https://api.labs.cognitive.microsoft.com'
path = '/answerSearch/v7.0/search '

query = 'Rock of Gibraltar'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

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
- [C# gyors üzembe helyezés](c-sharp-quickstart.md)
- [Java gyors üzembe helyezés](java-quickstart.md)
- [Csomópont gyors üzembe helyezés](node-quickstart.md)
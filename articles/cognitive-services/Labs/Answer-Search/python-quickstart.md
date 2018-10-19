---
title: 'Rövid útmutató: Válaszkereséses projekt, Python'
titlesuffix: Azure Cognitive Services
description: Egy Python-alapú példa a Válaszkereséses projekt használatának első lépéseihez.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: c33a88b65dcdf5ddddff9f5109afbe0cca7247c4
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48869828"
---
# <a name="quickstart-project-answer-search-with-python"></a>Rövid útmutató a Válaszkereséses projekt a Pythonnal való használatához

Ez a Python-alapú példa egy, a „Gibraltár-sziklával” kapcsolatos információkra vonatkozó kérést hoz létre és küld el.

## <a name="prerequisites"></a>Előfeltételek

Egy hozzáférési kulcs a [Cognitive Services Labs](https://aka.ms/answersearchsubscription) ingyenes próbaverziójához.

Ez a példa a Python 3.6.4-et használja.

## <a name="code-scenario"></a>Kódforgatókönyv 

A következő kód egy URL-előnézetet hoz létre.
Implementálása a következő lépésekben történik:
1. A változók deklarálása a végpont megadásához gazdagép és elérési út szerint.
2. Az előnézetben megtekintendő lekérdezési URL megadása és egy lekérdezési paraméter hozzáadása.  
3. A lekérdezési paraméter megadása.
4. A kérést létrehozó és az *Ocp-Apim-Subscription-Key* fejlécet felvevő Search-függvény definiálása.
5. Az *Ocp-Apim-Subscription-Key* fejléc megadása. 
6. A kapcsolat létrehozása és a kérés elküldése.
7. A JSON-eredmények kinyomtatása.

Az útmutatóban használt teljes kód a következő:

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
- [C# – rövid útmutató](c-sharp-quickstart.md)
- [Java – rövid útmutató](java-quickstart.md)
- [Node – rövid útmutató](node-quickstart.md)
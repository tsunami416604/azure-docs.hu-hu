---
title: 'Rövid útmutató: Az URL-cím előnézete projekt, Python'
titlesuffix: Azure Cognitive Services
description: Az URL-cím előnézete projekt Python nyelvvel való azonnali használatába bevezető szkriptminta.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-url-preview
ms.topic: quickstart
ms.date: 03/29/2018
ms.author: rosh
ms.openlocfilehash: c4af701188c6453bd3c067b838519c32862af13d
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867272"
---
# <a name="quickstart-url-preview-with-python"></a>Rövid útmutató: Az URL-cím előnézete Python nyelven

Az alábbi Python-példa létrehoz egy URL-előnézetet a SwiftKey webhelyhez: https://swiftkey.com/en.

## <a name="prerequisites"></a>Előfeltételek

Hozzáférési kulcs lekérése a [Cognitive Services Labs](https://aka.ms/answersearchsubscription) ingyenes próbaverziójához

Ez a példa a Python 3.6-ot használja.

## <a name="code-scenario"></a>Kódforgatókönyv 

A következő kód egy URL-előnézetet hoz létre.
Implementálására a következő lépésekben kerül sor:
1. Deklarálja a változókat a végpont gazdagép és útvonal szerinti megadásához.
2. Megadja a lekérdezési URL-címet az előnézethez, és hozzáad egy lekérdezési paramétert.  
3. Megadja a lekérdezési paramétert.
4. Definiálja a kérést létrehozó és az *Ocp-Apim-Subscription-Key* fejlécet hozzáadó Search függvényt.
5. Megadja az *Ocp-Apim-Subscription-Key* fejlécet. 
6. Létrehozza a kapcsolatot, és elküldi a kérést.
7. Megjeleníti a JSON-eredményeket.

Az útmutatóban használt teljes kód a következő:

````
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'your-subscription-key'

host = 'api.labs.cognitive.microsoft.com'
path = '/urlpreview/v7.0/search'

query = 'https://SwiftKey.com'

params = '?q=' + urllib.parse.quote (query)

def get_preview ():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_preview ()
print (json.dumps(json.loads(result), indent=4))
````
## <a name="next-steps"></a>További lépések
- [C# – rövid útmutató](csharp.md)
- [Java – rövid útmutató](java-quickstart.md)
- [JavaScript – rövid útmutató](javascript.md)
- [Node URL – rövid útmutató](node-quickstart.md)
---
title: 'Gyors útmutató: Projekt válaszkeresés, Python'
titlesuffix: Azure Cognitive Services
description: Egy Python-alapú példa a Válaszkereséses projekt használatának első lépéseihez.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 8d0ddf1d532c77f23a930c46bb261e03bc5822e7
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218292"
---
# <a name="quickstart-project-answer-search-with-python"></a>Rövid útmutató a Válaszkereséses projekt a Pythonnal való használatához

Ez a Python-alapú példa egy, a „Gibraltár-sziklával” kapcsolatos információkra vonatkozó kérést hoz létre és küld el.

## <a name="prerequisites"></a>Előfeltételek

Hozzáférési kulcs lekérése a [Cognitive Services Labs](https://aka.ms/answersearchsubscription) ingyenes próbaverziójához

Ez a példa a Python 3.6.4-et használja.

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

```
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

```
## <a name="next-steps"></a>További lépések
- [C# – rövid útmutató](c-sharp-quickstart.md)
- [Java – rövid útmutató](java-quickstart.md)
- [Node – rövid útmutató](node-quickstart.md)

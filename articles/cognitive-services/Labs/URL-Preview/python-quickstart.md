---
title: Python gyors üzembe helyezési projekt URL-cím Preview - Microsoft kognitív szolgáltatások |} Microsoft Docs
description: Gyorsan az első lépéseiben a projekt URL-cím Preview a Microsoft Azure kognitív Services minta parancsfájlt.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 78b2d83b02aa9ea32509029c7456e04e420b8572
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348627"
---
# <a name="url-preview-python-quickstart"></a>URL-cím Preview Python gyors üzembe helyezés

Az alábbi Python-példakód létrehozza a SwiftKey webhely URL-cím előnézete: https://swiftkey.com/en.

## <a name="prerequisites"></a>Előfeltételek

Az ingyenes próbaidőszakra hozzáférési kulcs beszerzése [kognitív szolgáltatások Labs](https://aka.ms/answersearchsubscription)

A példa Python 3.6.

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
- [C# gyors üzembe helyezés](csharp.md)
- [Java gyors üzembe helyezés](java-quickstart.md)
- [JavaScript gyors üzembe helyezés](javascript.md)
- [Csomópont URL-cím gyors üzembe helyezés](node-quickstart.md)
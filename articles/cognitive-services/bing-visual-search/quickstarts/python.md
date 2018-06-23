---
title: Python gyors üzembe helyezés, a Bing Visual keresés API |} Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Bemutatja, hogyan lemezkép feltöltése a Bing Visual keresési API-hoz, és vissza a képet kaphat.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: a520466825eb429e45e0500b52bd7af502c0a38c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349334"
---
# <a name="your-first-bing-visual-search-query-in-python"></a>A Python első Bing Visual keresési lekérdezés

Bing Visual Search API, amely megadja a képfájl információt ad vissza. A kép URL-címe, lexikális elem, vagy egy kép feltöltésével egy insights segítségével megadhatja a lemezképet. További információ a kapcsolókról: [Bing Visual Search API újdonságai?](../overview.md) Ez a cikk bemutatja, hogy egy kép feltöltésével. Egy kép feltöltésével olyan mobil forgatókönyveknél, ahol készíthet egy képet arról a jól ismert jellegzetes, és arra vonatkozó információval segítségnyújtáshoz hasznos lehet. Például a feltárása a jellegzetes kapcsolatos trivia tartalmazhatnak. 

Ha feltölti egy helyi lemezképet, a következő jeleníti meg az űrlap adatait meg kell adni a FELADÁS egy vagy több törzsében. Az űrlap adatait tartalmaznia kell a tartalom-szabályozó fejlécben. A `name` paraméter "kép" értékre kell állítani, és a `filename` paraméter bármilyen karakterlánc lehet beállítani. A képernyő tartalmát a bináris kép. A maximális kép lehet, hogy feltölti mérete 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

A cikk tartalmaz egy egyszerű konzolalkalmazást, amely a Bing Visual keresési API-kérést küld, és a JSON-keresési eredményeit jeleníti meg. Az alkalmazás Python nyelven van megírva, amíg az API-t olyan kompatibilis bármely programozási nyelv, amely HTTP-kérelmeket, és elemezni a JSON a RESTful webes szolgáltatás. 

## <a name="prerequisites"></a>Előfeltételek

Szüksége [Python 3](https://www.python.org/) futtatásához ezt a kódot.

A gyors üzembe helyezés, a segítségével egy [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) előfizetés vagy egy fizetős billentyűt.

## <a name="running-the-walkthrough"></a>A forgatókönyv futtatása

Az alkalmazás futtatásához kövesse az alábbi lépéseket:

1. Hozzon létre egy új Python-projektet a kedvenc IDE vagy szerkesztő.
2. Hozzon létre egy visualsearch.py nevű fájlt, és adja hozzá a kódot, a gyors üzembe helyezés látható.
3. Cserélje le a `SUBSCRIPTION_KEY` az Előfizetés kulcs értékének.
3. Cserélje le a `imagePath` a kép töltse fel az elérési úttal rendelkező értékhez.
4. Futtassa a programot.



A következő bemutatja, hogyan elküldeni az üzenetet, több részből álló űrlapadat használatát a Python.

```python
"""Bing Visual Search upload image example"""

# Download and install Python at https://www.python.org/
# Run the following in a command console window
# pip3 install requests

import requests, json


BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

imagePath = '<pathtoyourimagetouploadgoeshere>'

file = {'image' : ('myfile', open(imagePath, 'rb'))}

def main():
    
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())

    except Exception as ex:
        raise ex


def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))



# Main execution
if __name__ == '__main__':
    main()
```


## <a name="next-steps"></a>További lépések

[Mélyebb betekintés insights token használatával kép](../use-insights-token.md)  
[Bing Visual keresési alkalmazás oktatóanyag](../tutorial-bing-visual-search-single-page-app.md)  
[Bing Visual keresési áttekintése](../overview.md)  
[Próbálja ki](https://aka.ms/bingvisualsearchtryforfree)  
[Egy ingyenes próba hozzáférési kulcs beszerzése](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Bing Visual keresési API-referencia](https://aka.ms/bingvisualsearchreferencedoc)

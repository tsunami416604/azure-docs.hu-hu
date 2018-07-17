---
title: A Bing Visual Search API a Python rövid |} A Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Bemutatja, hogyan tölthet fel képeket a Bing Visual Search API, majd a kép információival.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 96bd94e37c75d10726245fbcea7044d4ae2ed07e
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070375"
---
# <a name="your-first-bing-visual-search-query-in-python"></a>Az első a Bing vizuális keresési lekérdezés a Pythonban

A Bing Visual Search API az Ön által megadott kép adatait adja vissza. Megadhatja a kép URL-címét a képet, egy jogkivonat, vagy egy kép feltöltésével insights használatával. Ezek a beállítások kapcsolatos információkért lásd: [Mi az a Bing Visual Search API?](../overview.md) Ez a cikk bemutatja a kép feltöltése. Kép feltöltése mobil forgatókönyveknél, ahol, egy jól ismert tereptárgyak egy képet, majd az információk hasznosak lehetnek. Az insights például magukban foglalhatják a tereptárgyak kapcsolatos trivia. 

Ha a helyi rendszerképet tölt fel, az alábbiakban látható az űrlapadatok szerepelnie kell a bejegyzés törzse. Az űrlap adatait tartalmaznia kell a tartalom-szabályozó fejléc. A `name` paraméter "image" értékre kell állítani, és a `filename` paraméter bármilyen karakterlánc lehet beállítani. A képernyő tartalmát a rendszerkép a bináris. A maximális képméret tölthet fel érték 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Ez a cikk tartalmaz egy egyszerű konzolalkalmazást, amely a Bing Visual Search API-kérést küld, és a JSON keresési eredményeit jeleníti meg. Ez az alkalmazás pythonban írt, míg a API-ját kompatibilis minden programozási nyelvet, amely JSON elemzése és a HTTP-kéréseket a webes RESTful szolgáltatás. 

## <a name="prerequisites"></a>Előfeltételek

Szükséges [Python 3](https://www.python.org/) Ez a kód futtatásához.

Használhatja az ebben a rövid útmutatóban egy [az ingyenes próbaidőszak](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) előfizetési kulcs vagy egy fizetős kulcsot.

## <a name="running-the-walkthrough"></a>A forgatókönyv futtatása

Az alkalmazás futtatásához kövesse az alábbi lépéseket:

1. Hozzon létre egy új Python-projektet a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével a.
2. Hozzon létre egy fájlt visualsearch.py, és adja hozzá az ebben a rövid útmutatóban látható kódot.
3. Cserélje le a `SUBSCRIPTION_KEY` az előfizetési kulcs-érték.
3. Cserélje le a `imagePath` a Rendszerkép feltöltése az elérési úttal rendelkező értékhez.
4. Futtassa a programot.



Az alábbiakban látható a többrészes űrlap adatokat használó Python üzenet küldése.

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

[Lemezkép insights token használatával kapcsolatos elemzések lekérése](../use-insights-token.md)  
[A Bing Visual Search Rendszerkép feltöltése az oktatóanyag](../tutorial-visual-search-image-upload.md)
[Bing Visual Search egyoldalas alkalmazás oktatóanyaga](../tutorial-bing-visual-search-single-page-app.md)  
[Bing vizuális keresés áttekintése](../overview.md)  
[Próbálja ki](https://aka.ms/bingvisualsearchtryforfree)  
[Ingyenes próba hozzáférési kulcs lekérése](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[A Bing Visual Search API-referencia](https://aka.ms/bingvisualsearchreferencedoc)

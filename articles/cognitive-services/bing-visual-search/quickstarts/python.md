---
title: 'Gyors útmutató: Lemezkép-elemzések, a Bing Visual Search REST API és a Python használatával'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan kaphat elemzési információkat, és töltsön fel egy képet, a Bing Visual Search API.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 873da64592c2c2e925d8731d4b1154db95bed31d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863227"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-python"></a>Gyors útmutató: Az első a Bing vizuális keresési lekérdezés a Pythonban

Ez a rövid útmutató segítségével a Bing Visual Search API az első hívását, és megtekintheti a keresési eredmények között. Ez az egyszerű JavaScript alkalmazás Rendszerképadatok az API-t, és a visszaadott kapcsolatos információkat jeleníti meg. Az alkalmazás JavaScript nyelven van megírva, míg a API-ját egy REST-alapú webszolgáltatás szinte bármelyik programozási nyelvével kompatibilis.

Ha feltöltenek egy helyi lemezképet, a POST űrlapadatok tartalmaznia kell a tartalom-szabályozó fejléc. A `name` paraméter értéke „image” legyen, a `filename` paraméter értéke viszont bármilyen sztring lehet. Az űrlap tartalmát a kép bináris adatai adják. A legnagyobb feltölthető képméret 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Előfeltételek

* [Python 3.x](https://www.python.org/)


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Az alkalmazás inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével, és adja hozzá a következő importálási utasítást.

    ```python
    import requests, json
    ```

2. Az előfizetési kulcs, végpont és a lemezkép elérési útját a videófájl változók létrehozása.

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```

3. Hozzon létre egy szótárobjektum, amely tárolja a kérelmek fejléc-információkat. A karakterlánc kötést létrehozni az előfizetési kulcs `Ocp-Apim-Subscription-Key`lent látható módon.

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Hozzon létre egy másik szótár tartalmazza a rendszerképet, amely megnyílik, és a kérés küldésekor feltöltött. 

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>A JSON-válasz elemzése

1. Hozzon létre egy meghívott metódus `print_json()` az API válaszként, és nyomtassa ki a JSON-fájllal.

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>A kérés küldése

1. Használat `requests.post()` , a Bing Visual Search API-kérés küldése. A karakterlánc a végpont, fejléc és a fájl adatait tartalmazza. Nyomtatási `response.json()` az `print_json()`

    ```python
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())
    
    except Exception as ex:
        raise ex
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni keresés webes alkalmazás készítése](../tutorial-bing-visual-search-single-page-app.md)

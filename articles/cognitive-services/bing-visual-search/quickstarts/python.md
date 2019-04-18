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
ms.date: 4/02/2019
ms.author: scottwhi
ms.openlocfilehash: 7ec37b4c3bdeb924b3e35dbcb5d07a478611f631
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59047126"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Gyors útmutató: Lemezkép-elemzések, a Bing Visual Search REST API és a Python használatával

Ez a rövid útmutató segítségével győződjön meg arról, az első, a Bing Visual Search API hívása, és tekintse meg az eredményeket. A Python-alkalmazás feltölt egy képet az API-hoz, és visszaadja az információkat jeleníti meg. Bár ez az alkalmazás pythonban írt, az API-t az szinte bármelyik programozási nyelvével kompatibilis webes RESTful szolgáltatás.

Tartalmaznia kell a helyi rendszerképet tölt fel, amikor az űrlap adatait a `Content-Disposition` fejléc. Be kell állítani a `name` paraméter "image", és állíthatja a `filename` bármilyen karakterlánc paramétert. Az űrlap a rendszerkép a bináris adatokat is tartalmazza. A maximális képméret feltöltheti az 1 MB.

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

1. Hozzon létre egy új Python-fájlt a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével, és adja hozzá a következő `import` utasítást:

    ```python
    import requests, json
    ```

2. Hozzon létre változókat az előfizetési kulcs, végpont és a feltöltendő lemezkép elérési útja:

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```

3. Hozzon létre egy szótárobjektum, a kérelem fejlécében adatainak tárolására. A karakterlánc kötést létrehozni az előfizetési kulcs `Ocp-Apim-Subscription-Key`lent látható módon:

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Hozzon létre egy másik szótár tartalmazza a rendszerképet, amely megnyitva, és a kérés küldésekor feltöltött:

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>A JSON-válasz elemzése

1. Hozzon létre egy meghívott metódus `print_json()` az API válaszként, és nyomtassa ki a JSON:

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>A kérés küldése

1. Használat `requests.post()` , a Bing Visual Search API-kérés küldése. A karakterlánc a végpont, fejléc és a fájl adatait tartalmazza. Nyomtatási `response.json()` a `print_json()`:

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
> [Hozzon létre egy vizuális keresés egyoldalas webalkalmazást](../tutorial-bing-visual-search-single-page-app.md)

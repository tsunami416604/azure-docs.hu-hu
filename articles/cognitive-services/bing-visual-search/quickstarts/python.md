---
title: 'Rövid útmutató: Képelemzések beszereznie a REST API és a Python használatával – Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogy miként tölthet fel képet a Bing Visual Search API-ba, és hogyan kaphat betekintést.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: b56f6743b642904349797ac5b6167194f7916b45
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446591"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Rövid útmutató: Képelemzési adatok beszereznie a Bing Visual Search REST API-val és a Pythonnal

Ezzel a rövid útmutatóval elsőalkalommal hívhatja meg a Bing Visual Search API-t, és megtekintheti az eredményeket. Ez a Python-alkalmazás feltölt egy lemezképet az API-ba, és megjeleníti a visszaadott információkat. Bár ez az alkalmazás python nyelven íródott, az API egy RESTful webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

## <a name="prerequisites"></a>Előfeltételek

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Az alkalmazás inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc `import` IDE-ben vagy szerkesztőjében, és adja hozzá a következő utasítást:

    ```python
    import requests, json
    ```

2. Hozzon létre változókat az előfizetési kulcshoz, a végponthoz és a feltöltendő kép elérési úthoz. `BASE_URI`lehet az alábbi globális végpont, vagy az erőforráshoz az Azure Portalon megjelenő [egyéni altartomány-végpont:](../../../cognitive-services/cognitive-services-custom-subdomains.md)

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```
    
    Helyi kép feltöltésekénél az űrlapadatoknak tartalmazniuk kell a `Content-Disposition` fejlécet. Be kell `name` állítania a paraméter "kép", `filename` és beállíthatja a paramétert bármilyen karakterláncot. Az űrlap tartalma tartalmazza a kép bináris adatait. A feltölthető maximális képméret 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

3. Hozzon létre egy szótárobjektumot a kérelem fejlécadatainak tárolására. Kösse az előfizetési `Ocp-Apim-Subscription-Key`kulcsot a karakterlánchoz , az alábbiak szerint:

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Hozzon létre egy másik szótárt, amely tartalmazza a képet, amely a kérelem elküldésekor nyílik meg és töltődik fel:

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Elemezd a JSON-választ

1. Hozzon létre `print_json()` egy metódust, amelyet az API-válaszhoz hívnak meg, és nyomtassa ki a JSON-t:

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>A kérelem elküldése

1. Kérésküldése `requests.post()` a Bing Visual Search API-nak. Adja meg a végpont, a fejléc és a fájladatainak karakterláncát. Nyomtatás `response.json()` `print_json()`a következővel:

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
> [Vizuális keresés létrehozása egyoldalas webalkalmazáslétrehozása](../tutorial-bing-visual-search-single-page-app.md)

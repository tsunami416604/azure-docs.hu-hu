---
title: 'Gyors útmutató: Keressen videókat a Bing Video Search SDK a Pythonhoz készült használatával'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével a Bing Video Search SDK a Pythonhoz készült használatával videókeresési kérelmek küldése
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: rosh
ms.openlocfilehash: ead69b0165831fef04e68b4c2eb7ea43115ca4ea
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867800"
---
#  <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-python"></a>Gyors útmutató: Hajtsa végre a videókeresési Bing Video Search SDK-val a Pythonhoz

Ez a rövid útmutató segítségével kezdeni a keresést hírkeresés a Bing Video Search SDK a Pythonhoz készült. Míg a Bing Video Search REST API-val kompatibilis szinte bármelyik programozási nyelvével, az SDK biztosít egy egyszerű módja annak, hogy a szolgáltatás integrálása az alkalmazásokba. Ez a minta forráskódja találhatók [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py). További jegyzetek és szolgáltatásokat tartalmazza.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Python](https://www.python.org/) 2.x vagy 3.x
- A Bing Video Search SDK pythonhoz készült

Javasoljuk, hogy használja-e egy python [virtuális környezet](https://docs.python.org/3/tutorial/venv.html). Telepítése és a egy virtuális környezethez, inicializálja a [venv modul](https://pypi.python.org/pypi/virtualenv). Virtualenv telepítse a Python 2.7:

```console
python -m venv mytestenv
```
   
A Bing videók keresése az SDK telepítése:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-videosearch
```

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével, és adja hozzá a következő importálási utasításokat. 

    ```python
    from azure.cognitiveservices.search.videosearch import VideoSearchAPI
    from azure.cognitiveservices.search.videosearch.models import VideoPricing, VideoLength, VideoResolution, VideoInsightModule
    from msrest.authentication import CognitiveServicesCredentials
    ```
2. Hozzon létre egy változót az előfizetési kulcs. 
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    ```

## <a name="create-the-search-client"></a>A search-ügyfél létrehozása

Hozza létre a `CognitiveServicesCredentials` egy példányát, és példányosítsa az ügyfelet:

    ```python
    client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-get-a-response"></a>Egy keresési kérelmet küld, és a egy választ

1. Használat `client.videos.search()` a keresési lekérdezésnek egy kérelmet küld a Bing Video Search API, és választ kaphat.

    ```python
    video_result = client.videos.search(query="SwiftKey")
    ```

2. A válasz tartalmazza a keresési eredmények, ha az elsőt lekérése és nyomtatás Azonosítóját, nevét és URL-címe.

    ```python
    if video_result.value:
        first_video_result = video_result.value[0]
        print("Video result count: {}".format(len(video_result.value)))
        print("First video id: {}".format(first_video_result.video_id))
        print("First video name: {}".format(first_video_result.name))
        print("First video url: {}".format(first_video_result.content_url))
    else:
        print("Didn't see any video result data..")
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hozzon létre egy egyoldalas webalkalmazást](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Lásd még 

* [Mi az a Bing Videókeresési API?](../overview.md)
* [Cognitive services .NET SDK-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
---
title: 'Gyors útmutató: videók keresése a Pythonhoz készült SDK használatával – Bing Video Search'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval videó-keresési kéréseket küldhet a Pythonhoz készült Bing Video Search SDK-val
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: c1afea4e6cacc1f6e9ac84ca20e638836540396e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448386"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-python"></a>Gyors útmutató: videós keresés végrehajtása a Pythonhoz készült Bing Video Search SDK-val

Ezzel a rövid útmutatóval megkezdheti a hírek keresését a Pythonhoz készült Bing Video Search SDK-val. Habár a Bing Video Search REST API kompatibilis a legtöbb programozási nyelvvel, az SDK egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py) található további megjegyzésekkel és szolgáltatásokkal.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Python](https://www.python.org/) 2. x vagy 3. x
- A Pythonhoz készült Bing Video Search SDK

Javasoljuk, hogy használjon Python [virtuális környezetet](https://docs.python.org/3/tutorial/venv.html). A [venv modul](https://pypi.python.org/pypi/virtualenv)használatával telepítheti és inicializálhatja a virtuális környezeteket. Telepítse a virtualenv for Python 2,7-et a következővel:

```console
python -m venv mytestenv
```

Telepítse a Bing Video Search SDK-t a használatával:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-videosearch
```

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc IDE vagy szerkesztőben, és adja hozzá a következő importálási utasításokat. 

    ```python
    from azure.cognitiveservices.search.videosearch import VideoSearchAPI
    from azure.cognitiveservices.search.videosearch.models import VideoPricing, VideoLength, VideoResolution, VideoInsightModule
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Hozzon létre egy változót az előfizetési kulcshoz. 

    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    ```

## <a name="create-the-search-client"></a>A keresési ügyfél létrehozása

Hozza létre a `CognitiveServicesCredentials` egy példányát, és példányosítsa az ügyfelet:

```python
client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))
```

## <a name="send-a-search-request-and-get-a-response"></a>Keresési kérelem küldése és Válasz kérése

1. A `client.videos.search()` a keresési lekérdezéssel küldhet egy kérést a Bing Video Search APInak, és választ kaphat.

    ```python
    video_result = client.videos.search(query="SwiftKey")
    ```

2. Ha a válasz keresési eredményeket tartalmaz, szerezze be az elsőt, és nyomtassa ki az AZONOSÍTÓját, nevét és URL-címét.

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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyoldalas Webalkalmazás létrehozása](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Lásd még: 

- [Mi a Bing Video Search API?](../overview.md)
- [Cognitive services .NET SDK-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)

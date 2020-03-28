---
title: Bing Video Search Python-ügyféltár – rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.openlocfilehash: 7a9fab8ba8bb9d21c9284cbf14bc67226d2ef9d3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80289741"
---
Ezzel a rövid útmutatóval megkezdheti a hírek keresését a Bing Video Search ügyfélkódtárpythonhoz. Bár a Bing Video Search a legtöbb programozási nyelvvel kompatibilis REST API-val rendelkezik, az ügyfélkódtár egyszerű módot kínál a szolgáltatás alkalmazásokba való integrálására. A minta forráskódja a [GitHubon](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py) található további jegyzetekkel és funkciókkal.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Piton](https://www.python.org/) 2.x vagy 3.x
- A Bing Video Search ügyfélkönyvtár pythonhoz

Javasoljuk, hogy használjon python [virtuális környezetet.](https://docs.python.org/3/tutorial/venv.html) Virtuális környezetet telepíthet és inicializálhat a [venv modullal.](https://pypi.python.org/pypi/virtualenv) Telepítse a Virtualenv python 2.7-es telepítését a következőkkel:

```console
python -m venv mytestenv
```

Telepítse a Bing Video Search ügyfélkönyvtárat a következővel:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-videosearch
```

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc IDE-ben vagy szerkesztőjében, és adja hozzá a következő importálási kimutatásokat. 

    ```python
    from azure.cognitiveservices.search.videosearch import VideoSearchClient
    from azure.cognitiveservices.search.videosearch.models import VideoPricing, VideoLength, VideoResolution, VideoInsightModule
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Hozzon létre egy változót az előfizetési kulcshoz. 

    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    ```

## <a name="create-the-search-client"></a>A keresési ügyfél létrehozása

Hozza létre a `CognitiveServicesCredentials` egy példányát, és példányosítsa az ügyfelet:

```python
client = VideoSearchAPI(endpoint, CognitiveServicesCredentials(subscription_key))
```

## <a name="send-a-search-request-and-get-a-response"></a>Keresési kérelem küldése és válasz kérése

1. A `client.videos.search()` keresési lekérdezéssel kérést küldhet a Bing Video Search API-nak, és választ kaphat.

    ```python
    video_result = client.videos.search(query="SwiftKey")
    ```

2. Ha a válasz keresési eredményeket tartalmaz, az elsőt kapja meg, és nyomtassa ki az azonosítóját, nevét és url-címét.

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
> [Egyoldalas webalkalmazás létrehozása](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Lásd még 

- [Mi az a Bing Video Search API?](../../overview.md)
- [Kognitív szolgáltatások .NET SDK-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)

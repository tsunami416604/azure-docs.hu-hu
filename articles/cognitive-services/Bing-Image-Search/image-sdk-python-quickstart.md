---
title: Kép keresési SDK Python gyors üzembe helyezés |} Microsoft Docs
description: A telepítő lemezkép keresési SDK konzol alkalmazáshoz.
titleSuffix: Azure Image Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: e30852439ad8ec2d5ddc667b75167e8b5d35be33
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349450"
---
# <a name="image-search-sdk-python-quickstart"></a>Kép keresési SDK Python gyors üzembe helyezés

A Bing kép keresési SDK a REST API webkiszolgáló lekérdezések és elemzési eredmények funkcióit tartalmazza. 

A [forráskód Python Bing kép keresési SDK minták](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image_search_samples.py) érhető el a Git központ.

## <a name="application-dependencies"></a>Alkalmazásfüggőségek
Ha az még nincs, telepítse a Python. Az SDK nem kompatibilis a Python 2.7, 3.3-as, illetve 3.4, 3.5-ös és 3.6.

A Python fejlesztési általános javaslat, hogy használja a [virtuális környezet](https://docs.python.org/3/tutorial/venv.html). Telepítse, és a virtuális környezet inicializálása a [venv modul](https://pypi.python.org/pypi/virtualenv). Python 2.7 virtualenv kell telepítenie.
```
python -m venv mytestenv
```
Bing kép keresési SDK-függőség telepítése:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-imagesearch
```
## <a name="image-search-client"></a>Kép keresési ügyfél
Első egy [kognitív szolgáltatások hozzáférési kulcs](https://azure.microsoft.com/try/cognitive-services/) alatt *keresési*. Adja hozzá a importálásokat:
```
from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
from azure.cognitiveservices.search.imagesearch.models import ImageType, ImageAspect, ImageInsightModule
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Hozzon létre egy példányát a `CognitiveServicesCredentials`, és az ügyfél példányának:
```
client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Keresési lekérdezés (Yosemite) lemezképek animált GIF és széles aspektus szűri. Ellenőrizze az eredmények számát, és nyomtassa ki insightsToken Miniatűr URL-cím és webes URL-címet az első találatra.
```
image_results = client.images.search(
        query="Yosemite",
        image_type=ImageType.animated_gif, # Could be the str "AnimatedGif"
        aspect=ImageAspect.wide # Could be the str "Wide"
    )
    print("\r\nSearch images for \"Yosemite\" results that are animated gifs and wide aspect")

    if image_results.value:
        first_image_result = image_results.value[0]
        print("Image result count: {}".format(len(image_results.value)))
        print("First image insights token: {}".format(first_image_result.image_insights_token))
        print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
        print("First image web search url: {}".format(first_image_result.web_search_url))
    else:
        print("Couldn't find image results!")

```
Animált GIF és széles aspektus szűri (Yosemite) lemezképek keresni.  Ellenőrizze az eredmények száma.  Kinyomtathatja a `insightsToken`, `thumbnail url` és `web url` az első találatra.
```
image_results = client.images.search(
    query="Yosemite",
    image_type=ImageType.animated_gif, # Could be the str "AnimatedGif"
    aspect=ImageAspect.wide # Could be the str "Wide"
)
print("\r\nSearch images for \"Yosemite\" results that are animated gifs and wide aspect")

if image_results.value:
    first_image_result = image_results.value[0]
    print("Image result count: {}".format(len(image_results.value)))
    print("First image insights token: {}".format(first_image_result.image_insights_token))
    print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
    print("First image web search url: {}".format(first_image_result.web_search_url))
else:
    print("Couldn't find image results!")

```

Trendekkel lekéréséhez:
```
trending_result = client.images.trending()
print("\r\nSearch trending images")

# Categorires
if trending_result.categories:
    first_category = trending_result.categories[0]
    print("Category count: {}".format(len(trending_result.categories)))
    print("First category title: {}".format(first_category.title))
    if first_category.tiles:
        first_tile = first_category.tiles[0]
        print("Subcategory tile count: {}".format(len(first_category.tiles)))
        print("First tile text: {}".format(first_tile.query.text))
        print("First tile url: {}".format(first_tile.query.web_search_url))
    else:
        print("Couldn't find subcategory tiles!")
    else:
        print("Couldn't find categories!")

```

## <a name="next-steps"></a>További lépések

[Kognitív szolgáltatások Python SDK-minták](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)



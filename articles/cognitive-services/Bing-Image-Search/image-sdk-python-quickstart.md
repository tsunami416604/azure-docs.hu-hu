---
title: 'Gyors útmutató: A kérés és a Python SDK-val rendszerképek szűrése'
description: Ebben a rövid útmutatóban a kérelem, és szűrheti a képeket, a Bing Image Search-be a Python által visszaadott.
titleSuffix: Azure Image Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: 4729f103bb9b50d4ff039907db8eb677f3dc290a
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2018
ms.locfileid: "41988617"
---
 # <a name="quickstart-request-and-filter-images-using-the-sdk-and-python"></a>Gyors útmutató: A kérés és az SDK-t és a Python használatával rendszerképek szűrése

Kép a Bing Search SDK tartalmazza a REST API, webes lekérdezések és az elemzési eredmények funkcióit. 

A [forráskódját Python Bing kép Search SDK-minták](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image_search_samples.py) Github érhető el.

## <a name="application-dependencies"></a>Alkalmazásfüggőségek
Ha ez már nincs, telepítse a Pythont. Az SDK-t, a Python 2.7-es, 3.3-as, 3.4-es, 3.5-ös és 3.6-os verziója kompatibilis.

A Python fejlesztési általános javaslat, hogy használja a [virtuális környezet](https://docs.python.org/3/tutorial/venv.html). Telepítse, és a virtuális környezet inicializálása a [venv modul](https://pypi.python.org/pypi/virtualenv). Python 2.7-es virtualenv gépre kell telepítenie.
```
python -m venv mytestenv
```
A Bing kép Search SDK-függőség telepítése:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-imagesearch
```
## <a name="image-search-client"></a>Kép keresési ügyfél
Get- [Cognitive Services hozzáférési kulcs](https://azure.microsoft.com/try/cognitive-services/) alatt *keresési*. Adja hozzá a importálásokat:
```
from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
from azure.cognitiveservices.search.imagesearch.models import ImageType, ImageAspect, ImageInsightModule
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Hozzon létre egy példányt a `CognitiveServicesCredentials`, és hozza létre az ügyfél:
```
client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Keresési lekérdezés (yosemite) támogatása,-rendszerképek animált GIF és számos szempont szűrve. Ellenőrizze az eredmények száma, és nyomtassa ki az insightsToken Miniatűr URL-cím és webes URL-címe első eredményt.
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
Animált GIF és számos szempont a szűrt (Yosemite), a lemezképek keresése.  Ellenőrizze az eredmények száma.  Nyomtassa ki `insightsToken`, `thumbnail url` és `web url` az első eredmény.
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

Felkapott eredményeinek beolvasása:
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

[Cognitive Services Python SDK-minták](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)



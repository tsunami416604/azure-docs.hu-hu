---
title: Méretezze át és vágja körül, miniatűr képeket – a Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Méretezze át, és vágja körül, a miniatűr képekhez, a Bing Image Search API érkező válaszokat tartalmazza.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: F4FFAE91-A003-4F7C-8E60-83A142485E28
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: c53b84f31100ab72458ab6fb79b2009450eda15c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789946"
---
# <a name="resize-and-crop-thumbnail-images"></a>Méretezze át és vágja körül, a miniatűrök

Esetén a keresési lekérdezés feldolgozása, a Bing hoz létre az összes rendszerkép miniatűr adatait a [válasz](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images#bing-image-search-response-format). Ez az információ segítségével megjelenítheti az összes vagy egy részét a visszaadott miniatűrök. Egy részhalmazát jeleníti meg, ha megadása során a fennmaradó képek megtekintését.


<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Ha a felhasználó a miniatűrre kattint, a [contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-contenturl) használatával megjelenítheti neki a teljes méretű képet. Ügyeljen arra, hogy megjelenítse a kép forrását.

Ha a `shoppingSourcesCount` vagy a `recipeSourcesCount` nullánál nagyobb, adjon hozzá egy jelvényt a miniatűrhöz, például egy bevásárlókosarat, ezzel jelezve, hogy a képen látható tárgy megvásárolható, vagy tartozik hozzá egy recept.

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

Ha további információkat szeretne lekérni a képről, például azokat a weboldalakat, amelyek tartalmazzák, vagy azokat a személyeket, akik felismerhetők rajta, használja az [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken) lehetőséget. További részletekért tekintse át a [képek elemzéseivel](image-insights.md) foglalkozó cikket.

## <a name="resizing-and-cropping-thumbnails"></a>Átméretezéssel és miniatűrök vágása

Méretezze át is, és bontsa ki a miniatűrök, például amikor fog megjelenni a felhasználó a kurzor fölé.
> [!NOTE]
> Ügyeljen arra, hogy kibontáskor megjelenítse a kép forrását. Például beolvashatja a gazdanevet a [hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-hostpagedisplayurl) URL-címből, és megjelenítheti a kép alatt.

[!INCLUDE [cognitive-services-bing-resize-crop-thumbnails](../../../includes/cognitive-services-bing-resize-crop-thumbnails.md)]

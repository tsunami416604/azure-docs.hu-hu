---
title: Méretezze át és vágja körül, a Bing miniatűrök |} A Microsoft Docs
description: Bemutatja, hogyan méretezze és vágja körül, a Bing-válasz tartalmazza a miniatűrök.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: F4FFAE91-A003-4F7C-8E60-83A142485E28
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 98c4caa50ca5e861f4276e26983ef501d17bd349
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2018
ms.locfileid: "41988796"
---
# <a name="resizing-and-cropping-thumbnail-images"></a>Átméretezéssel és vágása miniatűr képekhez

Esetén a keresési lekérdezés feldolgozása, a Bing hoz létre az összes rendszerkép miniatűr adatait a [válasz](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images#bing-image-search-response-format). Ez az információ segítségével megjelenítheti az összes vagy egy részét a visszaadott miniatűrök. Ha egy részhalmazát megjelenítéséhez lehetőséget nyújtanak olyan a fennmaradó képek megtekintését. 


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
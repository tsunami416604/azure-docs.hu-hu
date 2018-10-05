---
title: A Bing News Search SDK
titleSuffix: Azure Cognitive Services
description: A Bing News Search SDK össze, amelyek a webes alkalmazások számára.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 1/24/2018
ms.author: v-gedod
ms.openlocfilehash: b1d9eaa35416adfa11647f2116171256f82fe095
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801233"
---
# <a name="bing-search-sdk"></a>A Bing Search SDK
A Bing News Search API minták közé tartoznak a forgatókönyvek, amelyek:
1. Hírek lekérdezése a keresési feltételek `market` és `count` paraméterek, ellenőrizze az eredmények száma, és nyomtassa ki az `totalEstimatedMatches`, név, URL-cím, leírás, közzétételi idő és az első hírek eredmény szolgáltató neve.
2. Legfrissebb hírek lekérdezése a keresési feltételek `freshness` és `sortBy` paraméterek, ellenőrizze az eredmények száma, és nyomtassa ki az `totalEstimatedMatches`, URL-CÍMÉT, leírását, közzétételi idő és az első hírek eredmény szolgáltató neve.
3. Kategória hírek lekérdezése `movie` és `TV entertainment` a biztonságos keresési eredmények számát ellenőrizze, és nyomtassa ki a kategóriát, nevét, URL-CÍMÉT, leírását, közzétételi idő és az első hírek eredmény szolgáltató neve.
4. Felkapott hírtémakörök a Bing, ellenőrizze az eredmények száma, és nyomtassa ki a nevét, a lekérdezés szöveges lekérdezési `webSearchUrl`, `newsSearchUrl` és kép URL-címét az első hírek eredményt.

A Bing Search SDK-k győződjön webes keresőt könnyen elérhető, az alábbi programozási nyelveken:
* Ismerkedés a [.NET-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
    * [NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0)
    * Lásd még: [.NET-kódtárakra](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingNewsSearch) definíciókat és függőségeit.
* Ismerkedés a [Node.js-minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * Lásd még: [Node.js kódtárak](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/newsSearch) definíciókat és függőségeit.
* Ismerkedés a [Java-példák](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * Lásd még: [Java-kódtárak](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch) definíciókat és függőségeit.
* Ismerkedés a [példák Python programnyelvhez](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * Lásd még: [Python-kódtárakat](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-newssearch) definíciókat és függőségeit.

SDK-minták az egyes nyelvekhez Előfeltételek és a minták telepítése/futó fontos fájljai tartalmazzák.
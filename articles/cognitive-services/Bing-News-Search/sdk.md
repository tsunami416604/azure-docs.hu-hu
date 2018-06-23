---
title: Bing keresési SDK |} Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Bing keresési SDK olyan alkalmazásnál, amely a webes keresés.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.assetid: ''
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 1/24/2018
ms.author: v-gedod
ms.openlocfilehash: 4a40ea665e153536d2322706b455598902ce41eb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349463"
---
# <a name="bing-search-sdk"></a>Bing keresési SDK
A Bing hírek keresési API-mintákat a forgatókönyvek közé tartozik, amely:
1. Hírek lekérdezni a keresési feltételek `market` és `count` paraméterek, ellenőrizze az eredmények számát, és nyomtassa ki `totalEstimatedMatches`, név, URL-címe, leírása, közzétett idő és az első hírek eredmény szolgáltató neve.
2. A keresési feltételek legfrissebb hírek lekérdezése `freshness` és `sortBy` paraméterek, ellenőrizze az eredmények számát, és nyomtassa ki `totalEstimatedMatches`, URL-CÍMÉT, leírását, közzétett idő és az első hírek eredmény szolgáltató nevét.
3. A kategória hírek lekérdezése `movie` és `TV entertainment` biztonságos keresési eredmények számát ellenőrizze, és nyomtassa ki a kategóriát, nevét, URL-CÍMÉT, leírását, közzétett idő és az első hírek eredmény szolgáltató neve.
4. Lekérdezés, a Bing hírek trendekkel témakörei ellenőrizze eredmények számát, és nyomtassa ki a nevét, a lekérdezés szöveges `webSearchUrl`, `newsSearchUrl` és lemezképet az első hírek eredmény URL-CÍMÉT.

Bing keresési SDK-k ellenőrizze webes keresési funkcióját könnyen hozzáférhető a következő programozási nyelven:
* Ismerkedés a [.NET minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
    * [NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0)
    * Lásd még: [.NET-kódtárakra](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingNewsSearch) definíciókat és függőségeit.
* Ismerkedés a [Node.js – minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * Lásd még: [Node.js szalagtárak](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/newsSearch) definíciókat és függőségeit.
* Ismerkedés a [Java-minták](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * Lásd még: [Java szalagtárak](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch) definíciókat és függőségeit.
* Ismerkedés a [Python-minták](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * Lásd még: [Python szalagtárak](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-newssearch) definíciókat és függőségeit.

Az egyes nyelvekhez SDK-minták Előfeltételek és a minták telepítése/fut fontos fájljai közé tartozik.
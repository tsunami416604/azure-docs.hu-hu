---
title: Bing keresési SDK |} Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Bing webes keresés SDK olyan alkalmazásnál, amely a webes keresés.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.assetid: ''
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 1/11/2018
ms.author: v-gedod
ms.openlocfilehash: 9d2f363d772febfb9fffd2d9c2924398e438d6e0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349475"
---
# <a name="bing-search-sdk"></a>Bing keresési SDK
A Bing webes keresési API-mintákat közé tartoznak a forgatókönyvek, amelyek:
1. Egyszavas lekérdezéssel eredményt, és nyomtassa ki a nevét és URL-címet az első minden webes, kép, híreket és videó eredmények számára.
2. Kifejezést a lekérdezni, ellenőrizze a eredmények számát, és nyomtassa ki a nevét és URL-cím első eredményének.
3. A keresési kifejezés válasz szűrő kiszűri beállítása `news` és kinyomtathatja hírek eredmények részletes adatait.
4. A kívánt keresőkifejezést a lekérdezés `answerCount` és `promote` paramétereket, majd nyomtassa ki az eredmények részletei.

Bing keresési SDK-k ellenőrizze webes keresési funkcióját könnyen hozzáférhető a következő programozási nyelven:
* Ismerkedés a [.NET minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7) 
    * [NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0)
    * Lásd még: [.NET-kódtárakra](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingWebSearch) definíciókat és függőségeit.
* Ismerkedés a [Node.js – minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * Lásd még: [Node.js szalagtárak](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/webSearch) definíciókat és függőségeit.
* Ismerkedés a [Java-minták](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * Lásd még: [Java szalagtárak](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch) definíciókat és függőségeit.
* Ismerkedés a [Python-minták](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * Lásd még: [Python szalagtárak](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-websearch) definíciókat és függőségeit.

Az egyes nyelvekhez SDK-minták Előfeltételek és a minták telepítése/fut fontos fájljai közé tartozik.

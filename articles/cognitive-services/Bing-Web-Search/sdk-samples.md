---
title: Bing Web Search SDK-minták
titleSuffix: Azure Cognitive Services
description: A Bing Web Search SDK használatával keresési képességekkel láthatja el Python, Node.js, C# vagy Java nyelven írt alkalmazásait.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: sample
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: f13250bb2d6829cf7f1bf3dd0417974b810a36fe
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350432"
---
# <a name="bing-web-search-sdk-samples"></a>Bing Web Search SDK-minták

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A Bing Web Search SDK elérhető Pythonhoz, Node.js-hez, C#-hoz, és Javához. A kódmintákat, az előfeltételeket és a fordítási útmutatókat a GitHubon találja meg. Az alábbi esetekben használható:

* Egyszavas lekérdezés és a webhelyekre, képekre, hírekre vagy videókra vonatkozó első találat nevének és URL-címének megjelenítése.
* Kifejezés lekérdezése, a találatok számának ellenőrzése, és az első találat nevének és URL-címének megjelenítése.
* Kifejezésre való keresés `news` válaszszűrővel, majd a hírtalálatok adatainak megjelenítése.
* Kifejezésre való keresés `answerCount` és `promote` paraméterekkel, majd az eredmény adatainak megjelenítése.

## <a name="sdks-and-libraries"></a>SDK-k és kódtárak

A kívánt nyelvhez használható SDK-t az alábbi címeken érheti el.

* Első lépések [Python-mintákkal](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)
  * A definíciókat és a függőségeket a [Python-kódtárakban](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-websearch) találja.
* Ismerkedés a [Node.js-mintákkal](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
  * Lásd még: [Node.js Web Search](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesWebSearch).
* Ismerkedés a [.NET-mintákkal](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
  * [NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0)
  * További definíciókat és függőségeket találhat a [.NET-kódtárakban](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingWebSearch).
* Ismerkedés a [Java-mintákkal](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
  * A definíciókat és a függőségeket a [Java-kódtárakban](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch) találja.
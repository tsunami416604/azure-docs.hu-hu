---
title: Mi a Bing Web Search API?
titleSuffix: Azure Cognitive Services
description: A Bing Web Search API egy REST-alapú szolgáltatás, amely azonnali válaszokat biztosít a webes keresési lekérdezésekre. Az eredmények konfigurálásával weblapokat, képeket, videókat és híreket is megadhat. A találatok JSON formátumban érkeznek, és a keresési relevancia, valamint a Bing Web Search-előfizetései alapján állnak össze.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: overview
ms.date: 03/31/2020
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: e312265705161dd1cfa23856c9f6dfb71997ec82
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381099"
---
# <a name="what-is-the-bing-web-search-api"></a>Mi a Bing Web Search API?

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](https://aka.ms/cogsvcs/bingmove)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](https://aka.ms/cogsvcs/bingmigration).

A Bing Web Search API egy RESTful szolgáltatás, amely azonnali választ ad a felhasználói lekérdezésekre. A keresés könnyen konfigurálható, hogy weboldalakra, képekre, videókra, hírekre, fordításokra és egyéb találatokra is kiterjedjen. A Bing Web Search a keresési relevancia és a Bing Web Search-előfizetések alapján biztosítja az eredményeket JSON-ként.

Ez az API optimális az olyan alkalmazásokhoz, amelyek hozzáférést biztosítanak a felhasználók keresési lekérdezései szempontjából releváns tartalmakhoz. Ha olyan alkalmazást fejleszt, amelyhez csak a találatok adott típusa szükséges, érdemes lehet inkább a [Bing Image Search API-t](../Bing-Image-Search/overview.md), a [Bing Video Search API-t](../bing-video-search/overview.md) vagy a [Bing News Search API-t](../Bing-News-Search/search-the-web.md) használnia. A Bing Search API-k teljes listájáért lásd a [Cognitive Services API-kat](../index.yml) ismertető témakört.

Szeretné látni működés közben? Próbálja ki a [Bing Web Search API bemutatóanyagát](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/).

## <a name="features"></a>Szolgáltatások  

Bing Web Search nem csak az azonnali válaszokhoz biztosít hozzáférést. Emellett további funkciókat és funkciókat is biztosít, amelyek segítségével testre szabhatja a felhasználók keresési eredményeit.

| Szolgáltatás | Leírás |
|---------|-------------|
| [Keresési kifejezések valós idejű felkínálása](../bing-autosuggest/get-suggested-search-terms.md) | A Bing Autosuggest API a begépelés során javaslatokat tesz a keresési kifejezésekre, így könnyebbé teszi az alkalmazások használatát. |
| [Találatok szűrése és korlátozása a tartalom típusa alapján](filter-answers.md) | Keresési eredmények szűrőkkel és lekérdezési paraméterekkel történő testre szabása és finomítása a weboldalakra, képekre, videókra, a biztonságos keresésre és egyebekre vonatkozóan. |
| [Találatok kiemelése unicode-karakterekhez](hit-highlighting.md) | Nem kívánt unicode-karakterek azonosítása és eltávolítása a találatokból, mielőtt azok megjelennének a találatok kiemelése funkciót használó felhasználóknak. |
| [Találatok helyi szűkítése ország, régió és/vagy piac alapján](./language-support.md) | A Bing Web Search több mint három tucat országot vagy régiót támogat. A funkció használatával a találatok leszűkíthetők egy adott országra/régióra vagy piacra. |
| [Keresési metrikák elemzése a Bing Statistics használatával](bing-web-stats.md) | A Bing Statistics egy fizetős előfizetés, amely a meghívásmennyiségek, a leggyakoribb lekérdezési sztringek, a földrajzi eloszlás és egyéb szempontok elemzését végzi. |

## <a name="workflow"></a>Munkafolyamat

A Bing Web Search API bármely olyan programnyelvből meghívható, amely képes HTTP-lekérdezéseket kezelni és JSON-válaszokat elemezni. A szolgáltatás a [REST API](quickstarts/python.md) vagy a [Bing Web Search ügyféloldali kódtárak](./quickstarts/client-libraries.md)használatával érhető el.

1. [Hozzon létre egy Azure-erőforrást](../cognitive-services-apis-create-account.md) a Bing Search API-k számára. Ha nem rendelkezik Azure-előfizetéssel, [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/).  
2. Küldjön egy [kérést a Bing Web Search API-ra](quickstarts/python.md).
3. Elemezze a JSON-választ.

## <a name="next-steps"></a>Következő lépések

* A [Python rövid útmutatója](./quickstarts/client-libraries.md?pivots=programming-language-python) alapján végezze el az első hívást a Bing Web Search API-ra.  
* [Hozzon létre egy egyoldalas webalkalmazást](tutorial-bing-web-search-single-page-app.md).
* Tekintse át a [Web Search API 7-es verziójának referenciadokumentációját](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).  
* Tekintse át részletesebben a Bing Web Search [használati és megjelenítési követelményeit](./use-display-requirements.md).
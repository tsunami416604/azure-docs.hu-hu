---
title: Mi az a Bing Custom Search API?
titleSuffix: Azure Cognitive Services
description: A Bing Custom Search API lehetővé teszi, hogy testreszabott keresési funkciókat hozzon létre az Ön számára fontos témakörökhöz.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: e70d4d83fcd9641fa86f013688b5a3a6e3652919
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338401"
---
# <a name="what-is-the-bing-custom-search-api"></a>Mi az a Bing Custom Search API?

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A Bing Custom Search API lehetővé teszi, hogy testreszabott ad-ingyenes keresési funkciókat hozzon létre az Ön számára fontos témakörökhöz. Megadhatja, hogy a Bing milyen tartományokat és weblapokat keressen, valamint PIN-kódot, lendületet vagy lefokozni bizonyos tartalmakat, hogy egyéni nézetet hozzon létre a weben, és segítsen a felhasználóknak a releváns keresési eredmények gyors megtalálásában. 

## <a name="features"></a>Szolgáltatások

|Funkció  |Leírás  |
|---------|---------|
|[Egyéni valós idejű keresési javaslatok](define-custom-suggestions.md)     | Olyan keresési javaslatokat adhat meg, amelyek legördülő listaként jeleníthetők meg a felhasználók típusaként.       | 
|[Egyéni képkeresési élmények](get-images-from-instance.md)     | Lehetővé teheti a felhasználók számára, hogy az egyéni keresési példányban megadott tartományokból és webhelyekről keressenek rendszerképeket.        |        
|[Egyéni videók keresésének tapasztalatai](get-videos-from-instance.md)     | Lehetővé teheti a felhasználók számára, hogy az egyéni keresési példányban megadott tartományokból és helyekről keressenek videókat.        |    
|[Egyéni keresési példány megosztása](share-your-custom-search.md)     | Közösen szerkesztheti és tesztelheti a keresési példányt a csapat tagjaival.        | 
|[Felhasználói felület konfigurálása alkalmazásokhoz és webhelyekhez](hosted-ui.md)     | Egy üzemeltetett felhasználói felületet biztosít, amellyel egyszerűen integrálható a weboldalakba és a webalkalmazásba JavaScript-kódrészletként.        | 
## <a name="workflow"></a>Munkafolyamat

A [Bing Custom Search portál](https://customsearch.ai)használatával létrehozhat egy testreszabott keresési példányt. A portál lehetővé teszi, hogy olyan egyéni keresési példányt hozzon létre, amely meghatározza a Bing által keresendő tartományokat, webhelyeket és weblapokat, valamint azokat, amelyeket nem kíván keresni. A portálon a következő lehetőségek is elérhetők: előzetes verzió a keresési élményben, az API által biztosított keresési rangsorok beállítása, valamint a kereshető felhasználói felület beállítása a webhelyeken és alkalmazásokban való megjelenítéshez.

A keresési példány létrehozása után integrálhatja azt (és opcionálisan egy felhasználói felületet) a webhelyre vagy alkalmazásba a Bing Custom Search API meghívásával:

![Kép, amely azt mutatja, hogy az API-n keresztül kapcsolódhat a Bing Custom Search szolgáltatáshoz](media/BCS-Overview.png "Hogyan működik a Bing Custom Search.")


## <a name="next-steps"></a>További lépések

Annak érdekében, hogy gyorsan nekiláthasson, olvassa el [az első Bing Custom Search-példányok létrehozásáról](quick-start.md) szóló cikket.

A keresési példány testreszabásával kapcsolatos részletekért lásd az [az egyéni keresési példány definiálásával](define-your-custom-view.md) foglalkozó témakört.

Ügyeljen arra, hogy olvassa el a [Bing használati és megjelenítési követelményeit](../bing-web-search/use-display-requirements.md) a keresési eredmények használatához a szolgáltatásokban és az alkalmazásokban.

A többi elérhető API-k megismeréséhez látogasson el a [BING Search API hub oldalára](../bing-web-search/overview.md) .

Tekintse át az egyéni keresési végpontokra vonatkozó referenciákat. A referencia olyan végpontokat, fejléceket és lekérdezési paramétereket tartalmaz, amelyekkel keresési eredményeket kérhet le, és a válaszobjektumok definícióit is megtalálja benne.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

- [Custom Search API](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
- [Custom Image API](/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference)
- [Custom Video API](/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)
- [Custom Autosuggest API](/rest/api/cognitiveservices-bingsearch/bing-custom-autosuggest-api-v7-reference)
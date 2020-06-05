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
ms.openlocfilehash: 010f2960693a06a50cd15ac3bac2fe35b6c0985f
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84434575"
---
# <a name="what-is-the-bing-custom-search-api"></a>Mi az a Bing Custom Search API?

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


## <a name="next-steps"></a>Következő lépések

Annak érdekében, hogy gyorsan nekiláthasson, olvassa el [az első Bing Custom Search-példányok létrehozásáról](quick-start.md) szóló cikket.

A keresési példány testreszabásával kapcsolatos részletekért lásd az [az egyéni keresési példány definiálásával](define-your-custom-view.md) foglalkozó témakört.

Ügyeljen arra, hogy olvassa el a [Bing használati és megjelenítési követelményeit](./use-and-display-requirements.md) a keresési eredmények használatához a szolgáltatásokban és az alkalmazásokban.

A többi elérhető API-k megismeréséhez látogasson el a [BING Search API hub oldalára](../bing-web-search/search-the-web.md) .

Tekintse át az egyéni keresési végpontokra vonatkozó referenciákat. A referencia olyan végpontokat, fejléceket és lekérdezési paramétereket tartalmaz, amelyekkel keresési eredményeket kérhet le, és a válaszobjektumok definícióit is megtalálja benne.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

- [Custom Search API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
- [Custom Image API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference)
- [Custom Video API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)
- [Custom Autosuggest API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-autosuggest-api-v7-reference)


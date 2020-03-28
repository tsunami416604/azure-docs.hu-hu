---
title: Mi az a Bing Custom Search API?
titleSuffix: Azure Cognitive Services
description: A Bing egyéni keresési API lehetővé teszi, hogy személyre szabott keresési élményeket hozzon létre a önnek fontos témakörökhöz.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: 12c255f0e4ed11ce8acbd762530604218f8bc12b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448734"
---
# <a name="what-is-the-bing-custom-search-api"></a>Mi az a Bing Custom Search API?

A Bing egyéni keresési API lehetővé teszi, hogy személyre szabott, hirdetésmentes keresési élményeket hozzon létre az Ön számára fontos témakörökhöz. Megadhatja a Bing által kereshető tartományokat és weblapokat, valamint rögzítheti, kiemelheti vagy lefokozhatja az adott tartalmat, hogy egyéni nézetet hozzon létre az internetről, és segítsen a felhasználóknak gyorsan megtalálni a releváns keresési eredményeket. 

## <a name="features"></a>Szolgáltatások

|Szolgáltatás  |Leírás  |
|---------|---------|
|[Egyéni valós idejű keresési javaslatok](define-custom-suggestions.md)     | Adjon meg keresési javaslatokat, amelyek legördülő listaként jeleníthetők meg a felhasználók gépelése közben.       | 
|[Egyéni képkeresési élmények](get-images-from-instance.md)     | Engedélyezze a felhasználóknak, hogy képeket keressenek az egyéni keresési példányban megadott tartományokból és webhelyekről.        |        
|[Egyéni videókeresési élmények](get-videos-from-instance.md)     | Engedélyezze a felhasználóknak, hogy az egyéni keresési példányban megadott tartományokból és webhelyekről keressenek videókat.        |    
|[Egyéni keresési példány megosztása](share-your-custom-search.md)     | Közösen szerkesztheti és tesztelheti a keresési példányt a csapat tagjaival való megosztással.        | 
|[Felhasználói felület konfigurálása az alkalmazásokhoz és webhelyekhez](hosted-ui.md)     | Közösen szerkesztheti és tesztelheti a keresési példányt a csapat tagjaival való megosztással.        | 
## <a name="workflow"></a>Munkafolyamat

Testre szabott keresési példányt a [Bing Egyéni keresési portálon](https://customsearch.ai)hozhat létre. A portál lehetővé teszi, hogy hozzon létre egy egyéni keresési példányt, amely meghatározza azokat a tartományokat, webhelyeket és weblapokat, amelyeket a Bingnek keresnie kell, valamint azokat, amelyekben nem szeretné, hogy keressen. A portál segítségével: megtekintheti a keresési élményt, módosíthatja az API által biztosított keresési rangsort, és szükség esetén konfigurálhat egy kereshető felhasználói felületet a webhelyeken és alkalmazásokban való megjelenítéséhez.

A keresési példány létrehozása után integrálhatja azt (és opcionálisan egy felhasználói felületet) a webhelybe vagy az alkalmazásba a Bing Egyéni keresési API-jának felhívásával:

![A Bing egyéni kereséshez az API-n keresztül imitált képe](media/BCS-Overview.png "A Bing egyéni keresés működése.")


## <a name="next-steps"></a>További lépések

Annak érdekében, hogy gyorsan nekiláthasson, olvassa el [az első Bing Custom Search-példányok létrehozásáról](quick-start.md) szóló cikket.

A keresési példány testreszabásával kapcsolatos részletekért lásd az [az egyéni keresési példány definiálásával](define-your-custom-view.md) foglalkozó témakört.

Mindenképpen olvassa el a [Bing használatára és a megjelenítési követelményekre vonatkozó követelményeket](./use-and-display-requirements.md) a keresési eredmények szolgáltatásokban és alkalmazásokban való használatához.

Látogasson el a [Bing Search API-központ oldalára](../bing-web-search/search-the-web.md) a többi elérhető API-k felfedezéséhez.

Tekintse át az egyéni keresési végpontokra vonatkozó referenciákat. A referencia olyan végpontokat, fejléceket és lekérdezési paramétereket tartalmaz, amelyekkel keresési eredményeket kérhet le, és a válaszobjektumok definícióit is megtalálja benne.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

- [Custom Search API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
- [Custom Image API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference)
- [Custom Video API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)
- [Custom Autosuggest API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-autosuggest-api-v7-reference)


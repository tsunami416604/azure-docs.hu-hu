---
title: Mi az a Bing Custom Search API?
titlesuffix: Azure Cognitive Services
description: A Bing Custom Search API lehetővé teszi, hogy a témakörök, amelyek az Ön számára személyre szabott keresési funkciókkal.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: overview
ms.date: 03/4/2019
ms.author: aahi
ms.openlocfilehash: e788da047cb0567fc00f27130621a2f21e575dc4
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337736"
---
# <a name="what-is-the-bing-custom-search-api"></a>Mi az a Bing Custom Search API?

A Bing Custom Search API lehetővé teszi, hogy a témakörök, amelyek az Ön számára személyre szabott ingyenes keresési funkciókkal. Megadhatja, hogy a tartományok és weblapjait a Bing keresés, valamint a PIN-kód, növelheti, vagy adott tartalmat, a webes egyéni nézet létrehozása, és segítségével gyorsan megtalálhatja a releváns keresési eredmények a felhasználók lefokozása. 

## <a name="features"></a>Szolgáltatások

|Szolgáltatás  |Leírás  |
|---------|---------|
|[Valós idejű egyéni keresési javaslatok](define-custom-suggestions.md)     | Adja meg a keresési javaslatok, mint egy legördülő lista a felhasználók által beírt jelenhet meg.       | 
|[Egyéni rendszerkép keresési funkciókkal](get-images-from-instance.md)     | Engedélyezze a felhasználókat, hogy a tartományok és az egyéni keresőpéldányok megadott webhelyek Képkeresés.        |        
|[Egyéni videó keresési funkciókkal](get-videos-from-instance.md)     | Engedélyezze a felhasználókat, hogy a tartományok és az egyéni keresőpéldányok megadott helyek videók keresése.        |    
|[Az egyéni keresőpéldányok megosztása](share-your-custom-search.md)     | Ügyfelek bevonásában szerkesztheti, és tesztelje a keresési példány megosztja azokat a csapat tagjai a.        | 
|[A felhasználói felület az alkalmazások és webhelyek konfigurálása](hosted-ui.md)     | Ügyfelek bevonásában szerkesztheti, és tesztelje a keresési példány megosztja azokat a csapat tagjai a.        | 
## <a name="workflow"></a>Munkafolyamat

Létrehozhat egy egyéni keresési példány használatával az [Bing Custom Search portál](https://customsearch.ai). A portál, amely meghatározza a tartományok, webhelyek és, amelyek azt szeretné, hogy szeretne keresni, amelyekre már nincs szükség rá, keresése és a Bing egyéni keresés-példány létrehozását teszi lehetővé. A portálon is használhatja: a keresési funkció előzetes verzió, az az API által biztosított keresési eredményeinkben és igény szerint állítsa be a webhelyek és alkalmazások megjelenítésre kereshető felhasználói felületet.

Miután létrehozta a keresési példányát, is integrálja azt (és opcionálisan egy felhasználói felületet) webhelyéhez vagy alkalmazásához a Bing Custom Search API meghívásával:

![Kép, hogy képes-e csatlakozni a Bing egyéni keresés az API-n keresztül](media/BCS-Overview.png "hogyan Bing Custom Search működik.")


## <a name="next-steps"></a>További lépések

Annak érdekében, hogy gyorsan nekiláthasson, olvassa el [az első Bing Custom Search-példányok létrehozásáról](quick-start.md) szóló cikket.

A keresési példány testreszabásával kapcsolatos részletekért lásd az [az egyéni keresési példány definiálásával](define-your-custom-view.md) foglalkozó témakört.

Olvassa el [követelmények Bing használata és megjelenítése](./use-and-display-requirements.md) a keresési eredmények a szolgáltatások és alkalmazások.

Tekintse át az egyéni keresési végpontokra vonatkozó referenciákat. A referencia olyan végpontokat, fejléceket és lekérdezési paramétereket tartalmaz, amelyekkel keresési eredményeket kérhet le, és a válaszobjektumok definícióit is megtalálja benne.

- [Custom Search API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference)
- [Custom Image API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference)
- [Custom Video API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference)
- [Custom Autosuggest API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-autosuggest-api-v7-reference)


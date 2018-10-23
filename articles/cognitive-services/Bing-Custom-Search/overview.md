---
title: Mi a Bing Custom Search?
titlesuffix: Azure Cognitive Services
description: Általános áttekintést nyújt a Bing Custom Search funkcióról.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: overview
ms.date: 09/29/2017
ms.author: v-brapel
ms.openlocfilehash: f2946918f0c1a7a516788989042825e8f49d7b0b
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49318724"
---
# <a name="what-is-bing-custom-search"></a>Mi a Bing Custom Search?

A Bing Custom Search szolgáltatással személyre szabott keresési funkciókat hozhat létre olyan témakörökhöz kapcsolódóan, amelyek jelentőséggel bírnak az Ön számára. Ha például egy keresési funkciót is biztosító webhellyel rendelkezik, megadhatja azokat a tartományokat, webhelyeket és weblapokat, amelyeken a Bingnek keresnie kell. A felhasználók az érdeklődési körükhöz igazított keresési eredményeket látnak ahelyett, hogy több oldalnyi irreleváns találatot kellene átnézniük.

Az egyéni kívánalmaknak megfelelő webes keresés létrehozásához használja a Bing Custom Search [portált](https://customsearch.ai). A portálon létrehozható egy egyéni keresési példány, amely meghatározza a Bing által keresendő tartományokat, webhelyeket és weboldalakat, és azokat a webhelyeket, amelyeken nem végezhet keresést. Az ismert tartalmak URL-címeinek megadása mellett a portál arra is használható, hogy további vonatkozó tartalmakra is rátaláljon.

A portál azt is lehetővé teszi, hogy egy meghatározott weboldalt rögzítsen a keresési eredmények elején, ha a felhasználó egy bizonyos keresési kifejezést ad meg. 

A példány definiálása után az egyéni keresés integrálható a webhellyel, asztali vagy mobilalkalmazással a Custom Search API meghívásával. Webhely vagy webes alkalmazás esetén az üzemeltetett felhasználói felület renderelheti a keresési felületet.

A következő kép azt illusztrálja, milyen könnyen integrálható az egyéni keresés.

![picture alt](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/cognitive-services/Bing-Custom-Search/media/BCS-Overview.png "A Bing Custom Search működése.")

## <a name="adding-custom-search-box-suggestions"></a>Egyéni keresőmező-javaslatok hozzáadása

Gazdagabbá teheti az egyéni keresési élményt a keresőmező mellett megjelenő javaslatokkal. Ez a funkció lehetővé teszi, hogy az adott kereséshez kapcsolódó egyéni keresési javaslatokat adjon meg. Miközben a felhasználó gépel a keresőmezőbe, a legördülő lista lekérdezési sztringeket kínál fel az addig begépelt kifejezés alapján. Megadhatja, hogy csak a saját egyéni javaslatait szeretné-e megjeleníteni, vagy a Bing javaslatait is. [További információ](define-custom-suggestions.md).

## <a name="adding-custom-image-search-experience"></a>Egyéni képkeresési élmény hozzáadása

Képekkel gazdagabbá teheti az egyéni keresés élményét. A webes találatokhoz hasonlóan az egyéni keresés a képkeresést is támogatja azokon a webhelyeken, amelyek a keresési példányhoz engedélyezve vannak. [További információ](get-images-from-instance.md).

## <a name="adding-custom-video-search-experience"></a>Egyéni videókeresési élmény hozzáadása

Videókkal még gazdagabbá teheti az egyéni keresés élményét. A webes találatokhoz hasonlóan az egyéni keresés a videókeresést is támogatja azokon a webhelyeken, amelyek a keresési példányhoz engedélyezve vannak. [További információ](get-videos-from-instance.md).

## <a name="sharing-your-custom-search-instance-with-others"></a>Az egyéni keresési példány megosztása másokkal

Ha a példányt megosztja csapata tagjaival, akkor közösen is dolgozhatnak annak szerkesztésén és tesztelésén. [További információ](share-your-custom-search.md).

## <a name="next-steps"></a>További lépések

Annak érdekében, hogy gyorsan nekiláthasson, olvassa el [az első Bing Custom Search-példányok létrehozásáról](quick-start.md) szóló cikket.

A keresési példány testreszabásával kapcsolatos részletekért lásd az [az egyéni keresési példány definiálásával](define-your-custom-view.md) foglalkozó témakört.

Tekintse át az egyéni keresési végpontokra vonatkozó referenciákat. A referencia olyan végpontokat, fejléceket és lekérdezési paramétereket tartalmaz, amelyekkel keresési eredményeket kérhet le, és a válaszobjektumok definícióit is megtalálja benne.

- [Custom Search API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference)
- [Custom Image API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference)
- [Custom Video API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference)
- [Custom Autosuggest API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-autosuggest-api-v7-reference)


Mindenképpen olvassa el a [Bing használati és megjelenítési előírásait,](./use-and-display-requirements.md) hogy ne szegje meg a keresési eredmények használatának szabályait.

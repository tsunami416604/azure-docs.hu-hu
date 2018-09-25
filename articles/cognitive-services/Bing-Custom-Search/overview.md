---
title: Mi a Bing Custom Search? | Microsoft Docs
description: Magas szintű áttekintést nyújt a Bing egyéni keresés
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/29/2017
ms.author: v-brapel
ms.openlocfilehash: b6f50844d6571cca6d63c1db7a85863e3d22d411
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948077"
---
# <a name="what-is-bing-custom-search"></a>Mi a Bing Custom Search?

Bing Custom Search lehetővé teszi, hogy a témakörök, amelyek az Ön számára személyre szabott keresési funkciókkal. Például ha saját keresési funkciókat biztosító webhely, megadhatja a tartományok, webhelyek és a Bing keresési weblapok. Megjelennek a felhasználók számára a tartalom nem érdeklő szabott keresési eredmények – keresési eredmények, amelyek irreleváns tartalma oldalra.

A webes egyéni nézetének létrehozásához használja a Bing Custom Search [portál](https://customsearch.ai). A portál segítségével hozhat létre egy egyéni keresési példány, amely meghatározza a tartományok, webhelyek, és, amelyek azt szeretné, hogy a Keresés a Bing és a webhelyeket, amelyeket nem szeretné, hogy a keresés. A tartalom, hogy megismerkedett az URL-címek megadása, mellett is használhatja a portálon található kapcsolódó tartalom, lehet, hogy hozzá szeretné.

A portál segítségével rögzítheti egy adott weboldal a keresési eredmények tetejére, ha a felhasználó egy adott keresési kifejezést ad meg. 

A példány meghatározása, után integrálhatja egyéni keresési webhelyre, az asztali alkalmazás, vagy a mobilalkalmazás az egyéni keresési API meghívásával. Ha egy web-alapú webhely vagy alkalmazás, hagyhatja, hogy a központi felhasználói felület jelennek meg a keresési felület az Ön számára.

Az alábbi képen látható az egyéni keresési integráció egyszerűsége.

![kép alt](./media/bcs-overview.png "hogyan a Bing Custom Search működik.")

## <a name="adding-custom-search-box-suggestions"></a>Egyéni keresési mező javaslatok hozzáadása

Gazdagabbá teheti az egyéni keresés be az egyéni keresési élmény. Ez a funkció lehetővé teszi, hogy egyéni keresési javaslatok kapcsolódik a keresési funkciót biztosíthat. A felhasználó beír a keresőmezőbe, a legördülő listából a felhasználó részleges lekérdezési karakterlánc alapján javasolt lekérdezési karakterláncokat tartalmaz. Megadhatja, hogy csak a vonatkozó egyéni javaslatok vissza vagy Bing javaslatokat is tartalmazhatnak. [További információk](define-custom-suggestions.md).

## <a name="adding-custom-image-search-experience"></a>Hozzáadásával egyéni rendszerkép keresési funkciót

Gazdagabbá teheti az egyéni keresési funkciókat lemezképekkel. Hasonló webes találatokat, képeket webhelyek listájában a példány keresése egyéni keresési támogatja. [További információk](get-images-from-instance.md).

## <a name="adding-custom-video-search-experience"></a>Egyéni videó keresési funkció hozzáadása

Gazdagabbá teheti a videókat egyéni keresési élményét. Hasonló webes találatokat, egyéni keresés támogatja a videók a webhelyek listája a példány keresése. [További információk](get-videos-from-instance.md).

## <a name="sharing-your-custom-search-instance-with-others"></a>Az egyéni keresőpéldányok megosztása másokkal

Könnyen engedélyezheti szerkesztését, és a tesztelést a példány szert a csapat tagjai által biztosított együttműködési környezettel. [További információk](share-your-custom-search.md).

## <a name="next-steps"></a>További lépések

Tekintse meg a gyors kezdéshez, [az első Bing Custom Search-példány létrehozása](quick-start.md).

A keresési példány testreszabásával kapcsolatos részletekért lásd: [egy egyéni keresési példány meghatározása](define-your-custom-view.md).

Ismerkedjen meg a referencia-tartalom minden, az egyéni keresési végpontok. A hivatkozás tartalmazza a végpontokat, fejlécek és lekérdezési paraméterek, amelyek a keresési eredmények kérelem szeretné használni. és a válaszobjektumok definícióit is megtalálja benne.

- [Egyéni keresési API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference)
- [Egyéni kép API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference)
- [Egyéni Vidoe API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference)
- [Egyéni automatikus kiegészítés API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-autosuggest-api-v7-reference)


Mindenképpen olvassa el a [Bing használati és megjelenítési előírásait,](./use-and-display-requirements.md) hogy ne szegje meg a keresési eredmények használatának szabályait.
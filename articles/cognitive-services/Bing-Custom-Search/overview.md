---
title: Mi az a Bing egyéni keresés? | Microsoft Docs
description: Magas szintű áttekintést nyújt a Bing egyéni keresés
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/29/2017
ms.author: v-brapel
ms.openlocfilehash: 7cd61fc63d0d7734b842ed222c67c6753da9a418
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347871"
---
# <a name="what-is-bing-custom-search"></a>Mi az a Bing egyéni keresés?

Bing egyéni keresés szabott keresési lép témakörök, amelyek az Ön számára legfontosabb létrehozását teszi lehetővé. Például ha Ön a tulajdonosa a webhelyet, ahol a keresési élményt biztosít, megadhatja a tartományok, webhelyek és a Bing keres a weblapokat. A tartalom, így ahelyett hogy érdeklő igazított találatok jelennek meg a felhasználók lapra a keresési eredmények, amelyek tartalma nem számít.

A webes egyéni nézet létrehozásához használja a Bing egyéni keresési [portal](https://customsearch.ai). A portál segítségével hozzon létre egy egyéni keresési példányt, amely meghatározza a tartományok, webhelyek, és azt szeretné, hogy internetes keresés a weblapokat és a webhelyeket, amelyeket szeretné, hogy a keresés. Mellett adja meg a tartalom tudnia: az URL-címeket, is használhatja a portál található kapcsolódó tartalom érdemes lehet hozzáadni.

A portál segítségével rögzítheti egy adott weboldal a keresési eredmény tetejére, ha a felhasználó adja meg a keresési kifejezés is. 

A példány meghatározása, után egyéni keresési integrálhatja a webhely, az asztali alkalmazás vagy a mobilalkalmazás az egyéni keresési API-JÁNAK meghívásával. Ha egy webes helyre vagy alkalmazásra, hogy a központi felhasználói felületen a keresési felület jeleníthető meg.

A következő kép bemutatja az egyéni keresési integráció egyszerűsége.

![kép alt](./media/bcs-overview.png "hogyan Bing egyéni keresés működését.")

## <a name="customize-search-suggestions"></a>Keresési javaslatok testreszabása

Egyéni keresés megfelelő szinten előfizetett (lásd a [lapok árképzési](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), testre szabhatja a keresési javaslatok végzett az egyéni keresési élményt biztosít. Az egyéni automatikus kiegészítési API-t egy részleges lekérdezési karakterlánc, amely a felhasználó alapján javasolt lekérdezések listáját adja vissza. Az egyéni automatikus kiegészítési adja meg a keresési élményt vonatkozó egyéni keresési javaslatok. Megadhatja, hogy csak egyéni javaslatok vissza, vagy a Bing javasol. Bing javaslatok érhetők el, ha egyéni javaslatok a Bing nyújt javaslatokat előtt jelennek meg. Bing javaslatok a egyéni keresési példányát keretében korlátozódnak.

## <a name="next-steps"></a>További lépések

Gyors használatbavétel, lásd: [hozza létre az első Bing egyéni keresési példányát](quick-start.md).

A keresési példányát testreszabásához rendelkezésre álló beállításokkal kapcsolatos részletekért lásd: [adja meg egy egyéni keresési példányát](define-your-custom-view.md).

Ismerje meg, a [egyéni Search API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference) hivatkozás. A hivatkozás tartalmazza a végpontok, fejlécek és lekérdezési paraméterek, kérje a keresési eredmények között szeretné használni. A válasz objektumok meghatározását is tartalmaz.

Megtudhatja, hogyan szabhatja testre a javaslatok, lásd: [határozza meg az egyéni keresési javaslatok](define-custom-suggestions.md).

Olvassa el [Bing használatát és a megjelenített követelmények](./use-and-display-requirements.md) , nem megszakítja a keresési eredmények használatára vonatkozó szabályok egyike.
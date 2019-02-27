---
title: Mi a Bing Spell Check API?
titlesuffix: Azure Cognitive Services
description: Ismerje meg a Bing Spell Check API, amely gépi tanulás és statisztikai gépi fordítási használ szövegkörnyezetei helyesírás-ellenőrzés.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 22f75efb3cb4baa645030e7ad64072674de662ed
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889955"
---
# <a name="what-is-the-bing-spell-check-api"></a>Mi a Bing Spell Check API?

A Bing Spell Check API lehetővé teszi a környezetfüggő szintaxis és a helyesírás-ellenőrzést a szöveg is végezhet. A legtöbb helyesírás-Pepita szótár-alapú szabálykészletek támaszkodnak, amíg a Bing helyesírás-ellenőrző kihasználja a gépi tanulás és statisztikai gépi fordítási biztosít pontos és környezetfüggő javításokat. 

## <a name="features"></a>Szolgáltatások


|  |  |
|---------|---------|
|Több helyesírás-ellenőrzési mód     | Több helyesírás-ellenőrzési mód lehetővé teszi javításokat, nyelvtani és/vagy helyesírási összpontosított beolvasása. |
|Szleng és az informális nyelvezet felismerése     | Gyakori kifejezések és az informális kifejezések a szöveg ismeri fel.         |
|Megkülönböztetni a hasonló szavakat     | Keresse meg a szavak közötti megfelelő használati adott hang hasonló, de eltérő (például "Lásd a" és "tenger") számára        |
|Márka, a jogcímre és a népszerű használatának támogatása     | Ismeri fel új márkaneveket, címeket és más népszerű kifejezések a megjelenésük pillanatától |

## <a name="workflow"></a>Munkafolyamat

A Bing Spell Check API hívása minden programozási nyelvet, amely HTTP-kérelmeket és elemezni a JSON-válaszok sem ördöngösség. A szolgáltatás nem érhető el a REST API vagy a Bing helyesírás-ellenőrzése SDK használatával. 

1. Hozzon létre egy [Cognitive Services API-fiókot](../cognitive-services-apis-create-account.md), amely hozzáféréssel rendelkezik a Bing Search API-khoz. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy ingyenes fiókot. 
2. A Bing Web Search API-kérés küldése.
3. A JSON-válasz elemzése

## <a name="next-steps"></a>További lépések

Először próbálja meg a Keresés a Bing Spell Check API [interaktív bemutatót](https://azure.microsoft.com/services/cognitive-services/spell-check/) hogyan gyorsan ellenőrizheti a szövegek számos megtekintéséhez.

Amikor kész meghívni az API-t, hozzon létre egy [Cognitive Services API-fiókot](../../cognitive-services/cognitive-services-apis-create-account.md). Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

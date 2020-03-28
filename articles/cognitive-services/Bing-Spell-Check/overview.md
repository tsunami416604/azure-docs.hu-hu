---
title: Mi a Bing Spell Check API?
titleSuffix: Azure Cognitive Services
description: Ismerje meg a Bing helyesírás-ellenőrző API-t, amely gépi tanulást és statisztikai gépi fordítást használ a környezetfüggő helyesírás-ellenőrzéshez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: 5586704e098fc568c714e779c2eed44aa3d1df9c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382999"
---
# <a name="what-is-the-bing-spell-check-api"></a>Mi a Bing Spell Check API?

A Bing helyesírás-ellenőrző API lehetővé teszi a szöveg környezetfüggő nyelvhelyessedésének és helyesírás-ellenőrzésének elvégzését. Míg a legtöbb helyesírás-ellenőrzőszótár-alapú szabálykészletekre támaszkodik, a Bing helyesírás-ellenőrzője a gépi tanulás és a statisztikai gépi fordítás thasználja a pontos és környezetfüggő javítások biztosításához. 

## <a name="features"></a>Szolgáltatások


|  |  |
|---------|---------|
|Több helyesírás-ellenőrzési mód     | A több helyesírás-ellenőrzési mód lehetővé teszi, hogy a javítások at a nyelvhelyességre és/vagy a helyesírásra összpontosítsa. |
|Szleng és informális nyelvfelismerés     | Ismerje fel a szövegben használt gyakori kifejezéseket és informális kifejezéseket.         |
|Különbséget tenni a hasonló szavak között     | Keresse meg a helyes használat ot a szavak között, amelyek hasonlónak tűnnek, de jelentésükben különböznek (például "lásd" és "tenger")        |
|Márka, cím és népszerű használati támogatás     | Ismerje el az új márkák, címek, és más népszerű kifejezések jelennek meg |

## <a name="workflow"></a>Munkafolyamat

A Bing helyesírás-ellenőrző API-t könnyen meg lehet hívni bármely olyan programozási nyelvről, amely HTTP-kéréseket és JSON-válaszokat képes elemezni. A szolgáltatás érhető el a REST API-t vagy a Bing helyesírás-ellenőrző SDK-k. 

1. Hozzon létre egy [Cognitive Services API-fiókot](../cognitive-services-apis-create-account.md), amely hozzáféréssel rendelkezik a Bing Search API-khoz. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy ingyenes fiókot. 
2. Küldjön egy kérést a Bing Web Search API-ra.
3. Elemezd a JSON-választ

## <a name="next-steps"></a>További lépések

Először próbálja ki a Bing Helyesírás-ellenőrző Keresés API [interaktív bemutatóját,](https://azure.microsoft.com/services/cognitive-services/spell-check/) hogy lássa, hogyan ellenőrizheti gyorsan a különböző szövegeket.

Amikor kész meghívni az API-t, hozzon létre egy [Cognitive Services API-fiókot](../../cognitive-services/cognitive-services-apis-create-account.md). Ha nem rendelkezik Azure-előfizetéssel, ingyenesen [létrehozhat egyet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

A [Bing Search API-központ lapon](../bing-web-search/search-the-web.md) a többi elérhető API-t is felfedezheti.
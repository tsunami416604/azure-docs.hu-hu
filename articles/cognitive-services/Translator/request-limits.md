---
title: Kérelemkorlátok – Fordítószöveg API
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Translator Text API kérelemkorlátait sorolja fel. A költségek a karakterszám alapján merülnek fel, nem kérésenként 5000 karakteres gyakorisággal. A karakterkorlátok előfizetésalapúak, az F0-t óránként 2 millió karakterre korlátozzák.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: swmachan
ms.openlocfilehash: 8d26efec2783d6f121c319e46b1b505b6e1b1e09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498928"
---
# <a name="request-limits-for-translator-text"></a>A fordítószöveg reklamációinak korlátozása

Ez a cikk a Translator Text API szabályozási korlátokat biztosít. A szolgáltatások közé tartozik a fordítás, az átírás, a mondathossz-felismerés, a nyelvfelismerés és az alternatív fordítások.

## <a name="character-and-array-limits-per-request"></a>Karakter- és tömbkorlátok kérésenként

Minden fordítási kérelem legfeljebb 5000 karakterből állhat, az összes olyan célnyelven, amelyre fordít. Ha például egy 1500 karakteres fordítási kérelmet küld 3 különböző nyelvre lefordítani, akkor a kérelem mérete 1500x3 = 4500 karakter, amely megfelel a kérelemkorlátnak. Karakterenként kell fizetnie, nem a kérések száma miatt. Javasoljuk, hogy rövidebb kéréseket küldjön.

Az alábbi táblázat a Translator Text API minden egyes műveletéhez tömbelem- és karakterkorlátokat sorol fel.

| Művelet | Tömbelem maximális mérete |   Tömbelemek maximális száma |  A kérelem maximális mérete (karakterek) |
|:----|:----|:----|:----|
| Fordítás | 5000 | 100   | 5000 |
| Átírás | 5000 | 10    | 5000 |
| Észlelés | 10,000 | 100 |   50 000 |
| BreakSentence | 10,000    | 100 | 50 000 |
| Keresés a szótárban| 100 |  10  | 1,000 |
| Szótár – példák | 100 szöveg és 100 fordítás (összesen 200)| 10|   2000 |

## <a name="character-limits-per-hour"></a>Karakterkorlátok óránként

Az óránkénti karakterkorlát a Fordítószöveg-előfizetési szintalapján történik. 

Az óránkénti kvótát egyenletesen kell fogyasztani az óra során. Az F0 réteg óránkénti 2 millió karakteres korlátjánál például a karaktereket nem szabad gyorsabban felhasználni, mint a nagyjából 33 300 karakter/perc csúszóablak (2 millió karakter osztva 60 perccel).

Ha eléri vagy meghaladja ezeket a korlátokat, vagy rövid idő alatt túl nagy részt küld a kvóta egy részéből, valószínűleg kvótaválasz ban fog részesülni. Az egyidejű kérelmek nem korlátozódnak.

| Szint | Karakterkorlát |
|------|-----------------|
| F0 | 2 millió karakter óránként |
| S1 | 40 millió karakter óránként |
| S2 / C2 | 40 millió karakter óránként |
| S3 / C3 | 120 millió karakter óránként |
| S4 / C4 | 200 millió karakter óránként |

A [többszolgáltatásos előfizetések](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) korlátai megegyeznek az S1 csomagéval.

Ezek a korlátozások a Microsoft szabványos fordítási modelljeire korlátozódnak. Egyéni fordítási modellek, amelyek egyéni fordító csak 1800 karakter másodpercenként.

## <a name="latency"></a>Késés

A Translator Text API maximális késése 15 másodperc szabványos modellek és 120 másodperc egyéni modellek használata esetén. A *100 karakteren belüli szövegre adott* válaszok általában 150 ezredmásodpercben és 300 ezredmásodpercben kerülnek visszaadásra. Az egyéni fordítómodellek hasonló késési jellemzőkkel rendelkeznek a tartós kérelmek aránya, és előfordulhat, hogy a kérelem aránya időszakos. A válaszidők a kérelem méretétől és a nyelvpártól függően változnak. Ha ezen időkereten belül nem kap fordítást vagy [hibaválaszt,](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) ellenőrizze a kódot, a hálózati kapcsolatot, és próbálkozzon újra. 

## <a name="sentence-length-limits"></a>Mondathossz-határértékek

A [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) függvény használataesetén a mondat hossza legfeljebb 275 karakter lehet. A következő nyelveken vannak kivételek:

| Nyelv | Kód | Karakterkorlát |
|----------|------|-----------------|
| Kínai | Zh | 132 |
| Német | de de | 290 |
| Olasz | ez | 280 |
| Japán | ja (ja) | 150 |
| Portugál | pt | 290 |
| Spanyol | Igen | 280 |
| Olasz | ez | 280 |
| Thai | Th | 258 |

> [!NOTE]
> Ez a korlát nem vonatkozik a fordításokra.

## <a name="next-steps"></a>További lépések

* [Díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Régiónkénti rendelkezésre állás](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [3-as verziójú Translator Text API-referencia](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

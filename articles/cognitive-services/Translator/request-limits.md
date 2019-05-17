---
title: Kérelmekre vonatkozó korlátok – Translator Text API
titleSuffix: Azure Cognitive Services
description: Ez a cikk felsorolja a Translator Text API kérelmekre vonatkozó korlátok. Díjait értéket, és a egy korlátja kérelmenként 5000 karakternél nem kérelem gyakorisága alapján. Karakter korlátozások, a F0 legfeljebb 2 millió karakter / óra alapú előfizetés.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: 91cc002f373318e5124fc21f76edbfd000d17238
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796907"
---
# <a name="request-limits-for-translator-text"></a>A Translator Text kérelmekre vonatkozó korlátok

Ez a cikk a Translator Text API a sávszélesség-szabályozási korlátait tartalmazza. Szolgáltatások közé tartoznak a fordítás, átbetűzésű, mondat hossza észlelési, nyelv észlelése és alternatív fordításokat.

## <a name="character-and-array-limits-per-request"></a>Kérelem karakter és tömb vonatkozó korlátok

Minden egyes fordítás kérelme, mert legfeljebb 5000 karakter hosszúságú lehet. A kérések száma nem a karakter, díját. Azt javasoljuk, hogy rövidebb kérelmek küldésére.

Az alábbi táblázat listák tömb elem és karakter korlátok, a Translator Text API minden művelethez.

| Művelet | Tömbelem maximális mérete |   Tömb elemek maximális számát |  Kérelem maximális mérete (karakter) |
|:----|:----|:----|:----|
| Translate | 5,000 | 100   | 5,000 |
| Transliterate | 5,000 | 10    | 5,000 |
| Detect | 10,000 | 100 |   50,000 |
| BreakSentence | 10,000    | 100 | 5,0000 |
| Keresés a szótárban| 100 |  10  | 1,000 |
| Szótár – példák | a szöveg és a fordítás (200 teljes) 100 100| 10|   2,000 |

## <a name="character-limits-per-hour"></a>Karakter korlátozások / óra

A karakteres korlátot óránként a Translator Text előfizetési szint alapján történik. Az óránkénti kvóta egyenletesen fel kell használni az óra során. Ha Ön vagy előremutató ezeket a korlátokat, vagy túl nagy a kvóta egy részét, egy rövid idő alatt, valószínűleg kap kívüli kvóta választ. 

| Szint | Karakteres korlátot |
|------|-----------------|
| F0 | 2 millió karakter / óra |
| S1 | 40 millió karakter / óra |
| S2 / C2 | 40 millió karakter / óra |
| S3 / C3 | 120 millió karakter / óra |
| S4 / C4 | óránként 200 millió karakter |

Korlátozza a [több szolgáltatásos előfizetések](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) ugyanazok, mint az S1 szintet.

Ezek a korlátok a Microsoft standard fordítói modelljeinek korlátozódnak. Egyéni a fordítót használó egyéni fordítási modellek korlátozva, másodpercenként 1,800 karakter.

## <a name="latency"></a>Késés

A Translator Text API egy standard szintű modellek használata a 15 másodperces időtúllépési maximális késleltetés van. Egyéni modellek használatával fordítási rendelkezik 25 másodpercig maximális késését. Ez idő szerint fog kapott egy eredményt vagy időtúllépés választ. Általában válaszok visszatér 150 ezredmásodpercben 300 ezredmásodperc. Válaszidők a kérelmek és a nyelvi pár méretétől függően változik. Ha nem kapja meg a fordítást, vagy egy [hibaválaszt](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) adott időtartamon belül kell ellenőrizze a hálózati kapcsolatot, és próbálkozzon újra.

## <a name="sentence-length-limits"></a>Mondat hosszának korlátozása

Használatakor a [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) függvény mondat hossza legfeljebb 275 karakter hosszúságú lehet. Ezen nyelvek kivételek vannak:

| Nyelv | Kód | Karakteres korlátot |
|----------|------|-----------------|
| kínai | zh | 132 |
| német | de | 290 |
| olasz | it | 280 |
| japán | ja | 150 |
| portugál | pt | 290 |
| spanyol | es | 280 |
| olasz | it | 280 |
| thai | . | 258 |

> [!NOTE]
> Ez a korlátozás nem vonatkozik a fordításokat.

## <a name="next-steps"></a>További lépések

* [Díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Régiónkénti rendelkezésre állás](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [a Translator Text API v3-leírás](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

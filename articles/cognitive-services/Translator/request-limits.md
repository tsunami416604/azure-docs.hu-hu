---
title: Kérelmekre vonatkozó korlátok – Translator Text API
titleSuffix: Azure Cognitive Services
description: Ez a cikk felsorolja a Translator Text API kérelmekre vonatkozó korlátok. Díjait értéket, és a egy korlátja kérelmenként 5000 karakternél nem kérelem gyakorisága alapján. Karakter korlátozások, a F0 legfeljebb 2 millió karakter / óra alapú előfizetés.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: erhopf
ms.openlocfilehash: 29ef4cc594a3335d37bd813c0b682b248f96cf22
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2018
ms.locfileid: "51858345"
---
# <a name="request-limits-for-translator-text"></a>A Translator Text kérelmekre vonatkozó korlátok

Ez a cikk a Translator Text API a sávszélesség-szabályozási korlátait tartalmazza. Szolgáltatások közé tartoznak a fordítás, átbetűzésű, mondat hossza észlelési, nyelv észlelése és alternatív fordításokat.

## <a name="character-limits-per-request"></a>Kérelem karakter vonatkozó korlátok

Minden egyes kérelme, mert legfeljebb 5000 karakter hosszúságú lehet. A kérések száma nem a karakter, díját. Rövidebb kérelmek küldésére, és szeretné, hogy egyes egy adott időpontban szálankénti függőben lévő kérések ajánlott.

A Translator Text API szálankénti függőben lévő kérések száma nincs korlátozva van.

## <a name="character-limits-per-hour"></a>Karakter korlátozások / óra

A karakteres korlátot óránként a Translator Text előfizetési szint alapján történik. Ha eléri vagy előremutató ezeket a korlátokat, valószínűleg kap kívüli kvóta választ:

| Szint | Karakteres korlátot |
|------|-----------------|
| F0 | 2 millió karakter / óra |
| S1 | 40 millió karakter / óra |
| S2 | 40 millió karakter / óra |
| S3 | 120 millió karakter / óra |
| S4 | óránként 200 millió karakter |

Ezek a korlátok a Microsoft általános rendszerek korlátozódnak. A Microsoft Translator Hub használó egyéni fordítási rendszerek korlátozva, másodpercenként 1,800 karakter.

## <a name="latency"></a>Késés

Fordítói szöveg rendelkezik 13 másodperc maximális késését. Ez idő szerint fog kapott egy eredményt vagy időtúllépés választ. Általában válaszok visszatér 150 ezredmásodpercben 300 ezredmásodperc. Válaszidők a méretét vagy a kérelem és nyelvi pár alapján változhat.

## <a name="sentence-length-limits"></a>Mondat hosszának korlátozása

Használatakor a [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) függvény mondat hossza legfeljebb 275 karakter hosszúságú lehet. Ezen nyelvek kivételek vannak:

| Nyelv | Kód | Karakteres korlátot |
|----------|------|-----------------|
| kínai | zh | 132 |
| német | de | 290 |
| olasz | it | 280 |
| japán | ja | 150 |
| portugál | csendes-óceáni idő | 290 |
| spanyol | es | 280 |
| olasz | it | 280 |
| thai | . | 258 |

> [!NOTE]
> Ez a korlátozás nem vonatkozik a fordításokat.

## <a name="next-steps"></a>További lépések

* [Díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Régiónkénti rendelkezésre állás](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [a Translator Text API v3-leírás](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

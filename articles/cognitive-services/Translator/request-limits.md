---
title: Kérelmek korlátai – Translator Text API
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Translator Text API kérelmekre vonatkozó korlátozásait sorolja fel. A díjak a karakterek száma alapján merülnek fel, a kérelmek gyakorisága pedig a 5 000 karakteres korlátot kéri. A karakterkészletek az előfizetés-alapúak, a F0 pedig óránként 2 000 000 karakterre van korlátozva.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 79cf87cef4e58cf4e5a2039f30289d55038c8b92
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671963"
---
# <a name="request-limits-for-translator-text"></a>Kérelmekre vonatkozó korlátozások Translator Text

Ez a cikk a Translator Text API szabályozási korlátait tartalmazza. A szolgáltatások közé tartoznak a fordítás, az írás, a mondatok hosszának észlelése, a nyelvfelismerés és az alternatív fordítások.

## <a name="character-and-array-limits-per-request"></a>Karakter és tömb korlátja kérelmek szerint

Minden fordítási kérelem 5 000 karakterre van korlátozva. A díjat nem a kérelmek száma alapján számítjuk fel. A rövidebb kérelmek küldését javasoljuk.

A következő táblázat a Translator Text API egyes műveleteinek tömb elemeit és karakteres korlátait sorolja fel.

| Művelet | Tömb elemének maximális mérete |   Tömb elemeinek maximális száma |  Kérelmek maximális mérete (karakter) |
|:----|:----|:----|:----|
| Translate | 5,000 | 100   | 5,000 |
| Transliterate | 5,000 | 10    | 5,000 |
| Detect | 10,000 | 100 |   50,000 |
| BreakSentence | 10,000    | 100 | 50,000 |
| Keresés a szótárban| 100 |  10  | 1,000 |
| Szótár – példák | 100 szöveg és 100 fordításhoz (200 összesen)| 10|   2,000 |

## <a name="character-limits-per-hour"></a>Karakteres korlátok óránként

A karakteres korlát/óra a Translator Text előfizetési szintjétől függ. 

Az óránkénti kvótát egyenletesen kell használni az egész órában. Például a F0 küszöbértéke óránként 2 000 000 karakter, a karaktereket nem lehet kevesebb, mint durván 33 300 karakter/perc csúszó ablak (2 000 000 karakter, amely 60 perc).

Ha eléri vagy meghaladja ezeket a korlátokat, vagy a kvóta egy részének rövid időn belül túl nagy részét küldi el, valószínű, hogy a kvóta kiválasztva lesz. Az egyidejű kérések esetében nincsenek korlátok.

| Szint | Karakteres korlát |
|------|-----------------|
| F0 | 2 000 000 karakter/óra |
| S1 | 40 000 000 karakter/óra |
| S2/C2 | 40 000 000 karakter/óra |
| S3 / C3 | 120 000 000 karakter/óra |
| S4 / C4 | 200 000 000 karakter/óra |

A [több szolgáltatásra vonatkozó előfizetések](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) korlátai megegyeznek az S1 szinttel.

Ezek a korlátok a Microsoft szabványos fordítási modelljeire korlátozódnak. Az egyéni fordítót használó egyéni fordítási modellek másodpercenként 1 800 karakterre vannak korlátozva.

## <a name="latency"></a>Késés

A Translator Text API a standard modell használatával 15 másodperces késéssel rendelkezik. Az egyéni modellek használatával végzett fordítás legfeljebb 25 másodperces késéssel rendelkezik. Ekkor egy eredmény vagy időtúllépési válasz érkezett. A válaszokat általában 150 ezredmásodperc és 300 ezredmásodperc között adja vissza a rendszer. A válaszidő a kérelem és a nyelvi pár méretétől függően változhat. Ha nem kap fordítást vagy [hibaüzenetet](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) az adott időkereten belül, ellenőrizze a hálózati kapcsolatokat, és próbálkozzon újra.

## <a name="sentence-length-limits"></a>Mondat hosszának korlátai

A [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) függvény használatakor a mondat hossza 275 karakterre van korlátozva. Ezekhez a nyelvekhez kivételek tartoznak:

| Nyelv | Kód | Karakteres korlát |
|----------|------|-----------------|
| kínai | zh | 132 |
| német | de | 290 |
| olasz | it | 280 |
| japán | ja | 150 |
| portugál | pt | 290 |
| spanyol | es | 280 |
| olasz | it | 280 |
| thai | adik | 258 |

> [!NOTE]
> Ez a korlát nem vonatkozik a fordításokra.

## <a name="next-steps"></a>További lépések

* [Díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Régiónkénti rendelkezésre állás](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [v3 Translator Text API – dokumentáció](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

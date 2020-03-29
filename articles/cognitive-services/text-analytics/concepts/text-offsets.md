---
title: Szövegeltolások a Szövegelemzés i.
titleSuffix: Azure Cognitive Services
description: További információ a többnyelvű és emoji kódolások által okozott eltolásokról.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/09/2020
ms.author: aahi
ms.reviewer: jdesousa
ms.openlocfilehash: 6e404c710a244f06676edf50c3f5c95a7d681e35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219235"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Szövegeltolások a Text Analytics API kimenetében

A többnyelvű és emoji támogatás unicode kódolásokhoz vezetett, amelyek egynél több [kódpontot](https://wikipedia.org/wiki/Code_point) használnak egyetlen megjelenített karakter, az úgynevezett grapheme ábrázolására. Például az emojik, 👍 például 🌷, és több karaktert is használhatnak az alakzat összeállításához további karakterekkel a vizuális jellemzőkhöz, például a bőrtónushoz. Hasonlóképpen, a hindi szó `अनुच्छेद` öt betűként és három egyesítő jelként van kódolva.

A lehetséges többnyelvű és emoji kódolások különböző hossza miatt a Text Analytics API eltolásokat adhat vissza a válaszban.

## <a name="offsets-in-the-api-response"></a>Eltolások az API-válaszban. 

Amikor eltolásokat ad vissza az API-válasz, például [a Named Entity Recognition](../how-tos/text-analytics-how-to-entity-linking.md) vagy a Sentiment [Analysis,](../how-tos/text-analytics-how-to-sentiment-analysis.md)ne feledje a következőket:

* A válasz elemei a hívott végpontra jellemzőek lehetnek. 
* A HTTP POST/GET hasznos adatok [az UTF-8-ban](https://www.w3schools.com/charsets/ref_html_utf8.asp)vannak kódolva, ami lehet, hogy nem az alapértelmezett karakterkódolás az ügyféloldali fordítón vagy az operációs rendszeren.
* Az eltolások a [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) szabványon alapuló grapheme-számokra vonatkoznak, nem a karakterszámokra.

## <a name="extracting-substrings-from-text-with-offsets"></a>Részkarakterláncok kibontása eltolással rendelkező szövegből

Az eltolások problémákat okozhatnak a karakteralapú karakterlánc-alapú módszerek, például a .NET [részkarakterláncok()](https://docs.microsoft.com/dotnet/api/system.string.substring?view=netframework-4.8) metódus használatakor. Az egyik probléma az, hogy az eltolás hatására a részkarakterlánc-metódus végén a közepén egy többkarakteres grapheme kódolás helyett a végén.

A .NET-ben érdemes a [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) osztályt használni, amely lehetővé teszi, hogy egy karakterláncot szöveges elemek sorozataként dolgozzon, ne pedig egyedi karakterobjektumokat. A grapheme splitter könyvtárakat is megkeresheti a kívánt szoftverkörnyezetben. 

A Text Analytics API ezeket a szöveges elemeket is visszaadja, a kényelem érdekében.

## <a name="see-also"></a>Lásd még

* [Szövegelemzés – áttekintés](../overview.md)
* [Hangulatelemzés](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Entitások felismerése](../how-tos/text-analytics-how-to-entity-linking.md)
* [Nyelv felismerése](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Nyelvfelismerés](../how-tos/text-analytics-how-to-language-detection.md)

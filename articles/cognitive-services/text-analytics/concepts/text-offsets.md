---
title: Szöveg eltolása a Text Analytics APIban
titleSuffix: Azure Cognitive Services
description: A többnyelvű és Emoji kódolások által okozott eltolások ismertetése.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "79219235"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Szöveg eltolása a Text Analytics API kimenetében

A többnyelvű és Emoji támogatás olyan Unicode-kódolást eredményezett, amely egynél több [adatpontot](https://wikipedia.org/wiki/Code_point) használ egy megjelenített karakter (Graféma) megjelenítéséhez. Például a hangulatjelek, mint a 🌷 👍 , és több karaktert is használhatnak a vizuális attribútumok (például a bőr tónusa) további karakterekkel való összeállításához. Hasonlóképpen, a hindi szó `अनुच्छेद` öt betűt és három kombinációt jelöl.

A lehetséges többnyelvű és Emoji kódolások különböző hossza miatt a Text Analytics API a válaszban visszaadott eltolásokat adhat vissza.

## <a name="offsets-in-the-api-response"></a>Eltolások az API-válaszban. 

Ha az eltolásokat az API válasza adja vissza, például [elnevezett entitások felismerése](../how-tos/text-analytics-how-to-entity-linking.md) vagy [Hangulatelemzés](../how-tos/text-analytics-how-to-sentiment-analysis.md), jegyezze fel a következőket:

* A válasz elemei a hívott végpontra jellemzőek lehetnek. 
* A HTTP POST/GET adattartalmak [UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp)kódolással vannak kódolva, ami esetleg nem lehet az alapértelmezett karakterkódolás az ügyféloldali fordító vagy operációs rendszer esetében.
* Az eltolások a Graféma a [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) standard alapján, nem a karakterek számán alapulnak.

## <a name="extracting-substrings-from-text-with-offsets"></a>Alsztringek kibontása a szövegből eltolásokkal

Az eltolások problémákat okozhatnak a karakteres alsztring metódusok használatakor, például a .NET [alsztring ()](https://docs.microsoft.com/dotnet/api/system.string.substring?view=netframework-4.8) metódust. Az egyik probléma az, hogy az eltolás egy alkarakterlánc-metódus befejezését eredményezheti egy többkarakteres Graféma-kódolás közepén, a végpont helyett.

A .NET-ben érdemes lehet a [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) osztályt használni, amely lehetővé teszi, hogy egy karakterláncot használjon szöveges elemekként, nem pedig az egyes karakteres objektumokat. Az előnyben részesített szoftveres környezetben is megkeresheti a Graféma-elosztó kódtárait. 

A Text Analytics API ezeket a szöveges elemeket is visszaadja az egyszerűség kedvéért.

## <a name="see-also"></a>Lásd még

* [Text Analytics áttekintése](../overview.md)
* [Hangulatelemzés](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Entitások felismerése](../how-tos/text-analytics-how-to-entity-linking.md)
* [Nyelv felismerése](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Nyelvi felismerés](../how-tos/text-analytics-how-to-language-detection.md)

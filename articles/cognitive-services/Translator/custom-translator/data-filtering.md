---
title: Adatszűrés - Egyéni fordító
titleSuffix: Azure Cognitive Services
description: Amikor elküldi az egyéni rendszer betanításához használt dokumentumokat, a dokumentumok feldolgozási és szűrési lépések sorozatán mennek keresztül a betanításelőkészítése érdekében.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 1028443eaaf6c483cd7cd57289b0dcf2a9f11902
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595901"
---
# <a name="data-filtering"></a>Adatszűrés

Amikor elküldi az egyéni rendszer betanításához használt dokumentumokat, a dokumentumok feldolgozási és szűrési lépések sorozatán mennek keresztül a betanításelőkészítése érdekében. Ezeket a lépéseket itt ismertetem. A szűrés ismerete segíthet megérteni az egyéni fordítóban megjelenített mondatszámot, valamint azokat a lépéseket, amelyeket az egyéni fordítóval való képzéshez készíthet.

## <a name="sentence-alignment"></a>Mondatok igazítása
Ha a dokumentum nem XLIFF, TMX vagy ALIGN formátumban van, az Egyéni fordító mondatokat igazítja egymáshoz, mondatról mondatra. A fordító nem végez dokumentumigazítást – a dokumentumok elnevezését követi, hogy megtalálja a másik nyelv megfelelő dokumentumát. A dokumentumon belül az egyéni fordító megpróbálja megtalálni a megfelelő mondatot a másik nyelven. A dokumentumjelölést, például a beágyazott HTML-címkéket használja az igazítás elősegítésére.  

Ha nagy eltérést lát a forrás- és a céloldali dokumentumokban lévő mondatok száma között, előfordulhat, hogy a dokumentum nem volt párhuzamos, vagy más okból nem lehetett igazítani. A dokumentum nagy különbséggel (>10%) a mondatok mindkét oldalon indokolja a második pillantást, hogy megbizonyosodjon arról, hogy valóban párhuzamos. Egyéni fordító egy figyelmeztetést jelenít meg a dokumentum mellett, ha a mondatszám gyanúsan eltér.  


## <a name="deduplication"></a>Deduplikáció
Egyéni fordító eltávolítja a mondatokat, amelyek jelen vannak a vizsgálati és hangolási dokumentumok betanítási adatokból.Az eltávolítás dinamikusan történik a betanítási futtatáson belül, nem az adatfeldolgozási lépésben. Egyéni fordító jelenti a mondat számít, hogy a projekt áttekintése az eltávolítás előtt.  

## <a name="length-filter"></a>Hosszszűrő
* Távolítsa el azokat a mondatokat, amelyek mindkét oldalán csak egy szó van.
* Távolítsa el azokat a mondatokat, amelyek mindkét oldalán több mint 100 szó van.A kínaiak, a japánok, a koreaiak mentességet élveznek.
* A 3 karakternél rövidebb mondatokat távolítsa el. A kínaiak, a japánok, a koreaiak mentességet élveznek.
* Távolítsa el a 2000 karakternél nagyobb karaktereket felszolgáló mondatokat a kínai, a japán, a koreai nyelv esetében.
* Távolítsa el az 1%-nál kisebb alfa karaktereket.
* Az 50 szónál többet tartalmazó szótárbejegyzések eltávolítása.

## <a name="white-space"></a>Szóköz
* Cserélje le a szóközkarakterek tetszőleges sorozatát, beleértve a tabulátorokat és a CR/LF szekvenciákat is, egyetlen szóközkarakterre.
* A sortávolság vagy a záró szóköz eltávolítása a mondatból

## <a name="sentence-end-punctuation"></a>Mondat záró írásjelek
Cseréljen le több mondatvégi írásjeleket egyetlen példányra.  

## <a name="japanese-character-normalization"></a>Japán karakter normalizálása
A teljes szélességű betűket és számjegyeket félszélességű karakterekké alakíthatja.

## <a name="unescaped-xml-tags"></a>Nem sérült XML-címkék
A szűrés a nem megadott címkéket escaped címkékké alakítja át:
* `&lt;`Lesz`&amp;lt;`
* `&gt;`Lesz`&amp;gt;`
* `&amp;`Lesz`&amp;amp;`

## <a name="invalid-characters"></a>Érvénytelen karakterek
Az Egyéni fordító eltávolítja az U+FFFD Unicode karaktert tartalmazó mondatokat. Az U+FFFD karakter sikertelen kódolási átalakítást jelez.

## <a name="next-steps"></a>További lépések

- [Modell betanítása](how-to-train-model.md) az egyéni fordítóban.

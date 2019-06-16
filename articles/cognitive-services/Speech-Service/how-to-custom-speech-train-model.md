---
title: A modell betanítását egyéni beszéd – beszédszolgáltatások
titlesuffix: Azure Cognitive Services
description: Egy hang-szöveg transzformációs képzési szükség, mindkét a Microsoft baseline modell, vagy szeretné létrehozni egy egyéni modell pontosságának javítása érdekében. A modell tanítása emberi címkével beszédátírás és a kapcsolódó szövegmezőbe. Ezek az adatkészletek korábban feltöltött hívásaiból, valamint pontosíthatja vagy szavak, kifejezések, betűszavakat, nevek és más termékkel kapcsolatos használati felismerni a hang-szöveg transzformációs modell betanítását szolgálnak.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: e12cef052db6aabad94b47283eda11f60f3b2b13
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063922"
---
# <a name="train-a-model-for-custom-speech"></a>A modell betanítását egyéni beszéd

Egy hang-szöveg transzformációs képzési szükség, mindkét a Microsoft baseline modell, vagy szeretné létrehozni egy egyéni modell pontosságának javítása érdekében. A modell tanítása emberi címkével beszédátírás és a kapcsolódó szövegmezőbe. Ezek az adatkészletek korábban feltöltött hívásaiból, valamint pontosíthatja vagy szavak, kifejezések, betűszavakat, nevek és más termékkel kapcsolatos használati felismerni a hang-szöveg transzformációs modell betanítását szolgálnak. A több-tartományban található adatkészleteknek biztosító (kapcsolódó adatok felhasználók tudatja, és várhatóan felismerni), a pontosabb a modell lesz, továbbfejlesztett felismerés eredményez. Ne feledje, hogy számból kapcsolódó adatok egyetlen, a képzés, csökkentheti vagy csökkentheti a modell pontosságát.

## <a name="use-training-to-resolve-accuracy-issues"></a>Használja a betanítási pontossága problémák megoldása

Ha lépnek problémák felismerése a modellt, emberi címkével használatával szövegekben és további kapcsolódó adatok segítségével pontosságának növelése érdekében. Ez a táblázat segítségével mely adatkészlet használatával oldja meg a probléma meghatározásához:

| Használati eset | Adattípus | Adatok mennyisége |
|----------|-----------|---------------|
| Pontos nevét a rendszer kifejezést | Kapcsolódó szöveg (mondatokat/utterances) | 10 MB – 500 MB |
| Szavak vannak kifejezést egy hangsúlyos miatt | Kapcsolódó szöveg (írásmódja) | Adja meg a misrecognized szavakat |
| Gyakori szavakat törölt vagy kifejezést | Hang + emberi címkével szövegekben | 10-es, 1000 beszédátírási óra |

> [!IMPORTANT]
> Ha még nem töltött fel egy adatkészletet, tekintse meg [előkészítése és a Tesztadatok](how-to-custom-speech-test-data.md). Ez a dokumentum útmutatást fel az adatokat, és kiváló minőségű adatkészleteket létrehozására vonatkozó irányelveket.

## <a name="train-and-evaluate-a-model"></a>Betanítása és kiértékelése modell

A modell betanításához az első lépés, hogy töltse fel a betanítási adatok. Használja [előkészítése és a Tesztadatok](how-to-custom-speech-test-data.md) a részletes készíti elő az emberi címkével beszédátírás és a kapcsolódó szöveges (utterances és kiejtés). Betanítási adatok feltöltése után indítsa el a modell tanítása az alábbi lépésekkel:

1. Navigáljon a **hang-szöveg transzformációs > Custom Speech > képzési**.
2. Kattintson a **tanítási modell**.
3. Ezután adjon a tanítási egy **neve** és **leírás**.
4. Az a **a forgatókönyv és a Referenciakonfiguráció modell** legördülő menüben válassza ki a forgatókönyvet, amely legjobban megfelel a tartományban. Ha biztos benne, hogy mely forgatókönyv kiválasztása, válassza ki a **általános**. A modell szolgál kiindulópontként a betanításhoz. Ha nem rendelkezik megfelelő módot, a legújabb is használhatja.
5. Az a **válassza ki a betanítási adatok** lapon a betanítási használni kívánt egy vagy több hang + emberi címkével beszédátírási adatkészletek.
6. A betanítási befejeződése után lehet váltani, hajtsa végre a tesztelést az újonnan betanított modell pontossága. Ez a lépés nem kötelező.
7. Válassza ki **létrehozás** a egyéni modell létrehozásához.

A képzési tábla egy új bejegyzést, amely megfelel az újonnan létrehozott modell jeleníti meg. A tábla állapota is látható:  Feldolgozás, a sikeres, sikertelen.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>A betanított modell pontosságát kiértékelése

Vizsgálja meg az adatokat, és használja ezeket a dokumentumokat modellpontosságból kiértékelése:

* [Az adatok vizsgálata](how-to-custom-speech-inspect-data.md)
* [Az adatok](how-to-custom-speech-evaluate-data.md)


Ha úgy döntött, hogy tesztelje a pontosság, fontos egy eltérő valósághű megtapasztalhatja a modell teljesítményét, hogy a modell használt akusztikai adatkészlet kiválasztása.

## <a name="next-steps"></a>További lépések

* [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>További források

* [Készítse elő és az adatok tesztelése](how-to-custom-speech-test-data.md)
* [Az adatok vizsgálata](how-to-custom-speech-inspect-data.md)
* [Az adatok](how-to-custom-speech-evaluate-data.md)
* [A modell tanítása](how-to-custom-speech-train-model.md)

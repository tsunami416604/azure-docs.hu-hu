---
title: Modell betanítása egyéni beszédfelismeréshez – Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszéd-szöveg modell betanítása javíthatja a Microsoft alapmodellének vagy egyéni modelljének felismerési pontosságát. A modell emberi címkével ellátott átiratok és a kapcsolódó szöveg használatával van betanítva.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: a2bc39a35299f56ba52a0143ce123560bd4d88fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77137774"
---
# <a name="train-a-model-for-custom-speech"></a>Modell betanítása egyéni beszédhez

A beszéd-szöveg modell betanítása javíthatja a Microsoft alapmodelljének felismerési pontosságát. A modell emberi címkével ellátott átiratok és a kapcsolódó szöveg használatával van betanítva. Ezek az adatkészletek a korábban feltöltött hangadatokkal együtt a szavak, kifejezések, rövidítések, nevek és egyéb termékspecifikus kifejezések felismerésére szolgálnak a szöveggé alakítási modell finomítására és betanítására. Minél több tartományon belüli adatkészletet ad meg (olyan adatokat, amelyek kapcsolódnak ahhoz, amit a felhasználók mondanak és amelyeket várhatóan felismernek), annál pontosabb lesz a modell, ami jobb felismerést eredményez. Ne feledje, hogy a nem kapcsolódó adatok betanításával csökkentheti vagy megsérülhet a modell pontosságát.

## <a name="use-training-to-resolve-accuracy-issues"></a>A pontossági problémák megoldásához használjon oktatást

Ha felismerési problémákat tapasztal a modellel kapcsolatban, az emberi címkével ellátott átiratok és a kapcsolódó adatok további betanításhoz való használata segíthet a pontosság javításában. Ebben a táblában határozhatja meg, hogy melyik adatkészletet használja a probléma(ok) megoldásához:

| Használati eset | Adattípus |
| -------- | --------- |
| Javíthatja a felismerés pontosságát az iparág-specifikus szókincsés nyelvtan, például az orvosi terminológia vagy az informatikai zsargon. | Kapcsolódó szöveg (mondatok/kimondott szövegek) |
| Adja meg a nem szabványos kiejtéssel rendelkező szó vagy kifejezés fonetikus és megjelenített formáját, például a termékneveket vagy a betűszavakat. | Kapcsolódó szöveg (kiejtés) |
| Javíthatja a felismerés pontosságát a beszédstílusokon, az ékezetes stílusokon vagy az adott háttérzajokon. | Audio + emberi címkével ellátott átiratok |

> [!IMPORTANT]
> Ha még nem töltött fel adatkészletet, olvassa el [Az adatok előkészítése és tesztelése](how-to-custom-speech-test-data.md). Ez a dokumentum utasításokat tartalmaz az adatok feltöltéséhez, és útmutatást nyújt a kiváló minőségű adatkészletek létrehozásához.

## <a name="train-and-evaluate-a-model"></a>Modell betanítása és értékelése

A modell betanításának első lépése a betanítási adatok feltöltése. [Az Adatok előkészítése és tesztelése](how-to-custom-speech-test-data.md) segítségével lépésről lépésre előkészítheti az emberi címkével ellátott átiratokat és a kapcsolódó szövegeket (kimondott szövegeket és kiejtéseket). A betanítási adatok feltöltése után kövesse az alábbi utasításokat a modell betanításának megkezdéséhez:

1. Jelentkezzen be az [Egyéni beszédportálra.](https://speech.microsoft.com/customspeech)
2. Nyissa meg a **beszédfelismerést > egyéni beszédfelismerési > oktatást.**
3. Kattintson **a Vonat modell gombra.**
4. Ezután adjon nevet **és** leírást a **képzésnek.**
5. A Forgatókönyv és az **Alapmodell legördülő** menüből válassza ki azt a forgatókönyvet, amely a legjobban megfelel a tartományának. Ha nem biztos abban, hogy melyik forgatókönyvet válassza, válassza az **Általános**lehetőséget. Az alapmodell a betanítás kiindulópontja. Ha nincs preferenciája, használhatja a legújabbat.
6. A **Betanítási adatok kiválasztása** lapon válasszon ki egy vagy több hang + emberi címkével ellátott átírási adatkészleteket, amelyeket betanításhoz szeretne használni.
7. Miután a betanítás befejeződött, kiválaszthatja, hogy az újonnan betanított modell pontossági tesztelése. Ez a lépés nem kötelező.
8. Az egyéni modell létrehozásához válassza a **Létrehozás** lehetőséget.

A Betanítás tábla egy új bejegyzést jelenít meg, amely megfelel ennek az újonnan létrehozott modellnek. A tábla a következő állapotot is megjeleníti: Feldolgozás, Sikeres, Sikertelen.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>A betanított modell pontosságának kiértékelése

Az alábbi dokumentumok segítségével megvizsgálhatja az adatokat, és kiértékelheti a modell pontosságát:

- [Az adatok vizsgálata](how-to-custom-speech-inspect-data.md)
- [Az adatok kiértékelése](how-to-custom-speech-evaluate-data.md)

Ha úgy döntött, hogy teszteli a pontosságot, fontos, hogy válasszon ki egy akusztikai adatkészletet, amely eltér a modell használt a modell reális értelemben a modell teljesítményét.

## <a name="next-steps"></a>További lépések

- [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>További források

- [Az adatok előkészítése és tesztelése](how-to-custom-speech-test-data.md)
- [Az adatok vizsgálata](how-to-custom-speech-inspect-data.md)
- [Az adatok kiértékelése](how-to-custom-speech-evaluate-data.md)
- [A modell betanítása](how-to-custom-speech-train-model.md)

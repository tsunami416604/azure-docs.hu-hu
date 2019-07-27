---
title: Modell tanítása Custom Speech-Speech Service-hez
titleSuffix: Azure Cognitive Services
description: A Microsoft alapmodelljének vagy a létrehozni kívánt egyéni modellnek a felismerési pontosságának javítása érdekében a beszédfelismerési szöveg betanítása szükséges. A modell emberi címkével ellátott átírásokkal és kapcsolódó szöveggel van betanítva. Ezek az adatkészletek a korábban feltöltött hangadatokkal együtt a beszéd-szöveg modell pontosítására és betanítására szolgálnak, hogy felismerjék a szavakat, kifejezéseket, betűszókat, neveket és más termékspecifikus kifejezéseket.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: b5893b4f07444b07bf142971a5df4776e549d307
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562813"
---
# <a name="train-a-model-for-custom-speech"></a>Custom Speech modell betanítása

A Microsoft alapmodelljének vagy a létrehozni kívánt egyéni modellnek a felismerési pontosságának javítása érdekében a beszédfelismerési szöveg betanítása szükséges. A modell emberi címkével ellátott átírásokkal és kapcsolódó szöveggel van betanítva. Ezek az adatkészletek a korábban feltöltött hangadatokkal együtt a beszéd-szöveg modell pontosítására és betanítására szolgálnak, hogy felismerjék a szavakat, kifejezéseket, betűszókat, neveket és más termékspecifikus kifejezéseket. Minél több tartományi adatkészletet ad meg (amelyek a felhasználók által kimondott és a várhatóan felismert adatokkal kapcsolatosak), annál pontosabbak lesznek a modell, ami jobb felismerést eredményez. Ne feledje, hogy a nem kapcsolódó adatoknak a képzésbe való etetésével csökkentheti vagy megsértheti a modell pontosságát.

## <a name="use-training-to-resolve-accuracy-issues"></a>A pontossággal kapcsolatos problémák megoldása a képzés használatával

Ha a modell felismerésével kapcsolatos problémákat tapasztal, az emberi címkével ellátott átiratok és a további képzéshez kapcsolódó adatok használatával javíthatja a pontosságot. A táblázat segítségével meghatározhatja, hogy melyik adatkészletet használja a probléma (ok) kezelésére:

| Használati eset | Adattípus |
|----------|-----------|
| Az iparág specifikus szókincsének és nyelvhelyességének, például az orvosi terminológia vagy az informatikai szakzsargonnak az ismertségének javítása | Kapcsolódó szöveg (mondat/hosszúságú kimondott szöveg) |
| Definiálja egy olyan szó vagy kifejezés fonetikus és megjelenített formáját, amely nem szabványos kiejtéssel rendelkezik, például Terméknév vagy betűszó. | Kapcsolódó szöveg (kiejtés) |
| Az elismerés pontosságának javítása a beszélő stílusok, hangsúlyozások vagy konkrét háttérzajok esetén | Hang + emberi – címkézett átiratok |
> [!IMPORTANT]
> Ha még nem töltött fel adathalmazt, olvassa el [az adatelőkészítés és-tesztelés](how-to-custom-speech-test-data.md)című témakört. Ez a dokumentum útmutatást nyújt az adatfeltöltéshez és a kiváló minőségű adatkészletek létrehozásához szükséges irányelvekhez.

## <a name="train-and-evaluate-a-model"></a>Modell betanítása és kiértékelése

A modellek betanításának első lépése a betanítási adatok feltöltése. Az emberi címkével ellátott átiratok és a kapcsolódó szövegek (hosszúságú kimondott szöveg és kiejtések) előkészítéséhez használja az [előkészítés és a tesztelés](how-to-custom-speech-test-data.md) részletes útmutatását. A betanítási adatai feltöltése után kövesse az alábbi utasításokat a modell képzésének megkezdéséhez:

1. Navigáljon a **beszéd-szöveg > Custom Speech > képzésre**.
2. Kattintson a **tanítási modell**elemre.
3. Ezután adja meg a betanítás **nevét** és **leírását**.
4. A **forgatókönyv és az alapterv modell** legördülő menüben válassza ki a tartományhoz legjobban illő forgatókönyvet. Ha nem biztos abban, hogy melyik forgatókönyvet szeretné kiválasztani, válassza az **általános**lehetőséget. Az alapmodell a betanítás kiindulási pontja. Ha nem rendelkezik a beállításokkal, a legújabbat használhatja.
5. A betanítási **adatok kiválasztása** lapon válasszon ki egy vagy több hang + emberi címkével ellátott átírási adatkészletet, amelyeket képzésre szeretne használni.
6. A képzés befejezését követően dönthet úgy, hogy pontossági tesztelést végez az újonnan betanított modellen. Ez a lépés nem kötelező.
7. Válassza a **Létrehozás** lehetőséget az egyéni modell kiépítéséhez.

A betanítási táblázat egy új bejegyzést jelenít meg, amely megfelel az újonnan létrehozott modellnek. A tábla az állapotot is megjeleníti:  A feldolgozás, sikeres, sikertelen.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>Betanított modell pontosságának kiértékelése

Az adatok megvizsgálása és a modell pontosságának kiértékelése a következő dokumentumokkal végezhető el:

* [Az adatai ellenőrzése](how-to-custom-speech-inspect-data.md)
* [Az adatai kiértékelése](how-to-custom-speech-evaluate-data.md)


Ha a pontosság tesztelését választotta, fontos, hogy olyan akusztikai adatkészletet válasszon ki, amely eltér a modell teljesítményének reális értelmezéséhez.

## <a name="next-steps"></a>További lépések

* [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>További források

* [Az adatfeldolgozás előkészítése és tesztelése](how-to-custom-speech-test-data.md)
* [Az adatai ellenőrzése](how-to-custom-speech-inspect-data.md)
* [Az adatai kiértékelése](how-to-custom-speech-evaluate-data.md)
* [A modell betanítása](how-to-custom-speech-train-model.md)

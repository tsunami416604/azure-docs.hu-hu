---
title: Modell tanítása Custom Speech-Speech Service-hez
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési modell betanítása javíthatja a Microsoft alapmodelljét vagy egyéni modelljét. A modell emberi címkével ellátott átírásokkal és kapcsolódó szöveggel van betanítva.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 7630659deeece7fbf8d0ca1fd00b539a8de83b0e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072497"
---
# <a name="train-a-model-for-custom-speech"></a>Custom Speech modell betanítása

A beszédfelismerési modell betanítása javíthatja a Microsoft alapmodellje vagy a létrehozni kívánt egyéni modell felismerési pontosságát. A modell emberi címkével ellátott átírásokkal és kapcsolódó szöveggel van betanítva. Ezek az adatkészletek a korábban feltöltött hangadatokkal együtt a beszéd-szöveg modell pontosítására és betanítására szolgálnak, hogy felismerjék a szavakat, kifejezéseket, betűszókat, neveket és más termékspecifikus kifejezéseket. Minél több tartományi adatkészletet ad meg (amelyek a felhasználók által kimondott és a várhatóan felismert adatokkal kapcsolatosak), annál pontosabbak lesznek a modell, ami jobb felismerést eredményez. Ne feledje, hogy a nem kapcsolódó adatoknak a képzésbe való etetésével csökkentheti vagy megsértheti a modell pontosságát.

## <a name="use-training-to-resolve-accuracy-issues"></a>A pontossággal kapcsolatos problémák megoldása a képzés használatával

Ha a modell felismerésével kapcsolatos problémákat tapasztal, az emberi címkével ellátott átiratok és a további képzéshez kapcsolódó adatok használatával javíthatja a pontosságot. A táblázat segítségével meghatározhatja, hogy melyik adatkészletet használja a probléma (ok) kezelésére:

| Használati eset | Data type |
| -------- | --------- |
| Az iparágra jellemző szókincs és nyelvtan – például az orvosi terminológia vagy az informatikai szakzsargon – jobb felismerési pontosságának javítása. | Kapcsolódó szöveg (mondat/hosszúságú kimondott szöveg) |
| Definiálja egy olyan szó vagy kifejezés fonetikus és megjelenített formáját, amely nem szabványos kiejtéssel rendelkezik, például Terméknév vagy betűszó. | Kapcsolódó szöveg (kiejtés) |
| Az elismerés pontosságának javítása a beszélő stílusok, hangsúlyozások vagy konkrét háttérzajok esetében. | Hang + emberi – címkézett átiratok |

> [!IMPORTANT]
> Ha még nem töltött fel adathalmazt, olvassa el [az adatelőkészítés és-tesztelés](how-to-custom-speech-test-data.md)című témakört. Ez a dokumentum útmutatást nyújt az adatfeltöltéshez és a kiváló minőségű adatkészletek létrehozásához szükséges irányelvekhez.

## <a name="train-and-evaluate-a-model"></a>Modell betanítása és kiértékelése

A modellek betanításának első lépése a betanítási adatok feltöltése. Az emberi címkével ellátott átiratok és a kapcsolódó szövegek (hosszúságú kimondott szöveg és kiejtések) előkészítéséhez használja az [előkészítés és a tesztelés](how-to-custom-speech-test-data.md) részletes útmutatását. A betanítási adatai feltöltése után kövesse az alábbi utasításokat a modell képzésének megkezdéséhez:

1. Jelentkezzen be a [Custom Speech portálra](https://speech.microsoft.com/customspeech).
2. Navigáljon a **beszéd-szöveg > Custom Speech > képzésre**.
3. Kattintson a **tanítási modell**elemre.
4. Ezután adja meg a betanítás **nevét** és **leírását**.
5. A **forgatókönyv és az alapterv modell** legördülő menüben válassza ki a tartományhoz legjobban illő forgatókönyvet. Ha nem biztos abban, hogy melyik forgatókönyvet szeretné kiválasztani, válassza az **általános**lehetőséget. Az alapmodell a betanítás kiindulási pontja. Ha nem rendelkezik a beállításokkal, a legújabbat használhatja.
6. A **betanítási adatok kiválasztása** lapon válasszon ki egy vagy több hang + emberi címkével ellátott átírási adatkészletet, amelyeket képzésre szeretne használni.
7. A képzés befejezését követően dönthet úgy, hogy pontossági tesztelést végez az újonnan betanított modellen. Ez a lépés nem kötelező.
8. Válassza a **Létrehozás** lehetőséget az egyéni modell kiépítéséhez.

A betanítási táblázat egy új bejegyzést jelenít meg, amely megfelel az újonnan létrehozott modellnek. A tábla a következő állapotot is megjeleníti: feldolgozás, sikeres, sikertelen.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>Betanított modell pontosságának kiértékelése

Az adatok megvizsgálása és a modell pontosságának kiértékelése a következő dokumentumokkal végezhető el:

- [Az adatai ellenőrzése](how-to-custom-speech-inspect-data.md)
- [Az adatai kiértékelése](how-to-custom-speech-evaluate-data.md)

Ha a pontosság tesztelését választotta, fontos, hogy olyan akusztikai adatkészletet válasszon ki, amely eltér a modell teljesítményének reális értelmezéséhez.

## <a name="next-steps"></a>További lépések

- [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>További források

- [Az adatfeldolgozás előkészítése és tesztelése](how-to-custom-speech-test-data.md)
- [Az adatai ellenőrzése](how-to-custom-speech-inspect-data.md)
- [Az adatai kiértékelése](how-to-custom-speech-evaluate-data.md)
- [A modell betanítása](how-to-custom-speech-train-model.md)

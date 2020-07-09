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
ms.openlocfilehash: bf9209e0c256412ccb06ea62a197046a7b012e00
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84629020"
---
# <a name="train-a-model-for-custom-speech"></a>Modell betanítása a Custom Speech szolgáltatáshoz

A beszédfelismerési modell betanítása javíthatja a Microsoft alapmodelljének felismerési pontosságát. A modell emberi címkével ellátott átírásokkal és kapcsolódó szöveggel van betanítva. Ezek az adatkészletek a korábban feltöltött hangadatokkal együtt a beszéd-szöveg modell pontosítására és betanítására szolgálnak.

## <a name="use-training-to-resolve-accuracy-issues"></a>A pontossággal kapcsolatos problémák megoldása a képzés használatával

Ha a modell felismerésével kapcsolatos problémákat tapasztal, az emberi címkével ellátott átiratok és a további képzéshez kapcsolódó adatok használatával javíthatja a pontosságot. A táblázat segítségével meghatározhatja, hogy melyik adatkészletet használja a probléma (ok) kezelésére:

| Használati eset | Adattípus |
| -------- | --------- |
| Az iparágra jellemző szókincs és nyelvtan – például az orvosi terminológia vagy az informatikai szakzsargon – jobb felismerési pontosságának javítása. | Kapcsolódó szöveg (mondat/hosszúságú kimondott szöveg) |
| Definiálja egy olyan szó vagy kifejezés fonetikus és megjelenített formáját, amely nem szabványos kiejtéssel rendelkezik, például Terméknév vagy betűszó. | Kapcsolódó szöveg (kiejtés) |
| Az elismerés pontosságának javítása a beszélő stílusok, hangsúlyozások vagy konkrét háttérzajok esetében. | Hang + emberi – címkézett átiratok |

> [!IMPORTANT]
> Ha még nem töltött fel adathalmazt, olvassa el [az adatelőkészítés és-tesztelés](how-to-custom-speech-test-data.md)című témakört. Ez a dokumentum útmutatást nyújt az adatfeltöltéshez és a kiváló minőségű adatkészletek létrehozásához szükséges irányelvekhez.

## <a name="train-and-evaluate-a-model"></a>Modell betanítása és kiértékelése

A modellek betanításának első lépése a betanítási adatok feltöltése. Az emberi címkével ellátott átiratok és a kapcsolódó szövegek (hosszúságú kimondott szöveg és kiejtések) előkészítéséhez használja az [előkészítés és a tesztelés](how-to-custom-speech-test-data.md) részletes útmutatását. A betanítási adatai feltöltése után kövesse az alábbi utasításokat a modell képzésének megkezdéséhez:

1. Jelentkezzen be a [Custom Speech portálra](https://speech.microsoft.com/customspeech).
2. Navigáljon a **beszéd – szöveg > Custom Speech > [projekt neve] > képzés**elemre.
3. Kattintson a **tanítási modell**elemre.
4. Ezután adja meg a betanítás **nevét** és **leírását**.
5. A **forgatókönyv és az alapterv modell** legördülő menüben válassza ki a tartományhoz legjobban illő forgatókönyvet. Ha nem biztos abban, hogy melyik forgatókönyvet szeretné kiválasztani, válassza az **általános**lehetőséget. Az alapmodell a betanítás kiindulási pontja. A legújabb modell általában a legjobb választás.
6. A **betanítási adatok kiválasztása** lapon válasszon ki egy vagy több hang + emberi címkével ellátott átírási adatkészletet, amelyeket képzésre szeretne használni.
7. A képzés befejezését követően dönthet úgy, hogy pontossági tesztelést végez az újonnan betanított modellen. Ez a lépés nem kötelező.
8. Válassza a **Létrehozás** lehetőséget az egyéni modell kiépítéséhez.

A betanítási táblázat egy új bejegyzést jelenít meg, amely megfelel az újonnan létrehozott modellnek. A tábla a következő állapotot is megjeleníti: feldolgozás, sikeres, sikertelen.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>Betanított modell pontosságának kiértékelése

Az adatok megvizsgálása és a modell pontosságának kiértékelése a következő dokumentumokkal végezhető el:

- [Az adatai ellenőrzése](how-to-custom-speech-inspect-data.md)
- [Az adatai kiértékelése](how-to-custom-speech-evaluate-data.md)

Ha a pontosság tesztelését választotta, fontos, hogy olyan akusztikai adatkészletet válasszon ki, amely eltér a modell teljesítményének reális értelmezéséhez.

## <a name="next-steps"></a>Következő lépések

- [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>További források

- [Az adatfeldolgozás előkészítése és tesztelése](how-to-custom-speech-test-data.md)
- [Az adatai ellenőrzése](how-to-custom-speech-inspect-data.md)
- [Az adatai kiértékelése](how-to-custom-speech-evaluate-data.md)
- [A modell betanítása](how-to-custom-speech-train-model.md)

---
title: Custom Speech beszédfelismerési szolgáltatás pontosságának kiértékelése
titleSuffix: Azure Cognitive Services
description: Ebből a dokumentumból megtudhatja, hogyan mérhető a beszédfelismerési modell vagy az egyéni modell minősége. A hang-és az emberi-címkével ellátott átírási adatoknak a pontosság teszteléséhez, valamint 30 perc és 5 órányi reprezentatív hang megadása szükséges.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: f710b8bfdd4dcfd3b7a63aa0b457036ab7037016
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806096"
---
# <a name="evaluate-custom-speech-accuracy"></a>Custom Speech pontosságának kiértékelése

Ebből a dokumentumból megtudhatja, hogyan mérhető a Microsoft beszédfelismerési modellje és az egyéni modell minősége. A hang-és az emberi-címkével ellátott átírási adatoknak a pontosság teszteléséhez, valamint 30 perc és 5 órányi reprezentatív hang megadása szükséges.

## <a name="what-is-word-error-rate-wer"></a>Mi az a Word Error Rate (WER)?

A modell pontosságának méréséhez használt iparági szabvány a *Word Error Rate* (WER). A WER megszámolja az elismerés során azonosított helytelen szavak számát, majd az emberi címkével ellátott átiratban szereplő szavak teljes számával elosztja. Végezetül ezt a számot 100%-kal kell megszorozni a WER kiszámításához.

![WER-képlet](./media/custom-speech/custom-speech-wer-formula.png)

A helytelenül azonosított szavak három kategóriába sorolhatók:

* Beszúrás (I): helytelenül hozzáadott szavak a hipotézis átiratában
* Törlés (D): a hipotézis átiratában nem észlelhető szavak
* Helyettesítő (k): a hivatkozás és a hipotézis között helyettesített szavak

Például:

![Helytelenül azonosított szavak – példa](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Hibák elhárítása és a WER fejlesztése

Az alkalmazással, eszközzel vagy termékkel használt modell minőségének kiértékeléséhez a számítógép-felismerési eredményekből használhatja a WERt. Az 5%-10%-os WER jó minőségűnek számít, és készen áll a használatra. 20%-os WER elfogadható, azonban érdemes lehet további képzést is megfontolni. A WER 30%-os vagy újabb gyenge minőséggel rendelkezik, és testreszabást és képzést igényel.

Fontos a hibák elosztása. Ha a rendszer sok törlési hibát észlel, általában a gyenge hangjelek erőssége miatt. A probléma megoldásához a hangadatokat közelebb kell gyűjteni a forráshoz. A beillesztési hibák azt jelentik, hogy a hang zajos környezetben lett rögzítve, és lehetséges, hogy az áthallási problémákat okoz. A helyettesítési hibák akkor fordulnak elő, ha a tartományhoz tartozó feltételek nem elégséges mintája emberi feliratú átiratként vagy kapcsolódó szövegként van megadva.

Az egyes fájlok elemzésével meghatározhatja, hogy milyen típusú hibák léteznek, és mely hibák egyediek egy adott fájlra vonatkozóan. A fájlok szintjén megjelenő problémák segítenek megcélozni a fejlesztési funkciókat.

## <a name="create-a-test"></a>Teszt létrehozása

Ha szeretné kipróbálni a Microsoft beszéd-szöveg típusú alapmodelljét vagy egy Ön által betanított egyéni modellt, összehasonlíthatja a két modellt egymás mellett, hogy kiértékelje a pontosságot. Az összehasonlítás magában foglalja a WER és a felismerés eredményeit. Az egyéni modelleket általában a Microsoft alapmodellje hasonlítja össze.

Modellek kiértékelése egymás mellett:

1. Jelentkezzen be a [Custom Speech portálra](https://speech.microsoft.com/customspeech).
2. Navigáljon a **beszéd-szöveg > Custom Speech > teszteléshez**.
3. Kattintson a **teszt hozzáadása**gombra.
4. Válassza ki a **pontosság kiértékelése**elemet. Adja meg a teszt nevét, leírását, és válassza ki a hang + emberi-címkével ellátott átírási adatkészletet.
5. Válasszon legfeljebb két modellt, amelyeket szeretne tesztelni.
6. Kattintson a  **Create** (Létrehozás) gombra.

A teszt sikeres létrehozása után összehasonlíthatja az eredményeket egymás mellett.

## <a name="side-by-side-comparison"></a>Párhuzamos összehasonlítás

A teszt befejezése után az állapot változása *sikeres*volt, a tesztben szereplő mindkét modellhez meg kell adni egy wer-számot. Kattintson a teszt nevére a tesztelési részletek oldal megtekintéséhez. Ez a részletes lap felsorolja az adatkészlet összes hosszúságú kimondott szöveg, amely a két modell felismerési eredményét jelzi a beküldött adatkészlet átírása mellett. Az egymás melletti összehasonlítások megvizsgálása érdekében különböző típusú hibákat válthat ki, beleértve a beszúrást, a törlést és a helyettesítést is. Ha az egyes oszlopokban figyeli a hangfelismerési eredményeket, és összehasonlítja az összes olyan oszlopot, amely az emberi címkével ellátott átírást és az eredményeket mutatja két beszéd – szöveg modell esetében, eldöntheti, hogy melyik modell felel meg az igényeinek, és hogy a további képzések és Újdonságok szükséges.

## <a name="next-steps"></a>Következő lépések

* [A modell betanítása](how-to-custom-speech-train-model.md)
* [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>További források

* [Az adatfeldolgozás előkészítése és tesztelése](how-to-custom-speech-test-data.md)
* [Az adatai ellenőrzése](how-to-custom-speech-inspect-data.md)

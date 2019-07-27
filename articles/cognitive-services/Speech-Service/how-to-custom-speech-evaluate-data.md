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
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: bd8bbc28247ecd924db25cb4b916d1d466065606
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562872"
---
# <a name="evaluate-custom-speech-accuracy"></a>Custom Speech pontosságának kiértékelése

Ebből a dokumentumból megtudhatja, hogyan mérhető a Microsoft beszédfelismerési modellje és az egyéni modell minősége. A hang-és az emberi-címkével ellátott átírási adatoknak a pontosság teszteléséhez, valamint 30 perc és 5 órányi reprezentatív hang megadása szükséges.

## <a name="what-is-word-error-rate-wer"></a>Mi az a Word Error Rate (WER)?

A modell pontosságának méréséhez használt iparági szabvány a *Word Error Rate* (WER). A WER megszámolja az elismerés során azonosított helytelen szavak számát, majd az emberi címkével ellátott átiratban szereplő szavak teljes számával elosztja. Végezetül ezt a számot 100%-kal kell megszorozni a WER kiszámításához.

![WER-képlet](./media/custom-speech/custom-speech-wer-formula.png)

A helytelenül azonosított szavak három kategóriába sorolhatók:

* Beszúrás (I): A hipotézis átiratában helytelenül hozzáadott szavak
* Törlés (D): A hipotézis átiratában nem észlelhető szavak
* Helyettesítő (k): A hivatkozás és a hipotézis között helyettesített szavak

Például:

![Helytelenül azonosított szavak – példa](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Hibák elhárítása és a WER fejlesztése

Az alkalmazással, eszközzel vagy termékkel használt modell minőségének kiértékeléséhez a számítógép-felismerési eredményekből használhatja a WERt. Az 5%-10%-os WER jó minőségűnek számít, és készen áll a használatra. 20%-os WER elfogadható, azonban érdemes lehet további képzést is megfontolni. A WER 30%-os vagy újabb gyenge minőséggel rendelkezik, és testreszabást és képzést igényel.

Fontos a hibák elosztása. Ha a rendszer sok törlési hibát észlel, általában a gyenge hangjelek erőssége miatt. A probléma megoldásához a hangadatokat közelebb kell gyűjteni a forráshoz. A beillesztési hibák azt jelentik, hogy a hang zajos környezetben lett rögzítve, és lehetséges, hogy az áthallási problémákat okoz. A helyettesítési hibák akkor fordulnak elő, ha a tartományhoz tartozó feltételek nem elégséges mintája emberi feliratú átiratként vagy kapcsolódó szövegként van megadva.

Az egyes fájlok elemzésével meghatározhatja, hogy milyen típusú hibák léteznek, és mely hibák egyediek egy adott fájlra vonatkozóan. A fájlok szintjén megjelenő problémák segítenek megcélozni a fejlesztési funkciókat.

## <a name="create-a-test"></a>Teszt létrehozása

Ha szeretné kipróbálni a Microsoft beszéd-szöveg típusú alapmodelljét vagy egy Ön által betanított egyéni modellt, összehasonlíthatja a két modellt egymás mellett, hogy kiértékelje a pontosságot. Az összehasonlítás magában foglalja a WER és a felismerés eredményeit. Az egyéni modelleket általában a Microsoft alapmodellje hasonlítja össze.

Modellek kiértékelése egymás mellett:

1. Navigáljon a **beszéd-szöveg > Custom Speech > teszteléshez**.
2. Kattintson a **teszt hozzáadása**gombra.
3. Válassza ki a **pontosság**kiértékelése elemet. Adja meg a teszt nevét, leírását, és válassza ki a hang + emberi-címkével ellátott átírási adatkészletet.
4. Válasszon legfeljebb két modellt, amelyeket szeretne tesztelni.
5. Kattintson a **Create** (Létrehozás) gombra.

A teszt sikeres létrehozása után összehasonlíthatja az eredményeket egymás mellett.

## <a name="side-by-side-comparison"></a>Párhuzamos összehasonlítás

A teszt befejezése után az állapot változása *sikeres*volt, a tesztben szereplő mindkét modellhez meg kell adni egy wer-számot. Kattintson a teszt nevére a tesztelési részletek oldal megtekintéséhez. Ez a részletes lap felsorolja az adatkészlet összes hosszúságú kimondott szöveg, amely a két modell felismerési eredményét jelzi a beküldött adatkészlet átírása mellett. Az egymás melletti összehasonlítások megvizsgálása érdekében különböző típusú hibákat válthat ki, beleértve a beszúrást, a törlést és a helyettesítést is. Ha az egyes oszlopokban figyeli a hangfelismerési eredményeket, és összehasonlítja az összes olyan oszlopot, amely az emberi címkével ellátott átírást és az eredményeket mutatja két beszéd – szöveg modell esetében, eldöntheti, hogy melyik modell felel meg az igényeinek, és hogy a további képzések és Újdonságok szükséges.

## <a name="next-steps"></a>További lépések

* [A modell betanítása](how-to-custom-speech-train-model.md)
* [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>További források

* [Az adatfeldolgozás előkészítése és tesztelése](how-to-custom-speech-test-data.md)
* [Az adatai ellenőrzése](how-to-custom-speech-inspect-data.md)

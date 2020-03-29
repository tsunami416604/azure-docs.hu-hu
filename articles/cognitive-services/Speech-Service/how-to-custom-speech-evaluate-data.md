---
title: Az egyéni beszédfelismerés pontosságának kiértékelése – Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: Ebben a dokumentumban megtudhatja, hogyan mérje mennyiségileg a beszéd-szöveg modell vagy az egyéni modell minőségét. Audio + emberi címkével ellátott átírási adatok szükségesek a pontosság vizsgálatához, és 30 perc től 5 óráig reprezentatív hangot kell biztosítani.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: f710b8bfdd4dcfd3b7a63aa0b457036ab7037016
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806096"
---
# <a name="evaluate-custom-speech-accuracy"></a>Egyéni beszédpontosság kiértékelése

Ebben a dokumentumban megtudhatja, hogyan mérheti mennyiségileg a Microsoft beszéd-szöveg modell vagy az egyéni modell minőségét. Audio + emberi címkével ellátott átírási adatok szükségesek a pontosság vizsgálatához, és 30 perc től 5 óráig reprezentatív hangot kell biztosítani.

## <a name="what-is-word-error-rate-wer"></a>Mi a Word hibaarány (WER)?

A modell pontosságának mérésére a Word hibaarány (WER) mértékipari *szabványa.* A WER megszámolja az elismerés során azonosított helytelen szavak számát, majd elosztja az emberi címkével ellátott átiratban megadott szavak teljes számával. Végül ezt a számot megszorozzuk 100%-kal a WER kiszámításához.

![WER képlet](./media/custom-speech/custom-speech-wer-formula.png)

A helytelenül azonosított szavak három kategóriába sorolhatók:

* Beszúrás (I): Helytelenül hozzáadott szavak a hipotézis átiratában
* Törlés (D): A hipotézis átiratában észrevétlenül felderített szavak
* Helyettesítés (S): A referencia és a hipotézis közötti helyettesítésre használt szavak

Például:

![Példa helytelenül azonosított szavakra](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>A hibák elhárítása és a WER javítása

A gépfelismerési eredmények wer-jével kiértékelheti az alkalmazással, eszközzel vagy termékkel használt modell minőségét. A WER 5%-10% tekinthető jó minőségű, és készen áll a használatra. A WER 20%-os elfogadható, de érdemes megfontolni a további képzés. A WER 30%-os vagy annál nagyobb jelek rossz minőségű, és megköveteli a testreszabásés a képzés.

Fontos, hogy a hibák hogyan oszlanak meg. Ha sok törlési hiba merül fel, az általában a gyenge hangjelerősség miatt van. A probléma megoldásához a forráshoz közelebb i. hangadatokat kell gyűjtenie. A beszúrási hibák azt jelentik, hogy a hang zajos környezetben került rögzítésre, és az áthallás jelen lehet, ami felismerési problémákat okoz. A helyettesítési hibák gyakran akkor fordulnak elő, ha a tartományspecifikus kifejezésekből nem elegendő mintát adtak meg emberi címkével ellátott átiratként vagy kapcsolódó szövegként.

Az egyes fájlok elemzésével meghatározhatja, hogy milyen típusú hibák léteznek, és hogy mely hibák egyediek egy adott fájlban. A problémák fájlszintű megértése segít a fejlesztések megcélzásában.

## <a name="create-a-test"></a>Teszt létrehozása

Ha szeretné tesztelni a Microsoft beszéd-szöveg alapmodell vagy egy egyéni modell minőségét, amelyet betanított, összehasonlíthatja a két modellegymás mellett a pontosság kiértékeléséhez. Az összehasonlítás tartalmazza a WER-t és az elismeréseredményeit. Általában egy egyéni modell a Microsoft alapmodelljével van összehasonlítva.

A modellek egymás melletti értékelése:

1. Jelentkezzen be az [Egyéni beszédportálra.](https://speech.microsoft.com/customspeech)
2. Nyissa meg a **beszédfelismerést > egyéni beszédfelismerési > tesztelése lehetőséget.**
3. Kattintson **a Teszt hozzáadása gombra.**
4. Válassza **a Pontosság kiértékelése**lehetőséget. Adjon nevet, leírást a tesztnek, és válassza ki a hang + emberi címkével ellátott átírási adatkészletet.
5. Legfeljebb két modellt választhat ki, amelyeket tesztelni szeretne.
6. Kattintson **a Létrehozás gombra.**

A teszt sikeres létrehozása után összehasonlíthatja az eredményeket egymás mellett.

## <a name="side-by-side-comparison"></a>Egymás melletti összehasonlítás

Miután a teszt befejeződött, jelezve az állapotváltozás *sikeres*, talál egy WER számot mindkét modell szerepel a tesztben. Kattintson a teszt nevére a tesztelés részleteit tartalmazó lap megtekintéséhez. Ez a részletes lap felsorolja az adatkészlet összes kimondott szövegét, jelezve a két modell felismerési eredményeit a beküldött adatkészlet ből történő átírás mellett. Az egymás melletti összehasonlítás ellenőrzése érdekében válthat a különböző hibatípusok között, beleértve a beszúrást, a törlést és a helyettesítést. A hang lejátszásával és az egyes oszlopokban elért felismerési eredmények összehasonlításával, amely az emberi címkével ellátott átírást és két beszéd-szöveg modell eredményeit mutatja, eldöntheti, hogy melyik modell felel meg az igényeinek, és hol van további képzés és fejlesztés Szükséges.

## <a name="next-steps"></a>További lépések

* [A modell betanítása](how-to-custom-speech-train-model.md)
* [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>További források

* [Az adatok előkészítése és tesztelése](how-to-custom-speech-test-data.md)
* [Az adatok vizsgálata](how-to-custom-speech-inspect-data.md)

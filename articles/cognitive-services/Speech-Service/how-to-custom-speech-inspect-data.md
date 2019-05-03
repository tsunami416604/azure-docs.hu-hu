---
title: A Custom Speech - beszédszolgáltatások adatminőség vizsgálata
titlesuffix: Azure Cognitive Services
description: Egyéni Beszédmodell eszközöket biztosít, amelyek lehetővé teszik vizuálisan megvizsgálják a modell minőségét felismerése a felismerés ennek megfelelő eredményt hívásaiból összehasonlításával. A Custom Speech-portál, a feltöltött hang lejátszása, és megvizsgálja, hogy a megadott felismerés eredményét helyes-e.  Ez az eszköz gyors vizsgálata a Microsoft baseline hang-szöveg transzformációs modellt vagy egyéni betanított modell minőségét bármely hívásaiból lefényképezze nélkül teszi lehetővé.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 682f3df362a7fbb0e95a07aa8a8f3a068367eef2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025818"
---
# <a name="inspect-custom-speech-data"></a>Egyéni beszédfelismerés adatok vizsgálata

> [!NOTE]
> Ezen a lapon azt feltételezi, hogy elolvasta [előkészítése tesztadatai a Custom Speech](how-to-custom-speech-test-data.md) és feltöltött egy adatkészletet ellenőrzés.

Egyéni Beszédmodell eszközöket biztosít, amelyek lehetővé teszik vizuálisan megvizsgálják a modell minőségét felismerése a felismerés ennek megfelelő eredményt hívásaiból összehasonlításával. A Custom Speech-portál, a feltöltött hang lejátszása, és megvizsgálja, hogy a megadott felismerés eredményét helyes-e. Ez az eszköz gyors vizsgálata a Microsoft baseline hang-szöveg transzformációs modellt vagy egyéni betanított modell minőségét bármely hívásaiból lefényképezze nélkül teszi lehetővé.

Ebből a dokumentumból megismerheti, hogyan vizuálisan megvizsgálják a betanítási adatok korábban feltöltött használó modell minőségét, lesz.

Ezen az oldalon megtudhatja, hogyan vizuálisan vizsgálhatja meg a Microsoft baseline hang-szöveg transzformációs modell és/vagy egy egyéni modell számára betanított minőségét. A feltöltött adatokat fogja használni a **adatok** tesztelési lapot.

## <a name="create-a-test"></a>Hozzon létre egy tesztet

Kövesse ezeket az utasításokat egy teszt létrehozása:

1. Navigáljon a **hang-szöveg transzformációs > Custom Speech > tesztelés**.
2. Kattintson a **teszt hozzáadása**.
3. Válassza ki **vizsgálata (csak hang-adatok) minőségi**. Adja meg a teszt nevét, leírását, és válassza ki az audio adatkészlet.
4. Válassza ki a tesztelni kívánt legfeljebb két modellek.
5. Kattintson a **Create** (Létrehozás) gombra.

Egy teszt sikeres létrehozása után összehasonlíthatja a modellek egymás mellett.

## <a name="side-by-side-model-comparisons"></a>Egymás melletti modell összehasonlítása

Ha a vizsgálat állapota *sikeres*, kattintson a részletek a teszt teszt cikk-neve. A Részletek lap felsorolja az adatkészlet, a két modell mellett az elküldött adatkészletből a beszédátírási felismerés eredményét jelző kimondott szöveg.

Az egymás melletti összehasonlítás vizsgálata érdekében különböző alkalmazáshiba-típusok, beleértve a beszúrási, törlési és helyettesítési válthat. A hanganyag figyeli, és összehasonlítja az egyes oszlopok (emberi címkével beszédátírási és hang-szöveg transzformációs két modell eredményeinek megjelenítése) felismerési eredményeket, megadhatja, hogy melyik modellben az igényeinek, és ahol fejlesztései van szükség.

Minőségi teszteléséhez vizsgálatával hasznos minőségének beszédfelismerő végpontokat szolgáltatás elegendő egy alkalmazáshoz.  Egy cél mérték pontosságának, igénylő átírt hang-kövesse az utasításokat, a tesztelés található: Értékelje ki a pontosságot.

## <a name="next-steps"></a>További lépések

* [Az adatok](how-to-custom-speech-evaluate-data.md)
* [A modell tanítása](how-to-custom-speech-train-model.md)
* [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>További források

* [Tesztadatok előkészítéséhez egyéni beszéd](how-to-custom-speech-test-data.md)

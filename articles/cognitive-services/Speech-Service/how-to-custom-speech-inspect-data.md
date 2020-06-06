---
title: Az Custom Speech-Speech szolgáltatás adatminőségének vizsgálata
titleSuffix: Azure Cognitive Services
description: A Custom Speech olyan eszközöket biztosít, amelyekkel vizuálisan ellenőrizheti a modell felismerési minőségét a hangadatoknak a megfelelő felismerési eredménnyel való összehasonlításával. Lejátszhatja a feltöltött hangot, és megállapíthatja, hogy a megadott felismerési eredmény helyes-e.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: erhopf
ms.openlocfilehash: 7a8fec876556d943d29756a38ffc27ae8095e3c4
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2020
ms.locfileid: "84466238"
---
# <a name="inspect-custom-speech-data"></a>Custom Speech-adatok vizsgálata

> [!NOTE]
> Ez az oldal azt feltételezi, hogy elolvasta a [Custom Speechi tesztelési adatokat](how-to-custom-speech-test-data.md) , és feltöltött egy adatkészletet a vizsgálathoz.

A Custom Speech olyan eszközöket biztosít, amelyekkel vizuálisan ellenőrizheti a modell felismerési minőségét a hangadatoknak a megfelelő felismerési eredménnyel való összehasonlításával. A [Custom Speech-portálon](https://speech.microsoft.com/customspeech)lejátszhatja a feltöltött hangot, és megállapíthatja, hogy a megadott felismerési eredmény helyes-e. Ez az eszköz segít megvizsgálni a Microsoft alapszintű beszéd-szöveg modelljét, megvizsgálja a betanított egyéni modellt, vagy összehasonlítja a két modellből való átírást.

Ebből a dokumentumból megtudhatja, hogyan vizsgálhatja meg a modell minőségét a korábban feltöltött betanítási adatmennyiség használatával.

Ezen az oldalon megtudhatja, hogyan vizsgálhatja meg a Microsoft alapvető beszédfelismerési modelljét és/vagy a már betanított egyéni modellt. A teszteléshez az **adatlapra feltöltött adatlapokat** fogja használni.

## <a name="create-a-test"></a>Teszt létrehozása

Teszt létrehozásához kövesse az alábbi utasításokat:

1. Jelentkezzen be a [Custom Speech portálra](https://speech.microsoft.com/customspeech).
2. Navigáljon a **beszéd-szöveg > Custom Speech > teszteléshez**.
3. Kattintson a **teszt hozzáadása**gombra.
4. Válassza a **minőség vizsgálata (csak hangalapú adatok)** lehetőséget. Adja meg a teszt nevét, leírását, és válassza ki a hangkészletet.
5. Válasszon legfeljebb két modellt, amelyeket szeretne tesztelni.
6. Kattintson a **Létrehozás**gombra.

Egy teszt sikeres létrehozása után megtekintheti, hogy egy modell hogyan írja át a megadott hangkészletet, vagy összehasonlítja a két modell eredményeit egymás mellett.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Párhuzamos modell-összehasonlítások

Ha a teszt állapota _sikeres_, kattintson a teszt elem nevére a teszt részleteinek megtekintéséhez. Ez a részletes lap felsorolja az adatkészlet összes hosszúságú kimondott szöveg, és megjeleníti az összehasonlított két modell felismerési eredményeit.

Az egymás melletti összehasonlítások megvizsgálása érdekében különböző típusú hibákat válthat ki, beleértve a beszúrást, a törlést és a helyettesítést is. Ha az egyes oszlopokban figyeli a hangot, és összehasonlítja a felismerés eredményét (megjelenítve az emberi címkével ellátott átírást és a két beszéd – szöveg modell eredményét), eldöntheti, hogy melyik modell megfelel az igényeinek, és hogy hol szükségesek a tökéletesítések.

A párhuzamos modell tesztelése hasznos annak ellenőrzéséhez, hogy melyik beszédfelismerési modell a legmegfelelőbb az alkalmazásokhoz. Az átmásolt hangra vonatkozó objektív pontosság érdekében kövesse a [pontosság kiértékelése](how-to-custom-speech-evaluate-data.md)című részben található utasításokat.

## <a name="next-steps"></a>Következő lépések

- [Az adatai kiértékelése](how-to-custom-speech-evaluate-data.md)
- [A modell betanítása](how-to-custom-speech-train-model.md)
- [Modell továbbfejlesztése](how-to-custom-speech-improve-accuracy.md)
- [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>További források

- [Custom Speech tesztelési célú adatfeldolgozása](how-to-custom-speech-test-data.md)

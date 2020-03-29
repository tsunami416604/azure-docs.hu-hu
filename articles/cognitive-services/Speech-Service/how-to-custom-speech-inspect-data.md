---
title: Egyéni beszédfelismerési szolgáltatás adatminőségének vizsgálata
titleSuffix: Azure Cognitive Services
description: Az Egyéni beszéd eszközekkel vizuálisan ellenőrizheti a modell felismerési minőségét a hangadatok és a megfelelő felismerési eredmény összehasonlításával. Lejátszhatja a feltöltött hangot, és megállapíthatja, hogy a megadott felismerési eredmény helyes-e.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: erhopf
ms.openlocfilehash: 60b415f69800885f37ee53cc4f090098f5ebf1f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806079"
---
# <a name="inspect-custom-speech-data"></a>Egyéni beszédadatok vizsgálata

> [!NOTE]
> Ez a lap feltételezi, hogy elolvasta [a Tesztadatok előkészítése egyéni beszédhez,](how-to-custom-speech-test-data.md) és feltöltött egy adatkészletet ellenőrzésre.

Az Egyéni beszéd eszközekkel vizuálisan ellenőrizheti a modell felismerési minőségét a hangadatok és a megfelelő felismerési eredmény összehasonlításával. Az [Egyéni beszédportálon](https://speech.microsoft.com/customspeech)lejátszhatja a feltöltött hangot, és megállapíthatja, hogy a megadott felismerési eredmény helyes-e. Ez az eszköz lehetővé teszi a Microsoft eredeti beszéd-szöveg modelljének vagy egy betanított egyéni modell minőségének gyors ellenőrzését anélkül, hogy bármilyen hangadatot át kellene írnia.

Ebben a dokumentumban megtudhatja, hogyan vizsgálhatja meg vizuálisan egy modell minőségét a korábban feltöltött betanítási adatok használatával.

Ezen a lapon megtudhatja, hogyan vizsgálhatja meg vizuálisan a Microsoft eredeti beszéd-szöveg modell és/vagy a betanított egyéni modell minőségét. Az **Adatok** lapra feltöltött adatokat tesztelésre fogja használni.

## <a name="create-a-test"></a>Teszt létrehozása

A teszt létrehozásához kövesse az alábbi utasításokat:

1. Jelentkezzen be az [Egyéni beszédportálra.](https://speech.microsoft.com/customspeech)
2. Nyissa meg a **beszédfelismerést > egyéni beszédfelismerési > tesztelése lehetőséget.**
3. Kattintson **a Teszt hozzáadása gombra.**
4. Válassza **a Minőség vizsgálata (csak hangadatok)** lehetőséget. Adjon nevet, leírást a tesztnek, és jelölje ki a hangadatkészletet.
5. Legfeljebb két modellt választhat ki, amelyeket tesztelni szeretne.
6. Kattintson **a Létrehozás gombra.**

A teszt sikeres létrehozása után összehasonlíthatja a modelleket egymás mellett.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Egymás melletti modell-összehasonlítások

Ha a teszt állapota _sikeres,_ kattintson a tesztelem nevére a teszt részleteinek megtekintéséhez. Ez a részletes lap felsorolja az adatkészlet összes kimondott szövegét, jelezve a két modell felismerési eredményeit a beküldött adatkészlet ből történő átírás mellett.

Az egymás melletti összehasonlítás ellenőrzése érdekében válthat a különböző hibatípusok között, beleértve a beszúrást, a törlést és a helyettesítést. A hanglejátszással és az egyes oszlopokban elért felismerési eredmények összehasonlításával (amely az emberi címkével ellátott átírást és két beszéd-szöveg modell eredményeit mutatja), eldöntheti, hogy melyik modell felel meg az igényeinek, és hol van szükség fejlesztésekre.

A minőségvizsgálat vizsgálata akkor hasznos, ha ellenőrizni szeretné, hogy a beszédfelismerési végpont minősége elegendő-e egy alkalmazás számára. Az átírt hang átvitelére vonatkozó objektív pontosság érdekében kövesse a [Pontosság kiértékelése](how-to-custom-speech-evaluate-data.md)című részben található utasításokat.

## <a name="next-steps"></a>További lépések

- [Az adatok kiértékelése](how-to-custom-speech-evaluate-data.md)
- [A modell betanítása](how-to-custom-speech-train-model.md)
- [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>További források

- [Tesztadatok előkészítése egyéni beszédfelismerésre](how-to-custom-speech-test-data.md)

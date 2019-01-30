---
title: Tesztelése, és a egy modell – Custom Vision Service újratanítása
titlesuffix: Azure Cognitive Services
description: Ismerje meg a lemezkép tesztelése, és aztán a segítségükkel a modell újratanítása.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: d53e9a7c6c4e797de66b50feae81ee291bf3da4b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55212205"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Tesztelése, és a egy modellt a Custom Vision Service újratanítása

Miután a modell betanításához gyorsan tesztelheti egy helyileg tárolt képet vagy online lemezkép használatával. A teszt a közelmúltban betanított iteráció használ.

## <a name="test-your-model"></a>Modell tesztelése

1. Az a [Custom Vision weblap](https://customvision.ai), jelölje ki a projektet. Válassza ki **gyors teszteléséhez** jobb oldalán a felső menüsávon. Ez a művelet feliratú ablakot nyit a **gyors teszteléséhez**.

    ![A gyors vizsgálat gombra az ablak jobb felső sarokban látható.](./media/test-your-model/quick-test-button.png)

2. Az a **Quick Test** ablakban kattintson a a **képet nyújt** mezőben, majd adja meg a teszthez használni kívánt kép URL-CÍMÉT. Ha inkább egy helyileg tárolt képet szeretne, kattintson a **helyi fájlok tallózása** gombra, és válassza ki a helyi rendszerképet fájlt.

    ![A Küldés lemezkép lapjának képe](./media/test-your-model/submit-image.png)

A kiválasztott kép jelenik meg az oldal közepén. Ezután az eredmények megjelennek a címkével ellátott, két oszlopot tartalmazó tábla formájában az ábra alatti **címkék** és **megbízhatósági**. Miután az eredmények megtekintése, bezárhatja a **gyors teszteléséhez** ablak.

A tesztképre adhat hozzá a modellt, és ezután újratanítása a modell.

## <a name="use-the-predicted-image-for-training"></a>Az előre jelzett rendszerkép használata a betanításhoz.

A korábban elküldött képzéshez rendszerkép használatához használja az alábbi lépéseket:

1. Az osztályozó által igénybe vett elküldött rendszerképek megtekintéséhez nyissa meg a [Custom Vision weblap](https://customvision.ai) , és válassza ki a __előrejelzéseket__ fülre.

    ![Az előrejelzések lap képe](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > Az alapértelmezett nézet a jelenlegi verzió továbbfejlesztésében képeit jeleníti meg. Használhatja a __iteráció__ legördülő mező előző lépés során küldött kép megtekintéséhez.

2. Kép az osztályozó által előrejelzett is címkéinek megtekintéséhez mutasson.

    > [!TIP]
    > Képek rangsora, hogy a képek, amelyek a legtöbb nyereségek is előtérbe az osztályozó által igénybe vett tetején. Válassza ki, eltérő rendezést, használja a __rendezési__ szakaszban.

    Kép felvétele a betanítási adatok, válassza ki a lemezképet, jelölje ki a címkét, és válassza __mentse és zárja be__. A lemezkép el lesz távolítva __előrejelzéseket__ és hozzáadja a betanító kép. Kiválasztásával megtekintheti a __betanító kép__ fülre.

    ![A címkézési lapjának képe](./media/test-your-model/tag-image.png)

3. Használja a __Train__ újratanítás PowerShell osztályozó gombra.

## <a name="next-steps"></a>További lépések

[Tartalombesoroló teljesítményének](getting-started-improving-your-classifier.md)

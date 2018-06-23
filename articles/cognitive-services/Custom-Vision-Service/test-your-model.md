---
title: Tesztelje, és a modell - egyéni stratégiai szolgáltatás - kognitív Azure-szolgáltatások újratanítása |} Microsoft Docs
description: Lemezkép tesztelése és, amellyel a modell újratanítása útmutató.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 1933b1a45844ac99308baebe59b49687a957abfa
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348138"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Tesztelje, és egy modell egyéni stratégiai szolgáltatásban működik

Után modellje betanításához gyorsan tesztelheti a helyben tárolt lemezkép vagy online lemezkép használatával. A vizsgálat a közelmúltban képzett iterációs használja.

## <a name="test-your-model"></a>Modell tesztelése

1. Az a [egyéni stratégiai weblap](https://customvision.ai), válassza ki a projektet. Válassza ki **Gyorsellenőrzés** jobb oldalán a felső menüsoron. Ez a művelet megnyit egy ablakot feliratú **Gyorsellenőrzés**.

    ![A gyors vizsgálat gombra az ablak jobb felső sarokban látható.](./media/test-your-model/quick-test-button.png)

2. Az a **gyors teszteléséhez** ablakban kattintson a a **képet nyújt** mezőben, és írja be a teszteléshez használni kívánt kép URL-CÍMÉT. Ha helyileg tárolt lemezképet használ helyette, kattintson a **keresse meg a helyi fájlok** gombra, és jelöljön ki egy helyi lemezképfájlt.

    ![A Küldés kép lapjának képe](./media/test-your-model/submit-image.png)

A kiválasztott kép közepén az oldal jelenik meg. A kép feliratú két oszlopokat tartalmazó tábla formájában alatt jelennek meg, majd **címkék** és **abban, hogy**. Az eredmények megtekintése után bezárhatja a **Gyorsellenőrzés** ablak.

Most a lemezképet fel a modellbe, és a modell majd működik.

## <a name="use-the-predicted-image-for-training"></a>Használja az előre jelzett lemezképet képzési.

A lemezképet, a képzési korábban átadott használatára, tegye a következőket:

1. Az osztályozó számára küldött kép megtekintéséhez nyissa meg a [egyéni stratégiai weblap](https://customvision.ai) válassza ki a __előrejelzéseket__ fülre.

    ![Az előrejelzés lap képe](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > Az alapértelmezett nézet lemezképet rögzíthet a jelenlegi iterációs jeleníti meg. Használhatja a __iterációs__ legördülő mező az előző lépés során küldött kép megtekintéséhez.

2. Egy képre az osztályozó által előrejelzett volt címkéket mutasson.

    > [!TIP]
    > Lemezképek rangsora, hogy a képek, amely a legtöbb előnyökre is kerüljön a osztályozó tetején. Válassza ki, eltérő rendezést, használja a __rendezési__ szakasz.

    Kép felvétele a betanítási adatok, válassza ki a lemezképet, jelölje ki a címke, és válassza ki __mentse és zárja be__. Eltávolítja a lemezképet __előrejelzéseket__ és a képzési lemezképekhez hozzá. Kiválasztásával megtekintheti a __képzési képek__ fülre.

    ![A címkézési lapjának képe](./media/test-your-model/tag-image.png)

3. Használja a __vonat__ gombra kattintva a osztályozó működik.

## <a name="next-steps"></a>További lépések

[A besorolás javítása](getting-started-improving-your-classifier.md)
---
title: Modell tesztelése és újratanítása - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan tesztelheti a lemezképet, és majd használja a modell újratanításához a Custom Vision szolgáltatásban.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: c6ed8869f6d33609381a42fd22d728e9e5542802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73721203"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Modell tesztelése és újratanítása a Custom Vision Service szolgáltatással

A modell betanítása után gyorsan tesztelheti azt egy helyileg tárolt kép vagy egy online kép használatával. A teszt a legutóbb betanított iterációt használja.

## <a name="test-your-model"></a>Modell tesztelése

1. A [Custom Vision weblapon](https://customvision.ai)válassza ki a projektet. Válassza a **gyorsteszt** lehetőséget a felső menüsor jobb oldalán. Ez a művelet megnyit egy gyorsteszt feliratú **ablakot.**

    ![A Gyorsteszt gomb az ablak jobb felső sarkában látható.](./media/test-your-model/quick-test-button.png)

2. A **Gyorsteszt** ablakban kattintson a **Kép küldése** mezőre, és adja meg a teszthez használni kívánt kép URL-címét. Ha ehelyett helyileg tárolt képet szeretne használni, kattintson a **Helyi fájlok tallózása** gombra, és jelöljön ki egy helyi képfájlt.

    ![A küldőkép oldalképe](./media/test-your-model/submit-image.png)

A kijelölt kép az oldal közepén jelenik meg. Ezután az eredmények a kép alatt jelennek meg egy táblázat formájában, két oszlopgal, **címkék** és **megbízhatóság**felirattal. Az eredmények megtekintése után bezárhatja a **Gyorsteszt** ablakot.

Most már hozzáadhatja ezt a tesztképet a modellhez, majd újrataníthatja a modellt.

## <a name="use-the-predicted-image-for-training"></a>Az előre jelzett kép használata betanításhoz

A korábban beküldött kép betanításához kövesse az alábbi lépéseket:

1. Az osztályozónak küldött képek megtekintéséhez nyissa meg a [Custom Vision weboldalt,](https://customvision.ai) és válassza az __Előrejelzések__ lapot.

    ![Az előrejelzések lap képe](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > Az alapértelmezett nézet az aktuális iterációból származó képeket jeleníti meg. Az __Iteráció legördülő__ mezősegítségével megtekintheti a korábbi ismétlések során elküldött képeket.

2. Vigye az egérmutatót egy kép fölé az osztályozó által előre jelzett címkék megtekintéséhez.

    > [!TIP]
    > A képek rangsorolva vannak, így azok a képek, amelyek a legtöbb nyereséget hozhatják az osztályozónak, a tetején vannak. Másik rendezés kiválasztásához használja a __Rendezés szakaszt.__

    Ha képet szeretne hozzáadni a betanítási adatokhoz, jelölje ki a képet, jelölje ki a címkét, majd válassza a __Mentés és bezárás__lehetőséget. A rendszer eltávolítja a képet __az előrejelzésekből,__ és hozzáadja a betanítási képekhez. Megtekintheti a __Betanítási képek__ lapon.

    ![A címkézési oldal képe](./media/test-your-model/tag-image.png)

3. A __Vonat__ gombbal újratanítja az osztályozót.

## <a name="next-steps"></a>További lépések

[Tartalombesoroló teljesítményének fokozása](getting-started-improving-your-classifier.md)

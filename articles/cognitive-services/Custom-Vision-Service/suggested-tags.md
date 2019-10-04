---
title: Gyorsabban címkézheti a képeket a javasolt címkékkel
titleSuffix: Azure Cognitive Services
description: Ebből az útmutatóból megtudhatja, hogyan használhatja a javasolt címkéket a nagy számú kép gyorsabb címkézéséhez Custom Vision modellek betanításakor.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: pafarley
ms.openlocfilehash: 31b8dfc234ac99d6f04061d6596e3dc8113e8d0f
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213839"
---
# <a name="label-images-faster-with-suggested-tags"></a>Gyorsabban címkézheti a képeket a javasolt címkékkel

Ebből az útmutatóból megtudhatja, hogyan használható a javasolt címkék funkció a Custom Vision-modellek betanításakor a nagy számú kép gyorsabb címkézéséhez. 

Ha Custom Vision modellhez címkéz képeket, a szolgáltatás a modell legújabb betanított iterációját használja a címkézetlen lemezképek címkéjének előrejelzéséhez. Ezután ezeket az előrejelzéseket javasolt címkékként jeleníti meg a kiválasztott megbízhatósági küszöb és az előrejelzési bizonytalanság alapján. Ezután megerősítheti vagy módosíthatja a javaslatokat, és felgyorsíthatja a képek manuális címkézésének folyamatát a betanításhoz.

## <a name="when-to-use-suggested-tags"></a>Mikor kell használni a javasolt címkéket

Tartsa szem előtt a következő korlátozásokat:

* Csak olyan képekre vonatkozó ajánlott címkék kérése, amelyek tartalma már egyszer már betanítva van. Ne kapjon olyan új címkére vonatkozó javaslatokat, amelyet most elkezd betanítani.
* A felcímkézett képeken csak a javasolt címkéket lehet használni. a már címkézett képeken nem kaphat javaslatokat további címkékre.

> [!IMPORTANT]
> A javasolt címkék funkció ugyanazt az [árképzési modellt](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) használja, mint a normál előrejelzések. Amikor első alkalommal indítja el a javasolt címkéket egy adott rendszerképhez, az előrejelzési hívásokhoz hasonlóan kell fizetnie. Ezt követően a szolgáltatás 30 napig tárolja a kiválasztott lemezképek eredményeit egy adatbázisban, és bármikor elérhetővé teheti azokat az adott időszakon belül ingyenesen. 30 nap után díjat számítunk fel, ha újra kéri a javasolt címkéket.

## <a name="suggested-tags-workflow"></a>Javasolt címkék munkafolyamata

A következő lépések bemutatják, hogyan használhatja a javasolt címkék funkciót:

1. Töltse fel az összes betanítási lemezképet a Custom Vision projektbe.
1. Címkézze fel az adatkészletet, és az egyes címkékhez azonos számú lemezképet választ.
    > [!TIP]
    > Győződjön meg arról, hogy az összes olyan címkét használja, amelyhez később javaslatot szeretne tenni.
1. Indítsa el a betanítási folyamatot.
1. A képzés befejezésekor navigáljon a **címkézett** nézethez, és válassza a **javasolt címkék beolvasása** gombot a bal oldali ablaktáblán.
    > [!div class="mx-imgBorder"]
    > ![A javasolt címkék gomb a címkézetlen lemezképek lapon jelenik meg.](./media/suggested-tags/suggested-tags-button.png)
1. Válassza ki azokat a lemezképeket, amelyekhez javaslatokat szeretne létrehozni. A címkézetlen lemezképek egy részének csak a kezdeti címkével kapcsolatos javaslatok olvashatók be. A folyamat megismétlése után jobb címkézési javaslatokat kaphat.
1. Erősítse meg a javasolt címkéket, javítsa ki azokat, amelyek nem megfelelőek.
    > [!TIP]
    > A javasolt címkékkel rendelkező lemezképek az előrejelzési bizonytalanság szerint vannak rendezve (az alacsonyabb értékek nagyobb megbízhatóságot jeleznek). A rendezési sorrendet a **Rendezés bizonytalansága** lehetőséggel módosíthatja. Ha a rendelést **magasról alacsonyra**állítja, akkor először javítsa ki a magas bizonytalansági előrejelzéseket, majd gyorsan erősítse meg az alacsony bizonytalanságot.
    * A képbesorolási projektekben kiválaszthatja és jóváhagyhatja a címkéket a batchs szolgáltatásban. Egy adott javasolt címke alapján szűrheti a nézetet, törölje a helytelenül megjelölt rendszerképeket, majd erősítse meg a REST-t egy kötegben.
        > [!div class="mx-imgBorder"]
        > ![A javasolt címkék kötegelt módban jelennek meg az IC szűrőkkel.](./media/suggested-tags/ic-batch-mode.png)

        A javasolt címkéket egyéni rendszerkép módban is használhatja, ha kijelöl egy képet a katalógusból.

        ![A javasolt címkék az IC egyedi rendszerkép módban jelennek meg.](./media/suggested-tags/ic-individual-image-mode.png)
    * Az objektum-észlelési projektekben a Batch-megerősítések nem támogatottak, de a javasolt címkék alapján továbbra is szűrhetők és rendezhetők a szervezett címkézési élmény. A címkézetlen képek miniatűrje megjeleníti a javasolt címkék helyét jelző, határoló mezők átfedését. Ha nem választ ki egy javasolt címke szűrőt, az összes címkézetlen kép megjelenik a határoló mezők átfedése nélkül.
        > [!div class="mx-imgBorder"]
        > ![A javasolt címkék kötegelt módban jelennek meg az OD és a szűrők használatával.](./media/suggested-tags/od-batch-mode.png)

        Az objektum-észlelési címkék megerősítéséhez ezeket a katalógusban szereplő egyes rendszerképekre kell alkalmaznia.

        ![A javasolt címkék az OD egyedi rendszerkép módban jelennek meg.](./media/suggested-tags/od-individual-image-mode.png)
1. Indítsa el újra a betanítási folyamatot.
1. Ismételje meg az előző lépéseket, amíg meg nem felel a javaslat minőségének.

## <a name="next-steps"></a>További lépések

Egy rövid útmutató segítségével megkezdheti a Custom Vision projektek létrehozását és betanítását.

* [Osztályozó készítése](getting-started-build-a-classifier.md)
* [Objektum-detektor létrehozása](get-started-build-detector.md)
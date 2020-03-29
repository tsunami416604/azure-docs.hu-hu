---
title: A Smart Labeler segítségével gyorsabban címkézi a képeket
titleSuffix: Azure Cognitive Services
description: Ebből az útmutatóból megtudhatja, hogyan hozhat létre javasolt címkéket a képekhez az Intelligens címkéző használatával. Ez lehetővé teszi, hogy a címkék nagy számú kép gyorsabban betanítása során a Custom Vision modell.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: pafarley
ms.openlocfilehash: 94ca47e6114e4f8c3485f6072facd07c25e4b96a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647752"
---
# <a name="label-images-faster-with-smart-labeler"></a>A Smart Labeler segítségével gyorsabban címkézi a képeket

Ebből az útmutatóból megtudhatja, hogyan hozhat létre javasolt címkéket a képekhez az Intelligens címkéző használatával. Ez lehetővé teszi, hogy a címkék nagy számú kép gyorsabban betanítása során a Custom Vision modell.

Amikor egy Egyéni látásmodell lemezképeit címkézi, a szolgáltatás a modell legújabb betanított iterációját használja a címkézetlen képek címkéinek előrejelzéséhez. Ezután ezeket az előrejelzéseket javasolt címkékként jeleníti meg a kiválasztott megbízhatósági küszöbérték és az előrejelzési bizonytalanság alapján. Ezután megerősítheti vagy módosíthatja a javaslatokat, felgyorsítva a képek betanítási folyamatát.

## <a name="when-to-use-smart-labeler"></a>Mikor kell használni a Smart Labeler-t?

Tartsa szem előtt az alábbi korlátozásokat:

* Csak olyan képekhez kérjen javasolt címkéket, amelyek tartalma már egyszer be van tanítva. Ne kapjon javaslatot egy új címke, hogy éppen csak most kezdik a vonat.

> [!IMPORTANT]
> A Smart Labeler funkció ugyanazt a [díjszabási modellt](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) használja, mint a rendszeres előrejelzések. Amikor először indítja el a képek egy készletének javasolt címkéit, ugyanaz t számítunk fel, mint az előrejelzési hívásokért. Ezt követően a szolgáltatás 30 napig tárolja a kiválasztott képek eredményeit egy adatbázisban, és ezen időszakon belül bármikor ingyenesen elérheti őket. 30 nap elteltével, akkor kell fizetnie, ha újra kéri a javasolt címkéket.

## <a name="smart-labeler-workflow"></a>Intelligens címkéző munkafolyamat

A következő lépések bemutatják, hogyan használhatja a Smart Labeler-t:

1. Töltse fel az összes betanítási képet a Custom Vision projektbe.
1. Címkézze fel az adatkészlet egy részét, és válasszon egyenlő számú képet az egyes címkékhez.
    > [!TIP]
    > Győződjön meg arról, hogy az összes olyan címkét használja, amelyre később javaslatokat szeretne tenni.
1. Indítsa el a betanítási folyamatot.
1. Ha a betanítás befejeződött, keresse meg a **Címkézetlen** nézetet, és válassza a **Javasolt címkék betöltése** gombot a bal oldali ablaktáblán.
    > [!div class="mx-imgBorder"]
    > ![A javasolt címkék gomb a címkézetlen képek lapon látható.](./media/suggested-tags/suggested-tags-button.png)
1. A megjelenő előugró ablakban adja meg, hogy hány képhez szeretne javaslatokat tenni. A címkézetlen képek egy részéhez csak kezdeti címkejavaslatokat kell kapnia. Jobb címkejavaslatokat kap, amint végighalad ezen a folyamaton.
1. Erősítse meg a javasolt címkéket, és javítsa ki a nem megfelelő címkéket.
    > [!TIP]
    > A javasolt címkékkel rendelkező képek et előrejelzési bizonytalanságuk szerint rendezi a (az alacsonyabb értékek nagyobb bizalmat jeleznek). A rendezési sorrendet a Rendezés bizonytalanság szerint beállítással **módosíthatja.** Ha a sorrendet **magasra és alacsonyra**állítja, először kijavíthatja a nagy bizonytalansági előrejelzéseket, majd gyorsan megerősítheti az alacsony bizonytalanságot.
    * A képbesorolási projektekben kijelölheti és megerősítheti a címkéket a kötegekben. Szűrje a nézetet egy adott javasolt címke szerint, törölje a helytelenül címkézett képek kijelölését, majd erősítse meg a többit egy kötegben.
        > [!div class="mx-imgBorder"]
        > ![A javasolt címkék kötegelt módban jelennek meg a szűrőkkel rendelkező IC esetében.](./media/suggested-tags/ic-batch-mode.png)

        A javasolt címkéket az egyes képmódokban is használhatja, ha kiválaszt egy képet a galériából.

        ![A javasolt címkék az IC egyedi képmódjában jelennek meg.](./media/suggested-tags/ic-individual-image-mode.png)
    * Az objektumészlelési projektekben a kötegvisszaigazolások nem támogatottak, de a szervezettebb címkézési élmény érdekében továbbra is szűrheti és rendezheti a javasolt címkéket. A címkézetlen képek miniatűrjei a javasolt címkék helyét jelző határolókeret-átfedést jelenítenek meg. Ha nem jelöl ki javasolt címkeszűrőt, az összes címkézetlen kép a határolókeret átfedése nélkül jelenik meg.
        > [!div class="mx-imgBorder"]
        > ![A javasolt címkék kötegelt módban jelennek meg a szűrőkkel rendelkező OD esetében.](./media/suggested-tags/od-batch-mode.png)

        Az objektumészlelési címkék megerősítéséhez a katalógus minden egyes képére alkalmaznia kell őket.

        ![A javasolt címkék az OD egyes képmódjában jelennek meg.](./media/suggested-tags/od-individual-image-mode.png)
1. Indítsa el újra a betanítási folyamatot.
1. Ismételje meg az előző lépéseket, amíg meg nem elégszik a javaslat minőségével.

## <a name="next-steps"></a>További lépések

Kövesse a rövid útmutatót a Custom Vision projektek létrehozásának és betanításának megkezdéséhez.

* [Tartalombesoroló létrehozása](getting-started-build-a-classifier.md)
* [Objektumérzékelő készítése](get-started-build-detector.md)
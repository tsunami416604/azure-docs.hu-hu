---
title: Batch-előrejelzések futtatása az Azure Machine Learning Designerrel (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan taníthatja be a modellt, és a tervező használatával kötegelt előrejelzési folyamatot állíthat be. Telepítse a folyamatot paraméterezett webszolgáltatásként, amely bármely HTTP-tárból indítható.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: peterlu
author: peterclu
ms.date: 02/24/2020
ms.custom: Ignite2019
ms.openlocfilehash: 01d69bffcf2c17abceba8ba2e0893360bead8b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477221"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer-preview"></a>Batch-előrejelzések futtatása az Azure Machine Learning Designerrel (előzetes verzió)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Ebben a cikkben megtudhatja, hogyan használhatja a tervezőt egy kötegelt előrejelzési folyamat létrehozásához. A kötegelt előrejelzés lehetővé teszi, hogy folyamatosan nagy adatkészleteket szerezzen igény szerint egy olyan webszolgáltatás használatával, amely bármely HTTP-tárból aktiválható.

Ebben az útmutatóban megtanulod a következő feladatok elvégzését:

> [!div class="checklist"]
> * Kötegkövetkeztetési folyamat létrehozása és közzététele
> * Folyamatvégpont felhasználása
> * Végpontverziók kezelése

Ha meg szeretné tudni, hogyan állíthatja be a kötegelt pontozási szolgáltatásokat az SDK használatával, olvassa el a mellékelt [útmutatót.](how-to-run-batch-predictions.md)

## <a name="prerequisites"></a>Előfeltételek

Ez a how-to feltételezi, hogy már van egy betanítási folyamat. Egy irányított bevezetés a tervező, teljes [része az első a tervező bemutató](tutorial-designer-automobile-price-train-score.md). 

## <a name="create-a-batch-inference-pipeline"></a>Kötegkövetkeztetési folyamat létrehozása

A betanítási folyamatot legalább egyszer futtatni kell ahhoz, hogy ferencezési folyamatot hozzon létre.

1. Nyissa meg a **Tervező** lapot a munkaterületen.

1. Válassza ki a betanítási folyamat, amely betanítása a modellt szeretne használni, hogy előrejelzést.

1. **Küldje el** a folyamatot.

    ![A folyamat beküldése](./media/how-to-run-batch-predictions-designer/run-training-pipeline.png)

Most, hogy a betanítási folyamat futtatása, létrehozhat egy kötegelt következtetési folyamat.

1. A **Küldés**csoportban válassza az új **legördülő legördülő legördülő legördülő legördülő legördülő legördülő menüt.**

1. Válassza a **Kötegkövetkeztetések et**.

    ![Kötegkövetkeztetési folyamat létrehozása](./media/how-to-run-batch-predictions-designer/create-batch-inference.png)
    
Az eredmény egy alapértelmezett kötegkövetkeztetési folyamat. 

### <a name="add-a-pipeline-parameter"></a>Folyamatparaméter hozzáadása

Az új adatokra vonatkozó előrejelzések létrehozásához manuálisan csatlakoztathat egy másik adatkészletet ebben a folyamatpiszkot nézetben, vagy létrehozhat egy paramétert az adatkészlethez. A paraméterek segítségével módosíthatja a kötegelt következtetési folyamat viselkedését futásidőben.

Ebben a szakaszban hozzon létre egy adatkészlet paramétert, amely egy másik adatkészletet ad meg az előrejelzésekhez.

1. Jelölje ki az adatkészletmodult.

1. A vászontól jobbra egy ablaktábla jelenik meg. Az ablaktábla alján válassza a **Beállítás folyamatparaméterként lehetőséget.**
   
    Adja meg a paraméter nevét, vagy fogadja el az alapértelmezett értéket.

## <a name="publish-your-batch-inferencing-pipeline"></a>A köteghivatkozó folyamat közzététele

Most már készen áll a következtetési folyamat telepítésére. Ez telepíti a folyamatot, és elérhetővé teszi mások számára.

1. Válassza ki a **Közzététel** gombot.

1. A megjelenő párbeszédpanelen bontsa ki a **PipelineEndpoint**legördülő menüt, és válassza az **Új csővezetékvégpont lehetőséget.**

1. Adja meg a végpont nevét és a nem kötelező leírást.

    A párbeszéd alján láthatja a betanítás során használt adatkészlet-azonosító alapértelmezett értékével konfigurált paramétert.

1. Kattintson a **Publish** (Közzététel) elemre.

![Folyamat közzététele](./media/how-to-run-batch-predictions-designer/publish-inference-pipeline.png)


## <a name="consume-an-endpoint"></a>Végpont felhasználása

Most egy közzétett folyamat egy adatkészlet paraméter. A folyamat a betanítási folyamatban létrehozott betanított modellt fogja használni a paraméterként megadott adatkészlet pontozásához.

### <a name="submit-a-pipeline-run"></a>Folyamatfuttatás beküldése 

Ebben a szakaszban manuális folyamatfuttatást fog beállítani, és módosítja a folyamat paraméterét az új adatok pontozásához. 

1. A központi telepítés befejezése után nyissa meg a **Végpontok** szakaszt.

1. Válassza **a Folyamat végpontjait**.

1. Válassza ki a létrehozott végpont nevét.

![Végpont hivatkozása](./media/how-to-run-batch-predictions-designer/manage-endpoints.png)

1. Válassza **a Közzétett folyamatok lehetőséget.**

    Ez a képernyő az összes közzétett folyamatok közzé ebben a végpontban közzétett.

1. Válassza ki a közzétett folyamatot.

    A folyamat részletei lap részletes futtatási előzményeket és kapcsolati karakterlánc-információkat jelenít meg a folyamathoz. 
    
1. Válassza **a Küldés** lehetőséget a folyamat manuális futtatásához.

    ![A csővezeték részletei](./media/how-to-run-batch-predictions-designer/submit-manual-run.png)
    
1. Módosítsa a paramétert úgy, hogy egy másik adatkészletet használjon.
    
1. Válassza **a Küldés** lehetőséget a folyamat futtatásához.

### <a name="use-the-rest-endpoint"></a>A REST-végpont használata

A végpontok és a közzétett folyamat felhasználásával kapcsolatos információkat a **Végpontok** szakaszban találhatja meg.

A folyamatvégpont REST-végpontját a futtatási áttekintés panelen találhatja meg. A végpont hívásával az alapértelmezett közzétett folyamatot fogyasztja.

A közzétett folyamatot a Közzétett folyamatok lapon is **felhasználhatja.** Jelöljön ki egy közzétett folyamatot, és keresse meg a REST-végpontját. 

![A rest-végpont részletei](./media/how-to-run-batch-predictions-designer/rest-endpoint-details.png)

REST-hívás hoz, szüksége lesz egy OAuth 2.0-s bemutatóra szóló típusú hitelesítési fejlécre. A munkaterület hitelesítésének beállításával és a paraméterezett REST-hívással kapcsolatos további részleteket az alábbi [oktatóanyag-szakaszban](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint) ismertetheti.

## <a name="versioning-endpoints"></a>Végpontok verziószámozása

A tervező egy verziót rendel minden további folyamathoz, amelyet közzétesz egy végponthoz. Megadhatja a folyamat verzióját, amelyet paraméterként kíván végrehajtani a REST-hívásban. Ha nem ad meg verziószámot, a tervező az alapértelmezett folyamatot fogja használni.

Folyamat közzétételekor választhatja azt is, hogy az adott végpont új alapértelmezett folyamata legyen.

![Alapértelmezett folyamat beállítása](./media/how-to-run-batch-predictions-designer/set-default-pipeline.png)

Új alapértelmezett folyamatot is beállíthat a végpont **Közzétett folyamatok** lapján.

![Alapértelmezett folyamat beállítása](./media/how-to-run-batch-predictions-designer/set-new-default-pipeline.png)

## <a name="next-steps"></a>További lépések

Kövesse a tervező [oktatóanyag](tutorial-designer-automobile-price-train-score.md) betanításához és üzembe helyezéséhez egy regressziós modell.
''
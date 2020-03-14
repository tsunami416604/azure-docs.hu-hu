---
title: Modellek újratanítása Azure Machine Learning Designer használatával (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan lehet a modelleket a Azure Machine Learning Designerben (előzetes verzió) közzétett folyamatokkal áttanítani.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: keli19
author: likebupt
ms.date: 02/24/2020
ms.openlocfilehash: 264b169eefde18880f50feae2554aa3ca7037b1f
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368162"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Modellek újratanítása Azure Machine Learning designerrel (előzetes verzió)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Ebben a útmutatóban megismerheti, hogyan végezheti el a gépi tanulási modell újratanítását a Azure Machine Learning Designer használatával. Megtudhatja, hogyan használhatja a közzétett folyamatokat a gépi tanulási munkafolyamatok újraképzéshez való automatizálásához.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Gépi tanulási modell betanítása.
> * Hozzon létre egy folyamat paramétert.
> * A betanítási folyamat közzététele.
> * A modell újratanítása.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLFree).
* Azure Machine Learning munkaterület a vállalati SKU-val.

Ez a cikk azt feltételezi, hogy alapvető ismeretekkel rendelkezik a folyamatok létrehozásához a tervezőben. A Designer bevezetéséhez fejezze be az [oktatóanyagot](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Mintavételezési folyamat

Az ebben a cikkben használt folyamat a [3. minta: bevétel előrejelzése](how-to-designer-sample-classification-predict-income.md)alapján megtalált, módosított verzió. A minta adatkészlet helyett az [adatimportálási](algorithm-module-reference/import-data.md) modult használja, hogy bemutassa, hogyan kell a modelleket saját adataival betanítani.

![Képernyőfelvétel a módosított mintavételi folyamatról az Adatimportálási modul kiemelésével](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="train-a-machine-learning-model"></a>Gépi tanulási modell betanítása

Modell újratanításához kezdeti modellre van szükség. Ebből a szakaszból megtudhatja, hogyan végezheti el a modell betanítását és a mentett modell elérését a tervező használatával.

1. Válassza az **adatimportálás** modult.
1. A Tulajdonságok ablaktáblán válasszon egy adatforrást.

   ![Az Adatimportálási modul minta konfigurációját bemutató képernyőkép](./media/how-to-retrain-designer/import-data-settings.png)

   Ebben a példában az adattárolót egy Azure- [adattár](how-to-access-data.md)tárolja. Ha még nem rendelkezik adattárral, létrehozhat egyet most az **új adattár**lehetőség kiválasztásával.

1. Adja meg az adatai elérési útját. Kiválaszthatja az adattár tallózási **útvonalát** is. 
1. Válassza a **Futtatás** lehetőséget a vászon tetején.
    
   > [!NOTE]
   > Ha már beállított egy alapértelmezett számítást ehhez a folyamat-tervezethez, a folyamat automatikusan elindul. Ellenkező esetben a beállítások panelen megjelenő utasításokat követve állíthatja be most.

### <a name="find-your-trained-model"></a>A betanított modell megkeresése

A tervező az alapértelmezett Storage-fiókba menti az összes folyamat kimenetét, beleértve a betanított modelleket is. A betanított modellt közvetlenül a tervezőben is elérheti:

1. Várjon, amíg a folyamat befejeződik.
1. Válassza ki a **Train Model** modult.
1. A beállítások ablaktáblán válassza a **kimenetek + naplók**lehetőséget.
1. Válassza ki a **kimenet megtekintése** ikont, és kövesse az előugró ablakban megjelenő utasításokat a betanított modell megkereséséhez.

![A betanított modell letöltését bemutató képernyőkép](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="create-a-pipeline-parameter"></a>Folyamat paraméterének létrehozása

A folyamat paramétereinek hozzáadásával dinamikusan állíthatja be a változókat futásidőben. Ehhez a folyamathoz adjon hozzá egy paramétert a betanítási adat elérési útjához, hogy egy új adatkészleten át tudja képezni a modellt.

1. Válassza az **adatimportálás** modult.
1. A beállítások ablaktáblán válassza az **elérési út** mező feletti ellipsziseket.
1. Válassza **a Hozzáadás a folyamathoz paramétert**.
1. Adja meg a paraméter nevét és az alapértelmezett értéket.

   > [!NOTE]
   > A folyamat paramétereinek vizsgálatához és szerkesztéséhez válassza a folyamat piszkozatának címe melletti **Beállítások** fogaskerék ikont. 

![Képernyőkép, amely bemutatja, hogyan lehet létrehozni egy folyamat paramétert](media/how-to-retrain-designer/add-pipeline-parameter.png)

## <a name="publish-a-training-pipeline"></a>Betanítási folyamat közzététele

Amikor közzétesz egy folyamatot, egy folyamat-végpontot hoz létre. A folyamat végpontjai lehetővé teszik a folyamatok újrafelhasználását és kezelését az ismételhetőség és az automatizálás céljából. Ebben a példában beállította a folyamat átképzését.

1. Válassza a **Közzététel** a tervező vászon felett lehetőséget.
1. Válasszon ki vagy hozzon létre egy folyamat-végpontot.

   > [!NOTE]
   > Több folyamat is közzétehető egyetlen végponton. A végpont minden folyamata egy verziószámot kap, amelyet a folyamat végpontjának meghívásakor adhat meg.

1. Kattintson a **Publish** (Közzététel) elemre.

## <a name="retrain-your-model"></a>A modell újratanítása

Most, hogy már rendelkezik egy közzétett betanítási folyamattal, felhasználhatja a modell új adatkezeléssel való átképzéséhez. Elküldheti a futtatásokat egy folyamat végpontból a Azure Portalból, vagy programozott módon elküldheti őket.

### <a name="submit-runs-by-using-the-designer"></a>Futtatások beküldése a Designer használatával

A következő lépésekkel küldhet el egy folyamat-végpontot a tervezőből:

1. Lépjen a **végpontok** lapra.
1. Válassza a **folyamat végpontjai** lapot.
1. Válassza ki a folyamat végpontját.
1. Válassza a **közzétett folyamatok** fület.
1. Válassza ki a futtatni kívánt folyamatot.
1. Válassza a **Küldés**lehetőséget.
1. A beállítás párbeszédpanelen megadhat egy új értéket a bemeneti adatok elérési útja értékhez. Ez az érték az új adatkészletre mutat.

![Képernyőkép, amely bemutatja, hogyan állíthat be egy paraméteres folyamat futtatását a tervezőben](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Futtatások küldése kód használatával

Az Áttekintés panelen megtalálhatja a közzétett folyamat REST-végpontját. A végpont meghívásával Újrataníthatja a közzétett folyamatot.

A REST-hívások elvégzéséhez szüksége lesz egy OAuth 2,0 tulajdonosi típusú hitelesítési fejlécre. További információ a munkaterület hitelesítésének beállításáról és a paraméteres REST-hívás létrehozásáról: [Azure Machine learning folyamat létrehozása a Batch pontozáshoz](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Következő lépések

A regressziós modell betanításához és üzembe helyezéséhez kövesse a [tervezői oktatóanyagot](tutorial-designer-automobile-price-train-score.md) .

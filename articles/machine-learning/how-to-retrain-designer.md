---
title: Modellek újratanítása az Azure Machine Learning designer használatával (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan lehet újrabetanítása modellek közzétett folyamatok az Azure Machine Learning designer (előzetes verzió).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: keli19
author: likebupt
ms.date: 02/24/2020
ms.openlocfilehash: c8791e933882832dc7b0037c860a4c4e1e9a54c7
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389035"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Modellek újratanítása az Azure Machine Learning Designerrel (előzetes verzió)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Ebben az útmutató cikkben megtudhatja, hogyan használhatja az Azure Machine Learning-tervezőt egy gépi tanulási modell újratanításához. Ismerje meg, hogyan használhatja a közzétett folyamatokat a gépi tanulási munkafolyamatok automatizálására átképzéshez.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Gépi tanulási modell betanítása.
> * Hozzon létre egy folyamatparamétert.
> * Tegye közzé a betanítási folyamatot.
> * A modell újratanítása.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://aka.ms/AMLFree)
* Egy Azure Machine Learning-munkaterület a vállalati termékváltozattal.

Ez a cikk feltételezi, hogy alapvető ismeretekkel rendelkezik a tervezőben lévő folyamatok létrehozásához. Egy irányított bevezetés a tervező, töltse ki a [tutorial](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Mintafolyamat

A csővezeték használt ebben a cikkben egy módosított változata az egyik található [minta 3: Jövedelem előrejelzés](how-to-designer-sample-classification-predict-income.md). [Az Adatok importálása](algorithm-module-reference/import-data.md) modult használja a minta adatkészlet helyett, hogy megmutassa, hogyan taníthatja be a modellt a saját adatai használatával.

![Képernyőkép, amely en a módosított mintafolyamat látható az Adatok importálása modult kiemelő dobozzal](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="train-a-machine-learning-model"></a>Gépi tanulási modell betanítása

A modell újratanításához egy kezdeti modellre van szükség. Ebben a szakaszban megtudhatja, hogyan taníthatja be a modellt, és hogyan érheti el a mentett modellt a tervező használatával.

1. Válassza az **Adatok importálása** modult.
1. A tulajdonságok ablaktáblán adjon meg egy adatforrást.

   ![Képernyőkép, amely az Adatok importálása modul mintakonfigurációját mutatja](./media/how-to-retrain-designer/import-data-settings.png)

   Ebben a példában az adatok egy [Azure-adattárban tárolódnak.](how-to-access-data.md) Ha még nem rendelkezik adattár, hozhat létre egyet most kiválasztásával **Új adattár**.

1. Adja meg az adatok elérési útját. Az elérési **út tallózása** lehetőséget is választhatja az adattárba való tallózáshoz. 
1. Válassza a **Küldés** lehetőséget a vászon tetején.
    
   > [!NOTE]
   > Ha már beállított egy alapértelmezett számítási folyamat tervezetét, a folyamat automatikusan fog futni. Ellenkező esetben a beállítások ablaktáblán megjelenő utasításokat követve most is beállíthat egyet.

### <a name="find-your-trained-model"></a>A betanított modell megkeresése

A tervező menti az összes folyamat kimenetek, beleértve a betanított modellek, az alapértelmezett tárfiókba. Azonban a betanított modelleket közvetlenül a tervezőben is elérheti:

1. Várja meg, amíg a folyamat futása befejeződik.
1. Válassza ki a **Vonatmodell** modult.
1. A beállítások ablaktáblán válassza a **Kimenetek+naplók lehetőséget.**
1. Válassza a **Kimenet megtekintése** ikont, és kövesse az előugró ablakban található utasításokat a betanított modell megkereséséhez.

![Képernyőkép, amely bemutatja a betanított modell letöltését](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="create-a-pipeline-parameter"></a>Folyamatparaméter létrehozása

A változók futásidőben dinamikusan beállított folyamatparamétereinek hozzáadásával. Ehhez a folyamathoz adjon hozzá egy paramétert a betanítási adatok elérési útjára, hogy újrataníthassa a modellt egy új adatkészleten.

1. Válassza az **Adatok importálása** modult.
1. A beállítások ablaktáblán jelölje ki a **Görbe** mező feletti három pontot.
1. Válassza **a Hozzáadás a folyamathoz paramétert.**
1. Adja meg a paraméter nevét és az alapértelmezett értéket.

   > [!NOTE]
   > A folyamatparamétereket a folyamatvázlat címe melletti **Beállítások** fogaskerék ikonra kattintva vizsgálhatja meg és szerkesztheti. 

![Képernyőkép, amely bemutatja a folyamatparaméter létrehozását](media/how-to-retrain-designer/add-pipeline-parameter.png)

## <a name="publish-a-training-pipeline"></a>Betanítási folyamat közzététele

Folyamat közzétételekor létrehoz egy folyamatvégpontot. A folyamatvégpontok lehetővé teszik a folyamatok újrafelhasználását és kezelését az ismételhetőség és az automatizálás érdekében. Ebben a példában beállította a folyamatátképzés.

1. Válassza a **Közzététel** lehetőséget a tervezői vászon felett.
1. Jelöljön ki vagy hozzon létre egy folyamatvégpontot.

   > [!NOTE]
   > Egy végponton több folyamat is közzétehető. A végpont minden folyamata kap egy verziószámot, amelyet a folyamatvégpont hívásakor adhat meg.

1. Kattintson a **Publish** (Közzététel) elemre.

## <a name="retrain-your-model"></a>A modell újratanítása

Most, hogy rendelkezik egy közzétett betanítási folyamattal, használhatja a modell újratanításához új adatok használatával. Az Azure Portalról elküldheti a folyamatvégpontról történő futtatásokat, vagy programozott módon elküldheti őket.

### <a name="submit-runs-by-using-the-designer"></a>Futtatások beküldése a tervező használatával

A következő lépésekkel küldjön el egy folyamatvégpontot a tervezőtől:

1. Nyissa meg a **Végpontok** lapot.
1. Válassza a **Folyamat végpontok** fülét.
1. Válassza ki a folyamatvégpontot.
1. Válassza a **Közzétett folyamatok** lapot.
1. Válassza ki a futtatni kívánt folyamatot.
1. Válassza a **Küldés** lehetőséget.
1. A beállítási párbeszédpanelen új értéket adhat meg a bemeneti adatok elérési útértékéhez. Ez az érték az új adatkészletre mutat.

![Képernyőkép, amely bemutatja, hogyan állítható be egy paraméterezett folyamat futtatása a tervezőben](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Fut küldés kód használatával

A közzétett folyamat REST-végpontját az áttekintő panelen találhatja meg. A végpont hívásával újrabetaníthatja a közzétett folyamatot.

REST-hívás hozásához OAuth 2.0-s bemutatóra szóló típusú hitelesítési fejlécre van szükség. A hitelesítés beállításáról a munkaterületre és a paraméterezett REST-hívás létrehozásáról a [Kötegelt pontozáshoz egy Azure Machine Learning-folyamat létrehozása](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)című témakörben talál.

## <a name="next-steps"></a>További lépések

Kövesse a [tervező oktatóanyag](tutorial-designer-automobile-price-train-score.md) betanításához és üzembe helyezéséhez egy regressziós modell.

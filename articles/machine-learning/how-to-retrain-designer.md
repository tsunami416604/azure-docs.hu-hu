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
ms.date: 04/06/2020
ms.openlocfilehash: 721e5414fc4753cd5d58a17fc7ed51ea99868778
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810348"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Modellek újratanítása az Azure Machine Learning Designerrel (előzetes verzió)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Ebben az útmutató cikkben megtudhatja, hogyan használhatja az Azure Machine Learning-tervezőt egy gépi tanulási modell újratanításához. A közzétett folyamatok segítségével automatizálhatja a munkafolyamatot, és paramétereket állíthat be a modell új adatokra való betanításához. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Gépi tanulási modell betanítása.
> * Hozzon létre egy folyamatparamétert.
> * Tegye közzé a betanítási folyamatot.
> * A modell újratanítása új paraméterekkel.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning-munkaterület a vállalati termékváltozattal.
* A tervező számára elérhető adatkészlet. Ez a következők egyike lehet:
   * Az Azure Machine Learning regisztrált adatkészlete
    
     **-vagy-**
   * Az Azure Machine Learning-adattárban tárolt adatfájl.
   
Az adathozzáférésről a tervező használatával az [Adatok importálása a tervezőbe](how-to-designer-import-data.md)című témakörben talál további információt.

Ez a cikk azt is feltételezi, hogy alapvető ismeretekkel rendelkezik a tervezőben lévő folyamatok létrehozásához. Egy irányított bevezetés, töltse ki a [tutorial](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Mintafolyamat

A csővezeték használt ebben a cikkben egy módosított változata [Minta 3: Jövedelem előrejelzés](samples-designer.md#classification-samples). A folyamat az [Adatok importálása](algorithm-module-reference/import-data.md) modult használja a minta adatkészlet helyett, hogy megmutassa, hogyan taníthatja be a modelleket a saját adatai használatával.

![Képernyőkép, amely en a módosított mintafolyamat látható az Adatok importálása modult kiemelő dobozzal](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>Folyamatparaméter létrehozása

Hozzon létre folyamatparamétereket a változók futásidőben való dinamikus beállításához. Ebben a példában a betanítási adatok elérési útját rögzített értékről paraméterre módosítja, így a modellt különböző adatokon újrataníthatja.

1. Válassza az **Adatok importálása** modult.

    > [!NOTE]
    > Ez a példa az Adatok importálása modult használja a regisztrált adattárban lévő adatok eléréséhez. Azonban hasonló lépéseket is követhet, ha alternatív adatelérési mintákat használ.

1. A modul részletablakában, a vászontól jobbra válassza ki az adatforrást.

1. Adja meg az adatok elérési útját. Az Elérési **út tallózása** lehetőséget is választhatja a fájlfa tallózásához. 

1. Vigye át az **egérmutatót** a Görbe mezőre, és jelölje ki a megjelenő **Görbe** mező feletti három pontot.

    ![Képernyőkép, amely bemutatja a folyamatparaméter létrehozását](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. Válassza **a Hozzáadás a folyamathoz paramétert.**

1. Adja meg a paraméter nevét és az alapértelmezett értéket.

   > [!NOTE]
   > A folyamatparamétereket a folyamatvázlat címe melletti **Beállítások** fogaskerék ikonra kattintva vizsgálhatja meg és szerkesztheti. 

1. Kattintson a **Mentés** gombra.

1. Küldje el a folyamat futtatását.

## <a name="find-a-trained-model"></a>Betanított modell keresése

A tervező az összes folyamatkimenetet, beleértve a betanított modelleket is, az alapértelmezett munkaterületi tárfiókba menti. A betanított modelleket közvetlenül a tervezőben is elérheti:

1. Várja meg, amíg a folyamat futása befejeződik.
1. Válassza ki a **Vonatmodell** modult.
1. A modul részletei ablaktáblán, a vászontól jobbra válassza a **Kimenetek + naplók lehetőséget.**
1. A modell t a **más kimenetek** mellett a futó naplók.
1. Másik lehetőségként válassza a **Kimenet megtekintése** ikont. Itt a párbeszédben található utasításokat követve közvetlenül az adattárba navigálhat. 

![Képernyőkép, amely bemutatja a betanított modell letöltését](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="publish-a-training-pipeline"></a>Betanítási folyamat közzététele

A folyamat folyamatvégpontra való közzétételével a jövőben könnyen újra felhasználhatja a folyamatokat. A folyamatvégpont létrehoz egy REST-végpontot a jövőbeli folyamat meghívásához. Ebben a példában a folyamatvégpont lehetővé teszi a folyamat újratanítása a modell különböző adatokon.

1. Válassza a **Közzététel** lehetőséget a tervezői vászon felett.
1. Jelöljön ki vagy hozzon létre egy folyamatvégpontot.

   > [!NOTE]
   > Egy végponton több folyamat is közzétehető. Egy adott végpont minden folyamata kap egy verziószámot, amelyet a folyamatvégpont hívásakor adhat meg.

1. Kattintson a **Publish** (Közzététel) elemre.

## <a name="retrain-your-model"></a>A modell újratanítása

Most, hogy rendelkezik egy közzétett betanítási folyamattal, használhatja a modell újratanítására az új adatok. A folyamatvégpontról a stúdió munkaterületéről vagy programozott módon küldhet be futtatásokat.

### <a name="submit-runs-by-using-the-designer"></a>Futtatások beküldése a tervező használatával

A következő lépésekkel küldjön el egy paraméterezett folyamatvégpontot a tervezőtől:

1. Nyissa meg a **végpontok** lapot a stúdió munkaterületén.
1. Válassza a **Folyamat végpontok** fülét. Ezután válassza ki a folyamatvégpontot.
1. Válassza a **Közzétett folyamatok** lapot. Ezután válassza ki a futtatni kívánt folyamatverziót.
1. Válassza a **Küldés** lehetőséget.
1. A beállítási párbeszédpanelen megadhatja a futtatás paraméterértékeit. Ebben a példában frissítse az adatelérési utat a modell betanításához egy nem amerikai adatkészlet használatával.

![Képernyőkép, amely bemutatja, hogyan állítható be egy paraméterezett folyamat futtatása a tervezőben](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Fut küldés kód használatával

A közzétett folyamat REST-végpontját az áttekintő panelen találhatja meg. A végpont hívásával újrabetaníthatja a közzétett folyamatot.

REST-hívás hozásához OAuth 2.0-s bemutatóra szóló típusú hitelesítési fejlécre van szükség. A hitelesítés beállításáról a munkaterületre és a paraméterezett REST-hívás létrehozásáról a [Kötegelt pontozáshoz egy Azure Machine Learning-folyamat létrehozása](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)című témakörben talál.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan hozhat létre paraméterezett betanítási folyamat végpontot a tervező használatával.

A teljes forgatókönyv, hogyan telepítheti a modell t előrejelzéseket, tekintse meg a [tervező oktatóanyag](tutorial-designer-automobile-price-train-score.md) betanításához és üzembe helyezéséhez egy regressziós modell.

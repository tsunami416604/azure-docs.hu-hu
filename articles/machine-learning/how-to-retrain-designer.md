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
ms.date: 04/06/2020
ms.custom: designer
ms.openlocfilehash: c466684bf5c07b5e88e8052c29aa9fb0b8583a89
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84430071"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Modellek újratanítása az Azure Machine Learning Designerrel (előzetes verzió)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Ebben a útmutatóban megismerheti, hogyan végezheti el a gépi tanulási modell újratanítását a Azure Machine Learning Designer használatával. A közzétett folyamatok segítségével automatizálhatja a munkafolyamatot, és paramétereket állíthat be a modell új adatokra való betanításához. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Gépi tanulási modell betanítása.
> * Hozzon létre egy folyamat paramétert.
> * A betanítási folyamat közzététele.
> * A modell újratanítása új paraméterekkel.

## <a name="prerequisites"></a>Előfeltételek

* Azure Machine Learning munkaterület a vállalati SKU-val.
* Fejezze be a How-to sorozat 1. részét, [alakítsa át az adatelemzést a tervezőben](how-to-designer-transform-data.md).

A cikk azt is feltételezi, hogy alapvető ismeretekkel rendelkezik a folyamatok létrehozásához a tervezőben. Az interaktív bevezetéshez végezze el az [oktatóanyagot](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Mintavételezési folyamat

A cikkben használt folyamat a 3. minta módosított verziója [: bevétel előrejelzése](samples-designer.md#classification). A folyamat a minta adatkészlet helyett az [adat importálása](algorithm-module-reference/import-data.md) modult használja, hogy megmutassa, hogyan taníthatja be a modelleket a saját adatai alapján.

![Képernyőfelvétel a módosított mintavételi folyamatról az Adatimportálási modul kiemelésével](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>Folyamat paraméterének létrehozása

Folyamat paramétereinek létrehozása a változók dinamikus beállításához futásidőben. Ebben a példában egy rögzített értékről egy paraméterre módosítja a betanítási adatok elérési útját, hogy a modell a különböző adatokon is áttanítható legyen.

1. Válassza az **adatimportálás** modult.

    > [!NOTE]
    > Ez a példa az Adatimportálási modul használatával fér hozzá a regisztrált adattárban található adategységekhez. Azonban a hasonló lépéseket követve alternatív adathozzáférési mintákat is használhat.

1. A modul részletei ablaktáblán, a vászontól jobbra válassza ki az adatforrást.

1. Adja meg az adatai elérési útját. A **tallózási útvonalat** is kiválaszthatja a fájl faszerkezetének tallózásához. 

1. Az **elérési út** mezőre, majd a megjelenő **path (útvonal** ) mező fölé kattintva válassza ki a megjelenő ellipsziseket.

    ![Képernyőkép, amely bemutatja, hogyan lehet létrehozni egy folyamat paramétert](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. Válassza **a Hozzáadás a folyamathoz paramétert**.

1. Adja meg a paraméter nevét és az alapértelmezett értéket.

   > [!NOTE]
   > A folyamat paramétereinek vizsgálatához és szerkesztéséhez válassza a folyamat piszkozatának címe melletti **Beállítások** fogaskerék ikont. 

1. Kattintson a **Mentés** gombra.

1. A folyamat futtatásának elküldése.

## <a name="find-a-trained-model"></a>Betanított modell keresése

A tervező az összes folyamat kimenetét, beleértve a betanított modelleket is, az alapértelmezett munkaterület-Storage-fiókba menti. A tervezőben közvetlenül is elérheti a betanított modelleket:

1. Várjon, amíg a folyamat befejeződik.
1. Válassza ki a **Train Model** modult.
1. A modul részletek ablaktábláján, a vászontól jobbra válassza a **kimenetek + naplók**lehetőséget.
1. A modellt **más kimenetekben** is megtalálhatja a futtatási naplókkal együtt.
1. Másik lehetőségként válassza a **kimenet megtekintése** ikont. Innen követheti a párbeszédablak utasításait, hogy közvetlenül az adattárhoz navigáljon. 

![A betanított modell letöltését bemutató képernyőkép](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="publish-a-training-pipeline"></a>Betanítási folyamat közzététele

Egy folyamat közzététele egy folyamat végpontján, hogy a jövőben könnyedén újra fel lehessen használni a folyamatokat. A folyamat végpontja létrehoz egy REST-végpontot a folyamat későbbi meghívásához. Ebben a példában a folyamat végpontja lehetővé teszi, hogy újra felhasználja a folyamatot a modell más adattípusra való újratanításához.

1. Válassza a **Közzététel** a tervező vászon felett lehetőséget.
1. Válasszon ki vagy hozzon létre egy folyamat-végpontot.

   > [!NOTE]
   > Több folyamat is közzétehető egyetlen végponton. Egy adott végpont minden folyamata egy verziószámot kap, amelyet megadhat a folyamat végpontjának meghívásakor.

1. Kattintson a **Közzététel** lehetőségre.

## <a name="retrain-your-model"></a>A modell újratanítása

Most, hogy már rendelkezik egy közzétett betanítási folyamattal, felhasználhatja a modell új adataira való átképzéséhez. Elküldheti a futtatásokat egy folyamat-végpontról a Studio munkaterületről vagy programozott módon.

### <a name="submit-runs-by-using-the-designer"></a>Futtatások beküldése a Designer használatával

A következő lépések végrehajtásával elküldheti a paraméteres folyamat végpontját a tervezőből:

1. Ugrás a **végpontok** lapra a Studio-munkaterületen.
1. Válassza a **folyamat végpontjai** lapot. Ezután válassza ki a folyamat végpontját.
1. Válassza a **közzétett folyamatok** fület. Ezután válassza ki a futtatni kívánt folyamat verzióját.
1. Válassza a **Küldés** lehetőséget.
1. A telepítés párbeszédpanelen megadhatja a Futtatás paramétereinek értékeit. Ebben a példában frissítse az adatelérési utat a modell betanításához a nem USA-beli adatkészlet használatával.

![Képernyőkép, amely bemutatja, hogyan állíthat be egy paraméteres folyamat futtatását a tervezőben](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Futtatások küldése kód használatával

Az Áttekintés panelen megtalálhatja a közzétett folyamat REST-végpontját. A végpont meghívásával Újrataníthatja a közzétett folyamatot.

A REST-hívások elvégzéséhez szüksége lesz egy OAuth 2,0 tulajdonosi típusú hitelesítési fejlécre. További információ a munkaterület hitelesítésének beállításáról és a paraméteres REST-hívás létrehozásáról: [Azure Machine learning folyamat létrehozása a Batch pontozáshoz](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan hozhat létre egy paraméteres betanítási folyamat végpontját a tervező használatával.

Az előrejelzések készítésére szolgáló modellek üzembe helyezésének teljes bemutatása a [tervezői oktatóanyagban](tutorial-designer-automobile-price-train-score.md) a regressziós modell betanítása és üzembe helyezése című témakörben olvasható.

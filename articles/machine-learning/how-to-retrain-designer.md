---
title: Modellek újratanítása Azure Machine Learning Designer használatával (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan lehet a modelleket a Azure Machine Learning Designerben (előzetes verzió) közzétett folyamatokkal áttanítani.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: peterlu
author: peterclu
ms.date: 12/15/2019
ms.openlocfilehash: 734acd712eb954e66a9c0b037d10b7b1fd626c6a
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732161"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Modellek újratanítása Azure Machine Learning designerrel (előzetes verzió)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a útmutatóban megismerheti, hogyan végezheti el a gépi tanulási modell újratanítását a Azure Machine Learning Designer használatával. Megtudhatja, hogyan használhatja a közzétett folyamatokat a gépi tanulási munkafolyamatok újraképzéshez való automatizálásához.

Ebben a cikkben az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Gépi tanulási modell betanítása.
> * Hozzon létre egy folyamat paramétert.
> * A betanítási folyamat közzététele.
> * A modell újratanítása.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLFree).

* Azure Machine Learning munkaterület a vállalati SKU-val.

Ez az útmutató azt feltételezi, hogy alapszintű ismeretekkel rendelkezik a folyamatok létrehozásához a tervezőben. A Designer bevezetéséhez fejezze be az [oktatóanyagot](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Mintavételezési folyamat

Az ebben a cikkben használt folyamat a [3. minta-bevétel előrejelzésében](how-to-designer-sample-classification-predict-income.md)található, egy módosított verzió. A minta adatkészlet helyett az [adat importálása](algorithm-module-reference/import-data.md) modult használja, hogy megmutassa, hogyan taníthat a modelleket a saját adataival.

![Képernyőfelvétel a módosított mintavételi folyamatról az Adatimportálási modul kiemelésével](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="train-a-machine-learning-model"></a>Gépi tanulási modell betanítása

Modell újratanításához kezdeti modellre van szükség. Ebből a szakaszból megtudhatja, hogyan végezheti el a modell betanítását és a mentett modell elérését a Designer használatával.

1. Válassza az **adatimportálás** modult.
1. A Tulajdonságok ablaktáblán válasszon egy adatforrást.

    ![Az Adatimportálási modul minta konfigurációját ábrázoló képernyőfelvétel](./media/how-to-retrain-designer/import-data-settings.png)

    Ebben a példában az adattárolót egy Azure- [adattár](how-to-access-data.md)tárolja. Ha még nem rendelkezik adattárral, létrehozhat egyet most az **új adattár**lehetőség kiválasztásával.

1. Adja meg az adatai elérési útját. A **tallózási útvonalat** is kiválaszthatja vizuálisan navigálhat az adattárban. 

1. Válassza a **Futtatás** lehetőséget a vászon tetején.
    
    > [!NOTE]
    > Ha már beállított egy alapértelmezett számítást ehhez a folyamat-tervezethez, a folyamat automatikusan elindul. Ellenkező esetben a beállítások ablaktáblában megjelenő utasításokat követve láthatja, hogy most már be van állítva.

### <a name="locate-your-trained-model"></a>A betanított modell megkeresése

A tervező az alapértelmezett Storage-fiókba menti az összes folyamat kimenetét, beleértve a betanított modelleket is. A betanított modellt közvetlenül a tervezőben is elérheti:

1. Várjon, amíg a folyamat befejeződik.

1. Válassza ki a **Train Model** modult.

1. A beállítások ablaktáblán válassza a **kimenetek**lehetőséget.

1. A modell letöltéséhez válassza a **Trained_model** lehetőséget.

![A betanított modell letöltését bemutató képernyőkép](./media/how-to-retrain-designer/download-model.png)

## <a name="create-a-pipeline-parameter"></a>Folyamat paraméterének létrehozása

A folyamat paramétereinek hozzáadásával dinamikusan állíthatja be a változókat futásidőben. Ehhez a folyamathoz adjon hozzá egy paramétert a betanítási adat elérési útjához, hogy egy új adatkészleten át tudja képezni a modellt.

1. Válassza az **adatimportálás** modult.
1. A beállítások ablaktáblán válassza az **elérési út** mező feletti ellipsziseket.
1. Válassza **a Hozzáadás a folyamathoz paramétert**.
1. Adja meg a paraméter nevét és az alapértelmezett értéket.

    > [!NOTE]
    > A folyamat paramétereinek vizsgálatához és szerkesztéséhez válassza a folyamat piszkozatának címe melletti **Beállítások fogaskerék ikont** . 

![A folyamat paramétereinek létrehozását bemutató képernyőkép](media/how-to-retrain-designer/add-pipeline-parameter.png)

## <a name="publish-a-training-pipeline"></a>Betanítási folyamat közzététele

Amikor közzétesz egy folyamatot, egy folyamat-végpontot hoz létre. A folyamat végpontjai lehetővé teszik a folyamatok újrafelhasználását és kezelését az ismételhetőség és az automatizálás céljából. Ebben a példában a folyamat újraképzésre van beállítva.

1. Válassza a **Közzététel** a tervező vászon felett lehetőséget.
1. Válasszon ki vagy hozzon létre egy új folyamat-végpontot.

    > [!NOTE]
    > Több folyamat is közzétehető egyetlen végponton. A végpont minden folyamata egy verziószámot kap, amelyet a folyamat végpontjának meghívásakor adhat meg.

1. Kattintson a **Publish** (Közzététel) elemre.

## <a name="retrain-your-model"></a>A modell újratanítása

Most, hogy már rendelkezik egy közzétett betanítási folyamattal, felhasználhatja a modell új adataival való átképzéséhez. A futtatásokat egy folyamat-végpontból is elküldheti portálról vagy programozott módon.

### <a name="submit-runs-with-the-designer"></a>Futtatások küldése a tervezővel

A következő lépésekkel küldhet el egy folyamat-végpontot a tervezőből:

1. Lépjen a **végpontok** lapra.

1. Válassza a **folyamat végpontjai** lapot.

1. Válassza ki a folyamat végpontját.

1. Válassza a **közzétett folyamatok** fület.

1. Válassza ki a futtatni kívánt folyamatot.

1. Válassza a **Futtatás** lehetőséget.

1. A beállítás párbeszédpanelen megadhat egy új bemeneti adat elérési útja értéket, amely az új adatkészletre mutat.

![A paraméteres folyamat tervezőben való futtatásának módját bemutató képernyőkép](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-with-code"></a>Futtatások küldése kóddal

A REST-végpont programozott módon több módon is elérheti a fejlesztési környezettől függően. Megtalálhatja azokat a kódrészleteket, amelyek bemutatják, hogyan kell elküldeni a folyamat futását paraméterekkel a folyamat **felhasználás lapján.**

## <a name="next-steps"></a>Következő lépések

A regressziós modell betanításához és üzembe helyezéséhez kövesse a tervezői [oktatóanyagot](tutorial-designer-automobile-price-train-score.md) .

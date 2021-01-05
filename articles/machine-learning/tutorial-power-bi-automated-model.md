---
title: 'Oktatóanyag: a prediktív modell létrehozása automatizált ML használatával (2. rész)'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre és helyezhet üzembe automatizált gépi tanulási modelleket, hogy a legjobb modellt használja a Microsoft Power BIban várható eredmények előrejelzéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 6dc99d58f15653e9d3f991622de3bb3388690459
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814805"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-by-using-automated-machine-learning-part-1-of-2"></a>Oktatóanyag: Power BI integráció – a prediktív modell létrehozása automatizált gépi tanulás használatával (2. rész)

Az oktatóanyag 1. részében egy prediktív gépi tanulási modellt fog betanítani és üzembe helyezni. A Azure Machine Learning Studio automatikus gépi tanulást (ML) használ.  A 2. részben a legjobban teljesítő modellt fogja használni a Microsoft Power BI eredményeinek előrejelzéséhez.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Hozzon létre egy Azure Machine Learning számítási fürtöt.
> * Hozzon létre egy adatkészletet.
> * Hozzon létre egy automatizált gépi tanulási futtatást.
> * A legjobb modell üzembe helyezése valós idejű pontozási végponton.


Az Power BIban három módon hozhatja létre és helyezheti üzembe a modellt.  Ez a cikk a "C. lehetőség: az automatikus gépi tanulással a Studióban – a modellek betanítását és üzembe helyezését ismerteti."  Ez a beállítás a kód nélküli létrehozási élmény. Teljes mértékben automatizálja az adatelőkészítést és a modell betanítását. 

Ehelyett használhatja a további lehetőségek egyikét:

* [A. lehetőség: a Jupyter-jegyzetfüzetek használatával betaníthatja és üzembe helyezheti a modelleket](tutorial-power-bi-custom-model.md). Ez a Code-First authoring Experience a Azure Machine Learning Studioban üzemeltetett Jupyter notebookokat használja.
* [B. lehetőség: modellek betanítása és üzembe helyezése a Azure Machine learning Designer használatával](tutorial-power-bi-designer-model.md). Ez az alacsony kódú szerzői művelet egy fogd és vidd felhasználói felületet használ.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs előfizetése, használhat [ingyenes próbaverziót](https://aka.ms/AMLFree). 
- Egy Azure Machine Learning-munkaterület. Ha még nem rendelkezik munkaterülettel, tekintse meg [Azure Machine learning munkaterületek létrehozása és kezelése](./how-to-manage-workspace.md#create-a-workspace)című témakört.

## <a name="create-a-compute-cluster"></a>Számítási fürt létrehozása

Az automatizált Machine learning számos gépi tanulási modellt is betanít a "legjobb" algoritmus és paraméterek megtalálásához. Azure Machine Learning parallelizes a modell betanításának futtatását egy számítási fürtön.

A kezdéshez [Azure Machine learning Studio](https://ml.azure.com)a bal oldali menüben kattintson a **számítás** elemre. Nyissa meg a **számítási fürtök** lapot. Ezután válassza az **új**:

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="A számítási fürt létrehozását bemutató képernyőkép.":::

A **számítási fürt létrehozása** lapon:

1. Válassza ki a virtuális gép méretét. Ebben az oktatóanyagban egy **Standard_D11_v2** gép működik.
1. Kattintson a **Tovább** gombra.
1. Adjon meg egy érvényes számítási nevet.
1. Tartsa meg a **csomópontok minimális számát** a következő helyen: `0` .
1. A **csomópontok maximális számának** módosítása a következőre: `4` .
1. Kattintson a **Létrehozás** gombra.

A fürt állapota a **létrehozásra** módosul.

>[!NOTE]
> Az új fürt 0 csomóponttal rendelkezik, így a számítási költségek nem merülnek fel. A költségek csak akkor merülnek fel, ha az automatizált gépi tanulási feladatok futnak. A fürt 120 másodperc üresjárati idő után automatikusan 0-ra vált.


## <a name="create-a-dataset"></a>Adathalmaz létrehozása

Ebben az oktatóanyagban a [cukorbetegség adatkészletet](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)használja. Ez az adatkészlet elérhető az [Azure Open adatkészletekben](https://azure.microsoft.com/services/open-datasets/).

Az adatkészlet létrehozásához a bal oldali menüben válassza az **adatkészletek** elemet. Ezután válassza az **adatkészlet létrehozása** lehetőséget. A következő lehetőségek jelennek meg:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Az új adatkészlet létrehozását bemutató képernyőkép.":::

Válassza ki **a megnyitott adatkészletek** lehetőséget. Ezután az **adatkészlet létrehozása a megnyitott adatkészletek** lapról:

1. Keresse meg a *cukorbetegséget* a keresősáv használatával.
1. Válassza a **minta: cukorbetegség** elemet.
1. Kattintson a **Tovább** gombra.
1. Nevezze el az adathalmaz *cukorbetegségét*.
1. Kattintson a **Létrehozás** gombra.

Az adat megismeréséhez válassza ki az adatkészletet, majd válassza a **Tallózás** lehetőséget:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Az adatkészlet megismerését bemutató képernyőkép.":::

Az adatok 10 alapszintű bemeneti változóval rendelkeznek, mint például az életkor, a szex, a testtömeg-index, az átlagos vérnyomás és a hat Vérszérum-mérés. Emellett egy **Y** nevű Target változóval is rendelkezik. Ez a célként megadott változó a cukorbetegség előrehaladásának mennyiségi mértéke egy évvel az alapterv után.

## <a name="create-an-automated-machine-learning-run"></a>Automatikus gépi tanulási Futtatás létrehozása

[Azure Machine learning Studio](https://ml.azure.com)a bal oldali menüben válassza az **automatikus ml** elemet. Ezután válassza az **új AUTOMATIZÁLT ml Futtatás**:

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="Képernyőfelvétel: új gépi tanulási Futtatás létrehozása.":::

Ezután válassza ki a korábban létrehozott **diabétesz** -adatkészletet. Ezután válassza a **Next (tovább**) gombot:

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="Az adatkészlet kijelölését bemutató képernyőkép.":::
 
A **Futtatás beállítása** lapon:

1. A **kísérlet neve** területen válassza az **új létrehozása** lehetőséget.
1. Nevezze el a kísérletet.
1. A **cél oszlop** mezőben válassza az **Y** lehetőséget.
1. A **számítási fürt kiválasztása** mezőben válassza ki a korábban létrehozott számítási fürtöt. 

A befejezett űrlapnak így kell kinéznie:

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="Az automatizált gépi tanulás konfigurálását bemutató képernyőkép.":::

Végül válassza ki a Machine learning-feladatot. Ebben az esetben a feladat a **regresszió**:

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="A feladat konfigurálását bemutató képernyőkép.":::

Válassza a **Befejezés** gombot.

> [!IMPORTANT]
> Az automatizált gépi tanulás körülbelül 30 percet vesz igénybe az 100-modellek betanításának befejezéséhez.

## <a name="deploy-the-best-model"></a>A legjobb modell üzembe helyezése

Az automatizált gépi tanulás befejeződése után a **modellek** fülre kattintva megtekintheti az összes olyan gépi tanulási modellt, amelyet megpróbáltak. A modellek teljesítmény szerint vannak rendezve; először a legjobb teljesítményű modell jelenik meg. A legjobb modell kiválasztása után az **üzembe helyezés** gomb engedélyezve van:

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="A modellek listáját bemutató képernyőkép.":::

Válassza a **telepítés** lehetőséget a **modell üzembe helyezése** ablak megnyitásához:

1. Nevezze el a Model Service *diabétesz-Model* nevet.
1. Válassza a **Azure Container Service** lehetőséget.
1. Válassza az **Üzembe helyezés** lehetőséget.

Ekkor megjelenik egy üzenet, amely jelzi, hogy a modell telepítése sikeres volt.

## <a name="next-steps"></a>További lépések

Ebből az oktatóanyagból megtudhatta, hogyan végezheti el a gépi tanulási modellek betanítását és üzembe helyezését az automatizált gépi tanulás használatával. A következő oktatóanyagban megtudhatja, hogyan használhatja a modellt a Power BI.

> [!div class="nextstepaction"]
> [Oktatóanyag: modell felhasználása Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)

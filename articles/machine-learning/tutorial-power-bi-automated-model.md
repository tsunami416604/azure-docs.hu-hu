---
title: 'Oktatóanyag: a prediktív modell létrehozása automatizált ML használatával (2. rész)'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan hozhat létre és helyezhet üzembe automatizált ML-modelleket, így a legjobb modell segítségével előre megjósolhatja az eredményeket a Microsoft Power BIban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 897f493edf6ccdebb25c201e8e4f9babfb0754c5
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370226"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-using-automated-machine-learning-part-1-of-2"></a>Oktatóanyag: Power BI integráció – a prediktív modell létrehozása automatizált gépi tanulás használatával (2. rész)

Az oktatóanyag első részében egy prediktív gépi tanulási modellt fog betanítani és üzembe helyezni a Azure Machine Learning Studióban.  A 2. részben a legjobb teljesítményt nyújtó modellt fogja használni a Microsoft Power BI eredményeinek előrejelzéséhez.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Azure Machine Learning számítási fürt létrehozása
> * Adathalmaz létrehozása
> * Automatizált ML-Futtatás létrehozása
> * A legjobb modell üzembe helyezése valós idejű pontozási végponton


Az Power BIban három különböző módon hozhatja létre és helyezheti üzembe a modellt.  Ez a cikk a "C:" lehetőséget ismerteti, amely a Studióban található automatizált ML-t használja.  Ez a lehetőség egy kód nélküli szerzői élményt mutat be, amely teljes mértékben automatizálja az adatelőkészítést és a modell betanítását. 

Ehelyett használhatja a következőket:

* " [A" lehetőség: a modellek betanítása és üzembe helyezése jegyzetfüzetek használatával](tutorial-power-bi-custom-model.md) – a Azure Machine learning Studióban üzemeltetett Jupyter notebookokat használó kód első szerzői élmény.
* [B. lehetőség: modellek betanítása és üzembe helyezése a Designer használatával](tutorial-power-bi-designer-model.md)– a tervező (a fogd és vidd felhasználói felület) használatával alacsony kódú szerzői élmény.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés ([ingyenes próbaverzió érhető el](https://aka.ms/AMLFree)). 
- Egy Azure Machine Learning-munkaterület. Ha még nem rendelkezik munkaterülettel, kövesse [az Azure Machine learning-munkaterület létrehozását ismertető témakört](./how-to-manage-workspace.md#create-a-workspace).

## <a name="create-compute-cluster"></a>Számítási fürt létrehozása

Az automatikus ML a "legjobb" algoritmus és paraméterek megtalálásához automatikusan nagy mennyiségű különböző gépi tanulási modellt végez. Azure Machine Learning parallelizes a modell képzésének végrehajtását egy számítási fürtön.

A [Azure Machine learning Studio](https://ml.azure.com)a bal oldali menüben válassza a **számítás** elemet, majd a **számítási fürtök** lapot. **Új** kiválasztása:

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="A számítási fürt létrehozását bemutató képernyőkép":::

A **számítási fürt létrehozása** képernyőn:

1. Válassza ki a virtuális gép méretét (ebben az oktatóanyagban a `Standard_D11_v2` gép rendben van).
1. Kattintson a **Tovább** gombra.
1. Érvényes számítási nevet adjon meg
1. **A csomópontok minimális számának** 0-nál tartása
1. **Csomópontok maximális számának** módosítása 4-re
1. Kattintson a **Létrehozás** elemre.

Láthatja, hogy a fürt állapota a **Létrehozás** értékre módosult.

>[!NOTE]
> A fürt létrehozásakor 0 csomópont lesz, ami azt jelenti, hogy nincs számítási költség. Az automatikus ML-feladatok futtatásakor csak költségek merülnek fel. A fürt a 120 másodperc üresjárati idő után automatikusan 0-ra fogja méretezni.


## <a name="create-dataset"></a>Adatkészlet létrehozása

Ebben az oktatóanyagban a [diabétesz](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)adatkészletet használja, amely elérhető az [Azure Open adatkészletekben](https://azure.microsoft.com/services/open-datasets/).

Az adatkészlet létrehozásához válassza az **adatkészletek** bal oldali menüt, majd az **adatkészlet létrehozása** elemet – a következő lehetőségek jelennek meg:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Az új adatkészlet létrehozását bemutató képernyőkép":::

Válasszon **a megnyitott adatkészletek** közül, majd az **adatkészlet létrehozása a megnyitott adatkészletek** képernyőjén:

1. *Diabétesz* keresése a keresősáv használatával
1. **Minta kiválasztása: diabétesz**
1. Kattintson a **Tovább** gombra.
1. Adja meg az adatkészlet nevét – *diabétesz*
1. Kattintson a **Létrehozás** elemre.

Az adatokat úgy tekintheti meg, ha kiválasztja az adatkészletet, majd a következőket **tárja fel**:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Az adatkészlet megismerését bemutató képernyőkép":::

Az adatok 10 alapszintű bemeneti változóval rendelkeznek (például kor, nem, testtömeg-index, átlagos vérnyomás, hat Vérszérum-mérés) és egy **Y** nevű cél változót (a cukorbetegség egy évig mért mennyiségi mértékét).

## <a name="create-automated-ml-run"></a>Automatizált ML-Futtatás létrehozása

A [Azure Machine learning Studio](https://ml.azure.com) válassza az **automatikus ml** elemet a bal oldali menüben, majd az **új automatikus ml-futtatást**:

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="Képernyőfelvétel az új automatizált ML-Futtatás létrehozásáról":::

Ezután válassza ki a korábban létrehozott **diabétesz** -adatkészletet, és válassza a **Next (tovább**) gombot:

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="Az adatkészlet kijelölését bemutató képernyőkép":::
 
A **Futtatás konfigurálása** képernyőn:

1. A **kísérlet neve** területen válassza az **új létrehozása** lehetőséget.
1. Adja meg a kísérlet nevét
1. A cél oszlop mezőben válassza az **Y** lehetőséget.
1. A **számítási fürt kiválasztása** mezőben válassza ki a korábban létrehozott számítási fürtöt. 

A befejezett űrlapnak a következőhöz hasonlóan kell kinéznie:

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="Az automatikus ML konfigurálását bemutató képernyőkép":::

Végül ki kell választania a végrehajtandó Machine learning-feladatot, amely a **regresszió**:

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="A feladat konfigurálását bemutató képernyőkép":::

Válassza a **Befejezés** gombot.

> [!IMPORTANT]
> A 100 különböző modelljeinek betanítása után az automatikus ML körülbelül 30 percet vesz igénybe.

## <a name="deploy-the-best-model"></a>A legjobb modell üzembe helyezése

Az automatikus ML-Futtatás befejezése után a **modellek** lapon megtekintheti a különböző gépi tanulási modellek listáját. A modellek a teljesítmény sorrendje szerint vannak rendezve – a legjobb teljesítményű modell jelenik meg elsőként. A legjobb modell kiválasztásakor a **telepítés** gomb engedélyezve lesz:

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="A modellek listáját bemutató képernyőfelvétel":::

A **telepítés** kiválasztása után megjelenik a **modell üzembe helyezése** képernyő:

1. Adja meg a modell szolgáltatás nevét – **cukorbetegség – modell** használata
1. **Azure Container Service** kiválasztása
1. **Telepítés** kiválasztása

Ekkor megjelenik egy üzenet, amely szerint a modell központi telepítése sikeres volt.

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan végezheti el a gépi tanulási modell betanítását és üzembe helyezését az automatikus ML használatával. A következő oktatóanyagban bemutatjuk, hogyan kell használni a modellt a Power BI.

> [!div class="nextstepaction"]
> [Oktatóanyag: modell felhasználása Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)

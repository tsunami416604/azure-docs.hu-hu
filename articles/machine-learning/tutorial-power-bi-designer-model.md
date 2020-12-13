---
title: 'Oktatóanyag: fogd és vidd a prediktív modell létrehozásához (2. rész)'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre és helyezhet üzembe egy gépi tanulási prediktív modellt a Designer használatával, így a Microsoft Power BIban megjósolhatja az eredményeket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f0e1ffe60069a2379f8eddab1aae74db2b4eac50
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370200"
---
# <a name="tutorial--power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>Oktatóanyag: Power BI integráció – drag-and-drop a prediktív modell létrehozásához (2. rész)

Az oktatóanyag első részében egy prediktív gépi tanulási modellt kell betanítania és üzembe helyeznie Azure Machine Learning Designer használatával – egy alacsony kódú, fogd és vidd típusú felhasználói felületet. A 2. részben a modellt fogja használni a Microsoft Power BIban várható eredmények előrejelzéséhez.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Azure Machine Learning számítási példány létrehozása
> * Azure Machine Learning következtetési fürt létrehozása
> * Adathalmaz létrehozása
> * Regressziós modell betanítása
> * A modell üzembe helyezése valós idejű pontozási végponton


Az Power BIban három különböző módon hozhatja létre és helyezheti üzembe a modellt.  Ez a cikk a "B" lehetőséget tárgyalja: modellek betanítása és üzembe helyezése a Designer használatával.  Ez a lehetőség a Designer (a fogd és vidd felhasználói felület) használatával alacsony kódú szerzői élményt mutat be.  

Ehelyett használhatja a következőket:

* " [A" lehetőség: a modellek betanítása és üzembe helyezése jegyzetfüzetek használatával](tutorial-power-bi-custom-model.md) – a Azure Machine learning Studióban üzemeltetett Jupyter notebookokat használó kód első szerzői élmény.
* [C. lehetőség: modellek betanítása és üzembe helyezése AUTOMATIZÁLT ml használatával](tutorial-power-bi-automated-model.md) – a kód nélküli létrehozási élmény, amely teljes mértékben automatizálja az adatelőkészítést és a modell betanítását.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés ([ingyenes próbaverzió érhető el](https://aka.ms/AMLFree)). 
- Egy Azure Machine Learning-munkaterület. Ha még nem rendelkezik munkaterülettel, kövesse [az Azure Machine learning-munkaterület létrehozását ismertető témakört](./how-to-manage-workspace.md#create-a-workspace).
- A gépi tanulási munkafolyamatok bevezető ismerete.


## <a name="create-compute-for-training-and-scoring"></a>Számítás létrehozása képzéshez és pontozáshoz

Ebben a szakaszban egy *számítási példányt* hoz létre, amely a gépi tanulási modellek betanítására szolgál. Emellett olyan *következtetési fürtöt* is létrehozhat, amely a központilag üzembe helyezett modell valós idejű pontozáshoz való üzemeltetésére szolgál majd.

Jelentkezzen be a [Azure Machine learning studióba](https://ml.azure.com) , és a bal oldali menüben válassza a **számítás** lehetőséget, majd az **új** elemet:

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="A számítási példány létrehozását bemutató képernyőkép":::

Az eredményül kapott **számítási példány létrehozása** képernyőn válassza ki a virtuális gép méretét (ebben az oktatóanyagban válassza a `Standard_D11_v2` ), majd a Next ( **tovább**) lehetőséget. A beállítás lapon adjon meg egy érvényes nevet a számítási példányhoz, majd válassza a **Létrehozás** lehetőséget. 

>[!TIP]
> A számítási példány jegyzetfüzetek létrehozásához és végrehajtásához is használható.

Láthatja, hogy a számítási példány **állapota** **Létrehozva** – körülbelül 4 percet vesz igénybe a gép kiépítés után. Várakozás közben a számítás lapon válassza a **következtetési fürt** létrehozása fület, majd az **új** elemet:

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="A következtetést bemutató fürt létrehozásáról készült képernyőkép":::

Az eredményül kapott **következtetési hely létrehozása** lapon válasszon ki egy régiót, amelyet a virtuális gép mérete követ (ebben az oktatóanyagban válassza a elemet `Standard_D11_v2` ), majd kattintson a **tovább** gombra. A **beállítások konfigurálása** lapon:

1. Érvényes számítási nevet adjon meg
1. Válassza a **fejlesztési-tesztelés** lehetőséget a fürt céljaként (egyetlen csomópontot hoz létre az üzembe helyezett modell üzemeltetéséhez)
1. Kattintson a **Létrehozás** elemre.

Most már megtekintheti a következtetési fürt **állapotának** **létrehozását** – a rendszer körülbelül 4 percet vesz igénybe, hogy az egyetlen csomópontos fürt üzembe helyezése megtörténjen.

## <a name="create-a-dataset"></a>Adathalmaz létrehozása

Ebben az oktatóanyagban a [diabétesz](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)adatkészletet használja, amely elérhető az [Azure Open adatkészletekben](https://azure.microsoft.com/services/open-datasets/).

Az adatkészlet létrehozásához a bal oldali menüben válassza az **adatkészletek**, majd az **adatkészlet létrehozása** elemet – a következő beállításokat fogja látni:

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

## <a name="create-a-machine-learning-model-using-designer"></a>Machine Learning modell létrehozása a Designer használatával

A számítási és adatkészletek létrehozása után átléphet a Machine learning-modell létrehozásához a Designer használatával. A Azure Machine Learning Studióban válassza a **tervező** , majd az **új folyamat** elemet:

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="Az új folyamat létrehozásának módját bemutató képernyőkép":::

Ekkor megjelenik egy üres *vászon* , ahol a **Beállítások menü** is látható:

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="A számítási cél kijelölését bemutató képernyőkép":::

A **Beállítások menüben** válassza a **számítási cél lehetőséget** , majd válassza ki a korábban létrehozott számítási példányt, majd a **Mentés** lehetőséget. Nevezze át a **Piszkozat nevét** valami emlékezetre (például a *diabétesz-modellre*), és adjon meg egy leírást.

Ezután a felsorolt eszközök területen bontsa ki az **adatkészletek** csomópontot, és keresse meg a **cukorbetegség** -adatkészletet – húzza a modult a vászonra:

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="Az összetevők húzását bemutató képernyőkép":::

Ezután húzza a következő összetevőket a vászonra:

1. Lineáris regresszió ( **Machine learning algoritmusokban** található)
1. Betanítási modell (a **modell betanítása** alatt található)

A vászonnak a következőképpen kell kinéznie: (figyelje meg, hogy az összetevők felső és alsó részén a portok nevű kis körök szerepelnek az alábbi piros színnel kiemelve):

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="A nem összekapcsolt összetevőket bemutató képernyőkép":::
 
Ezután ezeket az összetevőket össze *kell állítania* . Válassza ki a **cukorbetegség** -adatkészlet alján található portot, és húzza azt a **kiépítési modell** összetevő tetején található jobb oldali portra. Válassza ki a **lineáris regressziós** összetevő alján található portot, és húzza a **vonat modell** portjának tetején található bal oldali portra.

Válassza ki az adatkészlet azon oszlopát, amelyet a címke (TARGET) változóként kíván használni az előrejelzéshez. Válassza ki a **Train Model** összetevőt, majd az **oszlop szerkesztése** elemet. A párbeszédpanelen jelölje be az **adja meg az oszlop nevét** , majd az **Y betűt** a legördülő listában:

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="A Label (címke) oszlop kiválasztása":::

Válassza a **Mentés** lehetőséget. A gépi tanulási *munkafolyamatnak* a következőképpen kell kinéznie:

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="A csatlakoztatott összetevőket bemutató képernyőkép":::

Válassza a **Submit (Küldés** ) lehetőséget, majd **hozzon létre újat** a kísérlet alatt. Adja meg a kísérlet nevét, majd **küldje** el.

>[!NOTE]
> Körülbelül 5 percet vesz igénybe, hogy a kísérlet befejeződjön az első futtatáskor. A későbbi futtatások sokkal gyorsabb-tervező gyorsítótárak, amelyek már futtatják az összetevőket a késés csökkentése érdekében.

A kísérlet befejezésekor a következő jelenik meg:

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="A befejezett futtatást ábrázoló képernyőfelvétel":::

A kísérlet naplóit a **kimenet + naplók** lehetőség **kiválasztásával** ellenőrizheti.

## <a name="deploy-the-model"></a>A modell üzembe helyezése

A modell üzembe helyezéséhez válassza a következő, a vászon tetején található **következtetés létrehozása folyamat** elemet, majd a **valós idejű következtetési folyamat**:

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="Képernyőfelvétel a valós idejű következtetéseket bemutató folyamatról":::
 
A folyamat csak a modell pontozásához szükséges összetevőket tömöríti. Az adatok kiértékelése során nem fogja tudni a cél változó értékeit, ezért az adatkészletből is eltávolíthatjuk az **Y** -t. Az eltávolításhoz adja hozzá a vászonhoz a **Select Columns** elemet az adatkészlet összetevőben. Adja meg az összetevőt, hogy a cukorbetegség adatkészlete a bemenet legyen, az eredmények pedig a **pontszám modell** összetevőjének kimenete:

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="Egy oszlop eltávolítását bemutató képernyőkép":::

Jelölje ki az **oszlopok kijelölése** a vászonon elemet a vásznon, majd az **Oszlopok szerkesztése** elemet. Az Oszlopok kiválasztása párbeszédpanelen válassza a **név alapján** lehetőséget, és győződjön meg arról, hogy az összes bemeneti változó ki van választva, de **nem** a cél:

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="Az oszlop beállításainak eltávolítását bemutató képernyőkép":::

Válassza a **Mentés** lehetőséget. Végül jelölje be a **pontszám modell** összetevőt, és győződjön meg arról, **hogy a pontszám hozzáfűzése oszlopok a kimenethez** jelölőnégyzet nincs bejelölve (csak az előrejelzések lesznek visszaküldve, nem pedig a bemenetek *és* az előrejelzések, a késés csökkentése):

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="A pontszám modell összetevőjének beállításait bemutató képernyőkép":::

A vászon tetején válassza a **Submit (Küldés** ) lehetőséget.

Ha sikeresen futtatta a következtetési folyamatot, a modellt üzembe helyezheti a következtetési fürtön. Válassza a **telepítés** lehetőséget, amely megjeleníti a **valós idejű végpont beállítása** párbeszédpanelt. Válassza az **új valós idejű végpont telepítése** elemet, nevezze el a végpontok **saját-cukorbetegség-modell** nevet, válassza ki a korábban létrehozott következtetést, majd válassza a **telepítés** lehetőséget:

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="A valós idejű végpontok beállításait bemutató képernyőfelvétel":::
## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan végezheti el a tervezői modell betanítását és üzembe helyezését. A következő részben megtudhatja, hogyan használhatja fel (pontszám) a modellt Power BIból.

> [!div class="nextstepaction"]
> [Oktatóanyag: modell felhasználása Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)

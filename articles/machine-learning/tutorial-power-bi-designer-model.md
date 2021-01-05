---
title: 'Oktatóanyag: drag and drop a prediktív modell létrehozásához (2. rész)'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan hozhat létre és helyezhet üzembe gépi tanulási prediktív modellt a tervező használatával. Később felhasználhatja az eredményeket a Microsoft Power BIban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.custom: designer
ms.openlocfilehash: 995979c7fe100637aa8e241489805fb09d6723f7
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814788"
---
# <a name="tutorial-power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>Oktatóanyag: Power BI integráció – drag and drop a prediktív modell létrehozásához (2. rész)

Az oktatóanyag 1. részében egy prediktív gépi tanulási modellt fog betanítani és üzembe helyezni a Azure Machine Learning Designer használatával. A Designer egy kis kódolású, húzást használó felhasználói felület. A 2. részben a modellt fogja használni a Microsoft Power BIban várható eredmények előrejelzéséhez.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Hozzon létre egy Azure Machine Learning számítási példányt.
> * Hozzon létre egy Azure Machine Learning következtetési fürtöt.
> * Hozzon létre egy adatkészletet.
> * Egy regressziós modell betanítása.
> * A modell üzembe helyezése valós idejű pontozási végponton.


Az Power BIban három módon hozhatja létre és helyezheti üzembe a modellt.  Ez a cikk a "B. lehetőség: a modellek betanítása és üzembe helyezése a Designer használatával" témakört ismerteti.  Ez a beállítás egy, a Designer felületet használó, alacsony kódú authoring-felület.  

Ehelyett használhatja a további lehetőségek egyikét:

* [A. lehetőség: a Jupyter-jegyzetfüzetek használatával betaníthatja és üzembe helyezheti a modelleket](tutorial-power-bi-custom-model.md). Ez a Code-First authoring Experience a Azure Machine Learning Studioban üzemeltetett Jupyter notebookokat használja.
* [C. lehetőség: modellek betanítása és üzembe helyezése automatizált gépi tanulás használatával](tutorial-power-bi-automated-model.md). Ez a kód nélküli létrehozási élmény teljes mértékben automatizálja az adatelőkészítést és a modell betanítását.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs előfizetése, használhat [ingyenes próbaverziót](https://aka.ms/AMLFree). 
- Egy Azure Machine Learning-munkaterület. Ha még nem rendelkezik munkaterülettel, tekintse meg [Azure Machine learning munkaterületek létrehozása és kezelése](./how-to-manage-workspace.md#create-a-workspace)című témakört.
- A gépi tanulási munkafolyamatok bevezető ismerete.


## <a name="create-compute-to-train-and-score"></a>Számítás létrehozása a betanításhoz és a pontszámhoz

Ebben a szakaszban egy *számítási példányt* hoz létre. A számítási példányok a gépi tanulási modellek betanítására szolgálnak. Létrehoz egy *következtetési fürtöt* is, amely a üzembe helyezett modellt valós idejű pontozásra futtatja.

Jelentkezzen be [Azure Machine learning Studioba](https://ml.azure.com). A bal oldali menüben válassza a **számítás** , majd az **új**:

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="A számítási példányok létrehozását bemutató képernyőkép.":::

A **számítási példány létrehozása** lapon válassza ki a virtuális gép méretét. Ebben az oktatóanyagban válasszon egy **Standard_D11_v2** virtuális gépet. Ezután kattintson a **Tovább** gombra. 

A **Beállítások** lapon nevezze el a számítási példányt. Ezután válassza a **Létrehozás** elemet. 

>[!TIP]
> A számítási példányt használhatja jegyzetfüzetek létrehozásához és futtatásához is.

A számítási példány **állapota** most **Létrehozva**. A gép kiépítése körülbelül 4 percet vesz igénybe. 

Amíg megvárja, a **számítás** lapon válassza ki a **következtetési fürtök** lapot. Ezután válassza az **új**:

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="A következtetést bemutató fürt létrehozásáról készült képernyőkép.":::

A **következtetési fürt létrehozása** lapon válassza ki a régiót és a virtuális gép méretét. Ebben az oktatóanyagban válasszon egy **Standard_D11_v2** virtuális gépet. Ezután kattintson a **Tovább** gombra. 

A **beállítások konfigurálása** lapon:

1. Adjon meg egy érvényes számítási nevet.
1. Válassza a **dev-test** lehetőséget a fürt céljaként. Ez a beállítás egyetlen csomópontot hoz létre az üzembe helyezett modell üzemeltetéséhez.
1. Kattintson a **Létrehozás** gombra.

A következtetési fürt **állapota** most **Létrehozva**. Az egycsomópontos fürt üzembe helyezése körülbelül 4 percet vesz igénybe.

## <a name="create-a-dataset"></a>Adathalmaz létrehozása

Ebben az oktatóanyagban a [cukorbetegség adatkészletet](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)használja. Ez az adatkészlet elérhető az [Azure Open adatkészletekben](https://azure.microsoft.com/services/open-datasets/).

Az adatkészlet létrehozásához a bal oldali menüben válassza az **adatkészletek** elemet. Ezután válassza az **adatkészlet létrehozása** lehetőséget. A következő lehetőségek jelennek meg:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Az új adatkészlet létrehozását bemutató képernyőkép.":::

Válassza ki **a megnyitott adatkészletek** lehetőséget. Az **adatkészlet létrehozása a nyitott adatkészletek** lapról lapon:

1. Keresse meg a *cukorbetegséget* a keresősáv használatával.
1. Válassza a **minta: cukorbetegség** elemet.
1. Kattintson a **Tovább** gombra.
1. Nevezze el az adathalmaz *cukorbetegségét*.
1. Kattintson a **Létrehozás** gombra.

Az adat megismeréséhez válassza ki az adatkészletet, majd válassza a **Tallózás** lehetőséget:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Az adatkészlet megismerését bemutató képernyőkép.":::

Az adatok 10 alapszintű bemeneti változóval rendelkeznek, mint például az életkor, a szex, a testtömeg-index, az átlagos vérnyomás és a hat Vérszérum-mérés. Emellett egy **Y** nevű Target változóval is rendelkezik. Ez a célként megadott változó a cukorbetegség előrehaladásának mennyiségi mértéke egy évvel az alapterv után.

## <a name="create-a-machine-learning-model-by-using-the-designer"></a>Machine learning-modell létrehozása a Designer használatával

A számítási és adatkészletek létrehozása után a Designer használatával hozhatja létre a Machine learning-modellt. A Azure Machine Learning Studio területen válassza a **tervező** , majd az **új folyamat** elemet:

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="Képernyőfelvétel: új folyamat létrehozása.":::

Ekkor megjelenik egy üres *vászon* és egy **Beállítások** menü:

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="A számítási cél kijelölését bemutató képernyőkép.":::

A **Beállítások** menüben válassza a **számítási cél kiválasztása** lehetőséget. Válassza ki a korábban létrehozott számítási példányt, majd válassza a **Mentés** lehetőséget. Módosítsa a **Piszkozat nevét** valami emlékezetre, például a *cukorbetegség-Model* névre. Végül adjon meg egy leírást.

Az eszközök listájában bontsa ki az **adatkészletek** elemet, és keresse meg a **diabétesz** -adatkészletet. Húzza át ezt az összetevőt a vászonra:

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="Az összetevők vászonra húzását bemutató képernyőkép.":::

Ezután húzza a következő összetevőket a vászonra:

1. **Lineáris regresszió** ( **Machine learning algoritmusokban** található)
1. **Betanítási modell** (a **modell betanítása** alatt található)

Figyelje meg a vásznon az összetevők tetején és alján található köröket. Ezek a körök portok.

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="A leválasztott összetevők portjait ábrázoló képernyőkép.":::
 
Az *összetevők* egyesítése most együtt. Válassza ki a **cukorbetegség** adatkészlet alján található portot. Húzza azt a portra a **Train Model** összetevő jobb felső részén. Válassza ki a **lineáris regressziós** összetevő alján található portot. Húzza azt a portra a **Train Model** összetevő bal felső részén.

Válassza ki a felirat (cél) változóként használandó adatkészlet oszlopot az előrejelzéshez. Válassza ki a **Train Model** összetevőt, majd válassza az **oszlop szerkesztése** lehetőséget. 

A párbeszédpanelen válassza az Y **oszlop nevének megadása** elemet  >  :

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="A Label (címke) oszlop kijelölését bemutató képernyőkép.":::

Válassza a **Mentés** lehetőséget. A gépi tanulási *munkafolyamatnak* így kell kinéznie:

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="A csatlakoztatott összetevőket bemutató képernyőkép.":::

Válassza a **Küldés** lehetőséget. A **kísérlet** területen válassza az **új létrehozása** lehetőséget. Nevezze el a kísérletet, majd válassza a **Küldés** lehetőséget.

>[!NOTE]
> A kísérlet első futtatása körülbelül 5 percet vesz igénybe. A későbbi futtatások sokkal gyorsabban működnek, mert a tervező a késés csökkentése érdekében futtatott összetevőket gyorsítótárazza.

A kísérlet befejeződése után a következő nézet jelenik meg:

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="A befejezett futtatást ábrázoló képernyőfelvétel.":::

A kísérlet naplófájljainak vizsgálatához válassza a **kiépítési modell** , majd a **kimenet + naplók** lehetőséget.

## <a name="deploy-the-model"></a>A modell üzembe helyezése

A modell üzembe helyezéséhez a vászon tetején válassza a **következtetési folyamat**  >  **valós idejű következtetési folyamatának** létrehozása lehetőséget:

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="Képernyőfelvétel: a valós idejű következtetési folyamat kiválasztásának helye.":::
 
A folyamat csak a modell kiértékeléséhez szükséges összetevőket tömöríti. Az adatok Pontozásakor nem fogja tudni a cél változó értékeit. Így eltávolíthatja az **Y** objektumot az adatkészletből. 

Az **Y** eltávolításához adjon hozzá egy **Select oszlopot az adatkészlet** összetevőben a vászonhoz. Adja meg az összetevőt, hogy a cukorbetegség adatkészlete a bemenet legyen. Az eredmények a **pontszám modell** összetevőjének kimenete:

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="Egy oszlop eltávolítását bemutató képernyőkép.":::

A vásznon jelölje ki az **Oszlopok kiválasztása az adatkészlet** összetevőben elemet, majd válassza az **Oszlopok szerkesztése** lehetőséget. 

Az **Oszlopok kiválasztása** párbeszédpanelen válassza a **név alapján** lehetőséget. Ezután győződjön meg arról, hogy az összes bemeneti változó *ki van választva* , de a cél nincs kiválasztva:

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="Az oszlopok beállításainak eltávolítását bemutató képernyőkép.":::

Válassza a **Mentés** lehetőséget. 

Végül jelölje be a **pontszám modell** összetevőt, és győződjön meg arról, **hogy a pontszám oszlopok hozzáfűzése a kimenethez** jelölőnégyzet be van jelölve. A késés csökkentése érdekében a rendszer visszaküldi az előrejelzéseket a bemenetek nélkül.

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="A pontszám modell összetevőjének beállításait bemutató képernyőkép.":::

A vászon tetején válassza a **Küldés** lehetőséget.

A következtetési folyamat sikeres futtatása után üzembe helyezheti a modellt a következtetési fürtön. Válassza az **Üzembe helyezés** lehetőséget. 

A **valós idejű végpont beállítása** párbeszédpanelen jelölje be az **új valós idejű végpont üzembe helyezése** jelölőnégyzetet. Nevezze el a végpontok *saját-cukorbetegség-modelljét*. Válassza ki a korábban létrehozott következtetést, majd válassza a **telepítés** lehetőséget:

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="A valós idejű végpontok beállításait bemutató képernyőkép.":::
## <a name="next-steps"></a>További lépések

Ebből az oktatóanyagból megtudhatta, hogyan végezheti el a tervezői modell betanítását és üzembe helyezését. A következő részben megtudhatja, hogyan használhatja fel (pontszám) a modellt Power BIban.

> [!div class="nextstepaction"]
> [Oktatóanyag: modell felhasználása Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)

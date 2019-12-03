---
title: 'Oktatóanyag: az autó árának előrejelzése a tervezővel'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan lehet a gépi tanulási modellt betanítani, bemutatni és üzembe helyezni egy drag-and-drop interfész használatával. Ez az oktatóanyag egy kétrészes sorozat első része, amely az autó árát lineáris regresszió használatával Jósolja meg.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 3d0efbaec6905d0d24d9dc1c310d472958011960
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74702082"
---
# <a name="tutorial-predict-automobile-price-with-the-designer-preview"></a>Oktatóanyag: az autó árának előrejelzése a tervezővel (előzetes verzió)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Ebben a kétrészes oktatóanyagban megtudhatja, hogyan hozhat létre és helyezhet üzembe egy prediktív elemzési megoldást a Azure Machine Learning Designer használatával, amely előre jelezheti a személygépkocsik árát. 

Az első részen beállíthatja a környezetet, a modulokat egy interaktív vászonra húzhatja, és összekapcsolhatja őket egy Azure Machine Learning folyamat létrehozásához.

Az oktatóanyag első részében az alábbiakkal fog elsajátítani:

> [!div class="checklist"]
> * Hozzon létre egy új folyamatot.
> * Importálja az adatimportálást.
> * Készítse elő az adatfeldolgozást.
> * Gépi tanulási modell betanítása.
> * A Machine learning-modell kiértékelése.

Az oktatóanyag [második részében](tutorial-designer-automobile-price-deploy.md) megtudhatja, hogyan helyezheti üzembe a prediktív modelljét valós idejű következtetési végpontként, hogy az Ön által elküldött technikai specifikációk alapján előre megjósolja az autó árát. 

> [!NOTE]
>Az oktatóanyag befejezett verziója minta folyamatként érhető el.
>
>A kereséshez nyissa meg a tervezőt a munkaterületen. Az **új folyamat** szakaszban válassza az **1. minta – regresszió: autó árának előrejelzése (alapszintű)** lehetőséget.

## <a name="create-a-new-pipeline"></a>Új folyamat létrehozása

Azure Machine Learning folyamatok több, függő gépi tanulási és adatfeldolgozási lépést szerveznek egyetlen erőforrásba. A folyamatok segítségével összetett gépi tanulási munkafolyamatokat szervezheti, kezelheti és használhatja fel a projektek és a felhasználók számára. Azure Machine Learning folyamat létrehozásához Azure Machine Learning munkaterületre van szükség. Ebből a szakaszból megtudhatja, hogyan hozhat létre mindkét erőforrást.

### <a name="create-a-new-workspace"></a>Új munkaterület létrehozása

Ha vállalati kiadással rendelkező Azure Machine Learning munkaterülettel rendelkezik, [ugorjon a következő szakaszra](#create-the-pipeline).

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal-enterprise.md)]

### <a name="create-the-pipeline"></a>A folyamat létrehozása

1. Jelentkezzen be a [ml.Azure.com](https://ml.azure.com)-be, és válassza ki a munkaterületet, amellyel dolgozni szeretne.

1. Válassza a **tervező**lehetőséget.

    ![A tervező elérését bemutató vizualizációs munkaterület képernyőképe](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Válassza **a könnyen használható előre elkészített modulok**elemet.

1. Válassza ki a vászon tetején **lévő alapértelmezett folyamat neve folyamatot** . Nevezze át valami értelmesre. Ilyen például az *autó árának előrejelzése*. A névnek nem kell egyedinek lennie.

## <a name="import-data"></a>Adatok importálása

A tervezőben több minta adatkészletet is megadhat a kipróbáláshoz. Ebben az oktatóanyagban használja az **Automobile Price (nyers) adatkészletet**. 

1. Az adatkészletek és modulok palettájának bal oldalán található. Válassza az **adatkészletek**lehetőséget, majd tekintse meg a **minták** szakaszt az elérhető minta-adatkészletek megtekintéséhez.

1. Válassza ki az adatkészlet **Automobile Price (nyers) adatokat**, és húzza rá a vászonra.

   ![Az adathúzás vászonra](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Az adatok vizualizációja

Megjelenítheti az adatokat, hogy megértse a használni kívánt adatkészletet.

1. Válassza ki a **személygépkocsi-árlista (RAW)** modult.

1. A vászontól jobbra található Tulajdonságok ablaktáblán válassza a **kimenetek**lehetőséget.

1. Válassza ki a Graph ikont az adatmegjelenítéshez.

    ![Az adatok vizualizációja](./media/tutorial-designer-automobile-price-train-score/visualize-data.png)

1. Válassza ki az adatablak különböző oszlopait az egyes adatok megtekintéséhez.

    Minden sor egy személygépkocsit jelöl, és az egyes autókhoz társított változók oszlopként jelennek meg. Ebben az adatkészletben 205 sor és 26 oszlop található.

## <a name="prepare-data"></a>Adatok előkészítése

Az adatkészletek általában némi előfeldolgozást igényelnek az elemzés előtt. Előfordulhat, hogy néhány hiányzó értéket észlelt az adatkészlet vizsgálata során. Ezeket a hiányzó értékeket meg kell tisztítani, hogy a modell helyesen elemezze az adatok elemzését.

### <a name="remove-a-column"></a>Oszlop eltávolítása

A modellek betanításakor meg kell tennie valamit a hiányzó információkkal kapcsolatban. Ebben az adatkészletben a **normalizált veszteségek** oszlop sok értéket tartalmaz, ezért a modellből kizárhatja az adott oszlopot.

1. A paletta tetején található keresőmezőbe írja be a **Kiválasztás elemet** , hogy megtalálja a **Select Columns elemet az adatkészlet** modulban.

1. Húzza az **Oszlopok kiválasztása az adatkészlet** modulban elemet a vászonra. Dobja el a modult az adatkészlet modul alatt.

1. Csatlakoztassuk az **Automobile Price (nyers)** adatkészletet az **adathalmaz-modul Select oszlopaihoz** . Húzza a mutatót az adatkészlet kimeneti portjáról, amely a vászonon található adatkészlet alján lévő kis kör, az **adatkészletben lévő oszlopok kiválasztása**bemeneti portjára, amely a modul tetején lévő kis kör.

    > [!TIP]
    > Az adatáramlás a folyamaton keresztül jön létre, amikor egy modul kimeneti portját egy másik bemeneti porthoz kapcsolja.
    >

    ![Modulok összekapcsolása](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. Válassza az **Oszlopok kiválasztása az adatkészlet** modulban lehetőséget.

1. A vászontól jobbra található Tulajdonságok ablaktáblán válassza a **paraméterek** > **oszlop szerkesztése**lehetőséget.

1. Új szabály hozzáadásához válassza ki a **+** .

1. A legördülő menüben válassza a **kizárás** és **oszlopnevek**lehetőséget.
    
1. Adja meg a *normalizált – veszteségeket* a szövegmezőben.

1. A jobb alsó sarokban kattintson a **Mentés** gombra az oszlop választójának bezárásához.

    ![Oszlop kizárása](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)
        
    A Tulajdonságok panelen látható, hogy a **normalizált veszteségek** oszlop ki van zárva.

1. Válassza az **Oszlopok kiválasztása az adatkészlet** modulban lehetőséget. 

1. A Tulajdonságok ablaktáblán válassza a **paraméterek** > a **Megjegyzés** lehetőséget, és adja meg a *normalizált veszteségek kizárása*beállítást.

### <a name="clean-missing-data"></a>Hiányzó adatértékek törlése

A **normalizált veszteségek** oszlop eltávolítása után az adatkészlet továbbra is hiányzó értékeket tartalmaz. A fennmaradó hiányzó adatelemet a **tiszta hiányzó** adatmodul használatával távolíthatja el.

> [!TIP]
> A bemeneti adatokból hiányzó értékek tisztítása előfeltétel a legtöbb modul a Designerben való használatához.

1. A **tiszta hiányzó** adatmodul megtalálásához a keresőmezőbe írja be a **tisztítás** kifejezést.

1. Húzza a **tiszta hiányzó** adatmodult a folyamat vászonra. Kapcsolódjon az **adathalmaz-modul Select oszlopaihoz** . 

1. A Tulajdonságok panelen válassza a **teljes sor eltávolítása** a **tisztítási mód**alatt lehetőséget.

1. A Tulajdonságok ablaktábla **Megjegyzés** mezőjébe írja be a *hiányzó érték sorok eltávolítása értéket*. 

    A folyamatnak ekkor a következőhöz hasonlóan kell kinéznie:
    
    ![Oszlop kijelölése](./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Gépi tanulási modell betanítása

Most, hogy feldolgozta az adatfeldolgozást, elvégezheti a prediktív modell betanítását.

### <a name="select-an-algorithm"></a>Algoritmus kiválasztása

A *besorolás* és a *regresszió* két algoritmus, amelynek segítségével felügyelt gépi tanítás valósítható meg. A besorolás egy adott kategóriába tartozó, például piros, kék vagy zöld színű választ ad meg. A rendszer a számok előrejelzésére regressziós módszert használ.

Mivel előre jelezni szeretné az árat, ami egy szám, regressziós algoritmust is használhat. Ebben a példában egy lineáris regressziós modellt használunk.

### <a name="split-the-data"></a>Az adatfelosztás

Ossza meg az adatait két külön adatkészletbe a modell betanításához és teszteléséhez.

1. Az **Adatfelosztási** modul megkereséséhez írja be a **feldarabolt** adatelemet a keresőmezőbe. Csatlakozás a **tiszta hiányzó** adatmodul bal oldali portjához.

1. Válassza ki az **Adatfelosztási** modult.

1. A Tulajdonságok ablaktáblában állítsa az **első kimeneti adatkészletben lévő sorok töredékét** 0,7-re.

    Ez a beállítás az adatmennyiség 70 százalékát felbontja a modell betanításához, valamint 30 százalékot a teszteléshez.

1. A Tulajdonságok ablaktábla **Megjegyzés** mezőjébe írja be *az adatkészlet felosztása betanítási készletbe (0,7) és a test set (0,3)* értéket.

### <a name="train-the-model"></a>A modell tanítása

A modell betanításához adjon meg egy olyan adathalmazt, amely tartalmazza az árat. A modell megkeresi az adatforrásokat, és összefüggéseket keres az autó funkciói és a modell kiépítése között.

1. A tanulási algoritmus kiválasztásához törölje a modul-paletta keresési mezőjét.

1. **Machine learning algoritmusok**kibontása.
    
    Ez a beállítás számos, a tanulási algoritmusok inicializálására használható modul-kategóriát jelenít meg.

1. Válassza a **regresszió** > a **lineáris regresszió**lehetőséget, majd húzza a folyamat vászonra.

1. Keresse meg és húzza a **Train Model** modult a folyamat vászonra. 

1. Kapcsolja össze a **lineáris regressziós** modul kimenetét a **Train Model** modul bal oldali bemenetével.

1. Kapcsolja össze az **adatok felosztása** modul betanítási adat kimenetét (bal oldali port) a **Train Model** modul jobb oldali bemenetével.

    ![Képernyőfelvétel a Train Model modul helyes konfigurációjának megjelenítéséről. A lineáris regressziós modul a betanítási modell moduljának bal oldali portjához csatlakozik, és a felosztott adatmodul csatlakozik a betanítási modell jobb portjához.](./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png)

1. Válassza ki a **Train Model** modult.

1. A Tulajdonságok ablaktáblán válassza az **oszlop szerkesztése** lehetőséget.

1. Az **oszlop felirata** párbeszédpanelen bontsa ki a legördülő menüt, és válassza az **oszlopnevek**lehetőséget. 

1. A szövegmezőbe írja be az *Ár*értéket. Az ár az az érték, amelyet a modell előre meg fog jósolni.

    A folyamatnak így kell kinéznie:

    ![Képernyőfelvétel: a folyamat helyes konfigurációjának megjelenítése a Train Model modul hozzáadása után.](./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png)

## <a name="evaluate-a-machine-learning-model"></a>Gépi tanulási modell kiértékelése

Miután az adatok 70 százalékával betanítja a modellt, a másik 30 százalékra kiterjedően megtekintheti, hogy a modell milyen jól működik.

1. A **pontszám modell** modul megkereséséhez írja be a *score Model* kifejezést a keresőmezőbe. Húzza a modult a folyamat vászonra. 

1. A **Train Model** modul kimenetének összekötése a **score Model**bal oldali bemeneti portjával. Az **adatforráshoz** tartozó adat kimenetének (jobb oldali portjának) összekötése a **score Model**megfelelő bemeneti portjára.

1. A **modell kiértékelése** modul megkereséséhez írja be a *kiértékelés* kifejezést a keresőmezőbe. Húzza a modult a folyamat vászonra. 

1. A **pontszám modell** modul kimenetének összekötése a **modell kiértékelésének**bal oldali bemenetével. 

    Az utolsó folyamatnak a következőképpen kell kinéznie:

    ![A folyamat helyes konfigurációját ábrázoló képernyőfelvétel.](./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png)

### <a name="run-the-pipeline"></a>A folyamat futtatása

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

### <a name="view-results"></a>Eredmények megtekintése

A Futtatás után megtekintheti a folyamat futtatásának eredményét. 

1. A **pontszám modell** modul kiválasztásával megtekintheti a kimenetét.

1. A Tulajdonságok ablaktáblán válassza a **kimenetek** > **Megjelenítés**elemet.

    Itt láthatja az előrejelzett árakat és a tényleges árakat a tesztelési adatokból.

    ![Képernyőkép a kimeneti vizualizáció kiemeléséről a mutató feliratú oszlop](./media/tutorial-designer-automobile-price-train-score/score-result.png)

1. Válassza ki a **modell kiértékelése** modult a kimenet megtekintéséhez.

1. A Tulajdonságok ablaktáblán válassza a **kimenet** > **Megjelenítés**elemet.

A modellhez a következő statisztikák láthatók:

* Átlagos **abszolút hiba (Mae)** : az abszolút hibák átlaga. Hiba a várt érték és a tényleges érték közötti különbség.
* **Legfelső szintű, négyzetes hiba (gyökátlagos)** : a tesztelési adatkészleten végrehajtott jóslatok átlagának négyzetes gyökere.
* **Relative Absolute Error** (relatív abszolút eltérés): a tényleges értékek és az összes tényleges értékek átlaga közötti különbségek abszolút eltérésének átlaga.
* **Relative Squared Error** (relatív négyzetes eltérés): a négyzetes eltérések átlaga a tényleges értékek és az összes tényleges érték átlaga közötti különbség négyzetes értékéhez viszonyítva.
* **Meghatározási együttható**: az R négyzetes értékként is ismert, ez a statisztikai metrika azt jelzi, hogy milyen jól illeszkedik a modell az adatokhoz.

Az összes hibastatisztikára igaz, hogy minél kisebb az érték, annál jobb a modell. A kisebb érték azt jelzi, hogy a jóslatok közelebb vannak a tényleges értékekhez. A meghatározási együttható esetében minél közelebb van az értéke egy (1,0), annál jobb az előrejelzések.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag első részében a következő feladatokat végezte el:

* Folyamat létrehozása
* Az adatok előkészítése
* A modell tanítása
* A modell pontszáma és kiértékelése

A második részből megtudhatja, hogyan helyezheti üzembe a modellt valós idejű végpontként.

> [!div class="nextstepaction"]
> [Modellek üzembe helyezésének folytatása](tutorial-designer-automobile-price-deploy.md)

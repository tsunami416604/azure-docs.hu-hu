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
ms.date: 03/04/2020
ms.openlocfilehash: ed3667ada834437e81ffdcb9161c2a726fe6a6dc
ms.sourcegitcommit: 3616b42a0d6bbc31b965995d861930e53d2cf0d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78933314"
---
# <a name="tutorial-predict-automobile-price-with-the-designer-preview"></a>Oktatóanyag: az autó árának előrejelzése a tervezővel (előzetes verzió)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Ebben a kétrészes oktatóanyagban megtudhatja, hogyan használhatja a Azure Machine Learning designert a gépi tanulási modellek betanítására és üzembe helyezésére, ami előre jelezheti a személygépkocsik árát. A Designer egy húzással elválasztó eszköz, amely lehetővé teszi, hogy a gépi tanulási modellek egyetlen soros kód nélkül legyenek létrehozva.

Az oktatóanyag első részében az alábbiakkal fog elsajátítani:

> [!div class="checklist"]
> * Hozzon létre egy új folyamatot.
> * Importálja az adatimportálást.
> * Készítse elő az adatfeldolgozást.
> * Gépi tanulási modell betanítása.
> * A Machine learning-modell kiértékelése.

Az oktatóanyag [második részében](tutorial-designer-automobile-price-deploy.md) a modelljét valós idejű következtetési végpontként helyezheti üzembe, hogy az Ön által elküldött technikai specifikációk alapján előre megjósolja az autó árát. 

> [!NOTE]
>Az oktatóanyag befejezett verziója minta folyamatként érhető el.
>
>A kereséshez nyissa meg a tervezőt a munkaterületen. Az **új folyamat** szakaszban válassza az **1. minta – regresszió: autó árának előrejelzése (alapszintű)** lehetőséget.

## <a name="create-a-new-pipeline"></a>Új folyamat létrehozása

Azure Machine Learning folyamatok több gépi tanulási és adatfeldolgozási lépést szerveznek egyetlen erőforrásba. A folyamatok lehetővé teszik összetett gépi tanulási munkafolyamatok rendszerezését, kezelését és felhasználását a projektek és a felhasználók számára.

Azure Machine Learning folyamat létrehozásához Azure Machine Learning munkaterületre van szükség. Ebből a szakaszból megtudhatja, hogyan hozhat létre mindkét erőforrást.

### <a name="create-a-new-workspace"></a>Új munkaterület létrehozása

A tervező használatához először Azure Machine Learning munkaterületre van szükség. A munkaterület a Azure Machine Learning legfelső szintű erőforrása, amely központi helyet biztosít a Azure Machine Learningban létrehozott összes összetevővel való együttműködéshez.

Ha vállalati kiadással rendelkező Azure Machine Learning munkaterülettel rendelkezik, [ugorjon a következő szakaszra](#create-the-pipeline).

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

### <a name="create-the-pipeline"></a>A folyamat létrehozása

1. Jelentkezzen be a <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.Azure.com</a>-be, és válassza ki a munkaterületet, amellyel dolgozni szeretne.

1. Válassza a **tervező**lehetőséget.

    ![A tervező elérését bemutató vizualizációs munkaterület képernyőképe](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Válassza **a könnyen használható előre elkészített modulok**elemet.

1. A vászon tetején válassza ki az alapértelmezett folyamat neve **folyamat – létrehozva**lehetőséget. Nevezze át az *autó árának előrejelzésére*. A névnek nem kell egyedinek lennie.

## <a name="set-the-default-compute-target"></a>Az alapértelmezett számítási cél beállítása

Egy folyamat egy számítási célra fut, amely a munkaterülethez csatolt számítási erőforrás. A számítási cél létrehozása után újból felhasználhatja azt későbbi futtatásokhoz.

Beállíthatja a teljes folyamat **alapértelmezett számítási célját** , ami azt jelzi, hogy az összes modul ugyanazt a számítási célt használja alapértelmezettként. A számítási célokat azonban modul alapján is megadhatja.

1. A folyamat neve mellett válassza a **fogaskerék ikont** a vászon tetején lévő fogaskerék](./media/tutorial-designer-automobile-price-train-score/gear-icon.png) ikon ![képernyőképen a **Beállítások** panel megnyitásához.

1. A vászontól jobbra található **Beállítások** ablaktáblán válassza a **számítási cél kiválasztása**lehetőséget.

    Ha már van elérhető számítási cél, akkor kiválaszthatja a folyamat futtatásához.

    > [!NOTE]
    > A tervező csak Azure Machine Learning számítási célokon futtathat kísérleteket. Más számítási célok nem jelennek meg.

1. Adja meg a számítási erőforrás nevét.

1. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > Számítási erőforrás létrehozása körülbelül öt percet vesz igénybe. Az erőforrás létrehozása után újra felhasználhatja azt, és kihagyhatja ezt a várakozási időt a jövőbeli futtatásokhoz.
    >
    > A számítási erőforrás kiszámítja a nulla csomópontot, ha a költségeket nem lehet megtakarítani. Ha a késést követően ismét felhasználja, előfordulhat, hogy a várakozási idő körülbelül öt percet vesz igénybe.

## <a name="import-data"></a>Adatok importálása

A tervezőben több minta adatkészletet is megadhat a kipróbáláshoz. Ebben az oktatóanyagban használja az **Automobile Price (nyers) adatkészletet**. 

1. Az adatkészletek és modulok palettájának bal oldalán található. Válassza az **adatkészletek**lehetőséget, majd tekintse meg a **minták** szakaszt az elérhető minta-adatkészletek megtekintéséhez.

1. Válassza ki az adatkészlet **Automobile Price (nyers) adatokat**, és húzza rá a vászonra.

   ![Az adathúzás vászonra](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Az adatok vizualizációja

Megjelenítheti az adatokat, hogy megértse a használni kívánt adatkészletet.

1. Válassza ki a **személygépkocsi-árlista (RAW)** modult.

1. A vászontól jobbra található modul részletei ablaktáblán válassza a **kimenetek**lehetőséget.

1. Válassza ki a Graph ikont az adatmegjelenítéshez.

    ![Az adatok vizualizációja](./media/tutorial-designer-automobile-price-train-score/visualize-data.png)

1. Válassza ki az adatablak különböző oszlopait az egyes adatok megtekintéséhez.

    Minden sor egy személygépkocsit jelöl, és az egyes autókhoz társított változók oszlopként jelennek meg. Ebben az adatkészletben 205 sor és 26 oszlop található.

## <a name="prepare-data"></a>Adatok előkészítése

Az adatkészletek általában némi előfeldolgozást igényelnek az elemzés előtt. Előfordulhat, hogy néhány hiányzó értéket észlelt az adatkészlet vizsgálata során. Ezeket a hiányzó értékeket meg kell tisztítani, hogy a modell helyesen elemezze az adatok elemzését.

### <a name="remove-a-column"></a>Oszlop eltávolítása

A modellek betanításakor meg kell tennie valamit a hiányzó információkkal kapcsolatban. Ebben az adatkészletben a **normalizált veszteségek** oszlop sok értéket tartalmaz, így a modellből teljes egészében ki kell zárnia ezt az oszlopot.

1. A vászon bal oldalán található modul palettán bontsa ki az **adatátalakítás** szakaszt, és keresse meg az **Oszlopok kiválasztása az adatkészlet** modulban.

1. Húzza az **Oszlopok kiválasztása az adatkészlet** modulban elemet a vászonra. Dobja el a modult az adatkészlet modul alatt.

1. Csatlakoztassuk az **Automobile Price (nyers)** adatkészletet az **adathalmaz-modul Select oszlopaihoz** . Húzza a mutatót az adatkészlet kimeneti portjáról, amely a vászonon található adatkészlet alján lévő kis kör, az **adatkészletben lévő oszlopok kiválasztása**bemeneti portjára, amely a modul tetején lévő kis kör.

    > [!TIP]
    > Az adatáramlás a folyamaton keresztül jön létre, amikor egy modul kimeneti portját egy másik bemeneti porthoz kapcsolja.
    >

    ![Modulok összekapcsolása](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. Válassza az **Oszlopok kiválasztása az adatkészlet** modulban lehetőséget.

1. A vászon jobb oldalán található modul részletei ablaktáblán válassza az **oszlop szerkesztése**lehetőséget.

1. Bontsa ki az **oszlopnevek** legördülő listát a **Belefoglalás**elem mellett, majd válassza az **összes oszlop**lehetőséget.

1. Új szabály hozzáadásához válassza ki a **+** .

1. A legördülő menüben válassza a **kizárás** és **oszlopnevek**lehetőséget.
    
1. Adja meg a *normalizált – veszteségeket* a szövegmezőben.

1. A jobb alsó sarokban kattintson a **Mentés** gombra az oszlop választójának bezárásához.

    ![Oszlop kizárása](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)

1. Válassza az **Oszlopok kiválasztása az adatkészlet** modulban lehetőséget. 

1. A vászon jobb oldalán található modul részletei ablaktáblán válassza a **Megjegyzés** szövegmezőt, és adja meg a *normalizált veszteségek kizárása*lehetőséget.

    A diagramon megjegyzések jelennek meg, amelyek segítenek a folyamat rendszerezésében.

### <a name="clean-missing-data"></a>Hiányzó adatok tisztítása

A **normalizált veszteségek** oszlop eltávolítása után az adatkészlet továbbra is hiányzó értékeket tartalmaz. A fennmaradó hiányzó adatelemet a **tiszta hiányzó** adatmodul használatával távolíthatja el.

> [!TIP]
> A bemeneti adatokból hiányzó értékek tisztítása előfeltétel a legtöbb modul a Designerben való használatához.

1. A vászon bal oldalán található modul palettán bontsa ki az **adatátalakítás**szakaszt, és keresse meg a **tiszta hiányzó** adatmodult.

1. Húzza a **tiszta hiányzó** adatmodult a folyamat vászonra. Kapcsolódjon az **adathalmaz-modul Select oszlopaihoz** . 

1. Válassza ki a **tiszta hiányzó** adatmodult.

1. A vászontól jobbra található modul részletei ablaktáblán válassza a **teljes sor eltávolítása** a **tisztítási mód**alatt lehetőséget.

1. A vászon jobb oldalán található modul részletei ablaktáblán válassza a **Megjegyzés** mezőt, és írja be a *hiányzó értékek eltávolítása sorokat*. 

    A folyamatnak ekkor a következőhöz hasonlóan kell kinéznie:
    
    ![Oszlop kijelölése](./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Gépi tanulási modell betanítása

Most, hogy már rendelkezik az adatfeldolgozáshoz szükséges modulokkal, beállíthatja a betanítási modulokat.

Mivel előre jelezni szeretné az árat, ami egy szám, regressziós algoritmust is használhat. Ebben a példában egy lineáris regressziós modellt használunk.

### <a name="split-the-data"></a>Az adatfelosztás

Az adatok felosztása gyakori feladat a gépi tanulásban. Az adatokat két külön adatkészletbe kell bontania. Az egyik adatkészlet betanítja a modellt, a másik pedig teszteli, hogy a modell milyen jól van elvégezve.

1. A modul palettáján bontsa ki az **adatátalakítás** szakaszt, és keresse meg az **adatfelosztási** modult.

1. Húzza az **Adatfelosztási** modult a folyamat vászonra.

1. A **tiszta hiányzó** adatmodul bal oldali portjának összekötése az **adatfelosztási** modulba.

    > [!IMPORTANT]
    > Ügyeljen arra, hogy a **tiszta hiányzó adatokat** tartalmazó bal oldali kimeneti portok a **felosztott adatokat**csatlakoztassa. A bal oldali port a megtisztított adathalmazokat tartalmazza. A megfelelő port tartalmazza a lefoglalt adatlemezeket.

1. Válassza ki az **Adatfelosztási** modult.

1. A vászon jobb oldalán található modul részletei ablaktáblán állítsa az **első kimeneti adatkészletben lévő sorok töredékét** 0,7-re.

    Ez a beállítás az adatmennyiség 70 százalékát felbontja a modell betanításához, valamint 30 százalékot a teszteléshez. A 70 százalékos adatkészlet a bal oldali kimeneti porton keresztül lesz elérhető. A fennmaradó adatokat a megfelelő kimeneti porton keresztül érheti el a rendszer.

1. A vászon jobb oldalán található modul részletei ablaktáblán válassza a **Megjegyzés** mezőt, majd adja meg *az adatkészlet felosztása betanítási készletbe (0,7) és a test set (0,3)* értéket.

### <a name="train-the-model"></a>A modell betanítása

A modell betanításához adja meg az árat tartalmazó adatkészletet. Az algoritmus létrehoz egy modellt, amely ismerteti a funkciók és a betanítási információ által bemutatott ár közötti kapcsolatot.

1. A modul palettáján bontsa ki a **Machine learning algoritmusok**elemet.
    
    Ez a beállítás számos, a tanulási algoritmusok inicializálására használható modul-kategóriát jelenít meg.

1. Válassza a **regresszió** > a **lineáris regresszió**lehetőséget, majd húzza a folyamat vászonra.

1. Keresse meg és húzza a **Train Model** modult a folyamat vászonra. 

1. Kapcsolja össze a **lineáris regressziós** modul kimenetét a **Train Model** modul bal oldali bemenetével.

1. Kapcsolja össze az **adatok felosztása** modul betanítási adat kimenetét (bal oldali port) a **Train Model** modul jobb oldali bemenetével.
    
    > [!IMPORTANT]
    > Ügyeljen arra, hogy a **felosztott adatokat** tartalmazó bal oldali kimeneti portok a **betanítási modellhez**kapcsolódjanak. A bal oldali port tartalmazza a betanítási készletet. A megfelelő port tartalmazza a tesztelési készletet.

    ![Képernyőfelvétel a Train Model modul helyes konfigurációjának megjelenítéséről. A lineáris regressziós modul a betanítási modell moduljának bal oldali portjához csatlakozik, és a felosztott adatmodul csatlakozik a betanítási modell jobb portjához.](./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png)

1. A modul palettáján bontsa ki a **modul betanítása**szakaszt, majd húzza a **Train Model** modult a vászonra.

1. Válassza ki a **Train Model** modult.

1. A vászon jobb oldalán található modul részletei ablaktáblán válassza az **oszlop szerkesztése** lehetőséget.

1. Az **oszlop felirata** párbeszédpanelen bontsa ki a legördülő menüt, és válassza az **oszlopnevek**lehetőséget. 

1. A szövegmezőbe írja be a *Price (ár* ) értéket a modell által előre jelzett érték megadásához.

    A folyamatnak így kell kinéznie:

    ![Képernyőfelvétel: a folyamat helyes konfigurációjának megjelenítése a Train Model modul hozzáadása után.](./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png)

### <a name="add-the-score-model-module"></a>A pontszám modell modul hozzáadása

Miután az adatok 70 százalékával betanítja a modellt, a másik 30 százalékra kiterjedően megtekintheti, hogy a modell milyen jól működik.

1. A **pontszám modell** modul megkereséséhez írja be a *score Model* kifejezést a keresőmezőbe. Húzza a modult a folyamat vászonra. 

1. A **Train Model** modul kimenetének összekötése a **score Model**bal oldali bemeneti portjával. Az **adatforráshoz** tartozó adat kimenetének (jobb oldali portjának) összekötése a **score Model**megfelelő bemeneti portjára.

### <a name="add-the-evaluate-model-module"></a>A modell kiértékelése modul hozzáadása

A **modell kiértékelése** modul használatával kiértékelheti, hogy a modell milyen jól szerezte be a teszt adatkészletet.

1. A **modell kiértékelése** modul megkereséséhez írja be a *kiértékelés* kifejezést a keresőmezőbe. Húzza a modult a folyamat vászonra. 

1. A **pontszám modell** modul kimenetének összekötése a **modell kiértékelésének**bal oldali bemenetével. 

    Az utolsó folyamatnak a következőképpen kell kinéznie:

    ![A folyamat helyes konfigurációját ábrázoló képernyőfelvétel.](./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png)

## <a name="run-the-pipeline"></a>A folyamat futtatása

Most, hogy elvégezte a folyamat minden beállítását, elküldheti a folyamat futtatását a Machine learning-modell betanításához. A folyamatokat bármikor elküldheti, miközben a tervezőben felépíti a folyamatokat. Ezt megteheti a munka ellenőrzéséhez, és a folyamat működésének ellenőrzéséhez a várt módon.

1. A vászon tetején válassza a **Küldés**lehetőséget.

1. A **folyamat futtatásának beállítása** párbeszédpanelen válassza az **új létrehozása**lehetőséget.

    > [!NOTE]
    > A kísérletek csoport hasonló folyamata együtt fut. Ha többször is futtat egy folyamatot, kiválaszthatja ugyanazt a kísérletet egymást követő futtatásokhoz.

    1. Adjon meg egy leíró nevet az **új kísérlet neveként**.

    1. Válassza a **Küldés**lehetőséget.
    
    A futtatási állapot és a részletek a vászon jobb felső sarkában tekinthetők meg.

### <a name="view-scored-labels"></a>Pontozásos címkék megtekintése

A Futtatás után megtekintheti a folyamat futtatásának eredményét. Először tekintse meg a regressziós modell által generált előrejelzéseket.

1. A **pontszám modell** modul kiválasztásával megtekintheti a kimenetét.

1. A vászon jobb oldalán található modul részletei ablaktáblán válassza a **kimenetek** > gráf ikon ![megjelenítés ikon](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) az eredmények megtekintéséhez.

    Itt láthatja az előrejelzett árakat és a tényleges árakat a tesztelési adatokból.

    ![Képernyőkép a kimeneti vizualizáció kiemeléséről a mutató feliratú oszlop](./media/tutorial-designer-automobile-price-train-score/score-result.png)

### <a name="evaluate-models"></a>Modellek kiértékelése

A **kiértékelési modell** használatával megtekintheti, hogy a betanított modell milyen jól lett elvégezve a teszt adatkészleten.

1. Válassza ki a **modell kiértékelése** modult a kimenet megtekintéséhez.

1. A vászon jobb oldalán található modul részletei ablaktáblán válassza a **kimenet** > gráf ikon ![megjelenítés ikon](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) az eredmények megtekintéséhez.

A modellhez a következő statisztikák láthatók:

* Átlagos **abszolút hiba (Mae)** : az abszolút hibák átlaga. Hiba a várt érték és a tényleges érték közötti különbség.
* **Legfelső szintű, négyzetes hiba (gyökátlagos)** : a tesztelési adatkészleten végrehajtott jóslatok átlagának négyzetes gyökere.
* **Relative Absolute Error** (relatív abszolút eltérés): a tényleges értékek és az összes tényleges értékek átlaga közötti különbségek abszolút eltérésének átlaga.
* **Relative Squared Error** (relatív négyzetes eltérés): a négyzetes eltérések átlaga a tényleges értékek és az összes tényleges érték átlaga közötti különbség négyzetes értékéhez viszonyítva.
* **Meghatározási együttható**: az R négyzetes értékként is ismert, ez a statisztikai metrika azt jelzi, hogy milyen jól illeszkedik a modell az adatokhoz.

Az összes hibastatisztikára igaz, hogy minél kisebb az érték, annál jobb a modell. A kisebb érték azt jelzi, hogy a jóslatok közelebb vannak a tényleges értékekhez. A meghatározási együttható esetében minél közelebb van az értéke egy (1,0), annál jobb az előrejelzések.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ugorja át ezt a szakaszt, ha folytatni szeretné az oktatóanyag 2. részét, és [telepítse a modelleket](tutorial-designer-automobile-price-deploy.md).

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

A második részből megtudhatja, hogyan helyezheti üzembe a modellt valós idejű végpontként.

> [!div class="nextstepaction"]
> [Modellek üzembe helyezésének folytatása](tutorial-designer-automobile-price-deploy.md)
